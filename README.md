# greenplum-salt

> **Development branch, not yet ready for production use, but we're working on it !**

-----

### Documentation

| Readme       | README.md |
| ------------ | --------- |
| Building     | [docs/BUILDING.md][DocBuild]    |
| Requirements | [docs/REQUIREMENTS.md][DocReqs] |
| Testing      | [docs/TESTING.md][DocTests]     |

### Creating Greenplum Cluster

#### Create and run test containers for Docker
```sh
./tasks dind run
```
#### Enter to the main container (with salt-master)
```sh
./tasks dind exec
```
##### Install GP in master
```sh
[mdw] cd /srv
[mdw] ./tasks dev init
[mdw] ./tasks dev apply
```
##### Install GP in slaves
```sh
[mdw] ./tasks dev apply sdw*
```
##### Initialize GP database
```sh
[mdw] ./tasks cfg sysinit
[mdw] ./tasks cfg sshinit
[mdw] ./tasks cfg gpinit
[mdw] ./tasks cfg gpperfmon
```
##### Clean GP
```
[mdw] salt 'sdw*' cmd.run 'ps aux | grep postgres'
[mdw] salt 'sdw*' cmd.run 'killall -9 postgres'
[mdw] salt 'sdw*' cmd.run 'rm -rf /data/primary/*'
[mdw] salt 'sdw*' cmd.run 'ls -la /data/primary/'
[mdw] salt 'sdw*' cmd.run 'rm -rf /data/mirror/*'
[mdw] salt 'sdw*' cmd.run 'ls -la /data/mirror/'
[mdw] salt 'sdw*' cmd.run 'ls -la /tmp/'
```
#### Remove test containers (mdw, sdw1, sdw2, sdw3)
```sh
./tasks dind del
```

License
----
MIT

**Copyright (c) 2018 Luxms Inc.**

[//]: # (These are reference links used in the body of this note and get stripped out when the markdown processor does its job. There is no need to format nicely because it shouldn't be seen. Thanks SO - http://stackoverflow.com/questions/4823468/store-comments-in-markdown-syntax)

   [DocBuild]: https://github.com/luxms/greenplum-salt/tree/dev/docs/BUILDING.md
   [DocReqs]:  https://github.com/luxms/greenplum-salt/tree/dev/docs/REQUIREMENTS.md
   [DocTests]: https://github.com/luxms/greenplum-salt/tree/dev/docs/TESTING.md
