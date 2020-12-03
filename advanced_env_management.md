# Advanced environment management in jupyter

I have recently reorganized a lot of my code, in particular at GFDL, to rely on one central preprocessing repo which has several notebooks for each model-configuration that do only one thing: 
- Read in modeldata, work  out quirks (like missing grid spec info etc) and then save to 'clean' `.zarr` archives.

With this setup it is currently quite annoying, when I want to reprocess files, because I usually do that in a separate environment. I have to quit jupyter, activate the other env and then restart jupyter. 

But now I discovered an [extension](https://github.com/Anaconda-Platform/nb_conda_kernels) that lets you choose the kernel from within the jupyter notebook or lab (I think this was already built into jupyterhub, but that wont exist at GFDL any time soon I guess *sniff*).

I will reorganize my environments in the following way:

My `base` env will become my go-to start env. I will need to install the following packages:
- nb_conda_kernels
- jupyterlab
- ...all the kick ass extensions...
(specifically for gfdl, otherwise you cant install from source or pip?)
- pip
- pytest
- pytest-runner


Then in each env, I only have to install `ipykernel` to be able to connect to them from my base env?
> That would be super sweet because I think the jupyterlab stuff is usually slowing down my env solutions a lot.

Holy SHIT this is awesome! What a game changer.


## Further questions
- Can I install a new env and have it available while a jupyterlab is running?


## workflow
```
$ conda install nb_conda_kernels jupyterlab pip pytest pytest-runner
```


I should set up a yml file.. have started on the laptop
