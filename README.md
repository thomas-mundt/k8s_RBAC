# k8s RBAC




Create certificates
```
mkdir /root/certificates
cd /root/certificates

openssl genrsa -out john.key 2048
openssl req -new -key john.key -subj "/CN=john/O=developers" -out john.csr
```


Create
```
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  name: john
spec:
  groups:
  - system:authenticated
  request: $(cat john.csr | base64 | tr -d '\n')
  signerName: kubernetes.io/kube-apiserver-client
  usages:
  - client auth
```


```
k get csr
k certificate approve john
k get csr
```

```
k get csr john -o jsonpath='{.status.certificate}'
k get csr john -o jsonpath='{.status.certificate}' | base64 -d > john.crt
```


```
ls -al
john.crt
john.csr
john.key
```



Add user
```
useradd -m john -s /bin/bash
cp john.crt john.key /home/john
cp /etc/kubernetes/pki/ca.crt /home/john
chown -R john.john /home/john
```

```
su - john
k get po --server=https://134.209.147.191:6443 --client-certificate /home/john/john.crt --certificate-authority /home/john/ca.crt --client-key /home/john/john.key
```
















