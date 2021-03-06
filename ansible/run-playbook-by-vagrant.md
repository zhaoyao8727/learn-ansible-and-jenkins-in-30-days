# 透過 Vagrant 運行 playbook

#### Vagrant 如何運行 playbook？

除了直接使用 Ansible 的 `ansible-playbook` 指令來對 managed node 進行部署外，若我們是用 Vagrant 搭建虛擬主機的話，我們還可以利用 Vagrant 內建的[ provision ](https://www.vagrantup.com/docs/provisioning/ansible.html)直接運行 playbook。由於 Vagrant 已經知道管理主機的各項資訊，因此我們可以完全省略在上一個章節中設定 `inventory` 及 `PRIVATE_KEY_FILE` 的部分。

首先，在 `Vagrantfile` 中添加以下內容來告訴 Vagrant 要以哪個 playbook 來做部署配置：

```ruby
Vagrant.configure("2") do |config|
  config.vm.box = "bento/ubuntu-14.04"
  config.vm.define "server"

  # run Ansible playbook from Vagrant host
  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "playbook.yml"
  end
end
```

接下來我們只要在終端機運行以下指令，就可以看到與使用 `ansible-playbook` 指令完全一樣的結果：

```shell
$ vagrant provision

==> server: Running provisioner: ansible...
Vagrant has automatically selected the compatibility mode '2.0'
according to the Ansible version installed (2.4.2.0).

Alternatively, the compatibility mode can be specified in your Vagrantfile:
https://www.vagrantup.com/docs/provisioning/ansible_common.html#compatibility_mode

    server: Running ansible-playbook...

PLAY [server] ******************************************************************

TASK [Gathering Facts] *********************************************************
ok: [server]

TASK [test connection] *********************************************************
ok: [server]

TASK [print debug message] *****************************************************
ok: [server] => {
    "msg": {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
}

PLAY RECAP *********************************************************************
server                     : ok=3    changed=0    unreachable=0    failed=0
```

雖然透過 Vagrant 運行 playbook 的步驟容易許多，但必須注意的是，這個方法只限於我們使用 Vagrant 搭建虛擬機的時候才能使用。若今天我們需要直接部署配置到實體主機上，我們就無法透過 Vagrant 指揮 managed node 了。這也是為什麼我們還是必須知道如何使用最正規的方法運行 playbook 進行部署，這個章節的教學只是提供一點搭配的小技巧，讓讀者在練習或開發 Ansible playbook 的時候能夠節省更多時間。
