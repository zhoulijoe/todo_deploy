# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"


Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "ubuntu/trusty64"

  config.vm.provision "hosts" do |provisioner|
    provisioner.add_host "10.20.1.2", ["todo-api"]
    provisioner.add_host "10.20.1.3", ["todo-db"]
  end

  config.vm.provision "ansible" do |ansible|
    ansible.sudo = true
    ansible.playbook = "master.yml"
    ansible.groups = {
      "webserver" => ["api"],
      "dbserver" => ["db"],
      "all_groups:children" => ["webserver", "dbserver"]
    }
    ansible.limit = "all"
  end

  # Setup db server first, so API servlet can connect to DB and initialize correctly
  config.vm.define "db" do |db|
    db.vm.hostname = "todo-db"
    db.vm.network "private_network", ip: "10.20.1.3"
  end

  config.vm.define "api", primary: true do |api|
    api.vm.hostname = "todo-api"
    api.vm.network "private_network", ip: "10.20.1.2"
    api.vm.network "public_network", bridge: "en0: Wi-Fi (AirPort)"
    if FileTest::directory?("../java_todo_server/build/libs")
      api.vm.synced_folder "../java_todo_server/build/libs/", "/home/vagrant/todo_server/"
    end
  end

#  config.vm.define "webapp" do |webapp|
#    webapp.vm.hostname = "todo-webapp"
#    if FileTest::directory?("../../js/js_todo_app/dist")
#      web.vm.synced_folder "../../js/js_todo_app/dist/", "/home/vagrant/todo_webapp/"
#    end
#  end

end
