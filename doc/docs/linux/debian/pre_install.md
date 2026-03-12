K3S KubeCluster Raspberry Pi (5)

OS: Raspberry Pi OS Lite 64-bit

(HANDY: apt install -y curl wget iptables net-tools mlocate)
apt install -y curl wget iptables

cat << EOF > /etc/profile.d/alias.sh
alias vi=/usr/bin/vim.tiny
EOF

sed -i.bak '/127.0.1.1/s/^/#/' /etc/hosts

cat << EOF >> /etc/hosts
192.168.178.181 p01 p01.jrn.lan proxy proxy.jrn.lan
192.168.178.182 p02 p02.jrn.lan kmaster kmaster.jrn.lan
192.168.178.183 p03 p03.jrn.lan kworker01 kworker01.jrn.lan
192.168.178.184 p04 p04.jrn.lan kworker02 kworker02.jrn.lan
EOF


sed -i.bak 's/CONF_SWAPSIZE=512/CONF_SWAPSIZE=0/g' /etc/dphys-swapfile
OR
add 

add cgroup_memory=1 cgroup_enable=memory to /boot/firmware/cmdline.txt
reboot

#MASTER NODE
## https://docs.tigera.io/calico/latest/getting-started/kubernetes/k3s/quickstart
## Look @ this: curl -sfL https://get.k3s.io | K3S_KUBECONFIG_MODE="644" INSTALL_K3S_EXEC="--flannel-backend=none --cluster-cidr=192.168.0.0/16 --disable-network-policy --disable=traefik" sh -
curl -sfL https://get.k3s.io | INSTALL_K3S_EXEC="--disable traefik" sh -s -
#curl -sfL https://get.k3s.io | sh -
systemctl status k3s
cat /var/lib/rancher/k3s/server/node-token


# Change /etc/systemd/system/k3s.service
# ExecStart=/usr/local/bin/k3s \
#	 server \
#	 --disable=traefik \

# systemctl daemon-reload

kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.26.1/manifests/calico.yaml



#WORKER NODES
curl -sfL https://get.k3s.io | K3S_URL=https://kmaster.jrn.lan:6443 K3S_TOKEN=K10770f244af193c49c6f8cb4ccb6139644b90f02e0df4c4e8a661704cef35d9879::server:9b3b1b48c5e35629bf4032ef05885921 sh -


#INGRESS
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh

helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update

create file deploy-ingress-nginx.yaml (see contents)
kubectl create namespace ingress-nginx
kubectl apply -f deploy-ingress-nginx.yaml -n ingress-nginx

