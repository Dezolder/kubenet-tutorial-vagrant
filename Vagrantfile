Vagrant.configure("2") do |config|

  config.vm.define "kubenet" do |kubenet|
    config.vm.box = "ubuntu/jammy64"
    config.vm.hostname = "kubenet"
    config.vm.network "private_network", ip: "192.168.1.40"
    config.vm.network "public_network", bridge: "enp0s3"
    config.vm.provider "virtualbox" do |v|
      v.name = "kubenet"
      v.memory = 10240
      v.cpus = 4
      # Enable nested VT-x/AMD-V
      v.customize ["modifyvm", :id, "--nested-hw-virt", "on"]
    end

    # Adding shell provisioner to install kubectl and setup autocompletion
    config.vm.provision "shell", name: "Install Kubectl", inline: <<-SHELL
      # Install kubectl
      echo "\nPRECONDITION STEP: Installing kubectl"
      curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
      install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
      kubectl version --client
      # Setup autocompletion for kubectl
      echo 'source <(kubectl completion bash)' >> /home/vagrant/.bashrc
      echo 'alias k=kubectl' >> /home/vagrant/.bashrc
      echo 'complete -F __start_kubectl k' >> /home/vagrant/.bashrc
    SHELL

    config.vm.provision "shell", name: "Install Kubenetctl", inline: <<-SHELL
      # Install kubenetctl
      echo "\nPRECONDITION STEP: Installing kubenetctl"
      bash -c "$(curl -sL https://github.com/kubenet-dev/kubenetctl/raw/main/install.sh)" #-- -v 0.0.3
      kubenetctl version
    SHELL

    config.vm.provision "shell", name: "Install Kind", inline: <<-SHELL 
      # Install Kind
      echo "\nPRECONDITION STEP: Installing Kind"
      curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.11.1/kind-linux-amd64
      chmod +x ./kind
      mv ./kind /usr/local/bin/kind
      kind version
    SHELL

    config.vm.provision "shell", name: "Install Docker", inline: <<-SHELL
      # Install Docker
      echo "\nPRECONDITION STEP: Installing Docker"
      apt-get update
      apt-get install -y apt-transport-https ca-certificates curl software-properties-common
      curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
      add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
      apt-get update  
      apt-get install -y docker-ce docker-ce-cli containerd.io
      docker --version
    SHELL

    config.vm.provision "shell", name: "Install ContainerLab", inline: <<-SHELL
      # Install ContainerLab
      echo "\nPRECONDITION STEP: Installing ContainerLab"
      bash -c "$(curl -sL https://get.containerlab.dev)"
      whoami
      sudo whoami
      # sudo setsebool -P selinuxuser_execmod 1
    SHELL

    config.vm.provision "shell", name: "Create folder for ContainerLab", inline: <<-SHELL
      # Create folder for ContainerLab
      echo "\nKUBENET STEP0: Create folder for ContainerLab"
      mkdir -p kubenet; cd kubenet
    SHELL

    # Kubenetctl setup
    config.vm.provision "shell", name: "Kubenetctl setup", inline: <<-SHELL
      echo "\nKUBENET STEP1: Kubenetctl setup"
      kubenetctl setup
    SHELL

    config.vm.provision "shell", name: "Validate Kubernetes in Docker", inline: <<-SHELL
      echo "\nKUBENET STEP2: Validate Kubernetes in Docker"
      docker ps 
    SHELL
  
    config.vm.provision "shell", name: "Validate Pods", inline: <<-SHELL
      echo "\nKUBENET STEP3: Validate Pods"
      kubectl get pods -A
    SHELL

    config.vm.provision "shell", name: "Install kubenet components", inline: <<-SHELL
      echo "\nKUBENET STEP4: Install kubenet components"
      kubenetctl install
    SHELL

    config.vm.provision "shell", name: "Validate Pods", inline: <<-SHELL
      echo "\nKUBENET STEP5: Validate Pods"
      kubectl get pods -A
    SHELL

    config.vm.provision "shell", name: "Discovery", inline: <<-SHELL
      echo "\nKUBENET STEP6: Discovery"
      kubenetctl sdc
    SHELL

    config.vm.provision "shell", name: "Validate Targets", inline: <<-SHELL
      echo "\nKUBENET STEP7: Validate Targets"
      kubectl get targets
    SHELL

    config.vm.provision "shell", name: "Validate Running Configs", inline: <<-SHELL
      echo "\nKUBENET STEP8: Validate Running Configs"
      kubectl get runningconfigs.config.sdcio.dev core01 -o yaml
    SHELL

  end

end
