Criar pastas para o repositório git e cópia do projeto

```console
mkdir repositorios/docker-nginx-proxy.git
```

```console
mkdir volumes/docker-nginx-proxy
```

Entrar na pasta git e iniciar o repositório

```console
cd repositorios/docker-nginx-proxy.git
```

```console
git init --bare
```

Criar um git-hook para copiar todos os arquivos do repositório para a pasta de cópia do projeto

```console
nano hooks/post-receive
```

Conteúdo do arquivo post-receive

```sh
#!/bin/sh

CONTAINER_NAME="nginx-proxy"
DIRECTORY_DEPLOY="/root/volumes/docker-nginx-proxy"
GIT_WORK_TREE=${DIRECTORY_DEPLOY} git checkout master -f

if [ "$( docker ps -q -f name=^${CONTAINER_NAME}$ )" ] &&
   [ "$( docker container inspect -f '{{.State.Status}}' ${CONTAINER_NAME} )" = "running" ];
then
    docker exec ${CONTAINER_NAME} nginx -s reload;
else
    docker-compose -f ${DIRECTORY_DEPLOY}/docker-compose.yml up -d;
fi
```

Dar permissão para o arquivo

```console
chmod +x hooks/post-receive
```

Criar um git remote no projeto local

```console
git remote add docker-nginx-proxy ssh://root@167.99.231.111/root/repositorios/docker-nginx-proxy.git
```

O primeiro git push deve ser feito assim

```console
git push docker-nginx-proxy +master:refs/heads/master
```

Após novas alterações o push pode ser com

```console
git push docker-nginx-proxy master
```
