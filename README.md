## Kubenet tutorial Vagrantfile

This is step-by-step Kubenet tutorial from web-site: https://learn.kubenet.dev/

--- 

Currently it is fail on a step "kubenetctl sdc", and need further invistigation.

#### How to run Vagrantfile
Install VirtualBox and Vagrant locally on your Windows:
1) https://www.virtualbox.org/wiki/Downloads
2) https://developer.hashicorp.com/vagrant/install
3) "vagrant up" in a shell.

#### Inpect output
See https://github.com/Dezolder/kubenet-tutorial-vagrant/blob/main/output.txt

In a vagrant provision shells - there step-by-step scripts. Use std-output to see the progress.

#### KNOWN ISSUES
1) Dummy Kubernetes namespace at kubenetctl v0.0.4. Issue on a github: https://github.com/kubenet-dev/kubenetctl/pull/8
