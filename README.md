# Projeto Final
Projeto Final de conclusão da disciplina INF331 - Componentização e Reuso de Software: Conceitos e Práticas (2020)

# Projeto `<Título>`

# Equipe
*   Agner Esteves Ballejo
*   Ian Poli Tavares
*   José Eduardo Porte
*   Mateus Gonçalves Geracino
*   Marcos Vinícius Piaia
*   Tatiany Fermino Rodrigues de Oliveira


# Nível 1

Apresente aqui o detalhamento do Nível 1 conforme detalhado na especificação com, no mínimo, as seguintes subseções:

## Diagrama Geral do Nível 1

Apresente um diagrama conforme o modelo a seguir:

> ![Modelo de diagrama no nível 1](images/coreografia.png)

### Detalhamento da interação de componentes

O detalhamento deve seguir um formato de acordo com o exemplo a seguir:

* O `componente X` inicia o leilão publicando no barramento a mensagem de tópico "`leilão/<número>/início`" através da interface `Gerente Leilão`, iniciando um leilão.
* O `componente Y` assina no barramento mensagens de tópico "`leilão/+/início`" através da interface `Participa Leilão`. Quando recebe uma mensagem…

Para cada componente será apresentado um documento conforme o modelo a seguir:

## Componente `<Nome do Componente>`

> <Resumo do papel do componente e serviços que ele oferece.>

![Componente](diagrama-componente-mensagens.png)

**Interfaces**
> * Listagem das interfaces do componente.

As interfaces listadas são detalhadas a seguir:

## Detalhamento das Interfaces

### Interface `<nome da interface>`

> Resumo do papel da interface.

**Tópico**: `<tópico que a respectiva interface assina ou publica>`

Classes que representam objetos JSON associados às mensagens da interface:

![Diagrama Classes REST](images/diagrama-classes-rest.png)

~~~json
<Formato da mensagem JSON associada ao objeto enviado/recebido por essa interface.>
~~~

Detalhamento da mensagem JSON:

Atributo | Descrição
-------| --------
`<nome do atributo>` | `<objetivo do atributo>`

## Exemplo

### Interface DadosPedido

Interface para envio de dados do pedido com itens associados.

**Tópico**: `pedido/{id}/dados`

Classes que representam objetos JSON associados às mensagens da interface:

![Diagrama Classes REST](images/diagrama-classes-rest.png)

~~~json
{
  "number": 16,
  "duoDate": "2009-10-04",
  "total": 1937.01,
  "items": {
    "item": {
       "itemid": "1245",
       "quantity": 1
    },
    "item": {
       "itemid": "1321",
       "quantity": 1
    }
  }  
}
~~~

Detalhamento da mensagem JSON:

**Order**
Atributo | Descrição
-------| --------
number | número do pedido
duoDate | data de vencimento
total | valor total do pedido
items | itens do pedido

**Item**
Atributo | Descrição
-------| --------
itemid | identificador do item
quantity | quantidade do item

# Nível 2

Apresente aqui o detalhamento do Nível 2 conforme detalhado na especificação com, no mínimo, as seguintes subseções:

## Diagrama do Nível 2

Apresente um diagrama conforme o modelo a seguir:

> ![Modelo de diagrama no nível 2](images/diagrama-subcomponentes.png)

### Detalhamento da interação de componentes

O detalhamento deve seguir um formato de acordo com o exemplo a seguir:

* O componente `Entrega Pedido Compra` assina no barramento mensagens de tópico "`pedido/+/entrega`" através da interface `Solicita Entrega`.
  * Ao receber uma mensagem de tópico "`pedido/+/entrega`", dispara o início da entrega de um conjunto de produtos.
* Os componentes `Solicita Estoque` e `Solicita Compra` se comunicam com componentes externos pelo barramento:
  * Para consultar o estoque, o componente `Solicita Estoque` publica no barramento uma mensagem de tópico "`produto/<id>/estoque/consulta`" através da interface `Consulta Estoque` e assina mensagens de tópico "`produto/<id>/estoque/status`" através da interface `Posição Estoque` que retorna a disponibilidade do produto.

Para cada componente será apresentado um documento conforme o modelo a seguir:

## Componente `<Nome do Componente>`

> <Resumo do papel do componente e serviços que ele oferece.>

![Componente](images/diagrama-componente.png)

**Interfaces**
> * Listagem das interfaces do componente.

As interfaces listadas são detalhadas a seguir:

## Detalhamento das Interfaces

### Interface `IControlaHistorico`

![Diagrama da Interface](images/i-controla-historico.png)
  
> O objetivo dessa Interface é buscar o histórico seja ele de um Cliente, Fornecedor, Produto ou Logística e a mesma está localizada no `Controller Historico`

Método | Objetivo
-------| --------
`getHistorico` | Retorna um objeto do tipo Historico que contém o histórico de um Cliente, Fornecedor, Produto ou Logística

### Interface `IHistoricoCliente`

> ![Diagrama da Interface](images/i-historico-cliente.png)

> O objetivo dessa Interface é retornar o histórico de um Cliente e a mesma está localizada dentro do `Controller Historico"`

Método | Objetivo
-------| --------
`getHistoricoCliente` | Retorna o histórico de um Cliente

### Interface `IHistoricoFornecedor`

> ![Diagrama da Interface](images/i-historico-fornecedor.png)

> O objetivo dessa Interface é retornar o histórico de um Fornecedor e mesma está localizada dentro do `Controller Historico"`

Método | Objetivo
-------| --------
`getHistoricoFornecedor` | Retorna o histórico de um Fornecedor

### Interface `IHistoricoProduto`

> ![Diagrama da Interface](images/i-historico-produto.png)

> O objetivo dessa Interface é prover o histórico de um Produto e a mesma está localizada dentro do `Controller Historico"`

Método | Objetivo
-------| --------
`getHistoricoProduto` | Retorna o histórico de um Produto

### Interface `IHistoricoLogistica`

> ![Diagrama da Interface](images/i-historico-logistica.png)

> O objetivo dessa Interface é prover o histórico de um parceiro Logístico e a mesma está localizada dentro do `Controller Historico"`

Método | Objetivo
-------| --------
`getHistoricoLogistica` | Retorna o histórico de um parceiro Logístico

# Multiplas Interfaces

> Escreva um texto detalhando como seus componentes  podem ser preparados para que seja possível trocar de interface apenas trocando o componente View e mantendo o Model e Controller.
>
> É recomendado a inserção de, pelo menos, um diagrama que deve ser descrito no texto. O formato do diagrama é livre e deve ilustrar a arquitetura proposta.
