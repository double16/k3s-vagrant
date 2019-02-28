Vagrant Ubuntu image with k3s and Helm installed.

```shell
vagrant up
vagrant ssh
kubectl get all
helm version
```

# Run Kafka

Modify your Vagrantfile and `vagrant reload`:
```
config.vm.provider "virtualbox" do |vb|
  vb.cpus = 4
  vb.memory = "8192"
end
```

```shell
helm install incubator/kafka --set persistence.enabled=false
```

# Run Mongo

See above to increase your VM resources.

```shell
helm install stable/mongodb --set persistence.enabled=false --set replicaSet.enabled=true
```

