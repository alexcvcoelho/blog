---
layout: post
title:  "Ambiente de Desenvolvimento PHP com Docker + XDebug + VSCode"
date:   2019-07-12 21:57:04 -0300
categories: php docker xdebug vscode
---
> Neste artigo tratarei de como montar um ambiente de desenvolvimento PHP no Linux, 
> rápido de ser instalado através do docker e que seja possível realizar 
> debug utilizando o VSCode como IDE.

![](https://user-images.githubusercontent.com/15811067/75740409-4d757c00-5ce6-11ea-844f-865094d30c9c.png)

{% include embed.html url="https://www.youtube.com/embed/uKtL10q6Rh0" %}

O ambiente de desenvolvimento funcionará segundo a arquitetura descrita na Figura 1, tendo o Visual Studio Code como IDE que realizará o debug do código, e o Docker rodando um contêiner com PHP e XDebug, além de ter um volume mapeado entre a pasta em que está o código a se realizar debug e a pasta raíz do apache dentro do contêiner (`/var/www/html`), desta forma o código alterado no computador também está sendo alterado dentro do contêiner.

![Figura 1: Arquitetura do ambiente de desenvolvimento (Fonte: [XDebug](https://xdebug.org/docs/remote))](https://user-images.githubusercontent.com/15811067/75740766-5024a100-5ce7-11ea-9015-fc53eb029b6a.gif)

## Configuração do Ambiente
Inicialmente se você não possui o docker instalado em seu computador recomento baixar através do [site oficial](https://docs.docker.com/install/){:target="_blank"}.

**1** - Crie o arquivo Dockerfile apresentado abaixo em seu computador.

{% gist 4e5195240cbb870fd32f407809fca67b %}

Nele coloquei alguns comentários para que você entenda também o que está acontecendo exatamente na criação da imagem, como pode ser visto, estou usando uma imagem oficial com Apache e PHP na versão 7.3 como base, caso você precise de uma versão específica, basta alterar a imagem base para a sua versão, aqui você pode consultar as imagens oficiais disponíveis.

---

**2** - No diretório em que foi criado o arquivo Dockerfile, abra o terminal e execute o comando para construir a imagem.

** Aqui estou nomeando essa imagem como php/debug, fique a vontade para utilizar um nome diferente, ou versioná-la utilizando a notação `php/debug:1.0` no meu caso a versão utilizada será a `latest` **

``` 
docker build -t php/debug . 
```

---

**3** - Com a imagem criada, é preciso agora criar o contêiner, execute o seguinte comando **dentro da pasta em que está o código**.

** O primeiro parâmetro passado para o Docker está dizendo para fazer um link entre a porta 80 do host (seu computador) com a porta 80 do contêiner, o segundo parâmetro faz o link entre a pasta atual do host com a pasta pública de dentro do contêiner **

```
docker run -p "80:80" -v $PWD:/var/www/html -d php/debug
```

Neste momento o contêiner já está funcionando com o XDebug pronto para responder qualquer chamada feita pelo VSCode, pois em nosso Dockerfile já existe a configuração `xdebug.remote_connect_back=on` que permite ao XDebug responder a qualquer IDE que realizar uma requisição a ele, sem a necessidade de especificarmos a qual IP o XDebug deve responder.

---

**4** - Agora iremos configurar o VSCode, para isso abra a pasta do código dentro do VSCode, agora clique em “Debug” no menu lateral da esquerda (ou Ctrl+Shift+D) e clique na engrenagem que aparecerá na parte superior do menu lateral (open launch.json) no arquivo `launch.json` utilize a seguinte configuração.

{% gist e369f71382eb434df3b50c653b73ee72 %}

Nele é especificado a porta pela qual o VSCode vai se comunicar com o XDebug (por padrão é a 9000), a configuração `stopOnEntry` diz se o debug parará na primeira linha de código ou apenas quando encontrar um breakpoint. A configuração que não pode faltar neste arquivo é a `pathMappings`, a qual garante o sincronismo entre os arquivos da IDE com os que estão sendo analisados pelo XDebug dentro do contêiner, essa configuração contêm o caminho da pasta em que está o código no contêiner (`/var/www/html`), seguido do caminho da pasta no host (no meu caso `/home/apps/php/projetoteste`).

---

**5** - Neste momento o nosso ambiente já está configurado e para ver o resultado, basta adicionar um Breakpoint no código, apertar F5 no VSCode e acessar seu código pelo browser :)

---

## Dica
Durante a criação do meu ambiente um dos erros que cometi foi mapear errado as pastas dentro do VSCode na configuração pathMappings, e só fui descobrir que o erro que eu estava tendo era esse, quando habilitei o stopOnEntry , então fica a dica, se algo der errado verifique se o caminho está mapeado corretamente, tanto no VSCode quanto na hora da criação do contêiner.

## Conclusão
Uma possível melhoria a ser feita nesse ambiente é criar um docker-compose para o contêiner PHP adicionando outros contêineres que por ventura venham a existir em sua aplicação, como MySQL por exemplo, assim é possível rodar todo o ambiente de desenvolvimento utilizando apenas imagens do Docker sem a necessidade de instalar essas ferramentas diretamente no host, mas meu foco aqui foi mostrar um pouco mais detalhado como realizar o debug de um código que está dentro de um contêiner.


