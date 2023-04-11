Vagrant.configure("2") do |config|
  group_name = "/ansible-test"
  root_password = "password"

  # ansible controller
  controller_ip = "172.16.0.10"
  controller_cpus = 1
  controller_memory = "1024"
  config.vm.define "controller" do |controller|
    controller.vm.box = "centos/7"
    controller.vm.network "private_network", ip: controller_ip
    controller.vm.hostname = "controller"

    controller.vm.provider "virtualbox" do |v|
      v.cpus = controller_cpus
      v.memory = controller_memory
      v.customize ["modifyvm", :id, "--groups", group_name]
      v.name = "controller"
    end

    controller.vm.provision "shell", inline: <<-SCRIPT
      sudo yum install epel-release -y
      sudo yum install vim git tree wget net-tools -y

      # python3 설치
      sudo yum install -y https://repo.ius.io/ius-release-el7.rpm
      sudo yum install -y python36u python36u-libs python36u-devel python36u-pip

      # ansible 설치
      sudo yum -y install ansible

      # timezone 설정
      sudo timedatectl set-timezone Asia/Seoul

      # root sshd접속 허용
      sed -i -e 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config
      systemctl restart sshd

      # root 계정 password 설정
      echo "#{root_password}" | sudo passwd root --stdin
    SCRIPT

  end

  # ansible agent
  agent_count = 3
  agent_ips = (1..agent_count).map { |i| "172.16.0.1#{i+1}" }
  agent_cpus = 1
  agent_memory = "512"
  agent_count.times do |i|
    config.vm.define "agent-#{i+1}" do |agent|
      agent.vm.box = "centos/7"
      agent.vm.network "private_network", ip: agent_ips[i]
      agent.vm.hostname = "agent-#{i}"

      agent.vm.provider "virtualbox" do |v|
        v.cpus = agent_cpus
        v.memory = agent_memory
        v.customize ["modifyvm", :id, "--groups", group_name]
        v.name = "agent-#{i}"
      end

      agent.vm.provision "shell", inline: <<-SCRIPT
        # timezone 설정
        sudo timedatectl set-timezone Asia/Seoul

        # root sshd접속 허용
        sed -i -e 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config
        systemctl restart sshd

        # root 계정 password 설정
        echo "#{root_password}" | sudo passwd root --stdin
      SCRIPT

    end
  end

end