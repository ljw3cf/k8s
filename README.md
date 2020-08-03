서비스 구성도
===========


선행조건 1. TLS Termination Proxy(TTP) 구성을 위한 인증서 생성
=============================================
TTP를 위한 TLS 인증서 생성

1. TLS 종료 프록시의 인증서/키를 저장할 디렉토리를 생성
<pre>
<code>
$ mkdir ingress-tls
</code>
</pre>

2. 인그레스 컨트롤러를 위한 TLS 키 생성
<pre>
<code>
$ openssl genrsa -out ingress-tls/ingress-tls.key 2048
</code>
</pre>

3. 인그레스 컨트롤러를 위한 TLS 사설 인증서 생성
<pre>
<code>
$ openssl req -new -x509 ingress-tls/ingress-tls.key \
--out ingress-tls/ingress-tls.crt \
--days 3650 -subj /CN=wp.example.com
</code>
</pre>

TLS 키 및 인증서를 위한 시크릿 생성

4. 인그레스 컨트롤러를 위한 인증서와 키를 시크릿에 저장한다.
<pre>
<code>
$ kubectl create secret tls ingress-tls-secret \
--key=ingress-tls/ingress-tls.key \
--cert=ingress-tls/ingress-tls.crt

secret/ingress-tls-secret created
</code>
</pre>

5. 인증서와 키가 시크릿에 저장되었는지 확인한다.
<pre>
<code>
$ kubectl describe secret ingress-tls-secret

...
Data
====
tls.crt: 1001 bytes
tls.key: 1679 bytes
</code>
</pre>

선행조건2. 동적 볼륨 프로비저닝을 위한 Ceph 스토리지 구축 (Rook 사용)
==========================================================
ROOK 다운로드
<pre>
<code>
$ cd ~
$ git clone --single-branch --branch release-1.3 https://github.com/rook/rook.git
</code>
</pre>

Ceph Cluster 생성
<pre>
<code>
$ cd ~/rook/cluster/examples/kubernetes/ceph
$ kubectl create -f common.yaml
$ kubectl create -f operator.yaml
$ kubectl create -f cluster.yaml

# Public cloud의 경우 cluster-on-pvc.yaml로 생성
# Minikube의 경우 cluster-test.yaml로 생성
</code>
</pre>

Ceph Toolbox 생성 및 Ceph healthcheck
<pre>
<code>
$ kubectl create -f toolbox.yaml
$ kubectl exec <rook-ceph-tools 파드명> -n rook-ceph --- ceph status
</code>
</pre>

RBD 생성
<pre>
<code>
$ kubectl create -f csi/rbd/storageclass.yaml

# Production Erasure Coding의 경우 storageclass-ex.yaml로 생성
# Minikube의 경우 storageclass-test.yaml로 ㅅㅐㅇ성
</code>
</pre>

CephFS 생성
<pre>
<code>
$ kubectl create -f filesystem.yaml
$ kubectl create -f csi/cephfs/storageclass.yaml
</code>
</pre>

Storageclass 생성여부 확인
<pre>
<code>
$ kubectl get storageclass

NAME                        PROVISIONER                     AGE
rook-ceph-block             rook-ceph.rbd.csi.ceph.com      9h
rook-cephfs                 rook-ceph.cephfs.csi.ceph.com   9h
</code>
</pre>

