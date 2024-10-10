# SPDX-FileCopyrightText: 2020-present Open Networking Foundation <info@opennetworking.org>
#
# SPDX-License-Identifier: Apache-2.0

Vagrant.configure(2) do |config|
    config.vm.box = "5g-spector.box"
    config.vm.define "5g-spector" do |ran|
        ran.vm.disk :disk, size: "50GB", primary: true
        ran.vm.hostname = "5g-spector"
        ran.vm.network :public_network, :dev => "br0", :ovs => true, :mode => "bridge", :type => "bridge"
        ran.vm.provider "libvirt" do |v|
            v.cpus = 8
                #v.cpu_mode = "host-passthrough"
                v.cpu_model = "qemu64"
                #v.cpuset = '1-4'
                #v.cputopology :sockets => '1', :cores => '2', :threads => '1'
            v.memory = 8192
            v.storage_pool_name = "main_pool"

        end

        ran.vm.provision "shell", inline: <<-SHELL
            wget -O - https://apt.kitware.com/keys/kitware-archive-latest.asc 2>/dev/null | gpg --dearmor - | sudo tee /usr/share/keyrings/kitware-archive-keyring.gpg >/dev/null
            sudo apt update -y
            sudo apt install -y software-properties-common python3-pip jq httpie ipvsadm ethtool
            sudo bash -c 'echo net.ipv4.ip_forward = 1 >> /etc/sysctl.conf'
        SHELL

        ran.vm.provision "shell", run: 'always', inline: <<-SHELL
            sudo ifconfig eth1 192.168.11.10/24 up || true
            sudo ip addr add 192.168.13.21/24 dev eth1 || true
            sudo route add -net 192.168.10.0/24 dev eth1 || true
            sudo route add -net 192.168.11.0/24 dev eth1 || true
        SHELL
    end
end
