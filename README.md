**Before reading any further:** do **NOT** try this on a standard Synology DS415+ with 4GB of memory. You need at least 8GB to to run GitLab and still be able to use your Synology. I upgraded my DS415+ to 8GB which would have voided the warranty if it hadn't expired yet. 

## Installation procedure

* Open DSM Package Center and install Docker.
  * Open DSM Control Panel and under “File Sharing” open “Shared Folder”.
  * Create a new shared folder with the name “gitlab” on Volume 1.
  * Hide this shared folder in “My Network Places”.
  * Disable the Recycle Bin.
* Open DSM Docker and under “Registry” download “gitlab/gitlab-ce”.
  * In the “Choose Tag” dialog box select “latest”.
* In DSM Docker under “Image” launch “gitlab/gitlab-ce”.
  * In the “General Settings” dialog box open the “Advanced Settings”.
  * Under “Volume” create and mount folder /volume1/gitlab/logs as /var/log/gitlab
  * Under “Volume” create and mount folder /volume1/gitlab/data as /var/opt/gitlab
  * Under “Volume” create and mount folder /volume1/gitlab/config as /etc/gitlab
  *	Under “Port Settings” bind local port 8022 to container port 22
  *	Under “Port Settings” bind local port 8080 to container port 80
  *	Apply the settings and start the container (this takes a couple of minutes).
*	Open http://example.com:8080/ and enter a new password for user ‘root’.

## Configuration procedure

*	Open http://example.com:8080/ and sign in as user ‘root’.
  *	Open “Admin Area” => “Settings” => “General” => “Visibility and access controls”.
  *	Configure “Custom Git clone URL for HTTP(S)” as “http://example.com:8080“.
*	Open DSM Control Panel and under “Applications” open “Terminal & SNMP”.
  *	Enable the SSH service.
*	Use an SSH client to connect as user ‘admin’ to the Synology.
  *	Use “sudo docker ps” to determine the ID of the running gitlab container.
  *	Use “sudo docker exec -it <container-id> /bin/bash” to access the container.
  *	Edit the gitlab configuration with “vi /etc/gitlab/gitlab.rb”: 
    *	external_url "http://example.com:8080"
    *	gitlab_rails['gitlab_ssh_host'] = 'example.com'
    *	gitlab_rails['gitlab_shell_ssh_port'] = 8022
    *	nginx['listen_port'] = 80
  *	Use “gitlab-ctl reconfigure“ to activate the modified settings.
*	Wait a couple of minutes to allow the GitLab to initialize after the reconfiguration.
*	Open http://example.com:8080/ and check if GitLab functions correctly.

Backup procedure

*	To be documented ...
