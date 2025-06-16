# Docker
- dockerfile reference:
## Containers 

### docker create 
cria o container

### docker start <container_id>
Inicia a execução do container
`-a`: pede para o docker observar pro retornos do container em execução

### docker logs <container_id>
obtém os logs exibidos por um determinado container em execução

### docker run <image-name> <command-name>
create and run a container from a image
<command-name> execute additional command inside the container

- `docker run --detach --name iam-api-test 

### docker ps
list all running containers
`--all` : mostra todos os containers já executados

### docker system prune
deleta containers parados
> serve para desocupar recursos usados por esses containers parados

### docker stop <container_id>
solicita a interrupção do container e espera pelo processo finalizar

### docker kill <container_id>
OBRIGA a interrupção do container IMEDIATAMENTE

### docker exec -it <container_id> <command-name>
executa um novo comando dentro de um container em execução
`-it` permite input ao container, O `-i` é para o input e o `-t` para formatação organizada

## Images

### Build https://docs.docker.com/reference/cli/docker/buildx/build/
`docker build --progress=plain .`
Constroi uma imagem e exibe as etapas do processo

docker build -f src\Yaba.Worker.Categorizer\Dockerfile -t yaba-worker-categorizer:v0.1.0 .

===

# Dockerhub

## docker push https://docs.docker.com/reference/cli/docker/image/push/

- docker login
    - insert credentials
- if necessary: docker tag local-image:tagname new-repo:tagname
- docker push new-repo:tagname

=====================================================================

# Docker Compose
- compose.yml reference: https://docs.docker.com/compose/compose-file
- docker compose CLI reference: https://docs.docker.com/compose/reference/ 

## docker compose up
run a new instance of container from image for all images
Equivalente a chamar o `docker run myimage`



### --detach
evita que os logs da aplicação sejam exibidos no cmd atual

### --build
Equivalente a chamar o `docker build .` antes

### Rebuild only the API service
`docker-compose build --no-cache [service]`

### Start only specific services
`docker-compose up [service1] [service2]`

## docker compose down
remove todos os containers

===

# Dockerfile

## ENTRYPOINT
An ENTRYPOINT allows you to configure a container that will run as an executable.
So when I use `ENTRYPOINT ["dotnet", "DotNet.Docker.dll"]` the container will run
`dotnet DotNet.Docker.dll`

That is a valid command: 
https://learn.microsoft.com/en-us/aspnet/core/host-and-deploy/docker/building-net-docker-images?view=aspnetcore-8.0#build-and-deploy-manually


## VOLUME

# Frequently Used Commands



OTEL COLECTOR
docker run -d --rm -it -p 4317:4317 -p 4318:4318 -v e/Projetos/Kinvo/otel.colector:/cfg otel/opentelemetry-collector-contrib:latest --config=/cfg/config.yaml


