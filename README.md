node-deploy-githook.bash
========================

<img alt="" src="ndg.png"/>

Want hasslefree deployment of multiple nodejs applications in one VPS or docker container?

ndg means KISS automatic node deployment for VPS (minimalist PAAS), node project bootstrapper in few lines of bash.

> Zero dependencies: only bash git and ssh!

## Usage

On the liveserver download & configure ndg on liveserver (once):

    $ ssh foo@liveserver.com 
    $ ndg config repositories_dir /srv/noderepos    # location of gitrepos
    $ ndg config apps_dir /srv/nodeapps             # where apps run

Locally: Yay! now we can bootstrap node-projects remotly:

    $ alias ndg='ssh foo@liveserver.com ndg "$@"'
    $ ndg init fooproject 8111 http://foo.liveserver.com

    ndg> Initialized empty Git repository in /srv/noderepos/fooproject
    ndg> --- initing repo
    ndg> --- initing .ndg/hooks/*
    ndg> --- committing README.md, LICENSE and ndg hooks
    ndg> [master (root-commit) 1870848] 1st commit
    ndg> 5 files changed, 10 insertions(+)
    ndg> create mode 100644 .gitignore
    ndg> create mode 100755 .ndg/hooks/build
    ndg> create mode 100755 .ndg/hooks/patch
    ndg> create mode 100755 .ndg/hooks/test
    ndg> create mode 100755 .ndg/hooks/start
    ndg> create mode 100755 .ndg/hooks/stop
    ndg> create mode 100644 LICENSE
    ndg> create mode 100644 README.md
    ndg> create mode 100644 package.json
    ndg> --- pushing to origin
    ndg> get    repo:    git clone foo@liveserver.com:/srv/noderepos/fooproject    
    ndg> logview app:    ssh foo@liveserver.com tailf /srv/noderepos/fooproject/nohup.out
    ndg> deploy  app:    git push origin master

## Code locally, deploy to remote

    $ git clone foo@liveserver.com:/srv/noderepos/fooproject

    ...(code and commit)..

    $ git push origin master

    ...(yay! app is deployed, npm packages installed/updated, infinite application-loop started..)

Your repo will contain a '.ndg'-folder with extra deploymenthooks..for free!

## Manage remotely

    $ alias ndg='ssh -t foo@liveserver.com ndg "$@"'
    $ ndg status fooproject
    app fooproject is running
    $ ndg app list
    ndg> fooproject
    $ ndg app stop fooproject
    $ ndg app start fooproject
    $ ndg app restart fooproject
    $ ndg app delete fooproject

## Scalable instances

Experimentl: Scaling can be achieved using symlinks 

    $ ndg app symlink fooproject fooproject-2 2345 http://fooproject-2.liveserver.com
    $ ndg app start fooproject-2

## Remote logging:

    $ ndg app logtail fooproject 
    trigger .ndg/hooks/stop
    Tue Apr 28 08:53:55 CEST 2015 stopping /srv/nodeapps/fooproject (pid 27474)
    trigger .ndg/hooks/build
    trigger .ndg/hooks/patch
    trigger .ndg/hooks/test
    Tue Apr 28 08:53:58 CEST 2015 starting /srv/nodeapps/fooproject at port 8111

## App start during server reboot

Just put this somewhere in an /etc/init.d/ script:

    ndg app status projectfoo || ndg app start projectfoo

All apps at once:

    for app in /srv/nodeapps/*; do 
      appname=$(basename $app)
      ndg app status $appname || ndg app start $appname
    done

## Optional: ndg-proxy 

ndg-proxy is a websocket/http proxy node application which allows running multiple node applications thru one port.

    ndg app #1 @ port 1234 <---->
    ndg app #2 @ port 1235 <---->  ndg-proxy @ port 80  <----- traffic
    ndg app #3 @ port 1236 <---->

ndg-proxy reads the .ndg/config files from projects during start..easy peasy.

> ndg + ndg-proxy: run containers in 1 container :)

* more info on ndg-proxy: [https://github.com/coderofsalvation/ndg-proxy](https://github.com/coderofsalvation/ndg-proxy)

## dont use githook for deployment!

*sigh*
