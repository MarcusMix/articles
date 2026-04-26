# Tutorial: Transforme seu Celular em um Servidor NAS Caseiro
#### Dificuldade: Fácil

Transformar um celular velho, antigo ou sem uso em um servidor é uma forma de reutilizar o aparelho, evitando o descarte eletrônico e dando uma segunda vida ao equipamento.

Vamos montar um **servidor NAS** (Network Attached Storage), que é um servidor projetado para armazenar e fornecer acesso a arquivos pela rede local. Com ele, você consegue guardar e acessar seus arquivos de qualquer dispositivo conectado ao mesmo Wi-Fi, direto pelo navegador.

Então bora começar esse servidorzinho, que ele é muito top!

---

## 📺 Vídeo

Prefere assistir ao invés de ler? Tem um vídeo completo cobrindo esse tutorial:

[![Thumbnail do vídeo - Servidor NAS no Celular](https://img.youtube.com/vi/jMXeb5JSE1s/maxresdefault.jpg)](https://www.youtube.com/watch?v=jMXeb5JSE1s)

---

## O que você vai precisar

- Um celular Android (qualquer versão razoavelmente recente)
- Acesso à internet para baixar os apps e pacotes
- Rede Wi-Fi local (todos os dispositivos devem estar na mesma rede para acessar o servidor)

---

## 1) Baixar a loja F-Droid

O F-Droid é uma loja gratuita com vários apps open-source. A maior vantagem é poder se desvincular da Google Play Store e baixar apps diretamente, sem precisar de login ou qualquer tipo de rastreamento.

**Link do F-Droid:** https://f-droid.org/

Após entrar no site, baixe e instale o `.apk` do F-Droid normalmente. O Android pode pedir permissão para instalar apps de fontes desconhecidas, só liberar.

---

## 2) Baixar o Termux

Após instalar o F-Droid, vamos instalar o **Termux**. Ele é um emulador de terminal Linux que roda diretamente no Android, sem precisar de root. É através dele que vamos instalar e gerenciar todos os serviços do nosso servidor.

> **Atenção:** Baixe o Termux pelo F-Droid, **não pela Google Play Store**. A versão da Play Store está desatualizada e pode causar problemas.

Pode aparecer um aviso do **Google Play Protect** tentando bloquear o download. Clique em *Mais detalhes* e instale mesmo assim, é seguro.

---

## 2.1) Atualizar o Termux

Após instalar e abrir o Termux pela primeira vez, a primeira coisa a fazer é atualizar o sistema de pacotes:

```bash
pkg update && pkg upgrade -y
```

Isso garante que você está com as versões mais recentes de tudo. Pode demorar um pouco dependendo da conexão.

---

## 3) Liberar acesso ao armazenamento

Para que o Termux consiga acessar os arquivos do celular, precisamos dar permissão de armazenamento:

```bash
termux-setup-storage
```

O Android vai exibir um pop-up pedindo permissão. Clique em **Permitir**. Sem isso, o File Browser não consegue enxergar seus arquivos.

---

## 4) Instalar o OpenSSH

Agora vamos instalar o **OpenSSH**, que nos permite acessar o celular de outros dispositivos via terminal (SSH). Isso é útil para gerenciar o servidor sem precisar pegar o celular fisicamente.

```bash
pkg install openssh -y
```

### 4.1) Iniciar o SSH

Para iniciar o servidor SSH:

```bash
sshd
```

### 4.2) Deixar o SSH iniciar automaticamente

Para que o SSH inicie sempre que o celular ligar, adicione ao `.bashrc`:

```bash
echo "sshd" >> ~/.bashrc
```

---

## 5) Instalar o File Browser

O **File Browser** vai ser a nossa interface NAS. Ele é leve, open-source e tem uma interface web simples e funcional que permite:

- Enviar (upload) arquivos do computador ou celular para o servidor
- Baixar arquivos do servidor
- Ler arquivos diretamente no navegador (PDF, imagens, vídeos, docs)
- Criar pastas e organizar tudo

Para instalar, execute:

```bash
curl -fsSL https://raw.githubusercontent.com/filebrowser/get/master/get.sh | bash
```

Esse comando baixa e instala o File Browser direto do repositório oficial.

---

## 6) Iniciar o File Browser

Para iniciar o File Browser:

```bash
filebrowser -a 0.0.0.0 -p 8080 -r ~/storage/shared
```

O que cada parte significa:
- `-a 0.0.0.0` — aceita conexões de qualquer dispositivo na rede
- `-p 8080` — define a porta de acesso (você pode trocar se quiser)
- `-r ~/storage/shared` — define a pasta raiz que o File Browser vai exibir (toda a memória interna do celular)

---

## 6.1) Impedir que o Termux "adormeça"

Por padrão, o Android pode matar processos em segundo plano para economizar bateria. Para evitar que o File Browser seja encerrado, rode:

```bash
termux-wake-lock
```

Esse comando faz três coisas importantes:
1. Impede que o Android mate o processo do File Browser
2. Mantém a conexão Wi-Fi ativa mesmo com a tela desligada
3. Garante respostas rápidas para quem acessa o servidor

---

## 7) Deixar o File Browser iniciar automaticamente

Da forma que configuramos no passo 6, o servidor para quando o celular reinicia. Para torná-lo totalmente automático, vamos usar o **Termux:Boot**.

### 7.1) Instalar o Termux:Boot

Instale o **Termux:Boot** pelo F-Droid. Após instalar, **abra o app pelo menos uma vez** — isso é necessário para que o sistema registre o serviço de boot.

### 7.2) Criar a pasta de boot

```bash
mkdir -p ~/.termux/boot
```

### 7.3) Criar o script de inicialização

```bash
vim ~/.termux/boot/start-services.sh
```

Dentro do vim, pressione `a` para entrar no modo de edição e escreva:

```bash
#!/data/data/com.termux/files/usr/bin/sh
termux-wake-lock
sshd
filebrowser -a 0.0.0.0 -p 8080 -r ~/storage/shared > /dev/null 2>&1 &
```

Para salvar e sair do vim: pressione `Esc`, depois digite `:wq` e pressione Enter.

### 7.4) Tornar o script executável

```bash
chmod +x ~/.termux/boot/start-services.sh
```

Pronto! A partir de agora, toda vez que o celular ligar, o SSH e o File Browser vão iniciar automaticamente.

---

## 8) Trocar a senha padrão do File Browser

O File Browser vem com o usuário `admin` e uma senha padrão. **Troque imediatamente** por uma senha sua:

```bash
filebrowser users update admin --password SUA_SENHA_AQUI
```

Substitua `SUA_SENHA_AQUI` pela senha que quiser usar.

---

## 9) Acessar o Servidor NAS

Com o File Browser rodando, precisamos descobrir o IP do celular na rede:

```bash
ifconfig
```

Procure a seção `wlan0` no resultado. O IP vai aparecer ao lado de `inet`, algo como `192.168.1.100`.

Com o IP em mãos, acesse pelo navegador de qualquer dispositivo na mesma rede:

```
http://192.168.1.100:8080
```

Faça login com `admin` e a senha que você definiu no passo 8.

> **Dica:** Cada dispositivo na rede pode ter um IP diferente. Se o IP do seu celular mudar, repita o `ifconfig` para descobrir o novo.

---

## Passo extra: Monitorar CPU e RAM via SSH

Para acompanhar o desempenho do servidor pelo terminal (via SSH de outro dispositivo), instale o `htop`:

```bash
pkg install htop -y
```

E execute com:

```bash
htop
```

---

## Considerações finais

Nosso Servidor NAS caseiro está pronto! Algumas observações importantes:

- **Performance de upload:** Para velocidades melhores, um **adaptador de rede USB** (USB-C para Ethernet) faz diferença considerável em relação ao Wi-Fi.
- **Velocidade da rede:** Se o celular estiver longe do roteador, o upload pode ficar lento. Tente manter o servidor com boa cobertura de sinal.
- **Limitações do hardware:** A performance varia de celular para celular. Celulares mais antigos ou com pouca RAM podem ficar lentos com muitos acessos simultâneos.
- **Expandindo o servidor:** Como cada serviço roda em uma porta diferente, é possível hospedar outros serviços no mesmo celular (servidor de música, Minecraft, etc.) sem conflito.

---

É isso rapaziada, curtiu?
Apoia o canal se esse tutorial foi útil, considera virar membro.

Caso algo tenha ficado confuso ou você tenha alguma melhoria, me avisa!

Tamo junto ✌️
