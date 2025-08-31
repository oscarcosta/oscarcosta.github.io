+++
author = "Oscar Costa"
title = "Instalando o Raspberry Pi OS de forma headless"
date = "2025-08-30"
description = "Instalando o Raspberry Pi OS de forma headless"
tags = [
    "blog", "raspberry pi"
]
+++

Recentemente precisei configurar um Raspberry Pi sem monitor ou teclado (headless) e não lembrava exatamente como fazê-lo. Após algumas buscas rápidas na internet, com o auxílio da inteligência artificial (IA), consegui resolver o problema. Porém, o processo acabou levando mais tempo do que o esperado, já que existem várias formas de realizar esse tipo de instalação e a IA costuma indicar apenas os métodos mais comuns. Para não esquecer, resolvi documentar aqui os passos que segui.

Equipamentos utilizados:

- Raspberry Pi 4 (4GB)
- Carregador compatível
- Cartão microSD de 32GB
- Cabo de rede Cat6

## 1. Baixar e gravar o Raspberry Pi OS

Como não queria instalar o **Raspberry Pi Imager** no meu PC, optei por baixar a imagem do **Raspberry Pi OS Lite (64-bit)** diretamente no site oficial: [Raspberry Pi OS downloads](https://www.raspberrypi.com/software/operating-systems/).

Para gravar a imagem no cartão microSD, utilizei o [balenaEtcher](https://etcher.balena.io/) que já tinha instalado no meu PC.

## 2. Habilitar o acesso remoto via SSH

Por ser uma instalação headless, não teria acesso ao primeiro boot para habilitar o SSH. Para ativá-lo, criei um arquivo vazio chamado `ssh` na partição `bootfs` do cartão microSD:

```bash
touch ssh
```

## 3. Configurar usuário e senha

Pelo mesmo motivo anterior, precisei criar um usuário padrão com senha para acessar o sistema. Para isso, salvei o usuário e o hash da senha em um arquivo na partição `bootfs` do microSD.

O hash foi gerado com o comando:

```bash
openssl passwd -6
```

Depois de digitare confirmar a senha, copiei o hash gerado e salvei no arquivo `userconf.txt` na partição `bootfs`, no formato:

```bash
username:password_hash
``` 

## 4. Iniciar o sistema e acessar via SSH

Inseri o microSD no Raspberry Pi, conectei-o à rede e liguei o dispositivo. A inicialização levou alguns minutos, o que é normal no primeiro boot.

Para acessá-lo, identifiquei o endereço IP do Raspberry Pi no roteador. E com o IP em mãos, conectei-me via SSH:

```bash
ssh username@192.168.1.100
``` 

## 5. Atualizar e configurar o sistema

Assim que consegui acesso, atualizei o sistema com:

```bash
sudo apt update && sudo apt upgrade -y
```

Alguns pacotes podem solicitar confirmação para sobrescrever configurações. Nesse momento, não tem problema aceitar.

Após atualizado, abri o utilitário de configuração do Raspberry Pi:

```bash
sudo raspi-config
``` 

As configurações variam conforme a utilização, mas as opções que geralmente altero são as seguintes:

- *Logging* -- para persistir os logs no disco, facilitando depurar erros -- `1 System Options -> S10 Logging -> 3 Persistent`
- *Fan settings* -- para que a ventoinha só ligue quando necessário -- `4 Performance Options -> P3 Fan`
- *Localisation settings* -- para deixar o sistema no fuso horário correto -- `5 Localisation Options`
- *Expand the filesystem* -- para ter acesso a todo espaço do disco -- `6 Advanced Options -> A1 Expand Filesystem`

Neste ponto, as configurações iniciais estão concluídas. Agora basta reiniciar o Raspberry Pi, e o sistema estará pronto para uso.

``` bash
sudo reboot -n
``` 

;)