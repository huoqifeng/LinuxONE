# How to get a s390x image

There are different arch on x86 and IBM Z or IBM LinuxONE, if you want want to run workload on IBM Z or IBM LinuxONE, you will need get a build that for s390x arch or build it by your self. This article explains how to find a s390x image and how to build it if you can not find one.

## Get it from official repo
If you are luck you may find the image from the official repo here: https://hub.docker.com/u/s390x/

## Get it from 3rd party repo
If you can not fimd the image form official repo, some 3rd party repo may provided. Such as gcr.io, icr.io, quay.io

Following is an example to check if you can get a s390x image.  

Given image name, get the tag list, eg: image name is `hyperkube`:  
```
curl -k -s -X GET https://gcr.io/v2/google_containers/hyperkube/tags/list | jq -r '.tags[]'
```

Pull amd64 image:  
```
docker pull k8s.gcr.io/hyperkube-amd64:v1.16.2
```

Pull s390x image:  
```
docker pull k8s.gcr.io/hyperkube-s390x:v1.16.2
```

If failed on above command, try as this:  
```
docker pull --platform amd64 k8s.gcr.io/hyperkube:v1.16.2
docker pull --platform s390x k8s.gcr.io/hyperkube:v1.16.2
```

You may check the image id via `docker images |grep hyperkube`.  
If the ID for `--platform amd64` and `--platform s390x` are not same, congratulations, you find the right image. Otherwise, if you got same ID, that means there is no s390x image, you will need build it by yourself.

Note: to use `docker pull --platform` you'll need enable experimental feature for your docker client following [guide](https://docs.docker.com/engine/reference/commandline/cli/)

## Build by self

### Get a free VM on LinuxONE
Follow [guide](https://community.ibm.com/community/user/ibmz-and-linuxone/blogs/barend-baarssen1/2019/10/14/try-a-free-vm-on-linuxone-with-node-red)

### Get the source code from repo
Usually you need clone a repo from github.com

### Refer community guide
If you are lucky, you may find the buide guide in [community](https://github.com/linux-on-ibm-z)

### Build by yourself
If you can not find the guide, try to build it by yourself, it's easy.

#### Start from Makefile
Initial command usually is `make`

Then what you need is to fix the errors met, believe me, it's not so hard :-) 

## Trouble shootings
Some common errors will be listed in this section

### Use s390x base image
Usually, the base image in your `Dockerfile` is based on x86, you need change it to use a s390x base image. which usually can be found in https://hub.docker.com/u/s390x/

### Add missing library
Sometime, the liberary name is not same for amd64 and s390x, you may need to correct that in `Makefile` or `Dockerfile`

### network error
`docker build`, `docker run`, `glide` command appears in Makefile or build scripts may encounter network problem, so that apt-get, apk, curl, wget may fail. You can try to fix the problem by add parameter `--network=host` to avoid network isolation.