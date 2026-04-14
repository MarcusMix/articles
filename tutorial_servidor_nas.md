# Tutorial de como Transformar seu Celular em um Servidor NAS Caseiro 
#### Dificuldade: fácil

Transformar um celular velho, antigo ou sem uso em um servidor, é uma forma de conseguir reutilizar o aparelho, evitando de jogar fora, além de ajudar o meio ambiente evitando o descarte e dando uma segunda vida pro aparelho.

Vamos fazer um servidor NAS, que é projetado para fornecer acesso a dados por meio da nossa rede.
Dessa forma vamos conseguir guardar e acessar arquivos de vários dispositívos simultâneos apenas acessando o endereço do servidor por um navegador.

Então bora começar esse servidorzinho, que ele é muito top!

## 1) Baixar a loja F-Droid

O F-Droid é uma loja com gratuito e com vários apps open-source, a maior vantagem é poder se desvincular da Google Play Store e baixar seus apps diretamente por ela, sem precisar de login ou qualquer coisa de KYC.

link do F-Droid: https://f-droid.org/

## 2) Baixar o Termux (cuidar com o Google Play Protect)

Após a instalação da F-Droid, vamos instalar o Termux, é um simulador de terminal linux dentro do Linux que roda sem root, ideial para projetos simples igual o que estamos fazendo.

Pra mim deu um aviso do Google Play Protect tentando evitar o download, mas foi só eu clicar em *mais detalhes* e instalar mesmo assim.


## 2.1) Atualizar o Termux (pkg update && pkg upgrade -y)

Após instalar o Termux, vamos iniciar ele, e a primeira coisa que faremos, é atualizar o sistema com os comandos 

```bash
pkg update && pkg upgrade -y)
```

Depois disso, com o Termux atualizado, podemos ir para o próximo passo.

## 3) Baixar o openssh 

Agora vamos baixar o openssh, para conseguir acessar o nosso celular (futuro servidor) em outros dispositivos via ssh, para isso rodamos o comando dentro do Termux.

```
pkg install openssh -y
```

O openssh é o carinha que vai fazer com que seja possível nós se conectar com o nosso celular via ssh (ssh é basicamente uma forma segura de se conectar com outro aparelho via terminal) de forma simples, rápida e segura.

## 3.1) Rodar o openssh

Depois que instalou o `openssh`, agora executamos dentro do terminal `sshd`, para poder executa-lo e testar.

## 4) Deixar o openssh executar sempre 

Para fazer o `openssh` executar sempre, mesmo após reiniciar o celular, precisamos rodar o comando abaixo, esse comando fará com que ele seja iniciado sempre que o sistema for ligado.

```bash
echo "sshd" >> ~/.bashrc
```

Depois disso nosso ssh já está configurado, e podemos passar pra etapa seguinte.


## 5) Instalar o File Browser

O File Browser será o nosso software NAS, ele é bem simples, porém para o nosso uso vai ser o suficiente.

Estou usando ele e gostei bastante, a interface é simples, mas funcional, com recursos para "upar" arquivos, fazer download e também é possível ler arquivos (pdf, docs, txt) diretamente com ele, o que já ajuda bastante.

Para baixar ele, vamos executar o comando:
```bash
curl -fsSL https://raw.githubusercontent.com/filebrowser/get/master/get.sh | bash`
```

Esse comando executa um `curl` que baixa do repositório oficial do File Browser.

## 6) Iniciar o File Browser

Para poder iniciar corretamente o File Browser, vamos executar o comando abaixo.

```bash
filebrowser -a 0.0.0.0 -p 8080 -r ~/storage/shared
```

Este comando faz o File Browser rodar na porta 8080 do nosso servidor!!

Nosso servidor está quase pronto, falta só mais alguns passos importantes.

## 6.1) Comando para o Termux não dormir

Esse comando abaixo, faz o Termux entender que ele não deve matar a nossa aplicação e deixar ele rodando.

```bash
termux-wake-lock
```

Na prática ele fala pro Android quando o não tiver ninguem mexendo no celular, ele não deixa o processo do File Browser morrer, mantém a conexão com WI-FI ativa e também garante respostas rápidas do nosso app.

## 7) Deixando o File Browser automático

A forma como rodamos o File Browser no passo 6, precisamos deixar o terminal aberto, se não o File Browser não conseguirá rodar, e toda vez que o celular desligar, precisariámos fazer todos esses passos novamente, mas como vamos querer que ele fique 100% automático, vamos precisar fazer algumas alterações.

Para isso vamos precisar instalar o Termux:Boot no F-Droid, e depois que instalar, entrar nele pelo menos uma vez, para o sistema entender.

Após isso, precisamos criar uma pasta `boot` dentro das configurações do Termux:

```bash
mkdir -p ~/.termux/boot
```

Agora que temos nossa pasta, vamos criar um executável que o Termux:Boot irá executar sempre que o celular (aka nosso Servidor NAS) reiniciar, ele vai `startar` o nosso servidor File Browser de forma automática! Top demais né...

Então podemos rodar o seguinte comando no terminal do Termux:


```bash
vim ~/.termux/boot/start-services.sh
```

Quando digitar isso, será preciso apertar a tecla `a` para poder escrever dentro do `vim`, e nós vamos precisar escrever o seguinte:

```bash
#!/data/data/com.termux/files/usr/bin/sh
termux-wake-lock

sshd

filebrowser -a 0.0.0.0 -p 8080 -r ~/storage/shared > /dev/null 2>&1 &
```

Esse comando a cima faz com que iniciamos o servico Wake Lock do Termux, o nosso ssh e também nosso File Browser na porta correta.

Para sair do editor vim, precionamos `esc+:+wqa` um de cada vez, que assim ele vai sair e salvar as alterações.

Agora o último passo, é transformar esse arquivo que acabamos de criar, em um arquivo executável.

```bash
chmod +x ~/.termux/boot/start-services.sh
```

Após isso, seu Servidor Nas File Browser vai iniciar automaticamente toda vez que o celular for ligado.

## 8) Mudando a senha padrão do File Browser

Após o File Browser estiver rodando, podemos agora alterar a senha do usuário padrão (admin) para uma senha do nosso gosto, com o comando:

```bash
filebrowser users update admin --password SUA_SENHA_AQUI
```

## 9) Acessando nosso Servidor NAS
Pronto, agora nosso File Browser já está no ar, e podemos acessar ele de qualquer dispositívo que esteja na mesma rede pelo navegador.

Para isso, no Termux precisamos descobrir qual é o IP do nosso servidor, e pra isso executamos o comando:

```bash
ifconfig
```

E o resultado do comando, perto da parte do `wlan0` sera o nosso IP, no meu caso é `192.168.200.240`, com esse número em mãos, só precisamos ir no navegador do pc, notebook ou celular que queremos usar para acessar o nosso servidor e digitar `192.168.200.240:8080`.

Não podemos esquecer de usar a porta `8080` para acessar o nosso File Browser.

Se você já percebeu, podemos fazer isso para vários tipos de serviços, e cada serviço ocupará uma porta, nesse caso o File Browser esta na `8080`, poderiamos hospedar outros serviços nas outras portas.

E agora é só desfrutar, fazer login com o usuário `admin` e colocar a senha que definimos no passo 8.

## Passo extra
Para a gente conseguir ver o uso de RAM e CPU do nosso servidor via ssh, podemos executar o seguinte comando:

```bash
pkg update && pkg install htop -y
```


## Considerações finais

Agora que temos o nosso Servidor Nas no celular, podemos usar e abusar dele.

Para melhor performance de upload dos arquivos, seria legal ter um adaptador de rede conectado ao celular, dessa forma, ele vai upar arquivos de forma muito mais rápida.

Também varia de dispositivo para dispositivo, as vezes a limitação vai ser do próprio celular ou da rede, se o celular estiver com o WI-FI muito longe, os uploads de arquivos vão ser mais lentos.

É isso rapaziada, curtiu?
Apoia o canal se esse artigo foi útil, considere virar membro.

Caso eu tenha feito alguma coisa de forma confusa, pode me chamar, ou se tiver alguma melhoria ou upgrade, me avisa também.

Tamo junto

