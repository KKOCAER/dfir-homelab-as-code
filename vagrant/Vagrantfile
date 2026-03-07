# -*- mode: ruby -*-
# vi: set ft=ruby :

# =============================================================
#  DFIR Lab as Code — Vagrantfile
#  VMs: attacker (Kali), sensor (Ubuntu), forensics (Ubuntu)
# =============================================================

VAGRANTFILE_API_VERSION = "2"

MACHINES = {
  "attacker" => {
    box:      "kalilinux/rolling",
    ip:       "10.10.10.10",
    cpus:     2,
    memory:   2048,
    playbook: nil   # Kali is pre-loaded; attacker role applied separately
  },
  "sensor" => {
    box:      "ubuntu/jammy64",
    ip:       "10.10.10.20",
    cpus:     2,
    memory:   3072,
    playbook: "ansible/playbooks/suricata.yml"
  },
  "forensics" => {
    box:      "ubuntu/jammy64",
    ip:       "10.10.10.30",
    cpus:     4,
    memory:   6144,
    playbook: "ansible/playbooks/forensic_tools.yml"
  }
}

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  # ── Shared SSH key for Ansible ──────────────────────────────
  config.ssh.insert_key = false
  config.vm.box_check_update = false

  MACHINES.each do |name, opts|
    config.vm.define name do |node|

      node.vm.box      = opts[:box]
      node.vm.hostname = name

      # Private host-only network for inter-VM comms
      node.vm.network "private_network", ip: opts[:ip]

      # VirtualBox provider settings
      node.vm.provider "virtualbox" do |vb|
        vb.name   = "dfir-#{name}"
        vb.cpus   = opts[:cpus]
        vb.memory = opts[:memory]
        vb.gui    = false

        # Promiscuous mode on adapter 2 (sensor needs it for SPAN)
        if name == "sensor"
          vb.customize ["modifyvm", :id, "--nicpromisc2", "allow-all"]
        end
      end

      # Shared folder — repo root mounted inside every VM
      node.vm.synced_folder ".", "/vagrant", type: "virtualbox"

      # ── Shell bootstrap (common for all nodes) ─────────────
      node.vm.provision "shell", inline: <<~SHELL
        set -e
        apt-get update -qq
        apt-get install -y -qq python3 python3-pip curl wget git 2>/dev/null || true
        echo "Bootstrap done on #{name}"
      SHELL

      # ── Ansible provisioner ─────────────────────────────────
      if opts[:playbook]
        node.vm.provision "ansible" do |ansible|
          ansible.playbook       = opts[:playbook]
          ansible.inventory_path = "ansible/inventory"
          ansible.verbose        = false
          ansible.extra_vars     = {
            lab_node: name,
            lab_network: "10.10.10.0/24"
          }
        end
      end

    end
  end

end
