
IMAGE:
docker pull registry.redhat.io/rhscl/nginx-116-rhel7
docker image ls
docker tag 9ef0d7a294ca sdpsvalmbf.rete.testposte:5000/globals/nginx-116-rhel7
docker push sdpsvalmbf.rete.testposte:5000/globals/nginx-116-rhel7



s2i build https://github.com/sclorg/nginx-container.git --context-dir=1.16/test/test-app/ rhscl/nginx-116-rhel7 nginx-sample-app



oc login
oc whoami -t  # GET ACCESS TOKEN


$ docker login -u <user_name> -e <email_address> -p <token_value> <registry_server>:<port>
docker login -u EUTIZIST -p 90gO2YxkGXK2__XbhxZIWZvdQaX9WQ3L3fg3C7DwG6E docker-registry.default.svc:5000

docker-registry.default.svc:5000/apigw-proxy-mutua/reverseproxy@sha256:7d9d38fbeae60729c817789cc29b40ded9724f93c69588ba6762ebcd551cce03
