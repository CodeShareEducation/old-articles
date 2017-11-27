# Primeiros passos com Docker

## Introdução

**Docker** está aqui para nos oferecer uma maneira eficiente e rápida de transportar aplicativos em sistemas e máquinas. É leve e permite que você tenha rapidamente aplicativos executando em seu próprio ambiente. Então vamos estudar um pouco sobre **Docker** e ver como começar a usar essa ferramenta.

Neste primeiro post vou abordar sobre a instalação do **Docker** no **Ubuntu**, mas caso você use **Windows** ou **Mac** você poderá
usar o **[Docker for Windows](http://www.docker.com/products/docker#/windows)** ou
**[Docker for Mac](https://www.docker.com/products/docker#/mac)**, em ambos os casos eles irão fazer download do instalador, e com isso vocês terão o **Docker** instalado na versão mais atual.

O primeiro passo para instalar o **Docker** no **Ubuntu** é conferir se você tem o `curl` instalado.

```shell
which curl
```

Caso não esteja instalado você pode instalar com o seguinte comando:

```shell
apt-get install curl
```

Com o `curl` instalado vamos instalar o **Docker:**

```shell
curl -sSL https://get.docker.com/ | sh
```

Este comando vai identificar automaticamente qual é a sua distribuição do **Linux** e vai instalar a última versão do **Docker**.
Para conferir se o **Docker** está instalado basta usar o comando:

```shell
docker
```

Com isso você verá uma lista de comandos que podem ser utilizados. Agora vamos conferir se o serviço do **Docker** está sendo executado,
para isso basta usar o comando:

```shell
service docker status
```

Caso a mensagem retornada seja, `Docker is not running` devemos iniciar o serviço com o comando:

```shell
service docker start
```

Beleza, agora que temos o **Docker** instalado e executando podemos começar a trabalhar. Para utilizar o **Docker** precisamos conhecer alguns comandos básicos. Abaixo irei listar alguns comandos que são necessário para começar a utilizar o **Docker:**

- Para procurar uma imagem:

```shell
docker search ubuntu
```

> Com o comando acima o docker vai listar todas as imagens ubuntu que estão no `Docker Hub`.

- Para fazer download de uma imagem:

```shell
docker pull ubuntu
```

- Para listar todas as imagens que você possui:

```shell
docker images
```

> Após ter baixado a imagem com `docker pull` podemos executar o comando `docker images` para ver a imagem baixada.

- Para remover uma imagem que não está sendo usada:

```shell
docker rmi images
```

- Para criar um container baseado na imagem que foi baixada anteriormente:

```shell
docker run ubuntu
```

  - Abaixo segue uma lista com algumas das variações do comando `docker run`:

    - `docker run -i -t ubuntu /bin/bash:` Cria um container e acessa esse container em modo iterativo no bash
    - `docker run -it ubuntu /bin/bash:` Tem o mesmo resultado do comando acima, é somente uma forma simplificada com `-it`
    - `docker run --name myFirstContainer --rm -i -t ubuntu bash:` cria um container com nome `myFirstContainer`, a flag `--rm` indica que o container deve ser removido ao sairmos da sessão
    - `docker run --name myFirstContainer ubuntu:` Cria um container com o nome de `myFirstContainer`

- Para listar todos os containers ativos:

```shell
docker ps
```

- Para listar todos os containers que foram criados (ativos e inativos):

```shell
docker ps -a
```

- Para iniciar um container precisamos passar o `id` ou `nome` do container, para saber essas informações precisamos usar o comando `docker ps -a` listado anteriormente:

```shell
docker start id_do_container/nome_container
```

- Para executar um comando em um container sem precisar acessar o console do prórpio container podemos utilizar o comando:

```shell
docker exec
```

  - Exemplo de como criar um diretório com nome `myDocker` dentro do nosso container:
  ```shell
  docker exec id_container ou nome_container mkdir /tmp/myDocker
  ```

> O docker exec executará apenas se o container estiver `running`.

- Para desativar um container que está ativo precisamos passar o `id` ou `nome` do container, para saber essas informações precisamos usar o comando `docker ps` listado anteriormente:

```shell
docker stop id_do_container/nome_container
```

- Para remover um container precisamos passar o `id` ou `nome` do container, para saber essas informações precisamos usar o comando `docker ps -a` listado anteriormente:

```shell
docker rm id_do_container ou nome_do_container
```

## Conclusão

Estes são apenas alguns comandos (acredito ser os mais utilizados), vale lembrar também que o **Docker** tem muitos outros comandos. Para aprender mais você pode acessar a **[documentação oficial](https://docs.docker.com/engine/reference/commandline/cli/)**.

Por hoje é isso pessoal, em breve estaremos aprendendo mais. Esse é o primeiro post sobre **Docker**, nos próximos artigos vamos ver como **criar uma imagem e enviar ela para o Docker Hub**, como usar **Dockerfile** e para finalizar vamos ver um pouco sobre **Docker Compose**. Espero que tenham gostado! Até o próximo artigo!!!

### Escrito por [Edson Junior](https://roinuj16.github.io)