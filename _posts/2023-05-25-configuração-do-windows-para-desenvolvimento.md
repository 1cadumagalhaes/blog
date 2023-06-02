---
layout: post
title:  "Configuração do Windows para desenvolvimento"
date:   2023-05-26 15:40:00 -0300
permalink: /:categories/:year/:title
categories: tutorial
tags: linux windows wsl
customcss: sim
css: blog
---

## Atualizando um post de alguns anos atrás

No ano passado eu convenci as pessoas do trabalho de que o WSL deveria ser padrão para os Engenheiros de Dados da empresam, para conseguir agilizar o desenvolvimento de soluções (muito porque os computadores vem com windows e da trabalho fazer o setup de linux), mas também porque acredito que é a melhor forma de desenvolver ~~para quem não quer usar linux~~.

Então resolvi atualizar esse post para servir de referencia para os meus colegas, mas também pra qualquer pessoa que queira fazer esse setup.

## O que é WSL e por que eu deveria usar isso?

Se você pesquisar essa sigla no google, vai descobrir que é assim que chamam a Liga Mundial de Surf (*World Surf League*, em inglês).

![world_surf_league](/assets/world_surf_league.png)

Mas, no nosso contexto, WSL significa *Windows Subsystem for Linux* , e foi a forma que a Microsoft encontrou de permitir que os usuários - principalmente desenvolvedores - pudessem executar um ambiente Linux (incluindo ferramentas de linha de comando, principalmente) diretamente no windows, sem modificar o sistema e sem a necessidade de criar uma máquina virtual.

O WSL foi lançado oficialmente em 2016, mas era apenas uma ~~gambiarra~~ camada de compatibilidade. Em 2019, a Microsoft anunciou o **WSL 2**, introduzindo algumas mudanças importantes como a utilização de um kernel de Linux, o que faz com que ele seja compatível com mais programas de linux.

Curiosamente, comecei a usar Linux em 2016, e testo o WSL desde o lançamento dentro do Windows Insider.

Existem alguns motivos pelos quais você deveria dar uma chance ao Linux para desenvolver, mas eu vou destacar somente alguns:

1. Disponibilidade de ferramentas: a maioria das bibliotecas de desenvolvimento são construídas para rodar em GNU. Um bom exemplo é o Docker, que para funcionar em windows usa o WSL como backend
2. Facilidade de instalação/manutenção de ferramentas

E considerando apenas o WSL, podemos acrescentar

1. A instalação e execução das coisas fica separada do seu sistema
2. Você não precisa de permissão de administrador para instalar ferramentas de desenvolvimento ou configurar variáveis de ambiente

## Requisitos

Estou utilizando:

- Windows 11 Pro versão 22H2 (necessário Windows 10 versão 2004 ou maior)
- [VS Code](https://code.visualstudio.com/download) como editor
- [Extensão do WSL](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-wsl) para o VS Code
- [Windows Terminal](https://apps.microsoft.com/store/detail/windows-terminal/9N0DX20HK701)

## Configuração do WSL 2

A partir daqui, estarei seguindo o [tutorial da microsoft](https://learn.microsoft.com/en-us/windows/wsl/install), e anotar como resolvo qualquer problema que encontrar.

A última vez que escrevi esse tutorial, eram necessários uns 3 ou 4 comandos. Para as versões mais recentes do Windows, isso é feito com um comando.

### 1. Abrir o PowerShell ou o Prompt de Comando como **administrador.**

**Dica**: você pode apertar Win + X para abrir o menu de contexto do Iniciar, e nele deve ter uma opção de executar o PowerShell como administrador.

### 2. Executar o comando

```powershell
wsl --install
```

### 3. Reinicie o computador

### 4. Escolher uma distro Linux (opcional)

Por padrão, ele vai instalar o Ubuntu, que vem na versão 22.04 LTS. Quando iniciar o computador, ele vai finalizar a instalação, e vai te pedir algumas configurações.

Para escolher outra distro, você pode ver as disponíveis com o comando

```powershell
wsl -l -o
```

Para instalar o Ubuntu 22.04, por exemplo:

```powershell
wsl --install -d Ubuntu-22.04
```

### 5. Configurar seu usuário e senha

Quando finalizar a instalação, ele vai pedir que você configure o seu UNIX username e a sua senha. Esse usuário será o padrão da instalação, e a senha será a que você precisa utilizar quando rodar comandos com `sudo`

![wsl_install](/assets/wsl_install.png)

### 6. Atualização de programas

Como estamos instalando uma versão com pouco mais de 1 ano de lançamento, é importante atualizar todos os pacotes. Dentre as atualizações, estão algumas atualizações de segurança do kernel Linux. 

```bash
sudo apt update && sudo apt upgrade -y
```

Além disso, uma vez por dia o WSL mostra a seguinte mensagem:

![wsl_login](/assets/wsl_login.png)

Para desabilitar, basta criar o arquivo .hushlogin na home do usuário:

```bash
touch ~/.hushlogin
```

## Configuração do Windows Terminal

O Windows Terminal é um aplicativo de terminal bem satisfatório. Ele permite abrir várias abas, mesmo de tipos diferentes (como cmd, powershell, e qualquer distro de wsl), além de ser bem rápido e customizável. 

Vamos começar abrindo as configurações (`Ctrl + ,` ).

### 1. Configurar perfil padrão

O primeiro passo é configurar o terminal para abrir automaticamente no WSL. Basta selecionar a sua distro no menu da primeira opção. No meu caso, Ubuntu 22.04 LTS

![wt_default_profile](/assets/wt_default_profile.png)

Pressione o botão de Salvar

### 2. Habilitar cópia de texto ao selecionar

No menu **Interaction,** basta marcar a primeira opção.

![wt_interaction](/assets/wt_interaction.png)

Pressione o botão de Salvar

### 3. Personalização de cores

No menu **Color schemes** podemos utilizar um esquema de cores já existente, ou criar um novo. No meu caso, vou configurar o [Dracula Theme](https://draculatheme.com/windows-terminal). O jeito mais fácil de instalar o Dracula é usar o botão **Open JSON file** no canto inferior direito, e inserir o JSON do site na lista **schemes**

### 4. Personalização de fontes

Mais abaixo vou mostrar como faço a configuração do [powerlevel10k](https://github.com/romkatv/powerlevel10k). Para utilizar ele (e qualquer tema mais complexo de terminal), você vai precisar instalar uma [Nerd Font](https://github.com/ryanoasis/nerd-fonts), pois elas vem com vários ícones. No caso do powerlevel10k, eles recomendam a fonte MesloLGS. Os links para download podem ser encontrados no [github](https://github.com/romkatv/powerlevel10k-media/raw/master/MesloLGS NF Regular.ttf).

Para configurar a fonte, você pode ir na parte de **Profiles**, selecionar o **Defaults** e ir no menu **Appearence**, nas configurações adicionais.

![wt_appearence](/assets/wt_appearence.png)

Nesse mesmo menu você encontra opções gerais de aparência, como habilitar a transparência da janela, ou aplicar alguns efeitos. Eu uso a opacidade em 99%, e não uso nenhum dos efeitos.

## Configuração do ZSH

Não vou entrar em detalhes, mas o ZSH (Z shell) é uma versão estendida do shell padrão de Linux, que traz uma boa performance, boas features de autocomplete e correção, além do suporte a plugins e temas, que é o principal foco aqui.

### 1. Instalar o zsh

```bash
echo "Installing ZSH"
sudo apt install zsh -y
```

### 2. Instalar o ohmyzsh

```bash
echo "Installing ohmyzsh"
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

Após executar o comando, ele vai pedir sua confirmação para trocar o shell padrão para zsh

![ohmyzsh_default](/assets/ohmyzsh_default.png)

Após a confirmação, o seu terminal estará assim:

![zsh_terminal](/assets/zsh_terminal.png)

### 3. Instalação de plugins

O oh myzsh tem uma [lista enorme de plugins](https://github.com/ohmyzsh/ohmyzsh/wiki/Plugins). No dia a dia os que mais me impactam são o zsh-autosuggestions, que usa o seu histórico para recomendar comandos inteiros; e o zsh-syntax-highlighting. Hoje também vou aproveitar para instalar o [asdf](https://github.com/asdf-vm/asdf), que é um gerenciador de versões de várias linguagens; o [dotenv](https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins/dotenv), que permite a exportação de variáveis de ambiente de um arquivo `.env` automaticamente; e os plugins de autocomplete de algumas ferramentas como node, gcloud, docker, docker-compose.

```bash
echo "Installing zsh-autosuggestions"
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
echo "Installing zsh-syntax-highlighting"
git clone https://github.com/zsh-users/zsh-syntax-highlighting ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
echo "Installing asdf"
git clone https://github.com/asdf-vm/asdf.git ~/.asdf
```

O jeito mais fácil de habilitar os plugins é abrir o arquivo de configuração do terminal no VS Code, e inserir os valores manualmente

```bash
code ~/.zshrc
```

Basta procurar a variável **plugins** (no meu está na linha 73) e inserir todos os plugins que quer ativar. No meu caso, ficou assim:

```bash
plugins=(
  git 
  zsh-autosuggestions 
  zsh-syntax-highlighting 
  asdf 
  dotenv sudo
  gcloud 
  node npm 
  docker docker-compose 
  )
```

Você também pode fazer essa substituição por linha de comando:

```bash
file=".zshrc"
old_line="plugins=(git)"
new_line="plugins=(git zsh-autosuggestions zsh-syntax-highlighting asdf dotenv sudo gcloud node npm docker docker-compose)"

echo "Setting plugins on .zshrc"
sed -i "s|$old_line|$new_line|" "$file"
```

## Configuração do powerlevel10k

O powerlevel10k é um tema de zsh que foca na flexibilidade do ambiente, mas com uma configuração bem simples.

![powerlevel10kdemo](/assets/powerlevel10kdemo.png)

Para instalar, basta fazer o download

```bash
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
```

E colocar o `ZSH_THEME="powerlevel10k/powerlevel10k"` no `.zshrc`

```bash
old_line='ZSH_THEME="[^"]*"'
new_theme='ZSH_THEME="powerlevel10k/powerlevel10k"'

echo "Setting the zsh theme to powerlevel10k"
sed -E -i "s|$old_line|$new_theme|" ".zshrc"
```

Agora basta reiniciar o terminal, e seguir a configuração interativa.

No final o meu terminal ficou assim:

![p10kcomplete](/assets/p10kcomplete.png)

Eu também faço algumas personalizações no `.p10k.zsh`, como para mostrar a configuração do gcloud o tempo todo (alterar nas linhas 1514 e 1515 para customizar o formato, e comentar as linha 1486 e 1529 para mostrar sempre o projeto)

![p10kcustom](/assets/p10kcustom.png)

Você pode navegar por esse arquivo e ler algumas das configurações, é possível personalizar completamente o comportamento do tema.

## Instalação de ferramentas

Agora que o terminal está pronto, vamos instalar as ferramentas necessárias.

Vou usar:

- Terraform
- gcloud cli
- gh cli
- python, pip e venv
- node
- docker (ou podman)

Também vou criar um arquivo para colocar alias e funções customizadas.

### 1. Terraform

```bash
echo "Installing terraform"
sudo apt-get update && sudo apt-get install -y gnupg software-properties-common -y
wget -O- https://apt.releases.hashicorp.com/gpg | \
    gpg --dearmor | \
    sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg
gpg --no-default-keyring \
    --keyring /usr/share/keyrings/hashicorp-archive-keyring.gpg \
    --fingerprint
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] \
    https://apt.releases.hashicorp.com $(lsb_release -cs) main" | \
    sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update
sudo apt-get install terraform -y
terraform -install-autocomplete
```

### 2. Google Cloud CLI

Tive alguns problemas para instalar o gcloud, mas esses são os comandos que uso no Linux

```bash
echo "Installing gcloud cli"
sudo apt-get install apt-transport-https ca-certificates gnupg -y
echo "deb [signed-by=/usr/share/keyrings/cloud.google.gpg] https://packages.cloud.google.com/apt cloud-sdk main" | sudo tee -a /etc/apt/sources.list.d/google-cloud-sdk.list
curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo tee /usr/share/keyrings/cloud.google.gpg
sudo apt-get update && sudo apt-get install google-cloud-cli -y
```

Para conseguir baixar, executei o seguinte:

```bash
echo "Removing duplicates"
sudo rm -rf /etc/apt/sources.list.d/google-cloud-sdk.list
echo "deb [signed-by=/usr/share/keyrings/cloud.google.gpg] https://packages.cloud.google.com/apt cloud-sdk main" \
| sudo tee -a /etc/apt/sources.list.d/google-cloud-sdk.list
curl https://packages.cloud.google.com/apt/doc/apt-key.gpg \
| sudo gpg --dearmor -o /usr/share/keyrings/cloud.google.gpg

#Esse comando ficou parado esperando que eu digitasse o nome do arquivo. Eu copiei exatamente o que já tem nele

sudo apt update && sudo apt install google-cloud-sdk
```

Configurando o gcloud:

Basta executar o comando:

```bash
gcloud init
```

Ele vai gerar uma URL, que você pode clicar para ir para o navegador, onde vai precisar liberar permissão para o SDK

![gcloud_allow](/assets/gcloud_allow.png)

Ao final, basta copiar o código e colocar no seu terminal

![gcloud_sign](/assets/gcloud_sign.png)

### 3. Github CLI

Basta executar os comandos

```bash
echo "Installing gh cli"
type -p curl >/dev/null || (sudo apt update && sudo apt install curl -y)
curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | sudo dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg \
&& sudo chmod go+r /usr/share/keyrings/githubcli-archive-keyring.gpg \
&& echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null \
&& sudo apt update \
&& sudo apt install gh -y
```

Em seguida, para configurar basta executar o comando e seguir a configuração interativa:

```bash
gh auth login
```

### 4. Python, pip e venv

```bash
echo "Installing python, pip and venv"
sudo apt install  python3-venv python3-pip -y
```

### 5. Node

```bash
echo "Installing nodejs with asdf"
asdf plugin add nodejs https://github.com/asdf-vm/asdf-nodejs.git
asdf install nodejs latest
asdf global nodejs latest
```

### 6.a Docker

```bash
echo "Installing docker"
sudo apt remove docker-desktop
rm -r $HOME/.docker/desktop
sudo rm /usr/local/bin/com.docker.cli
sudo apt purge docker-desktop
sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release -y
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin -y
```

Configurar para conseguir usar sem `sudo`

```bash
sudo groupadd docker
sudo usermod -aG docker $USER
```

Configurar para que o serviço inicie sempre que abrir o terminal. Para isso colocamos o comando no arquivo `/etc/wsl.conf`. Para aplicar a alteração, é necessário reiniciar o wsl

```bash
echo "Setting docker service to start on boot"
echo -e '[boot]\ncommand="service docker start"' | sudo tee /etc/wsl.conf
wsl.exe --shutdown
```

### 6.b Podman

Podman é um outro gerenciador de contêineres, parecido com o docker. Sua instalação é mais simples, basta executar:

```bash
sudo apt install podman -y
pip3 install podman-compose
```

O comando `podman` pode ser usado em quase todos os casos em que o comando `docker` funciona. A exceção que encontrei é que ele ainda não tem o `compose`, como o docker. Para isso, basta instalar o podman-compose e executar `podman-compose [comando]`. É comum alguns tutoriais sugerirem que você crie um `alias docker=podman` .

O motivo para usar o Podman ao invés do Docker é que a instalação do docker sugerida para windows requer o Docker Desktop, que vai usar o WSL como backend (o que fizemos aqui é uma gambiarra, cortesia que ganhamos ao usar Linux). O problema é que a licença do Docker Desktop foi atualizada esse ano, definindo que a utilização gratuita não pode ser feita por empresas que tenham mais de 250 funcionários ou mais de 10 milhões de dólares de receita anual (fonte: [Who’s required to pay for Docker Desktop?](https://www.notion.so/Who-Will-Teach-Silicon-Valley-to-Be-Ethical-3b3294ef186846b2afff8df30c872222)). Claro que isso não é um problema caso seu uso seja para estudos, uso pessoal ou para projetos open source não comerciais. Além disso, usuários de Windows estão acostumados a ter ferramentas de interface gráfica, que é o maior apelo do Docker Desktop. O [Podman Desktop](https://podman-desktop.io/) continua sendo 100% gratuito, mas sua instalação em Windows requer alguns ajustes que não vou comentar hoje.

### 7. Configurações de alias e ambiente em geral

Geralmente eu crio um arquivo `~/.profile` , que deixo salvo como backup no meu github.

Basta criar o arquivo com suas funções, variáveis e apelidos. E depois acrescentar o comando `source ~/.profile` no fim do seu `.zshrc` 

Eu costumo criar pastas para centralizar minhas service accounts e meus virtual environments de python, por isso tenho alguns comandos que fiz para facilitar

```bash
function set_google_credentials() {
  export GOOGLE_APPLICATION_CREDENTIALS="/home/$USER/.service_accounts/$1"
}

alias gconfig="gcloud config configurations"
```

Para gerenciar virtual environments, criei o pacote `pyenvs`, que está disponível no [gist do github](https://gist.github.com/1cadumagalhaes/beface90e882be4dc140d169228346db).

### 8. Gerenciamento de pastas e ambientes

Para usar o WSL, é recomendado que todos os arquivos que você vai utilizar estejam dentro do ambiente virtualizado. Ou seja, sempre que for criar um projeto novo (ou clonar um existente), abra o terminal e faça toda a execução dentro da home do seu usuário. Geralmente eu crio uma pasta `Projects` onde centralizo tudo.

```bash
mkdir Projects && cd Projects
mkdir exemplo && cd exemplo
npm init -y && echo "console.log('código de exemplo')">>index.js
```

Caso queira encontrar a pasta dos seus projetos pelo explorador de arquivos do windows, basta usar o comando abaixo dentro da pasta

```bash
explorer.exe .
```

**Dica**: no windows explorer, você pode fixar a pasta do seu wsl no menu lateral.

E por fim, sempre que vou abrir o VS Code eu abro pelo terminal, executando

```bash
code [caminho da pasta]
```

## Finalizando

Talvez eu tenha esquecido de deixar alguma configuração, por isso fiquem a vontade para comentar aqui ou me marcar no twitter que eu trago uma atualização.

Espero que isso tenha sido útil para alguém, até a próxima!
