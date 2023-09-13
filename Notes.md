you should update your github url on Build/Dockerfile : https://github.com/Velocity9919/Python-Webapp.git

you should install "make on jenkins server or docker server" (where docker installed the you should install "make")
````
apt install make
````
````
make --version
````
you should update docker image and registry details in "makefile"

IMAGE_REG ?= docker.io

IMAGE_REPO ?= nareshbabu1991/python-webapp

IMAGE_TAG ?= latest
