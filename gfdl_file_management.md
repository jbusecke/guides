# Tutorial: Staging large model runs on GFDL filesystem
The GFDL filesytem is build upon several different components to e.g. archive large amounts of data (`/archive` large size, slow access) and to process smaller chunks (several choices, I will use `/work`: smaller size (needs to be cleaned regularly), faster access).

I started at GFDL with little experience in how to handle data amounts of several TB [CM2.6 climate simulation](p). I initially started out with copying files from `/archive` (getting them from tape via `dmget` ) and sorting them with a loose system into my `/work/user` directory.

Quickly things got quite unorganized, and even more worrisome, after a while I lost track of where my files originated. For these kind of model data, files are often named exactly the same and you might quickly confuse the ocean temperature files from the control run with the climate change scenario - if you are unorganized as I was.

I refactored my directory so that it mirrored the directory structure of the source files, which turned out to be a royal PAIN IN THE ASS, given that the the particular simulation I worked with is spread around several user archives…

I needed a new solution. I wanted to be able to have a permanent central folder structure in my archive, which contains of links for files that other users created or raw data from the model (to avoid wasting disk space), and actual files for results I calculated (so that these would be ‘archived’ right away).

## Building the archive
Link existing output (e.g. from someone elses `archive/<USER>/` folder to your archive folder and organize like you see it fit. I recommend mirroring the filestructure as much as possible, which can be easily done with the `cp -sa /archive/<some user>/model/ .` which recreates all subfolders in `/archive/<some user>/model/` into the current directory.
> Make sure to use an absolute path and the `/` at the end, otherwise this method wont work.
An easier but less flexible way to do this is `ln -s /archive/<some user>/model .`. This will link the full directory but it is harder to select only certain files or patch together model output that is spread over several user accounts.

## Staging the data
I define staging the data as getting them from the tape and copy them to a faster disk location for a limited amount of time to speed up analysis. Usually this means to actually copy not link files from my `archive` folder to my `work` folder.

---------------------
*outdated step*
I am using a fairly simple [bash script](https://github.com/jbusecke/server_setup/tree/master/scripts/gfdl_utils) to stage files to a mirror folder structure on `/work/ ` .  This way I can easily delete files on `/work` and restore them at any time, the links to the original files are neat and organized in `archive`.
*outdated step*

---------------------

## Combination with `gfdl_preprocessing`
I use 



# Efficient searching for model output
I find myself often in postprocessing and history folder, hunting for certain variables and wondering if they were saved in a certain frequency. The most realiable way to check this is usually to untar folders from the history directory (the raw model output) and then search in the resulting files for variable names and timesteps.

The following steps are carried out in [this](TBW) script:
1. Untar target archive into current directory
2. Concatenate ncdump output into single text file

This enables searching all



-----------------------------------------
Below is pretty cluttered.
## History folders

## Investigating tar files
```
tar -tvf archive.tar # show contents
```
```
tar -xvf archive.tar # untar
```


## Untaring from history folders
`tar -xvf archive.tar -C path` with extract the full archive into `path`.
To extract only a certain file use `tar -xvf archive.tar -C path`


From within the `.history/` directory use a loop
`for folder in 'ocean' 'ocean_budgets' 'ocean_trans' 'ocean_minibling_100m' 'ocean_minibling_upper_100m' 'ocean_minibling_adv*' 'ocean_minibling_field*' 'ocean_minibling_term*' ; do mkdir -p ../pp/"${folder%'*'}"/av/monthly_1yr/;for f in *.tar; do tar -xvf $f -C ../pp/"${folder%'*'}"/av/monthly_1yr --wildcards "*.$folder.nc"; done ;done`
The `“${folder%'*'}"` removes the trailing wildcard, so that all the files are collected in one folder.

Some other runs have this weird `cpio` extension:
`for folder in 'ocean_month' 'ocean_budgets' 'ocean_trans' 'ocean_minibling_100m' 'ocean_minibling_upper_100m' 'ocean_minibling_adv*' 'ocean_minibling_field*' 'ocean_minibling_term*' ; do mkdir -p ../pp/"${folder%'*'}"/av/monthly_1yr/;for f in *nc.cpio; do cpio -idv "*$folder.nc" < $f && mv *".$folder.nc" ../pp/"${folder%'*'}"/av/monthly_1yr/; done ;done`

```
for folder in 'ocean_month' ; do mkdir -p ../pp/"${folder%'*'}"/av/monthly_1yr/;for f in *nc.cpio; do cpio -idv "*$folder.nc" < $f && mv *".$folder.nc" ../pp/"${folder%'*'}"/av/monthly_1yr/; done ;done
```

More stuff [How to Retrieve Specific Files From a Tape (cpio) (System Administration Guide: Basic Administration)](https://docs.oracle.com/cd/E19683-01/806-4073/6jd67r9pl/index.html)
https://www.thegeekstuff.com/2010/08/cpio-utility/


For more elaborate stuff (like only extract certain years…I will write a script

## Batch renaming
This converts filenames
`for f in *ocean_minibling_fields*; do mv "$f" "${f/ocean_minibling_fields/field}";done`

And this shortens the year spec
`for f in *field.nc; do mv "$f" "${f/0101/}"; done`

## Staging only certain folder using find
`find . -name "*monthly_1yr*" -type d -exec safe_stage_script.sh {} \;`


## Unix loop over certain years
I should really implement a year option for the stage script…. this works as a quick fix
`for i in {0000..0179}; do rm $i.field.nc; done`

Combined with find

`find . -name "*monthly_1yr*" -type d -exec bash -c 'for i in {0000..0179}; do rm "$1"/$i*.nc; done' - {} \;`

## Check quota use
`quotause j1b`

## Copy files TO GFDL [?]
I have not found a way to do this without copying them to my local machine and then to a node on analysis, because when logged into public it can not write...I will inquire with the helpdesk

## Copy files from GFDL to tigress
I route ssh connections via noble (which is not blocked from the 'outside world' like tiger and tigressdata).
The following code needs to be in your `~/.ssh/config`.
> I only recommend to use this for copying your own files (code etc.), since for many data at GFDL there are restrictions on how to move them off the system!

```
Host tiger-offcampus
     User <username>
     Hostname tiger.princeton.edu
     ProxyCommand ssh -A <username>@nobel.princeton.edu -W %h:%p
```


## Conda at GFDL
GFDL maintains a mirror of conda-forge behind the firewall. Adding `--override-channels -c https://anaconda.rdhpcs.noaa.gov/conda-forge` or editing your [`.condarc`](https://github.com/jbusecke/server_setup/blob/master/dotfiles/conda/condarc_gfdl) file enables you to install environments from the analysis nodes.
