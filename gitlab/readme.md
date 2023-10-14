# Run gitlab
```
sudo docker run --detach \
  --hostname < <your IP> \
  --publish 443:443 --publish 80:80 --publish 22:22 \
  --name gitlab \
  --restart always \
  --volume $GITLAB_HOME/config:/etc/gitlab \
  --volume $GITLAB_HOME/logs:/var/log/gitlab \
  --volume $GITLAB_HOME/data:/var/opt/gitlab \
  --shm-size 256m \
  gitlab/gitlab-ee:latest
```

### User: root
### Password: 
 ```
 docker exec -it gitlab grep 'Password:' /etc/gitlab/initial_root_password
 ``````


<!-- ## Gitlab password

- /etc/gitlab/initial_root_password -->

## Install gitlab runner

Run docker gitlab runner

First you have to download the runner binnary: 

``` curl -LJO "https://gitlab-runner-downloads.s3.amazonaws.com/latest/deb/gitlab-runner_amd64.deb" ```


## Follow the instructions of gitlab server
 - Settings -> CI/CD -> Runners -> new project runner

## Create a "loacal" Container registry sever

[Deploy a registry server](https://docs.docker.com/registry/deploying/)

docker run -d -p 5000:5000 --restart=always --name registry registry:2


## Configure dockerclient 

To add an insecure docker registry, add the file /etc/docker/daemon.json with the following content:

{
    "insecure-registries" : [ "hostname.cloudapp.net:5000" ]
}

sudo systemctl restart docker.service

## Create pipeline of gitlab



## COnfiguration of k8s to make a deploy





