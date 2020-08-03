서비스 구성도
===========


TLS Termination Proxy(TTP) 구성을 위한 인증서 생성
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


</pre>


