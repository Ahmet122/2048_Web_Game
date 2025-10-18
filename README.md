# For Windows 11

# Prerequisites

* Win 11/10
* Docker Desktop (WSL 2 backend)
* kubectl
* kind
* git

* You can install with winget:

winget install -e --id Docker.DockerDesktop

winget install -e --id Kubernetes.kubectl

winget install -e --id Kubernetes.kind

winget install -e --id Git.Git


1) Clone the repository

git clone --recurse-submodules https://github.com/Ahmet122/2048_Web_Game.git


2) Build the Docker image

docker build -t 2048:1.0 .


3) Create the KIND cluster

kind create cluster --name kind-2048 --config kind\kind-cluster.yaml


4) Install NGINX Ingress (for HTTP routing)

kubectl apply -f https://kind.sigs.k8s.io/examples/ingress/deploy-ingress-nginx.yaml

kubectl wait -n ingress-nginx --for=condition=ready pod -l app.kubernetes.io/component=controller --timeout=90s


5) Load the local image into KIND

kind load docker-image 2048:1.0 --name kind-2048


6) Apply Kubernetes yaml files

kubectl apply -f k8s/namespace.yaml

kubectl apply -f k8s/deployment.yaml

kubectl apply -f k8s/service.yaml

kubectl apply -f k8s/ingress.yaml


7) Map 2048.local to localhost (run as Administrator)

Add-Content -Path "$env:WINDIR\System32\drivers\etc\hosts" -Value "`n127.0.0.1 2048.local"


8) Open the game

* Open http://2048.local/ in Google Chrome Browser


## Everyday use (quick start next time)

* If Docker/KIND was stopped or after reboot:

docker start kind-2048-control-plane

kubectl -n game-2048 get pods

-----------------------------------------------------------------------------

# For MacOS

# Prerequisites

* macOS 12+ or newer
* Docker Desktop for Mac
* kubectl
* kind
* git

# Open Terminal and Install via Homebrew

* Install Homebrew if you don't have it: https://brew.sh

brew install kubectl kind git

brew install --cask docker


* Then launch Docker Desktop from Applications and wait until it's "running"

Verify tools:

kubectl version --client

kind version

git --version

docker version

* The image (`nginx:alpine`) and KIND node images are **multi-arch**; no extra flags needed on Apple Silicon.
* Make sure Docker Desktop is **running** before creating the cluster.


1) Clone the repository

git clone --recurse-submodules https://github.com/Ahmet122/2048_Web_Game.git


2) Build the Docker image

docker build -t 2048:1.0 .


3) Create the KIND cluster (maps host ports 80/443)

kind create cluster --name kind-2048 --config kind/kind-cluster.yaml


4) Install NGINX Ingress

kubectl apply -f https://kind.sigs.k8s.io/examples/ingress/deploy-ingress-nginx.yaml

kubectl wait -n ingress-nginx \
  --for=condition=ready pod \
  -l app.kubernetes.io/component=controller \
  --timeout=90s


5) Load your local image into KIND

kind load docker-image 2048:1.0 --name kind-2048


6) Apply Kubernetes yaml files

kubectl apply -f k8s/namespace.yaml

kubectl apply -f k8s/deployment.yaml

kubectl apply -f k8s/service.yaml

kubectl apply -f k8s/ingress.yaml


7) Map 2048.local to localhost

sudo cp /etc/hosts /etc/hosts.bak

grep -q '2048\.local' /etc/hosts || echo '127.0.0.1 2048.local' | sudo tee -a /etc/hosts >/dev/null


8) Open the game

* Open http://2048.local/ on browser: **[http://2048.local/](http://2048.local/)**

# Everyday use (after reboot / Docker stopped)

* Start the KIND control-plane container if it’s stopped

docker start kind-2048-control-plane

# If image pull issues occur (rare), reload and restart:

docker build -t 2048:1.0 .

kind load docker-image 2048:1.0 --name kind-2048

kubectl -n game-2048 rollout restart deploy/game-2048





