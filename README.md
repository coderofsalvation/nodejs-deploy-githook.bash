node-deploy-githook.bash
========================

<img alt="" src="ndg.png"/>

KISS automatic node deployment for VPS (minimalist PAAS), node project bootstrapper in few lines of bash.

> Zero dependencies: only bash git and ssh!

## Usage

Download & configure ndg on liveserver:

    $ ssh foo@liveserver.com 
    $ ndg config repositories_dir /srv/noderepos    # location of gitrepos
    $ ndg config apps_dir /srv/nodeapps             # where apps run

Yay! now we can remotely bootstrap node-projects:

    $ ssh foo@liveserver.com ndg init fooproject 8111

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
    ndg> deploy repo:    git push origin master

## Code locally, deploy to remote

    $ git clone foo@liveserver.com:/srv/noderepos/fooproject

    ...(code and commit)..

    $ git push origin master

    ...(yay! app is deployed, npm packages installed/updated, infinite application-loop started..)

Your repo will contain a '.ndg'-folder with extra deploymenthooks..for free!

## Manage remotely

    $ ssh foo@liveserver.com ndg status fooproject
    app fooproject is running
    $ ssh foo@liveserver.com ndg stop fooproject
    $ ssh foo@liveserver.com ndg start fooproject
    $ ssh foo@liveserver.com ndg restart fooproject

## Remote logging:

    $ ssh foo@liveserver.com tailf /srv/noderepos/fooproject/nohup.out
    trigger .ndg/hooks/stop
    Tue Apr 28 08:53:55 CEST 2015 stopping /srv/nodeapps/fooproject (pid 27474)
    trigger .ndg/hooks/build
    trigger .ndg/hooks/patch
    trigger .ndg/hooks/test
    Tue Apr 28 08:53:58 CEST 2015 starting /srv/nodeapps/fooproject at port 8111

