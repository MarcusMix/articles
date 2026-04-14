# Como fazer um servidor de Minecraft dentro do Servidor Caseiro no Celular via Termux

## Pré-requisitos necessários

- Termux instalado
- Openssh instalado

## Instalar o JDK necessário

Para conseguir ter um servidor de Minecraft, é necessário instalar o JDK adequado para a versão do servidor, no nosso contexto, iremos usar a versão `1.20.4`, que necessida do jdk17.

Então para isso, vamos instalar o JDK17 com o comando abaixo:

```bash
pkg install openjdk-17
```


## Criar pasta para o servidor

Agora vamos criar a pasta para os arquivos do servidor, é dentro dela que vamos baixar as dependências necessárias.

```bash
mkdir ~/minecraft && cd ~/minecraft
```

## Baixar o servidor Paper MC

Será necessário instalar o `wget`caso não tenha instalado ainda, para isso, rode:

```bash
pkg install wget
```

E após isso, podemos rodar o comando abaixo para instalar todas as dependências necessárias do nosso servidor Paper MC.

```bash
wget https://api.papermc.io/v2/projects/paper/versions/1.20.4/builds/497/downloads/paper-1.20.4-497.jar -O server.jar
```

## Aceitar o EULA

```bash
echo "eula=true" > eula.txt
```


## Startar o servidor Minecraft

Agora que temos o servidor devidamente baixado, apenas precisamos inicia-lo com o comando abaixo:


```bash
java -Xmx2G -Xms1G -jar server.jar nogui
```

O `-Xmx2G` limita pra 2GB de RAM. Se ficar pesado demais, troque pra 1G.

## Considerações

E é simples assim rodar um servidor de minezin no Servidor de Celular.

E pra deixar ainda melhor, da pra colocar o comando de rodar o servidor dentro do `.bashrc` com um alias ou dentro do `.termux/boot` para iniciar automaticamente sempre com o servidor. No meu caso eu deixei com alias por que nem sempre vou jogar, então eu inicio o servidor de minecraft com o alias "mine".


Se gostou, deixa uma estrela e da um fork no repo, valeus.
