# Setup
## References:
1. [General installation guide](https://docs.gitlab.com/omnibus/installation/)
2. [On Ubuntu installation guide](https://about.gitlab.com/install/#ubuntu)
## Using apt-get package
- Preparation: (Install postfix is optional if you want to use the email service)
  >> sudo apt update  
  >> sudo apt install ca-certificates curl openssh-server postfix  
- Add gitlab repository into your system
  >> curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ee/script.deb.sh | sudo bash
- Install the package: For the list of versions, Check [link](https://packages.gitlab.com/gitlab/gitlab-ce).
  >> sudo apt install gitlab-ce= \<version\>  
  For example:  
  >> sudo apt install gitlab-ce=gitlab-ce-16.9.3-ce.0
## Using gitlab docker image (recommended)
- Note: this is the most convenient way to setup, and migration, beyond my experience.
- Preparation: Install docker engine [link](https://docs.docker.com/engine/install/ubuntu/)
- Launch gitlab docker container, corresponding to your targeted version [link](https://docs.gitlab.com/ee/install/docker.html). Below is the script to start docker engine.
  > sudo docker run --detach \\  
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;--hostname gitlab.example.com \\  
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;--env GITLAB_OMNIBUS_CONFIG="external_url 'http://gitlab.example.com'" \\  
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;--publish 443:443 --publish 80:80 --publish 22:22 \\  
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;--name gitlab \\  
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;--restart always \\  
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;--volume $GITLAB_HOME/config:/etc/gitlab \\  
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;--volume $GITLAB_HOME/logs:/var/log/gitlab \\  
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;--volume $GITLAB_HOME/data:/var/opt/gitlab \\  
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;--shm-size 256m \\  
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;gitlab/gitlab-ce:\<version\>-ce.0
- Explanation of params in the script:
  - detach: This option will let docker container run in background. To access deployed container, use "sudo docker exec -it <container_name> bash". For example, "sudo docker exec -it gitlab bash". We will use this to config gitlab server in docker container.
  - env: This option will assign value to the param GITLAB_OMNIBUS_CONFIG, which is the domain gitlab server will link to, so that you can access from world wide. If you dont have domain name, you can set it as your host server IP. For example, your host server IP is 0.0.0.0, then we have "external_url 'http://0.0.0.0'"
  - publish: It is port-link between host server and docker container. The left number is physical port of the host server, and the right number is virtual port of the docker container. 443, 80, 22 are default port of https, http, and ssh repsectively.
  - name: It is a name of docker container
  - restart always: Let docker container restart if something gets wrong.
  - volume: it is folder-link between the host server and the docker container. It is to keep your changing in gitlab when docker container restart. If not, when docker container restart, everything will be refreshed. The left folders are ones on the host server, and the right folders are in the docker container. These are three important folders.
  - gitlab/gitlab-ce:<version>-ce.0: This is the version to execute. For example, gitlab/gitlab-ce:16.9.3-ce.0
## Configuration:
  - Setup external url which will be used for push/fetch/clone service through http/https. If you have domain name, add it to here. If you want use gitlab at your local network, you can add your host server ip address. To configure, open /etc/gitlab/gitlab.rb and look for below param, and replace 'https://example.com' by your input.
    >> external_url 'https://example.com'
  - Setup alert email service: The system will send the alerts to this email if there is something wrong with your SSL certificate. It works only if you install postfix. I obmit this step. To configure, open /etc/gitlab/gitlab.rb and look for below param, and replace the example email by your email.
    >> letsencrypt['contact_emails'] = ['bob@example.com']
  - Increase number of sidekiq workers, this will speedup background process in the gitlab server (optional). In below comment, we will run 4 sidekiq workers [link](https://docs.gitlab.com/ee/administration/sidekiq/extra_sidekiq_processes.html)
    >> sidekiq['queue_groups'] = ['*'] * 4
  - Update config: Save changes and close gitlab.rb. Then execute below command
    >> sudo gitlab-ctl reconfigure

