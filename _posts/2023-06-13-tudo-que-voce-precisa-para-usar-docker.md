---
layout: post
title:  "Tudo que você precisa saber pra usar Docker"
date:   2023-06-13 11:40:00 -0300
permalink: /:categories/:year/:title
categories: serie
tags: docker guia
customcss: sim
css: blog
---

De forma muito simplificada, Docker (a plataforma\*) é um software open source que foi feito com o objetivo de facilitar o processo de desenvolver, implantar e executar aplicações. Em resumo, é uma plataforma de *virtualização* através de **contêineres**.
Outra forma de resumir é dizer que é um dos softwares que tem o objetivo de evitar o clichê do

> Na minha máquina funciona.

- \* Docker pode ser o projeto, a ferramenta/plataforma, a empresa. Aqui vamos falar sobre a ferramenta*

Vou deixar alguns exemplos específicos de Node e Python, mas no geral esse post é feito para quem já sabe desenvolver ou já tem uma aplicação pronta, e quer colocar ela para rodar em contêineres.

Se você for iniciante, talvez alguns pedaços não façam muito sentido. Nesse caso pode deixar um comentário que eu tento explicar da melhor forma possível.

## Tudo que eu preciso saber sobre?

Meu objetivo aqui é simples: prover toda informação disponível para você (desenvolvedor) começar e conseguir usar a ferramenta da vez. Não pretendo responder tudo que você **pode** ou **deveria** saber, apenas o necessário.

## O que é Docker

![Docker Logo](../../assets/docker_moby_logo.png)

Docker é uma plataforma de virtualização por **contêiner**, que permite empacotar e executar aplicativos de forma **isolada** do sistema operacional.

Pra complementar, vou deixar um subtópico aqui:

### O que são contêineres

Contêineres (ou *containers)* são uma forma de virtualização de computadores que usa recursos do sistema operacional (SO) hospedeiro, ao invés de precisar fornecer um SO inteiro - como seria uma máquina virtual (VM) - para executar o que precisar. O contêiner compartilha o kernel do SO, e por isso é muito rápido para iniciar e interromper, e altamente portátil. Sua principal vantagem é permitir a execução de aplicações em um ambiente isolado, o que garante que a sua execução em qualquer máquina será a mesma.

## Quando eu deveria usar Docker

Desde que eu aprendi a usar, a minha resposta é **sempre**. Existem alguns aspectos que podem fazer a sua utilização parecer um empecilho, principalmente durante o desenvolvimento, mas vou tentar desmistificar tudo isso e te ajudar a usar da forma mais simples que conheço.

Mas respondendo de forma mais completa, os principais motivos para usar contêineres (na minha opinião) são:

### 1. Fazer uma aplicação com objetivo de publicar

Para resolver uma lista de exercícios de lógica de programação da faculdade usando contêineres, seria um pouco de exagero (mas é possível).

Mas toda vez que você estiver trabalhando em uma aplicação - um site, ou uma api por exemplo - que eventualmente vai ser publicada de alguma forma, encapsular em contêineres vai facilitar muito a sua vida na hora de fazer a implantação (ou *deploy*).

Isso porque o isolamento garante que todas as dependências utilizadas - e aqui podemos considerar pacotes (do npm ou do pip, por exemplo), programas disponíveis no sistema operacional ou até arquivos - estarão disponíveis quando chegar a hora da implantação.

### 2. Padronizar o ambiente de desenvolvimento

O motivo é o mesmo acima, basicamente. Caso você trabalhe em mais de um computador (eu uso o meu notebook pessoal com linux e o notebook da empresa com windows, por exemplo), ou simplesmente esteja desenvolvendo algo em equipe, conteinerizar a sua aplicação vai facilitar muito a configuração inicial do ambiente para começar a desenvolver sem gastar horas instalando programas e pacotes específicos.

## Por onde começar

### Instalação

No meu post sobre a [configuração do windows para desenvolvimento]([https://dev.to/1cadumagalhaes/configuracao-do-windows-para-desenvolvimento-blk#6a-docker](https://dev.to/1cadumagalhaes/configuracao-do-windows-para-desenvolvimento-blk#6a-docker)) eu já expliquei como se instala no Windows, por isso não vou repetir aqui (o link já está com a âncora do pedaço sobre Docker).

No caso de Linux, para instalar basta executar os comandos abaixo:

```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
```

Isso vai baixar o script de instalação oficial do docker e em seguida executar. Esse script não funciona para instalação no WSL, pois o script identifica que estamos no Windows e recomenda que você instale usando o Docker Desktop.

**Dica**: caso queira instalar em algum servidor de forma automática (com terraform, por exemplo), você pode executar o segundo comando como mostrado abaixo. Isso vai garantir que não fique parado no prompt de confirmação

```bash
yes | sudo sh [get-docker.sh](http://get-docker.sh/)
```

## Dockerfile

A base de conteinerizar aplicações está na construção do `Dockerfile`. Esse arquivo pode ser bem complexo, mas o que você precisa saber é:

### FROM

Logo no inicio do seu arquivo, o primeiro comando precisa ser o FROM. Ele é o ponto de partida da sua imagem, e define qual é a base do seu container.

Uso:

- `FROM <image>`
- `FROM <image>:<tag>`

Por exemplo, se eu usar

```docker
FROM python:3.11
```

Vou ter uma imagem que já vai vir com python instalado, além de uma série de dependências comuns de aplicações em python.

De forma semelhante, poderia usar `FROM node:20.3-slim` para inicializar uma aplicação em Node JS.

O seu Dockerfile pode ter vários comandos FROM, juntando pedaços de imagens diferentes para montar a sua aplicação.

Uma coisa que você precisa se atentar, é que as **imagens*** utilizadas vão ter tamanhos diferentes, dependendo da quantidade de coisas que tem instaladas. Imagens mais completas são mais pesadas, e a maioria das imagens comuns tem uma versão `-slim`  , que geralmente vai ter menos dependências.

De forma geral, se você estiver preocupado com o tamanho da imagem do seu container, o ideal seria começar com uma imagem mais “limpa” e instalar manualmente todas as ferramentas que vai utilizar. Mas normalmente não precisamos nos preocupar com isso, então usar a imagem `-slim` já é um bom começo.

### COPY

O próximo passo - considerando que a imagem que escolhemos já tem os programas que precisamos - é passar os arquivos com o seu código para ela, para que possam ser executados. Podemos fazer isso usando o comando `ADD`, ou o comando `COPY`. O primeiro é basicamente o segundo com esteróides, pois além de permitir a cópia, também permite o download de arquivos remotos (através de uma URL) e a descompressão de arquivos comprimidos.

Uso:

- `COPY arquivo`
- `COPY [arquivo_fonte.py](http://arquivo.py) arquivo_destino.py`
- `COPY pasta_fonte/ pasta_destino/`

Isso vai definir onde os seus arquivos vão ser copiados dentro da imagem do contêiner, e é necessário para que possamos executar. Um exemplo comum, no caso de uma aplicação em python por exemplo, é copiar o `requirements.txt` além da pasta com o seu código (no meu exemplo, `app/`

```docker
COPY requirements.txt
COPY app
```

Voltando para o Node, vou aproveitar e apresentar o `.dockerignore`. Semelhante ao `.gitignore`, a função dele é definir no seu projeto (ou pasta) quais arquivos ou diretórios não devem ser acrescentados na imagem. Node é um bom exemplo porque quando instalamos as dependências do nosso projeto (de um `package.json`, por exemplo), elas são salvas na pasta `node_modules/`, dentro do diretório em que estivermos. Essa pasta pode ser muito grande, por isso podemos criar o arquivo `.dockerignore` contendo ela:

```docker
node_modules/
```

E depois, quando fizermos a cópia, saberemos que essa pasta não estará na nossa imagem

```docker
COPY meu_projeto_node
```

### RUN

Em seguida, dependendo da sua aplicação vai ser necessário instalar as dependências do seu projeto para conseguir executar (por isso copiamos o `requirements.txt`, por exemplo). Para isso usamos o comando RUN, que serve para executar qualquer comando de script dentro da sua imagem. Você pode ter múltiplos comandos RUN, e eles vão ser executados **no momento da criação da sua imagem**.

Uso:

- `RUN comando`
- `RUN comando complemento`
- `RUN ["comando", "complemento"]`

Para instalar as dependências de um projeto de python por exemplo, podemos colocar o seguinte após o nosso COPY

```docker
RUN pip install -r requirements.txt
```

Esse comando também pode ser utilizado para instalar pacotes no seu sistema (com `apt install` por exemplo) ou executar qualquer comando de shell.

### CMD

Por fim, também podemos configurar comandos que devem ser executados quando o **container** for executado. É aqui que dizemos como a nossa aplicação deve ser executada, por exemplo.

Uso:

- `CMD comando`
- `CMD comando complemento`
- `CMD ["comando", "complemento"]`

Numa aplicação python simples por exemplo, podemos executar algo como

```docker
CMD python app.py
```

Ou numa aplicação em Node, podemos executar

```docker
CMD npm start
```

Um ponto importante sobre o CMD, é que somente o último comando inserido no seu Dockerfile vai ser executado. Por exemplo, se tivermos

```docker
CMD python main.py
CMD python app.py
```

Somente o segundo será executado.

Caso sua aplicação precise executar mais de um comando para ser inicializada, podemos usar o comando `ENTRYPOINT`, que funciona de forma semelhante.

## Comandos docker

### build

Dentro da pasta da sua aplicação onde você criar o seu arquivo Dockerfile (normalmente são colocados na raiz do projeto, mas vai depender da sua organização), você deve executar o comando

```bash
docker build [PATH]
```

Caso não passe nenhum `PATH`, ele procurará o Dockerfile no diretório atual. Se estiver em outra pasta, basta passar o seu caminho (algo como `docker build container/`

### run

Esse é o comando principal para utilização de docker, e não vou entrar em muitos detalhes, mas a sintaxe básica é:

```bash
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
```

As opções mais comuns que eu costumo utilizar são:

- `—name`: vai definir o nome do seu container. Se não for passado, vai criar com um nome aleatório
- `--volume`, ou `-v`: permite conectar um diretório local com um diretório do container
- `--expose`, ou `-p`: permite expor e mapear portas do seu container para o seu sistema local. Isso é muito importante caso esteja desenvolvendo serviços web
- `-e`: permite passar variáveis de ambiente para o contexto do seu container

## Acessando o seu container

Apesar de ser mais um comando, gosto de separar este pois permite que tenhamos acesso ao exato contexto do nosso container.

```bash
docker exec -it [container] bash
```

Executando esse comando, entraremos num shell interativo do nosso container e que nos permite acessar como qualquer terminal Linux. Dentro deles podemos navegar entre os arquivos, editar e executar comandos. Isso pode ser muito útil caso precise debugar algum problema, mas o caso de uso mais comum para mim foi fazer a manutenção de containeres baseados em imagens prontas, para criar um usuário de uma aplicação por exemplo (dentro do container de Airflow).

## Publicando a sua imagem

Esse tópico sozinho poderia ser um post a parte, por isso vou tentar resumir.

Depois de construir a nossa imagem, nós podemos disponibilizar ela em algum **repositório de imagens**. Isso facilita a implantação, pois a partir do momento em que a imagem está construida, você não precisa mais dos arquivos do código fonte para executar. Existem vários repositórios, mas minhas recomendações são:

### Registros públicos

- Docker Registry: o repositório oficial permite  que você poste suas imagens e deixe acessíveis para qualquer pessoa
- [Github Container registry]([https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry)): também é possível armazenar suas imagens no Github

### Registros privados

- Docker hub: A Docker Inc (a empresa, não o produto) também oferece um serviço de hospedagem privado para salvar suas imagens
- Qualquer nuvem, mas como exemplo vou destacar o Artifact Registry do Google Cloud

Dessa forma, quando for executar o seu serviço, basta referenciar o endereço remoto da imagem na hora da execução. Por exemplo:

```bash
docker run ghcr.io/1cadumagalhaes/TESTE_DOCKER:latest
```

Isso significa que, caso eu queira fazer o deploy da minha aplicação numa VM hospedada em alguma nuvem por exemplo, tudo que eu preciso fazer é:

1. Instalar o Docker
2. Executar esse comando referenciando a minha imagem já existente

Sem se preocupar em clonar o código e sem nenhum setup.

## Docker Compose

O Compose é uma ferramenta desenvolvida pra definir e executar aplicações que dependem de **múltiplos contêineres docker**. Com ele, você usa um arquivo de configuração YAML para definir os serviços da sua aplicação, e depois executa tudo com um único comando.

Apesar de ser focado em aplicações com múltiplos contêineres, eu considero muito prático usar para definir aplicações de um único contêiner também. Isso porque ele permite que nós façamos as definições do nosso serviço de forma declarativa. Por exemplo, digamos que eu esteja trabalhando no desenvolvimento de uma API feita em Python e que vai criar arquivos dentro do sistema, e que usa algumas configurações passadas via variável de ambiente. Para executar o meu container, eu precisaria fazer o seguinte comando:

```bash
docker run \
-v ./output:/app/output \
-p 8080:8080 \
-e NAMESPACE='staging' \
-e LOG_LEVEL='debug' \
-e API_KEY='abc123' \
--name meu_python \
.
```

Claro que podemos salvar tudo isso num shell script (ou simplesmente decorar o comando), mas eu considero inconveniente. Ao invés disso, podemos definir um `docker-compose.yaml` assim:

```yaml
services:
  meu_app:
    buid: .
  ports:
    - "8080:8080"
  environment:
    - NAMESPACE: 'staging'
    - LOG_LEVEL: 'debug'
    - API_KEY: 'abc123'
  volumes:
    - ./output:/app/output
```

E depois de configurar esse arquivo, podemos executar nossa configuração com

```bash
docker compose up
```

Também podemos usar os comandos `down` e `restart` além do `up`, e podemos passar várias opções, como:

- `-f` : Caso o nome do seu arquivo seja diferente de `docker-compose.yaml` , podemos passar através dessa opção
- `-d` : para executar os contêineres “desacoplados”, ou seja sem depender da sua sessão do terminal e ficar em background.

## Usando Hot Reload com Docker

Muitas vezes, quando estamos desenvolvendo precisamos da agilidade de reiniciar o serviço assim que salvamos nossas alterações.

No ambiente local existem ferramentas próprias para isso, como o `nodemon` de Node JS, e elas podem auxiliar muito para que vejamos os resultados das nossas alterações.

Por padrão, para alterar códigos dentro do seu contêiner, você precisa alterar a sua imagem. E para isso, é necessário compilar a imagem (`docker build`) e executar novamente (`docker run`).

Caso você queira usar uma ferramenta de hot/live reload, você pode definir um volume conectando o seu diretório local com o diretório do contêiner, e dessa forma as alterações estarão conectadas. Por exemplo, caso esteja construindo uma API em Python (com FastAPI por ex) e executando com Gunicorn, podemos fazer o seguinte:

No `Dockerfile`

```docker
FROM tiangolo/uvicorn-gunicorn-fastapi:python3.11
COPY requirements.txt /usr/src/requirements.txt
RUN pip install -r /usr/src/requirements.txt
COPY ./app /app/app
```

No `docker-compose.yaml`

```docker
services:
    api:
        build: .
           ports:
              - '8080:80'
           volumes:
              - ./app/:/app/app
           command:
              - /start-reload.sh
```

Dessa forma, quando fizermos qualquer alteração no nosso código, o serviço será reiniciado dentro do contêiner. Caso queira fazer o mesmo com Node, [esse post do Erick Wendel no freeCodeCamp]([https://www.freecodecamp.org/portuguese/news/como-habilitar-o-live-reload-em-aplicacoes-do-docker-utilizando-volumes/](https://www.freecodecamp.org/portuguese/news/como-habilitar-o-live-reload-em-aplicacoes-do-docker-utilizando-volumes/)) explica como fazer essa configuração.

## Onde encontrar mais informações

Além das documentações oficiais, a principal referência que eu tenho é o treinamento da LINUXtips, feito pelo [Jeferson Fernando]([https://twitter.com/badtux_](https://twitter.com/badtux_)). Ele disponibilizou **completamente de graça** o livro Descomplicando o Docker depois de dar o treinamento de graça pra 5 mil pessoas (incluindo eu, na época um jovem padawan estagiário):

- [https://livro.descomplicandodocker.com.br/](https://livro.descomplicandodocker.com.br/)

E depois ele resolveu liberar o treinamento **todo** **completamente de graça** **no youtube.**

- [Playlist Descomplicando o Docker no LINUXtips]([https://www.youtube.com/watch?v=Wm99C_f7Kxw&list=PLf-O3X2-mxDn1VpyU2q3fuI6YYeIWp5rR](https://www.youtube.com/watch?v=Wm99C_f7Kxw&list=PLf-O3X2-mxDn1VpyU2q3fuI6YYeIWp5rR))

Esse treinamento é MUITO completo e tem toda informação que você vai querer saber, então vamos combinar de ir lá no canal dele e deixar a Inscrição e deixar o like nos videos?

## Conclusão

Coloquei aqui tudo que eu precisei pra conseguir trabalhar com docker nos últimos meses, do desenvolvimento do 0 até o deploy do serviço em produção. Essa é uma ferramenta muito poderosa e que tem muitos detalhes que eu não cobri aqui, por isso reforço que de uma moral para os materiais da LINUXtips.

Se você não sabia nada sobre docker e **não conseguiu** usar depois desse guia, me procura no twitter pra gente tentar deixar esse post mais completo.

E caso esse post tenha te ajudado de alguma forma, compartilha com alguém que você acha que também pode aproveitar.

Me diz o que você achou desse tipo de post, e se você gostaria que isso virasse um vídeo no Youtube! Independente do resultado, o post ~~da semana que vem~~ dessa semana será sobre Terraform. Me acompanha no twitter pra saber mais!

Espero que isso tenha sido útil para alguém, até a próxima!
