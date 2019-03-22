# -*- mode: ruby -*-
# vi: set ft=ruby :

BOXNAME    = "corenlp-vagrant"

$script = <<INLINE

export DEBIAN_FRONTEND=noninteractive ; set -ex

export URL="https://nlp.stanford.edu/software/"
export REL="2018-10-05"

timedatectl set-timezone Europe/Berlin        &> /dev/null
dpkg-reconfigure -f noninteractive tzdata     &> /dev/null
apt update                                    &> /dev/null
apt -y install openjdk-11-jre busybox         &> /dev/null

pushd /vagrant/corenlp
  wget -q -nc ${URL}/stanford-corenlp-full-${REL}.zip
  cat ./stanford-corenlp-full-${REL}.zip | busybox unzip -d /home/vagrant -
popd

pushd /vagrant/languages
  for i in arabic chinese english english-kbp french german spanish
  do
    wget -q -nc ${URL}/stanford-${i}-corenlp-${REL}-models.jar
    cp ./stanford-${i}-corenlp-${REL}-models.jar /home/vagrant/stanford-corenlp-full-${REL}
  done
popd

java -cp "/home/vagrant/stanford-corenlp-full-2018-10-05/*" -mx8g edu.stanford.nlp.pipeline.StanfordCoreNLPServer &

bash -c 'while [[ "$(curl -s -o /dev/null -w ''%{http_code}'' localhost:9000)" != "200" ]]; do sleep 5; done'

echo "CoreNLP startet under http://10.10.10.10:9000"

INLINE

##################################

Vagrant.configure("2") do |config|

  config.vm.box            = "bento/ubuntu-18.04"

  config.vm.hostname       = BOXNAME

  config.vm.network :private_network, ip: "10.10.10.10"

  config.vm.provider "virtualbox" do |vbox|
    vbox.name = BOXNAME
    vbox.customize ["modifyvm", :id, "--cpus",                "2"]
    vbox.customize ["modifyvm", :id, "--memory",           "8192"]
  end

  config.vm.provision :shell, :inline => $script

end
