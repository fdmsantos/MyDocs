[Link](https://medium.com/@chamilad/adding-a-self-signed-ssl-certificate-to-aws-acm-88a123a04301)

openssl genrsa 2048 > my-aws-private.key

openssl req -new -x509 -nodes -sha1 -days 3650 -extensions v3_ca -key my-aws-private.key > my-aws-public.crt

aws acm import-certificate --certificate file://my-aws-public.crt --private-key file://my-aws-private.key --region eu-west-1

aws acm list-certificates


arn:aws:acm:eu-west-1:395563851492:certificate/7e0a51ef-c38d-489b-98ac-da1658392025

aws acm add-tags-to-certificate --certificate-arn arn:aws:acm:eu-west-1:395563851492:certificate/7e0a51ef-c38d-489b-98ac-da1658392025 --tags Key=Name,Value=Kubernetes-Certificate Key=Usedby,Value=Kubernetes-apiserver-loadbalancer