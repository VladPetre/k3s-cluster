#K3S
To install k3s use their official repo - https://github.com/k3s-io/k3s-ansible
sudo ansible-playbook -i inventory/sample/hosts.ini site.yml --ask-pass --ask-become-pass
My hosts are in inventory/rpik3s/hosts.ini

#Initial setup
sudo ansible-playbook -i inventory/hosts.ini first_setup.yaml --ask-pass --ask-become-pass
sudo ansible-playbook -i inventory/hosts.ini cgroup_support.yaml --ask-pass --ask-become-pass

#System update
sudo ansible-playbook -i inventory/rpik3s/hosts.ini rpi-update.yaml --ask-pass --ask-become-pass
sudo ansible-playbook -i inventory/rpik3s/hosts.ini set-auth-keys.yaml --ask-pass --ask-become-pass
sudo ansible-playbook -i inventory/rpik3s/hosts.ini set-hosts.yaml --ask-pass --ask-become-pass
sudo ansible-playbook -i inventory/rpik3s/hosts.ini insecure-registry.yaml --ask-pass --ask-become-pass

to copy something to remote https://youtu.be/w9eCU4bGgjQ?t=870  


helm install rancher rancher-stable/rancher   --namespace cattle-system   --set hostname=rancher.adu.lan   --set bootstrapPassword=admin

helm install rancher rancher-latest/rancher --namespace cattle-system --set hostname=rancher.rd.localhost --set bootstrapPassword=admin --wait --timeout=10m