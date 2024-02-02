# Changes

- docker challenge
  - add dynamic flag for container with ENV ```"FLAG={original flag}_{generated flag}"```
    - all challenge must access environment variable FLAG and manage their CTF accordingly
    - TODO: ```meta.yaml``` is allowed to not specify ```flag``` field. A completely generated flag will be issued to challenge
  - admin_docker_import.html
    - new end-point for import docker challenge from yaml file
    - require additional python package ```pyyaml```
  - view.html (update to current render system)
  - view.js (rework for new __init__.py file)
  - __init__.py (modified for docker registry v2 apis)
    - use docker self-hosted registry rather than a docker engine for containing images
    - add engine name attributes for docker engine name:port
  - add jQuery to base.html at 
    - ```<script src="https://ajax.googleapis.com/ajax/libs/jquery/3.7.1/jquery.min.js"></script>```
    - CTFd/themes/core/templates/
    - CTFd/themes/admin/templates/
    - CTFd/themes/core-beta/templates/ (this seems to be the one that has been used)
  - TODO: Add script ```view.js``` synced rather than async. User should direct see container info rather than click button to see it. (The problem is from the default behavior of ctfd platform not in script.)

# Setup

- docker registry
  - modify/create ```/etc/docker/daemon.json``` to allow docker engine manage localhost registry
    - ```{"insecure-registries" : [ "localhost:56156" ]}``` 
    - ```sudo systemctl daemon-reload```
    - ```sudo systemctl restart docker```
  - TODO: if registry is privileged, all image and ctfd platform also need to be privileged. (in other cases, either image will not be pushed to registry or platform cannot access/find images in registry.)
- docker engine
- Add plugin
  - ```git submodule add https://github.com/umass-cs-security/CTFd-Docker-Challenges.git ./CTFd/plugins/docker_challenges```
- on mac you can use ```docker run -d -v /var/run/docker.sock:/var/run/docker.sock -p 127.0.0.1:2375:2375 bobrik/socat TCP-LISTEN:2375,fork UNIX-CONNECT:/var/run/docker.sock``` to make docker engine listen to port 2375 and use it for http api
- to delete repo from docker registry, use the ```delete.sh``` in ```./delete```
- new dependency ```pyyaml```