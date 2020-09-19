# Projeto Final
Projeto Final de conclusão da disciplina INF331 - Componentização e Reuso de Software: Conceitos e Práticas (2020)

# Projeto `6MKT`

# Equipe

*   Agner Esteves Ballejo
*   Ian Poli Tavares
*   José Eduardo Porte
*   Mateus Gonçalves Geracino
*   Marcos Vinícius Piaia
*   Tatiany Fermino Rodrigues de Oliveira


# Nível 1

Segue aqui uma arquitetura geral dos componentes que compões o sistema de um market place com foco nos cenários de Gestão de Fornecedores:

## Diagrama Geral do Nível 1

Segue o diagrama do mesmo:

![Diagrama Nivel 1](./images/INF331_Nivel_1.jpg)

### Detalhamento da interação de componentes

O detalhamento deve seguir um formato de acordo com o exemplo a seguir:

1. O `componente MarketPlace` inscreve-se no tópico `/lance/*` utilizando a interface **Lance**;
1. O `componente Fornecedor` inscreve no tópico `/lance/leilao/inicio` utilizando a interface **Lance**;
1. O `componente Pedido` inscreve no tópico `/lance/*/finaliza` utilizando a interface **Lance**;
1. O `componente Cliente` escreve uma mensagem no tópico `/lance/request` utilizando a interface **Lance**;
1. O `componente MarketPlace` subscreve-se no tópico `/lance/request` através da interface `Leilao` para ouvir os pedidos de abertura de leilão e realiza os seguintes passos:
   - Escreve uma mensagem para o tópico `/lance/leilao/{id_leilao}/inicio` utilizando a interface **Lance**;
   - Subscreve-se no tópico `/leilao/{id_leilao‘/*` utilizando a interface **Leilao**;
1. O `componente Fornecedor`, por meio da interface **Leilao**, subscreve-se no tópico `/leilao/*` para encontrar leilões que ele possua algum produto:
   - Caso o mesmo possua um produto, ele escreve no tópico `/lance/[id_leilao]/*`;
1. O `componente Fornecedor` pode escrever no tópico `/lance/{id_leilao‘/oferta` a fim de realizar uma oferta para o leilão;
1. O `componente Marketplace` escuta o tópico `/lance/{id_leilao‘/oferta` e realizar os seguintes passos:
   - Identificar se o leilão ainda é válido através da propriedade `status`;
   - Realiza o ranqueamento da proposta, utilizando o timestamp informado como critério de desempate das ofertas;
   - Informa o status do ranking enviado, através da interface **GerenciaLeilao** no tópico `/lance/[id_leilao/ranking`;
1. O `componente Cliente` escuta o tópico `/leilao/{id_leilao}/ranking` utilizando a interface **Leilao**;
1. O `componente MarketPlace` escreve uma mensagem no tópico `/leilao/{id_leilao}/finaliza` utilizando a interface **GerenciaLeilao**;
1. O `componente Fornecedor` por meio da interfacee **Leilao**, escuta o tópico `/leilao/{id_leilao}/finaliza` e finaliza sua inscrição;
1. O `componente Cliente` por meio da interfacee **Leilao**, escuta o tópico `/leilao/{id_leilao}/finaliza` e finaliza sua inscrição;
1. O `componente Pedido` por meio da interfacee **Leilao**, escuta o tópico `/leilao/{id_leilao}/finaliza` e executa os seguintes passos:
   - Verifica se o leilao terminou com uma oferta válida escolhida;
   - Caso sim, gera um novo pedido deste leilão;
   - Envia uma notificação para o Fornecedor e Cliente.

A seguir serão mostradas com mais detalhes as mensagens utilizadas acima:

## Componente `Marketplace`

​	Este componete foi inserido aqui, no detalhamento do nível 1, visto a sua importância no cenário acima e na operação do MarketPlace como um todo.
Sua interface `Gerência de Pgto` representa a comunicação da platforma com os sistemas externos para o controle de pagamento do pedido realizado na plataforma. Além disso, sua Interface `Gerencia de Leilao` é a responsável pelo controle dos lances e do processo de compra após a finalização do leilão, além de controlar quais seriam as estatítcas avaliadas pelo sistema e possibilitar o acesso dessas informações aos gestores da platforma via a interface `Analytics`.

​	Dessa forma, vemos que os demais componetes do sistema acabam, de alguma forma conectados à ele, transformando-o no principal elemento do sistema.

![Componente MarketPlace](images/componente_marketplace.jpg)

## Detalhamento das Interfaces

### Interface `Leilao`

Esta interface tem como objetivo e responsabilidade fornecer meios para iniciar ou finalizar um leilão.

**Tópico**: `/leilao/{id_leilao}/lances`

Classes que representam objetos JSON associados às mensagens da interface:

![Diagrama Classes REST](images/diagrama-classes-lances.png)

~~~json
{
    "auction": {
        "id": "<String>"
    },
    "offer": {
        "supplier": {
            "id": "<String>"
        },
        "amount": "<BigDecimal>",
        "paymentMethod": {
            "type": "CREDIT_CART|BOLETO|DEBIT"
        },
        "occurred_time": "<DateTime>"
    }
}
~~~



### Interface `Lance`

Esta interface tem como objetivo e responsabilidade fornecer meios para iniciar um leilão e dar lances futuros no mesmo.

**Tópico**: `/lance/request`
Cria um leilão com base nas informações enviadas

Classes que representam objetos JSON associados às mensagens da interface:

![Diagrama Classes REST](images/diagrama-classes-criar-leilao.png)

~~~json
{
    "id": "<String>",
    "product": {
        "id": "<String>"
    },
    "client": {
        "id": "<String>"
    }
}
~~~

**Tópico**: `/lance/{id_leilao}/ofertas`
Realizar uma oferta em um leilão.

Classes que representam objetos JSON associados às mensagens da interface:

![Diagrama Classes REST](images/diagrama-classes-ofertas.png)

~~~json
{
    "id": "<String>",
    "product": {
        "id": "<String>"
    },
    "client": {
        "id": "<String>"
    },
    "offer": {
        "amount": "<BigDecimal>",
        "paymentMethod": {
            "type": "CREDIT_CART|BOLETO|DEBIT"
        },
        "supplier": {
            "id": "<String>"
        }
    }
}
~~~


**Tópico**: `/leilao/{id_leilao}/oferta`

Classes que representam objetos JSON associados às mensagens da interface:

![Diagrama Classes REST](images/diagrama-classes-oferta.png)

~~~json
{
    "id": "<String>",
    "product": {
        "id": "<String>"
    },
    "client": {
        "id": "<String>"
    },
    "status": "CREATED|FINALIZED|CANCELLED"
}
~~~

**Tópico**: `/leilao/{id_leilao}/ranking`

Classes que representam objetos JSON associados às mensagens da interface:

![Diagrama Classes REST](images/diagrama-classes-ranking.png)

~~~json
{
    "id": "<String>",
    "offers": [
        {
            "amount": "<BigDecimal>",
            "paymentMethod": {
                "type": "CREDIT_CART|BOLETO|DEBIT"
            },
            "supplier": {
                "id": "<String>"
            }
        }
    ]
}
~~~

**Tópico**: `/lance/{id_leilao}/compra`

Finalizar a compra de um leilão.

### Interface `GerenciaLeilao`

Esta interface tem como objetivo e responsabilidade fornecer meios para iniciar um leilão e dar lances futuros no mesmo.

**Tópico**: `/lance/{id_leilao}/inicio`

Inicia um leilão

**Tópico**: `/lance/{id_leilao}/finaliza`

Finaliza um leilão.

# Nível 2

Considerando a arquitetura acima, optou-se por detalhar o componente `Historico` considerando suas interfaces `Historico` e `Pedido`

Este componente tem por objetivo gerenciar as diversas informações geradas pelo sistema e que contribuem para o processo de avaliação do Fornecedor, além de auxiliar no processo de compra pelo Cliente. Dessa forma, ele monitora o barramento de comunicação do sistema acomulando informações relativas aos Pedidos, incluindo revisões do cliente sobre os elementos envolvidos neste e informações do componente de Logística relativas à preparação, envio, entrega e observações sobre a logística do Pedido.

Esses dados são acessados via as Interfaces Histórico, BuscaHist e GerenciaHist buscando atender ao conjunto de atividades detalhado abaixo:

![Diagrama de atividades - Historico](./images/flows_inf331_historico.png)

O Diagrama abaixo apresenta maiores detalhes sobre os elementos envolvidos neste componente.

## Diagrama do Nível 2

<img src="images/historicos_subcomponentes_mvc.jpg" alt="Diagrama de subcomponetes - Historico" style="width:100%;height:100%" />

## Componente `Historico`

Este componente considera o seguinte fluxo de atividade em seus subcomponentes.

- Como visto no diagrama acima, o componente `Controla Historico` concentra os meios de comunicação com os demais elementos do sistema, sendo que ele implementa as interfaces:

  - `Historico`
  - `Log`
  - `BuscaHist`
  - `Peidido`

  Com isso, o componente `Historico` assina os seguintes tópicos

  1. **`log/*`**
  2. **`pedido/*`**
  3. **`buscahist/*`**

- Ao receber uma mensagem com o tópico `/log/*` as seguintes ações são realizadas

  - O Componente `Controla Historico` utiliza a interface `Gerencia Hist Log` para acessar o componente `Historico Logistica`
  - O Componente `Historico Logistica` utiliza a interface `Model` para acessar o controlador dos dados do sistema
  - O Componente `Model Hist Lgisitica` realiza as manipulações necessárias sobre os dados da mensagem recebida e via a interface `Dataset`, persiste essas informações
  - O Componente `Data Connector` gerencia a interface com o subsistema sendo usado para a persistência dos dados.

- Ao receber uma mensage com o tópico `/pedido/*` as seguintes ações são realizadas

  - O Componente `Controla Historico` seleciona as informações de Cliente e utiliza a interface `Gerencia Hist Cliente` para acessar o componente `Histórico Cliente` e faz o mesmo procedimento com as informações de Fornecedor e Produto, utilizando os componentes `Historico Fornecedor`e `Historico Produto`.
  - O Componente `Historico Cliente` utiliza a interface `Model` para acessar o controlador dos dados do sistema. O mesmo procedimento acontece com os componentes `Historico Fornecedor`e `Historico Produto` que também implementaram a interface `Model`.
  - O Componente `Model Hist Cliente` realiza as manipulações necessárias sobre os dados da mensagem recebida e via a interface `Dataset`, persiste essas informações. O mesmo acontecendo nos componentes `Model Hist Fornecedor` e `Model Hist Produto`.
  - O Componente `Data Connector` gerencia a interface com o subsistema sendo usado para a persistência dos dados.

- Ao receber uma mensagem com o tópico `/buscahist/*` o componente `Controla Historico` entende que um conjunto de informações está sendo requisitado do mesmo. Vale ressaltar que essa mensagem vem identificada com um UUID, apresentando o seguinte formado: `/buscahist/{uuid}/*`.
  Para o tratamento desta mensagem, as seguintes ações são realizadas.

  - O componente `Controla Historico` identifica as solicitações presentes na mensagem, separando as buscas referentes a Cliente, Fornecedor, Produto e Losgística.
  - O componente `Controla Historico` utiliza as interfaces:
    - `Gerencia Hist Cliente` para enviar a solicitação de informações de histórico de Clientes.
    - `Gerencia Hist Fornecedor` para enviar a solciitação de informações de histórico de Fornecedores.
    - `Gerencia Hist Produto` para enviar a solicitação de informações de histórico de Produtos.
    - `Gerencia Hist Logistica` para enviar a solicitação de informações de Logística
  - Cada um dos seguintes componentes:
    -  `Historico Fornecedor` utiliza a Interface `Model` para obter e tratar os dados do componente `Model Hist Fornecedor` e retornar a informação desejada ao componente `Controla Historico`
    -  `Historico Cliente` utiliza a Interface `Model` para obter e tratar os dados do componente `Model Hist Cliente` e retornar a informação desejada ao componente `Controla Historico`
    -  `Historico Produto` utiliza a Interface `Model` para obter e tratar os dados do componente `Model Hist Produto` e retornar a informação desejada ao componente `Controla Historico`
    -  `Historico Logistica` utiliza a Interface `Model` para obter e tratar os dados do componente `Model Hist Logistica` e retornar a informação desejada ao componente `Controla Historico`
  - O Componente `Controla Historico` organiza a resposta à solicitação recebida
  - O componente `Controla Historico` realiza duas operações com essa informação
    - Utilizando a interface `Historico`, escreve no tópico **`/hist/{uuid}`** uma mensagem em resposta à solicitação recebida com esse **{uuid}** contendo as informações organizadas acima
    - Utilizando a interface `View Historico`, o componente `Controla View Historico` Iniciará o processo de exibição destes dados.
      - O componente Controla View Histórico utiliza a interface `Gerencia View` para acessar o barramento interno do componente usado para a interface com os demais componentes utilizados na visualização das informações.
        Estas. além disso, são repassadas pelo barramento utilizando os seguintes tópicos escritos pela interface `Gerencia View`.
        - **`resumo/*`** : tópico lido pela interface `HistView` implementada no componente `View Histórico Resumo`, o qual realiza a exibição dos dados solicitados como um resumo das informações.
        - **`log/*`** : tópico lido pela interface `HistView` implementada no componente `View Historico Logistica`, o qual apresenta os dados detalhados de eventos de "Logística" registrados no sistema para um determinado filtro informado na solicitação ao componente `Historico`
        - **`fornecedor/*`** : tópico lido pela interface `HistView` implementada no componente `View Historico Fornecedor`, o qual apresenta os dados detalhados dos eventos registrados para os Fornecedores presentes nos detalhes da solicitação enviada ao componente `Historico`
        -  **`cliente/*`** : tópico lido pela interface `HistView` implementada no componente `View Historico Cliente`, o qual apresenta os dados detalhados dos eventos registrados para os Clientes presentes nos detalhes da solicitação enviada ao componente `Historico`
        - **`produto/`**:  tópico lido pela interface `HistView` implementada no componente `View Historico Produto`, o qual apresenta os dados detalhados dos eventos registrados para os Produtos presentes nos detalhes da solicitação enviada ao componente `Historico`

  

  Segue abaixo um detalhamento de algumas interfaces consideradas acima.

## Detalhamento das Interfaces


### Interface `IControlaHistorico`

![Diagrama da Interface](images/i-controla-historico.png)

O objetivo dessa Interface é buscar o histórico seja ele de um Cliente, Fornecedor, Produto ou Logística e a mesma está localizada no `Controller Historico`

Método | Objetivo
-------| --------
`getHistorico` | Retorna um objeto do tipo Historico que contém o histórico de um Cliente, Fornecedor, Produto ou Logística

### Interface `IHistoricoCliente`

![Diagrama da Interface](images/i-historico-cliente.png)

O objetivo dessa Interface é retornar o histórico de um Cliente e a mesma está localizada dentro do `Controller Historico`

Método | Objetivo
-------| --------
`getHistoricoCliente` | Retorna o histórico de um Cliente

### Interface `IHistoricoFornecedor`



![Diagrama da Interface](images/i-historico-fornecedor.png)

O objetivo dessa Interface é retornar o histórico de um Fornecedor e mesma está localizada dentro do `Controller Historico`

Método | Objetivo
-------| --------
`getHistoricoFornecedor` | Retorna o histórico de um Fornecedor

### Interface `IHistoricoProduto`

![Diagrama da Interface](images/i-historico-produto.png)

O objetivo dessa Interface é prover o histórico de um Produto e a mesma está localizada dentro do `Controller Historico`

Método | Objetivo
-------| --------
`getHistoricoProduto` | Retorna o histórico de um Produto

### Interface `IHistoricoLogistica`

![Diagrama da Interface](images/i-historico-logistica.png)

O objetivo dessa Interface é prover o histórico de um parceiro Logístico e a mesma está localizada dentro do `Controller Historico`

Método | Objetivo
-------| --------
`getHistoricoLogistica` | Retorna o histórico de um parceiro Logístico


# Multiplas Interfaces

​	Nesse ponto é interessante considerar dois elementos do componente `View Histórico`

- A interface com todos os componentes existentes nele é realizada via um Barramento Interno que trabalha com uma arquitetura de tópicos contendo mensagens em um protocolo definido de comunicação.
  Com isso, toda a interface do módulo de `Controller Historico` sempre acontece via esta estrutura de comunicação, de forma que, se um novo componente `View`poderia ser usado diretamente, desde que utilizasse a mesma arquitetura de troca de mensagens, implementando as interfaces `HistView` e `ViewRequest`

- A abordagem já considerada dentro do próprio componente `View Historico`, onde um componente consegue exibir seus dados utilizando multiplas interfaces.
  A idéia é utilizar uma implementação como a do padrão `Abstract Facotry`, exemplificada via o componente `View Historico Produto` abaixo

  ![Abstract Factory - View Historico Produto](images/multiplas_interfaces_factory.jpg)



​	Com estas duas abordagens tratamos o possibilidade de variação dos componentes encapsulados no módulo `View`

