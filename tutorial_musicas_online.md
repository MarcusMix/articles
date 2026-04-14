# Como ter um Servidor de Músicas via Termux


## Instalar Navidrome no Termux

O Navidrome é um cliente web para ler e escutar nossas músicas baixadas, ele é open-source e 100% gratuito.

Para baixar ele, rodamos o comando: 

```bash
pkg install navidrome ffmpeg -y
```


## Preparar a pasta de músicas

Precisamos garantir que o Termux tem acesso ao nossos arquivos do dispositivo, para isso executamos comando:

```bash
termux-setup-storage
```

Depois disso, precisamos garantir que temos uma pasta para as músicas, para isso vamos usar a pasta padrão do Android:

`~/storage/shared/Music`

## Rodando o Navidrome pela primeira vez

Para iniciar o servidor e configurar onde os arquivos estão, vamos executar o comando:

```bash
navidrome --music-folder ~/storage/shared/Music --port 4533
```

- A flag `--music-folder` está dizendo ao Navidrome onde procurar as nossas músicas
- A flag `--port 4533` define a porta de acesso para o Navidrome

## Configuração inicial via Navegador

Agora precisamos fazer a configuração inicial do nosso Navidrome, para isso vamos acessa-lo.

1) Digite no navegador `http://[IP-DO-SEU-SERVIDOR]:4533`
2) Criar um usuário e senha
3) E pronto, já está pronto e podemos usufruir de tudo!


## Deixando o Navidrome rodando em segundo plano

Para deixar-mos ele rodando sempre e de forma automática, enquanto fazemos qualquer outra coisa, podemos adicionar ele no `termux-boot`.

Edite ou crie o arquivo `~/.termux/boot/start-devices.sh`:
```bash
navidrome --musicfolder ~/storage/sahred/Music --port 4533 > navidrome.log 2>&1 &
```

Ou rodar ele diretamente no terminal também funciona, porém toda vez que reiniciar, será necessário rodar na mão outra vez.


## Considerações finais

Rodar um servidor de músicas local é muito fácil e rápido, e te dá muita autonomia do que escutar e também de ser dono dos seus próprios dados.


Se gostou, da uma estrela e fork nesse repo, valeuss.
