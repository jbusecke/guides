# A tutorial for working with high resolution gcm data on princeton clusters

This replaces a lot of what I wrote I guess [Getting Started with Pangeo on HPC — Pangeo 0.0.1 documentation](https://pangeo-data.github.io/pangeo/setup_guides/hpc.html)
## Why?
The output of modern climate models, particularly eddy rich ocean models, easily reaches multiple TB in size and is simply too large to be processed in one piece on most (even potent) personal computers.

That leaves scientists with two approaches: 
1. Split the data and process it in batches which fit on the personal computer, or 
2. Use the resources of large analysis clusters and even HPC clusters to facilitate analysis of the full dataset

Both methods have their pros and cons, with but I would argue that the pros for 2) far outweigh 1) for these reasons:

* Organization: Copying files back and forth between servers and PC is not only time draining and boring it is also a likely source of errors! „Which file did I use exactly?“ „Was this the newest version of the processed output?“ are typical questions, that if proper documentation - or memory- is lacking, errors could be introduced.
* Cooperation: It is much easier to point collaborators to files on a central filesystem instead of uploading them to dropbox or the likes. Building your analysis code on the server also enables collaborators to easily modify it or to reproduce your results (for this see [[conda]]
* Creativity: Exhausting preprocessing and data shuffling prevents you from doing the actual science! I believe that an interactive workflow enables the user (aka scientist) to interact with datasets in a more non-standard („creative“) way, which in turn might uncover some interesting results.

## How?
Enough if the convincing, lets get started.
This tutorial assumes basic knowledge of the command line (or terminal on MacOS). 

### Python installation using Conda
The backbone of this tutorial is python and various modules. I strongly encourage you to use the package manager [Conda](tbd) to facilitate the installation and management of your python code
Amongst other things, the key features of conda are:
* Easy installation of new packages without need for any „manual stuff“(for the most part. Just run `conda install xarray` and all the necessary dependencies will be installed/upgraded/downgraded.
* Convenient management of code ‘environments’ - self-contained and easily switchable collections of software modules (possibly specific versions), that enable reproducible science, independent of the machine the code is running on

### Jupyter Notebooks/Lab
Jupyter notebooks encourages note taking and documentation of research by enabeling code, notes (in Markdown) and Math (in Latex) to coexist in the same document. 
The graphical user interface is realized using a web browser, which means the notebook looks and works the same if you run the kernel (the software part that actually runs the python code) locally (e.g. on your laptop) or on a server (like tigressdata).

### Xarray

### Github
Git setup ([Setting your username in Git - User Documentation](https://help.github.com/articles/setting-your-username-in-git/)
I strongly recommend setting up ssh keys for git to avoid typing your password in every time you want to pull or push content. 
[Connecting to GitHub with SSH - User Documentation](https://help.github.com/articles/connecting-to-github-with-ssh/)

## How exactly? A step by step Guide
I assume that you know how to do basic operations in the command line (edit textfiles, change directories etc.) If that is not the case, please refer to (/kickass guide I havent linked yet/)

Firstly log into  the machine with 
```
ssh username@tigressdata.princeton.edu
```

### Conda setup
Download the conda installer with 
```
wget https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh -O miniconda.sh
```

MacOSX-x86_64
> This might fail if wget is not installed. You can check if wget is installed with `which wget`. If it is not installed you have 2 choices: 
> 1) Install wget ( I recommend a package manager like [Homebrew](https://brew.sh))
> 2) Download the install manually from [Downloads - Anaconda](https://www.anaconda.com/download/#macos). Note that the exact steps might be slightly different from the ones below.

, and install it with 
```
bash miniconda.sh -p $HOME/code/miniconda
``` 
, and finally remove the installer files with 
```
rm miniconda.sh
```

All anaconda files will be installed in `$HOME/code/miniconda`.

> This should already set the appropriate path but if you dont see output for `which conda1 after installing, this likely means you have to set your path.
> Depending on the shell you are using the concrete steps with vary. 
> Add  `$HOME/code/miniconda/bin`  to your  [PATH variable]([PATH (variable) - Wikipedia](https://en.wikipedia.org/wiki/PATH_(variable))

Now install your first module needed for the next step:
```
conda install jupyter
```


Note: This will install the module in your *root* environment, which is the default loaded for conda. To set up environments follow [these](https://conda.io/docs/user-guide/tasks/manage-environments.html) instructions.

## Conda environments example
You likely need more than the jupyter modules for later analysis. You can see an example setup file for a conda environment [here](https://github.com/jbusecke/server_setup/blob/master/conda_files/standard.yml).


### Jupyter setup
To set options for jupyter notebooks permanently you can edit the file `~/.jupyter/jupyter_notebook_config.py`
Check if the file is already present, and if not create it with 
```bash
jupyter notebook --generate-config
```

Uncomment  the line `c.NotebookApp.port = 9997` and set the port number (in this example `9997` to any allowed combination (!!!in depth link for portforwarding).  This determines the port on which the notebook on the remote server is listening. We need this info later, when we want to connect from our local machine.

… `c.NotebookApp.ip = ‘*’`
`c.NotebookApp.open_browser = False`

To increase security (notebooks allow users to do some potentially nasty stuff) set a password for jupyter by editing the line `c.NotebookApp.password = ‘…’`.
DO NOT PUT your actual password in here! Instead follow the instructions in the  file above 
```
# Hashed password to use for web authentication.
#
#  To generate, type in a python/IPython shell:
#
#    from notebook.auth import passwd; passwd()
```
The instructions have to be run in python, so first start up python in the command line with
```
python
```

Then paste `from notebook.auth import passwd; passwd()` and hit enter.
The command line will promt you for your password. 
If I use `awesome_password` as my password (please use something else!) the function should return something like this
```
Python 3.6.1 |Continuum Analytics, Inc.| (default, May 11 2017, 13:09:58)
[GCC 4.4.7 20120313 (Red Hat 4.4.7-1)] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> from notebook.auth import passwd; passwd()
Enter password:
Verify password:
'sha1:694c4fb1fb1e:4f70d7d200b19499db9c88cbaac83c6189f08d9a'
```

Copy the last line and close python with 
```
exit()
```

Now you should go ahead and open up `~/.jupyter/jupyter_notebook_config.py` where you edit the line
`c.NotebookApp.password = 'sha1:694c4fb1fb1e:4f70d7d200b19499db9c88cbaac83c6189f08d9a'`
The actual values after the equal sign will be different if you chose a different password.  Note that the `'` signs have to be included!
Also change the lines
`c.NotebookApp.ip = '127.0.0.1'`
and
`c.NotebookApp.open_browser = False`

Now we can start a notebook with 
```
jupyter-notebook
```

Open another terminal tab/window and execute the following to connect to the notebook running on the remote server
```
ssh -N -L 9997:localhost:9997 username@tigressdata.princeton.edu
```

Now fire up a browser and go to 
`
Set up ssh connection

### First Analysis

### TODO:
* How to set up SSH keys
* Get a bigger quota
* How to set up ssh config or port forward
* 
* COnda setup `conda create env -f ...`
