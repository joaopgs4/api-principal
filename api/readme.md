Para executar localmente (compose):

chmod +x compiler.sh
./compiler.sh
docker-compose up --build

Para executar localmente em kubernets(minikube):

chmod +x deploy.sh
./deploy.sh

Todos os serviços estarão no namespace store

Use o clear.sh para resetar/limpar o minikube