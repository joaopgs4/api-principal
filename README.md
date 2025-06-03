Este é o rep principal. Utilize o clone.sh para clonar os outros reps dentro da pasta /api

chmod +x clone.sh
./clone.sh

O arquivo jankins.yaml contem uma pré configuração do jankens, pode ser usado com:
docker compose -f jankins.yaml up --build
E assim você tera um docker do jankins pronto, faltando apenas configurar as variaveis do jankins