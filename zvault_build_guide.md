# zVault.io Build Guide

*In an effort to release this sooner than latter, we have removed a line by line walkthrough of the process.  We feel that installing FreeBSD and Gitea is a fair litmus test to see if someone has the capabilities to address small glitches that will appear from time to time.*

1. Install FreeBSD 13.5.  Configure your network, storage pool, users, ssh access, etc; as you typically would.

2. Install Gitea and modify the app.ini (/usr/local/etc/gitea/conf/app.ini)  
   *(Install whatever gitforge you prefer, we are using gitea, so we will explain using it.)*

```
[git.timeout]
DEFAULT = 360
MIGRATE = 1200
MIRROR = 1200
CLONE = 300
PULL = 300
GC = 60
```

3. Set up mirror for each of the following repos:

* https://github.com/zvaultio/zvio-build
* https://github.com/zvaultio/zvio-os
* https://github.com/zvaultio/zvio-ports
* https://github.com/zvaultio/zvio-webui
* https://github.com/zvaultio/zvio-iocage
* https://github.com/zvaultio/zvio-middleware
* https://github.com/zvaultio/zvio-pkgtools
* https://github.com/zvaultio/zvio-py-fenced
* https://github.com/zvaultio/zvio-py-bsd
* https://github.com/zvaultio/zvio-py-licenselib

   In top gitea menu, click "+" and select "New Migration".  Then select the github icon. Be sure to click "This repository will be a mirror." under Migration Options.  Some of these repos will take a long time to mirror.  Migrate each repo one by one. 

   Once all the repos have been migrated as a mirror.  Fork each one locally on your gitea instance. This will allow you to have one set of repos that will stay in sync with our upstream repos, but allow you to build out of a separate repo you can tinker with and nuke&pave if necessary.

4. Once this has been completed, clone the 'zvio-build' repo to a directory path somewhere on your build machine. `cd` into the directory and switch to the `zvio-13.3` branch. This path is where the root path for the build system will be, so ensure there is plenty of space available. 

5. Run `make bootstrap-pkgs` to install and configure the necessary tooling to run the build system.

6. Modify `./build/profiles/zvault/repos.pyd` and update the URL to the URL of your gitea instance.

7. Launch a tmux session or whatever other tmux-like application you prefer.  

8. Run the following to check out all the repos into your build dir:  
    `make checkout zvault`

9. Once all the repos have been checked out, you can run:  
    `make release PROFILE=zvault PRODUCT=zVault Train=zVault-13`

*How long the build takes will directly relate to the compute and IO capabilities of the server you are building on.  Our build server is 32c/64t with 768GB of RAM. We are building within a ramdisk and the builds take about 2 hours.*

Note for rebranders:
    *If you wish to rebrand, you will need to view all of the commits in the `zvio-13.3` branches to see what we changed.  The major bulk of the work will be in `zvio-build` and `zvio-webui`, but there will be other adjustments needed in `zvio-middleware`.  Be aware, you cannot simply do a `sed` to string replace.  In TrueNAS, the char string `freenas` and `truenas` is used for multiple things.  Sometimes it's a variable, sometimes it's a path, sometimes its a declarative, and sometimes its a variable that is used to modify other variables. Determining which is which is a tedius process. In our repos you will see the values we changed, however there are others which should be changed as well for code cleanliness.* 
