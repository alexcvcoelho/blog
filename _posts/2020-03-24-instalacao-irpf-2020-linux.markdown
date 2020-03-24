---
layout: post
title:  "Instalação do IRPF 2020 no Linux"
date:   2020-03-23 00:00:00 -0300
categories: tutorial instalacao linux
---

Este não é um artigo originalmente meu, pois é baseado, para não dizer praticamente copiado do tutorial disponível no [Blog do Edivaldo](https://www.edivaldobrito.com.br/como-instalar-a-versao-multiplataforma-do-irpf-2020-no-linux-manualmente/){:target="_blank"}, então o mérito é todo dele. Mas vou fazer esse tutorial aqui também até para servir como uma documentação pessoal, pois é algo que todo ano eu utilizo e obviamente não lembro de cabeça, então vou manter aqui até para facilitar a minha vida e de outras pessoas também.

Tá, mas porque diabos eu to instalando o IRPF no Linux? É porque utilizo Linux no meu computador pessoal, mais especificamente um Kubuntu, mas porque Kubuntu? Gosto da estabilidade e facilidades do Ubuntu e acho linda a interface KDE, sem contar que no SSD a performance é muito boa. Mas enfim tudo isso são apenas gostos, se você usa o Windows, nada contra você, nem a favor, mas este tutorial não vai te ajudar muito, recomendo acessar algum outro tutorial pela net.

Vamos lá, bora instalar essa bagaça. Vou instalar de maneira manual que é a forma em que acredito ser mais fácil de gerenciar versões anteriores e saber onde está cada coisa.

### PASSO 1.

Abre o terminal né criatura.

---
### PASSO 2.

Se você ja seguiu este tutorial no ano passado a sua instalação vai estar na pasta `/opt`, se desejar excluir a instalação anterior é só rodar o seguinte comando excluindo qualquer diretório que contenha irpf inicialmente:
``` bash
sudo rm -Rf /opt/irpf*
```
Provavelmente você também tenha um atalho de acesso rápido criado, para deletá-lo use o seguinde comando, ou você pode reutilizá-lo apenas alterando algumas infotrmações dentro dele para que ele referencie a nova versão:
``` bash
sudo rm -Rf /usr/share/applications/irpf.desktop
```
---

### PASSO 3.

Baixe o arquivo zip através do comando a seguir:
``` bash
wget http://downloadirpf.receita.fazenda.gov.br/irpf/2020/irpf/arquivos/IRPF2020-1.5.zip -O irpf2020.zip
```

Caso o comando apresente algum problema de certificados execute ele com uma opção a mais, como descrito a seguir:
``` bash
wget --no-check-certificate https://downloadirpf.receita.fazenda.gov.br/irpf/2020/irpf/arquivos/IRPF2020-1.5.zip -O irpf2020.zip
```

---

### PASSO 4.

Descompacte o arquivo baixado para a pasta `/opt`
``` bash
sudo unzip irpf2020.zip -d /opt/
```

---

### PASSO 5.

Renomeie a pasta criada:
``` bash
sudo mv /opt/IRPF*/ /opt/irpf2020
```

---

### PASSO 6.

Para facilitar a execução do programa crie um lançador para ele, assim ele ficará disponível no menu de acesso rápido:
``` bash
echo -e '[Desktop Entry]\n Version=1.0\n Name=irpf2020\n Exec=java -Xms128M -Xmx512M -jar /opt/irpf2020/irpf.jar\n Icon=/\n Type=Application\n Categories=Application' | sudo tee /usr/share/applications/irpf2020.desktop
```

``` bash
sudo chmod +x /usr/share/applications/irpf2020.desktop
```

Caso deseje, você pode criar uma cópia deste arquivo na área de trabalho
``` bash
cp /usr/share/applications/irpf2020.desktop  ~/Área\ de\ Trabalho/
```

---

É isso aí, logo após esses passos você já pode executar o programa normalmente.