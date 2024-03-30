# Setup
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
  >** sudo docker run --detach \\
                     --hostname gitlab.example.com \\
                     --env GITLAB_OMNIBUS_CONFIG="external_url 'http://gitlab.example.com'" \\
                     --publish 443:443 --publish 80:80 --publish 22:22 \\
                     --name gitlab \\
                     --restart always \
                     --volume $GITLAB_HOME/config:/etc/gitlab \
                     --volume $GITLAB_HOME/logs:/var/log/gitlab \
                     --volume $GITLAB_HOME/data:/var/opt/gitlab \
                     --shm-size 256m \
                     gitlab/gitlab-ce:<version>-ce.0 **
## Configuration:
  - Setup external url which will be used for http/https push/fetch/clone service. If you have domain name, add it to here. If you want use gitlab at your local network, you can add your host server ip address. To configure, open /etc/gitlab/gitlab.rb and look for below param, and replace 'https://example.com' by your input.
    >> external_url 'https://example.com'
  - Setup alert email service: The system will send the alerts to this email if there is something wrong with your SSL certificate. It works only if you install postfix. I obmit this step. To configure, open /etc/gitlab/gitlab.rb and look for below param, and replace the example email by your email.
    >> letsencrypt['contact_emails'] = ['bob@example.com']
  - Update config: Save changes and close gitlab.rb. Then execute below command
    >> sudo gitlab-ctl reconfigure

