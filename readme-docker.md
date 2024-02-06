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
- vagrant
  - ```brew install vagrant-vmware-utility```
  - ```vagrant install vagrant-vmware-desktop```
  - ```vagrant up --provider=vmware_fusion```
- `sudo chmod -R 700 /CTFd_Platform` to modify permission of the folder (user has full access)
- docker registry
  - auth: ```docker run --entrypoint htpasswd httpd:2 -Bbn <username> <password> > auth/htpasswd```
    - ```docker run --entrypoint htpasswd httpd:2 -Bbn labuser cs561labuser > auth/htpasswd```
  - config file: ```{ "insecure-registries":["localhost:56156"] }```
    - Mac config file is at ```~/.docker/daemon.json```
    - Ubuntu config file is at ```/etc/docker/daemon.json```
  - modify/create ```/etc/docker/daemon.json``` to allow docker engine manage localhost registry
    - ```{"insecure-registries" : [ "localhost:56156" ]}``` 
    - ```sudo systemctl daemon-reload```
    - ```sudo systemctl restart docker```
  - TODO: if registry is privileged, all image and ctfd platform also need to be privileged. (in other cases, either image will not be pushed to registry or platform cannot access/find images in registry.)
- docker engine
- Add plugin
  - `git submodule update --init` to pull docker_challenge submodule in CTFd repo
  - ```git submodule add https://github.com/umass-cs-security/CTFd-Docker-Challenges.git ./CTFd/plugins/docker_challenges```
- on mac you can use ```docker run -d -v /var/run/docker.sock:/var/run/docker.sock -p 127.0.0.1:2375:2375 bobrik/socat TCP-LISTEN:2375,fork UNIX-CONNECT:/var/run/docker.sock``` to make docker engine listen to port 2375 and use it for http api
- to delete repo from docker registry, use the ```delete.sh``` in ```./delete```
- new dependency ```pyyaml, docker```
  - ```pyyaml``` for parsing yaml file when importing challenge from ```meta.yaml``` file
  - ```docker``` python docker sdk for registry login

To check whether VM has correct setup after executing `./startup.sh`, issue `sudo lsof -i -P -n | grep LISTEN` to check what ports are mapped from caro-1 to VM. (Suppose have two => 8080:80 for CTFd Platform, and 56151:56151 for ssh connection to VM's student management docker container.)

## Notes
- docker file need to have port `EXPOSE` and command `CMD` specified
- docker-compose file should have `image` field with value `localhost:56156/<you image name>`
- docker_config page is prefered to leave it along (but you need to select all needed images in the registry.)