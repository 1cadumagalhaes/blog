---
layout: post
title:  "Como configurar o windows para desenvolvimento com WSL 2"
date:   2020-06-09 19:00:00 -0300
permalink: /:categories/:year/:title
categories: tutorial windows
---

No final de Maio foi lançada a versão 2004 do Windows 10, e uma das novidades mais esperadas - para os desenvolvedores, pelo menos - é o lançamento da versão 2 do Windows Subsystem for Linux (WSL). O WSL 2 traz melhorias de performance e uma melhor integração com o sistema, além de funcionar de forma mais próxima do linux real, por possuir um kernel de verdade.

O WSL 2 foi anunciado em Maio de 2019, introduzindo mudanças importantes, como a utilização de um kernel de Linux real. Dentre as promessas, estavam uma velocidade consideravelmente maior na execução de comandos, além de melhorar a instalação de pacotes, compilação de projetos, visualização de arquivos e várias outras coisas. Tudo isso em comparação com a primeira versão do WSL, que foi lançada em 2016.

>Operações como **git clone, npm install, apt update, apt upgrade**, e mais tudo será visivelmente mais rápido. O aumento real da velocidade dependerá do aplicativo em execução e de como ele está interagindo com o sistema de arquivos. Os testes iniciais que executamos têm o WSL 2 executando até 20x mais rápido em comparação com o WSL 1 ao descompactar um tarball compactado e cerca de 2-5x mais rápido ao usar o git clone, npm install e cmake em vários projetos - Craig Loewen, em [comunicado oficial no blog](https://devblogs.microsoft.com/commandline/announcing-wsl-2/) 

Como usuário de Linux, fiquei curioso com esses anúncios e passei a testar essa nova versão, e ver se me atende de forma equivalente ao linux.

![Visual studio code rodando do wsl com terminal](/blog/assets/wsl_code.png)

As principais ferramentas utilizadas no meu dia a dia são: git, node, gcloud, e às vezes python. Algumas outras coisas (flutter, principalmente adb) ainda têm certas limitações. 

Se quiser ter um terminal igual a esse meu, basta seguir o tutorial até o final.

## Passos

1. Configuração do WSL 2
2. Instalando uma distro linux
3. Configurando o ambiente linux e instalando primeiros pacotes
4. (Opcional) Configuração do zsh
5. (Opcional) Instalação e configuração do windows terminal
   
### Requisitos
- [Windows 10 versão 2004](https://docs.microsoft.com/pt-br/windows/whats-new/whats-new-windows-10-version-2004)
- [Visual Studio Code para Windows](https://code.visualstudio.com/download)
- [VS Code Remote - WSL](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-wsl)

## 1. Configuração do WSL 2


Para habilitar o WSL 2, basta executar o powershell como administrador (você pode pesquisar powershell no menu, e selecionar "Executar como Administrador) e inserir os seguintes comandos:

Habilitar o recurso do WSL, caso ainda não tenha

```powershell
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
```
{: #code-example-1}

Habilitar a Plataforma de máquina virtual
```powershell
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```
{: #code-example-2}

**Reinicie o computador** para concluir a instalação.

Definir o WSL 2 como a versão padrão
```powershell
wsl --set-default-version 2
```
{: #code-example-3}

Para solução de problemas, consultar a documentação: 
- [Como solucionar problemas de instalação](https://docs.microsoft.com/pt-br/windows/wsl/install-win10#troubleshooting-installation)
  
No meu caso, precisei instalar o kernel do WSL2 manualmente. Segue o link:
- [WSL2 Kernel](https://docs.microsoft.com/pt-br/windows/wsl/wsl2-kernel)

## 2. Instalando uma distro linux
Basta abrir a Microsoft Store e escolher a distribuição Linux que quiser:
- [Microsoft Store](https://www.microsoft.com/pt-br/search?q=linux)

A versão que estou utilizando é o [Ubuntu 20.04 LTS](https://www.microsoft.com/store/apps/9n6svws3rx71). O Ubuntu já vem com git, apt, dpkg e python por padrão.

Após baixar e instalar, é preciso executar a distribuição. Na primeira vez que iniciar, um console será aberto e a instalação vai ser finalizada. Quando acabar, você vai [criar uma conta de usuário e uma senha para sua distro linux](https://docs.microsoft.com/pt-br/windows/wsl/user-support).

![criando conta de usuario no ws](/blog/assets/ubuntuinstall.png)

1. Basta digitar o nome de usuário, que vai ser utilizado para criar a pasta de home do sistema, e pressionar enter.
2. Em seguida, digite a senha e pressione enter (ao digitar a senha, não vai aparecer nenhum cursor). 
3. Confirme a senha e pressionar enter novamente.


Essa conta vai ser o administrador do Linux, de onde você vai poder executar os comandos de nível administrativo usando `sudo`.

Ao terminar, o bash será iniciado já dentro da sua distribuição.


## 3. Configurando o ambiente linux e instalando primeiros pacotes

Dentro do terminal da sua distribuição, execute os seguintes comandos:

```shell
cd ~
touch $HOME/.hushlogin
sudo apt update && sudo apt upgrade
sudo apt install git python3-pip -y && echo "alias python=python3" >> .bashrc && source .bashrc
wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.3/install.sh | bash
echo 'export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s "${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # This loads nvm' >> .bashrc && source .bashrc
nvm install --lts
```
{: #code-example-4}
Passo a passo:

1. Desabilitar a mensagem de "Bem vindo" da distro. 
   ```shell 
   touch $HOME/.hushlogin
   ```
    {: #code-example-5}

2. Atualizar os pacotes instalados.
    ```shell
    sudo apt update && sudo apt upgrade
    ```
    {: #code-example-6}

3. Garantir a instalação do git, instalar o pip para python 3 e acrescentar o alias `python` para executar.
    ```shell
    sudo apt install git python3-pip -y && echo "alias python=python3" >> .bashrc && source .bashrc
    ```
    {: #code-example-7}

4. Baixar o [Node Version Manager (nvm)](https://github.com/nvm-sh/nvm). NVM é um gerenciador de versões de node, que facilita a instalação e manutenção das versões do node e do npm.

   ```shell
   wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.3/install.sh | bash
   ```
   {: #code-example-8}


5. Adicionar as dependências do nvm no arquivo de perfil do terminal, para poder executar os comandos.

    ```shell
    echo 'export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s "${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")"
    [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # This loads nvm' >> .bashrc` && source .bashrc
    ```
    {: #code-example-9}
     

6. Usar o nvm para instalar a última versão estável do node.
   ```shell 
   nvm install --lts
   ```
   {: #code-example-10}
   Para mais detalhes sobre como instalar outras versões, basta consultar a [página oficial no github]((https://github.com/nvm-sh/nvm)).



## 4. (Opcional) Configuração do zsh
Zsh (ou Z Shell) é uma versão estendida do bash, com diversas novas funções, além de suportar plugins e temas. Como é baseado no bash, ele tem todas as mesmas funções, e para trocar entre eles basta uma linha de comando.

Para instalar no ubuntu, basta executar:
```shell
sudo apt-get install zsh
```
{: #code-example-11}

Após instalar, deve aparecer uma confirmação se você deseja trocar o padrão do seu sistema para o zsh. Caso não apareça, basta executar `chsh -s /usr/bin/zsh`. 

Para voltar ao bash, é só executar: `chsh -s /usr/bin/bash`.

Para saber como instalar em outras distros, consulte esse link: [Installing zsh](https://github.com/ohmyzsh/ohmyzsh/wiki/Installing-ZSH)

Também pode executar os seguintses comandos:

```shell
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
echo "alias python=python3" >> .zshrc
echo 'export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s "${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # This loads nvm' >> .zshrc

```
{: #code-example-12}

Isso serve para instalar o [ohmyzsh](https://github.com/ohmyzsh/ohmyzsh) - um framework gerenciador para as configurações do zsh - e replicar as configurações do python e nvm para o arquivo fonte do zsh (.zshrc). 

A maioria dos temas para o zsh precisa de alguma fonte powerline, como a [Fira Code](https://github.com/tonsky/FiraCode/releases/), para dar suporte aos ícones. Para instalar, basta baixar a última versão da fonte, extrair para algum lugar e executar o arquivo FiraCode-Medium.ttf dentro da pasta ttf. 

O tema que eu uso é o [spaceship-prompt](https://github.com/denysdovhan/spaceship-prompt). Para instalar, é só usar um `npm install -g spaceship-prompt`.

Para mais temas, você pode ir na página oficial do ohmyzsh: [Temas](https://github.com/ohmyzsh/ohmyzsh/wiki/Themes).

O ohmyzsh também possui vários plugins. Não configurei nenhum no windows, mas se quiser saber mais sobre:
- [ZInit](https://github.com/zdharma/zinit): gerenciador de plugins
- Um que uso no linux: [zsh-syntax-highlighting](https://github.com/zsh-users/zsh-syntax-highlighting)



## 5. (Opcional) Instalação e configuração do windows terminal

![windows terminal personalizado](/blog/assets/wsl_1.png)

O Windows Terminal foi anunciado no build 2019, e agradou muito a comunidade, pois ele centraliza todos os shell instalados (cmd, powershell, bash) em um só lugar, de forma muito personalizável e fácil de configurar, além de usar GPU pra renderizar. Depois de 1 ano em versão preview, foi [lançada a versão 1.0 no build 2020](https://devblogs.microsoft.com/commandline/windows-terminal-1-0/), 

Para instalar o windows terminal, basta ir até a loja da microsoft.
- [Windows Terminal](https://www.microsoft.com/pt-br/p/windows-terminal/9n0dx20hk701#activetab=pivot:overviewtab)

Depois disso, vamos alterar o terminal principal padrão (para abrir o linux automaticamente) e a fonte do terminal, para dar suporte aos ícones do tema do zsh.

Para isso:
1. Abra o windows terminal
2. Abra as configurações (Ctrl+,). Isso vai abrir um arquivo json
3. Na lista "list", dentro de "profiles", encontre a chave que contém a distro linux que você instalou. No meu caso:
   ```json
    {
        "guid": {código},
        "hidden": false,
        "name": "Ubuntu-20.04",
        "source": "Windows.Terminal.Wsl"
    }
   ```
   {: #code-example-13}
4. Altere esse objeto para acrescentar a fonte Fira Code:
    ```json
    {
        "guid": {código},
        "hidden": false,
        "name": "Ubuntu-20.04",
        "source": "Windows.Terminal.Wsl",
        "fontFace": "Fira Code"
    }
   ```
   {: #code-example-14}
5. Copie o valor da chave "guid", que é um código alfanumérico entre chaves.
6. Vá ao início do arquivo, na chave "defaultProfile". Cole o valor da chave guid do linux no lugar do código que já está ali. Por exemplo:
    ```json
    {
        "defaultProfile": {código}
    }
    ```
    {: #code-example-15}
7. Salve as configurações (Ctrl+s), feche o terminal e abra novamente.

Agora o seu terminal deve estar igual ao meu!


Para configurar a mesma fonte no terminal do vscode:
1. Abra o vscode
2. Vá para as configurações (Ctrl+,)
3. Pesquise "terminal.font"
4. Preencha a caixa de texto com "Fira Code"
![exemplo do vscode](/blog/assets/vscode_terminal.png)
5. Salve as configurações (Ctrl+S)





