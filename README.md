Start Docker
====

![Docker](docker.jpeg)


Can we start docker images with a shortcut ?
----


Install
----
`cp start_docker /usr/local/bin`

**PS：** 
* Current directory is mounted on /data of docker.

* Host and container share port 8000, 8888.

* Please run command "xhost +" before start_docker if you want to use x-windows applications in docker, test this feature with following commands in docker environment (Base on ubuntu system):

  `apt-get update`

  `apt-get install xarclock`

  `xarclock`

   



Run
----
`start_docker`

![start_docker](start_docker.png)



License
----
StartDocker is released under the [Apache 2.0 license]

