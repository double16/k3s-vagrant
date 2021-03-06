# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"
  if Vagrant.has_plugin?("vagrant-vbguest")
    config.vbguest.no_install = true
  end

#  config.vm.provider "virtualbox" do |vb|
#    vb.cpus = 4
#    vb.memory = "8192"
#  end

  config.vm.network "forwarded_port", guest: 6443, host: 7443

  config.vm.provision "shell", privileged: false, inline: <<-SHELL
    curl -sfL https://get.k3s.io | sudo sh -
    sleep 10s
    k3s kubectl get node
    export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
    grep -q KUBECONFIG /home/vagrant/.profile || (echo "export KUBECONFIG=/etc/rancher/k3s/k3s.yaml" >> /home/vagrant/.profile)
    kubectl apply -f https://raw.githubusercontent.com/rancher/local-path-provisioner/master/deploy/local-path-storage.yaml
    kubectl patch storageclass local-path -p '{"metadata":{"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'

    curl -sfL -o /tmp/helm.tar.gz https://storage.googleapis.com/kubernetes-helm/helm-v2.13.1-linux-amd64.tar.gz
    sudo tar -C /usr/bin -xzf /tmp/helm.tar.gz --strip-components=1 linux-amd64/helm linux-amd64/tiller
    rm /tmp/helm.tar.gz
    helm init
    helm repo add incubator https://kubernetes-charts-incubator.storage.googleapis.com/
    kubectl create serviceaccount --namespace kube-system tiller
    kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller
    kubectl patch deploy --namespace kube-system tiller-deploy -p '{"spec":{"template":{"spec":{"serviceAccount":"tiller"}}}}'
  SHELL
end
