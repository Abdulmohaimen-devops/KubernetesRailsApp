# KubernetesRailsApp
to deploy Ruby on Rails Application using kubernetes ( I used Minikube )

    1- Create an image for a Rails application 
    2- create a ConfigMap to define environment variables  or use secrets
    3- Create a Database, Redis and Rails services
    4- create deployment to deploy ( app , database , caching )

to install MiniKube and docker :
in Centos run these command: 

 I used this docs: https://vocon-it.com/2018/11/19/single-node-kubernetes-cluster-1-installing-minikube-on-centos/
   
    sudo echo nothing 2>/dev/null 1>/dev/null || alias sudo='$@'
    sudo tee /etc/yum.repos.d/docker.repo <<-'EOF' 
    [docker-ce-edge]
    name=Docker CE Edge - $basearch
    baseurl=https://download.docker.com/linux/centos/7/$basearch/edge
    enabled=1
    gpgcheck=1
    gpgkey=https://download.docker.com/linux/centos/gpg
    EOF
    curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 
    && chmod +x minikube
    sudo cp minikube /usr/local/bin && rm minikube
    yum install -y docker-ce-18.06.1.ce-3.el7.x86_64 \
    && sudo systemctl start docker \
    && sudo systemctl status docker \
    && sudo systemctl enable docker
    systemctl start docker
    systemctl enable docker
   
    KUBE_VERSION=$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)
    curl -Lo kubectl https://storage.googleapis.com/kubernetes-release/release/${KUBE_VERSION}/bin/linux/amd64/kubectl \
    && chmod +x kubectl \
    && mv -f kubectl /usr/local/bin/ \
    && kubectl version
    minikube start --vm-driver=none
   
Using the ruby on rails application that can be found here [https://semaphoreci.com/community/tutorials/dockerizing-a-ruby-on-rails-application]
   
build docker image for this app to use it in kubernetes in app dir :

    docker build -t app .
   
   now we will deploy kubernetes deployment for our app using secret:
     
     1- kubectl create -f deploy1/secret.yml          # add vars to useing them in deploy as a secret
     2- kubectl create -f deploy1/redis-volume.yml    # add redis volume 
     3- kubectl create -f deploy1/postgres-volum.yml  # add database volume
     4- kubectl create -f deploy1/postgres.yml        # add database deployment 
     5- kubectl create -f deploy1/redis.yml           # add redis deployment
     6- kubectl create -f deploy1/sidekiq.yml         # add job schedule deploymet ( sidekiq )
     7- kubectl create -f deploy1/drkiq.yml           # add app deployment
     8- minikube service drkiq --url                  # to get app link to access it
   
   to use config map insead of secret use this command:
   
     1- kubectl create -f deploy2/config-secret.yml   # add vars to useing them in deploy as a configMap 
     2- kubectl create -f deploy2/redis-volume.yml    # add redis volume 
     3- kubectl create -f deploy2/postgres-volum.yml  # add database volume
     4- kubectl create -f deploy2/postgres.yml        # add database deployment 
     5- kubectl create -f deploy2/redis.yml           # add redis deployment
     6- kubectl create -f deploy2/sidekiq.yml         # add job schedule deploymet ( sidekiq )
     7- kubectl create -f deploy2/drkiq.yml           # add app deployment
     8- minikube service drkiq --url                  # to get app link to access it

