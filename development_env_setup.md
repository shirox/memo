# vagrant dmg install

vagrant plugin install sahara

curl -L https://www.opscode.com/chef/install.sh | sudo bash -s -- -P chefdk
sudo chef gem install knife-zero chef-zero --no-ri --no-rdoc

vi ~/.zshrc
export "EDITOR=emacs"

cd ~/Vagrant

PROJECT_NAME=centos6
BOX_NAME=centos/6

echo ${PROJECT_NAME}
echo ${BOX_NAME}

mkdir ${PROJECT_NAME}
cd ${PROJECT_NAME}

vagrant init ${BOX_NAME}

vi Vagrantfile
config.vm.network "private_network", ip: "xxx.xxx.xxx.xxx"
# comment out

vagrant up --provider virtualbox

vagrant ssh-config --host ${BOX_NAME} >> ~/.ssh/config

mkdir chef-repo
cd chef-repo
vi knife.rb

```
local_mode true
chef_repo_path File.expand_path('../', __FILE__)

knife[:use_sudo] = true

knife[:automatic_attribute_whitelist] = %w[
  os
  os_version
  hostname
  ipaddress
  roles
  recipes
  platform
  platform_version
  cloud
  cloud_v2
  chef_packages
]
```

chef exec knife zero bootstrap ${BOX_NAME} --node-name ${PROJECT_NAME}
chef exec knife node list
chef exec knife node show ${PROJECT_NAME}

vi nodes/${PROJECT_NAME}.json						

```
"normal": {
  "knife_zero": {
    "host": "{ホスト名}"
  },

↓

"normal": {
  "knife_zero": {
    "host": "{ホスト名}"
  },
  "fqdn": "{ホスト名}", # <<<<< BOX_NAME
```

chef exec knife cookbook create common
vi cookbooks/common/recipes/default.rb

```
package "net-tools" do
  action :install
end
```

chef exec knife node run_list add ${PROJECT_NAME} common
chef exec knife zero converge "name:${PROJECT_NAME}"

git init
git add .
git commit -m 'first commit'

cd ../
vagrant sandbox on

## sandboxモード開始
# $ vagrant sandbox on
#
## 変更を決定する
# $ vagrant sandbox commit
#
## 変更を破棄し、ロールバックする
# $ vagrant sandbox rollback
#
## sandboxモード終了（commitしていないものは破棄される）
# $ vagrant sandbox off
#
## 現在sandboxモードかどうかの確認
# $ vagrant sandbox status
