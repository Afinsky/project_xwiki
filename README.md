# Project XWiki
This repo by project for deploy xwiki v. 11.4

## Zero point:

2 vagrant machines based on centos 7:

+ stage vm ip 192.168.0.240
+ prod vm ip 192.168.0.242


## Dockerfile stores image with ansible 2.7.10

Build the image as:
```
docker build -t <SOMENAME>/ansible .
```
For example SOMENAME as afinsky:
```
docker build -t afinsky/ansible .
```

Test the ansible running inside the container:
```
docker run --rm -it -v $(pwd):/ansible/playbooks \
    afinsky/ansible --version
```

## Jenkinsfile stores pipe description for local Jenkins machine

