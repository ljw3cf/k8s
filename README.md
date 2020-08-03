서비스 구성도
===========


TLS Termination Proxy(TTP) 구성을 위한 인증서 생성
========================================
1. TTP를 위한 TLS 인증서 생성

TLS 종료 프록시의 인증서/키를 저장할 디렉토리를 생성
<pre>
<code>
$ mkdir ingress-tls
</code>
</pre>

인그레스 컨트롤러를 위한 TLS 키 생성
<pre>
<code>
$ openssl genrsa -out ingress-tls/ingress-tls.key 2048
</code>
</pre>

인그레스 컨트롤러를 위한 TLS 사설 인증서 생성
<pre>
<code>
$ openssl req -new -x509 ingress-tls/ingress-tls.key \
--out ingress-tls/ingress-tls.crt \
--days 3650 -subj /CN=wp.example.com
</code>
</pre>

2. TLS 키 및 인증서를 위한 시크릿 생성

인그레스 컨트롤러를 위한 인증서와 키를 시크릿에 저장한다.
<pre>
<code>
$ kubectl create secret tls ingress-tls-secret \
--key=ingress-tls/ingress-tls.key \
--cert=ingress-tls/ingress-tls.crt

secret/ingress-tls-secret created
</code>
</pre>

인증서와 키가 시크릿에 저장되었는지 확인한다.
<pre>
<code.
$ kubectl describe secret ingress-tls-secret

리소스별 yaml 코드 설명
====================
1. TLS Termination Proxy (Ingress)
<pre>
<code>
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: wp-ing-tls-term
#tls 구성정보
spec:
  tls:
# 사용할 호스트의 FQDN 지정
    - hosts:
        - wp.example.com
# TLS 인증서 및 키가 저장된 시크릿 지정
      secretName: mynapp-tls-secret
  rules:
    - host: wp.example.com
      http:
        paths:
          - path: /
            backend:
              serviceName: wp-svc
              servicePort: 80         
</code>
</pre>


생성된 리소스 확인 및 설명
=====================

동작 확인이 필요한 리소스 동작확인 및 설명
==================================

5. Wordpress 동작화면
