This repo will help configure the Jenkins cluster and it's dependent software.

As part of this ansible playbook we install java, maven and docker inside slave node. Docker and docker compose inside master node.

Also there is another playbook called jenkins_install_playbook which creates container for the jenkins and nginx.
Below are the steps to execute the ansible playbook.

Please make sure inventory has updated with master and slave IPs.

1. ansible-playbook -i inventory setup_playbook_ubuntu.yml
2. ansible-playbook -i inventory jenkins_install_playbook.yml // Not required, added steps to above playbook

# Configure Jenkins Cluster
  
  1. Configure Jenkins master from UI with standard pulgins. http://<PublicIP>:4000
    * Plugins: Multibranch Scan Webhook Trigger, Blue Ocean (for stage wise logs), Docker, Docker Pipeline, SonarQube Scanner for Jenkins (Note: SonaQube server is by default included), Artifactory
  2. Install standard plugins.
  3. Execute ssh-keygen -t rsa -b 4096 -m PEM command in the master node. //Only allows respective PEM key to connect to slave.
  4. Copy the content of id_rsa.pub file and paste it in the authorized_key file of slave node.
  5. Copy the content of the id_rsa file from master node in step-7.
  6. Got to the Jenkins UI > Manage Jenkins -> Manage Credentials.
  7. Create a new Credentials of Kind "SSH Username and private Key". Add anyname as ID, username:ec2-user/ubuntu/centos etc..
  8. Then go the Manage Jenkins -> Manage Node. Create a new node.
      * Number of executers - max 23 executors/Threads/jobs per jenkins worker nodes
      * Remote root directory: /home/ubuntu/jenkins - on slave which folder to use for workspace/code checkout. // Create folder on slave node. 
      * Labels: define a label
      * Launch method: "Launch agents via SSH", Configure: (Host:Slave IP/Name), Credentials(Select created above), Host key verification strategy ("Non verifying verification strategy")
      * Launch Agent. 

