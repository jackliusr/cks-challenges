# cks-challenges


.vimrc

```
set tabstop=2
set expandtab
set shiftwidth=2
set autoindent
```

## Challenge 1

```bash
cp /root/usr.sbin.nginx /etc/apparmor.d/usr.sbin.nginx
apparmor_parser /etc/apparmor.d/usr.sbin.nginx
cat <<EOF | xargs -n1 -I{}  sh -c "echo {}; trivy image -q -s CRITICAL  {} | grep -B2 Total"
nginx:alpine
bitnami/nginx
nginx:1.13
nginx:1.17
nginx:1.16
nginx:1.14
EOF
#nginx:alpine will be least vulnerable version
```
https://kubernetes.io/docs/tutorials/security/apparmor/


# challenge 2

```bash
cat <<EOF >.dockerignore
Dockerfile
EOF
```

```Dockerfile
FROM python:3.6-alpine

## Install Flask
RUN pip install flask

WORKDIR /webapp/app
## Copy All files to /opt
COPY . ./
RUN pip install -r requirements.txt

## Flask app to be exposed on port 8080
EXPOSE 8080

## Flask app to be run as 'worker'
RUN adduser -D worker



USER worker

ENTRYPOINT ["python", "app.py"]
```

https://github.com/ahmetb/kubernetes-network-policy-recipes/blob/master/04-deny-traffic-from-other-namespaces.md

# challenge 3

```bash

mkdir -p /var/www/html/
cd /opt
curl -L https://github.com/aquasecurity/kube-bench/releases/download/v0.6.2/kube-bench_0.6.2_linux_amd64.tar.gz -o kube-bench_0.6.2_linux_amd64.tar.gz
tar -xvf kube-bench_0.6.2_linux_amd64.tar.gz

./kube-bench --config-dir `pwd`/cfg --config `pwd`/cfg/config.yaml >/var/www/html/index.html

ssh node01 'echo "protectKernelDefaults: true" >> /var/lib/kubelet/config.yaml'
ssh node01 systemctl restart kubelet

#TODO
sed '--profiling=false' /etc/kubernetes/manifests/kube-controller-manager.yaml

chown -R etcd:etcd /var/lib/etcd

# NOTICE
# any changes in /etc/kubernetes/manifests, can restart kubelet to speed up the pods launching
```

https://aliartiza75.medium.com/kubernetes-pod-security-policy-5f06a117dcd1
