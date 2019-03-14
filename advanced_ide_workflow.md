# Advanced project workflow with JupyterLab and Atom


## Enabeling remote editing with Atom
Atom offers a lot of useful packages that make it (as of now) a far better tool to write clean code compared to jupyterlabs editor. In order to edit files on remote directories, we will mount them locally using `sshfs`.
After installing [homebrew](https://brew.sh/), follow these steps to install sshfs.

```
$ brew install sshfs
```
this might require you to install macfuse. Go ahead and follow the intstructions and retry the above.

Now create a folder for your mounted drives (here we will use the homefolder)
```
$ mkdir -p ~/mounted_servers/servera
```
and mount your project directory with
```
$ sshfs servera:/scratch/gpfs2/jbusecke/projects ~/remote_drives/servera
```
Now you can navigate to that folder as usual and start atom with `atom .`.

If you need to unmount use
```
$ umount -f /Users/juliusbusecke/remote_drives/servera
```


## Recommended atom packages


