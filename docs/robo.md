# Robô

## **Introdução a Linguagem:**

LRB é uma linguagem de programação concebida por Luciano Wagner em 2008. A documentação buscar esclarecer as principais dúvidas sobre a estrutura da linguagem e as funções para uso de estudos e backtests no sistema operacional do robô.

## **Tag de execução:**

As estruturas da linguagem LRB, lembra muito a estruturua do HTLM, As tags são usadas para 
informar ao sistema a estrutura da estratégia. A principal característica das tags são formadas 
por sinais de chevron (sinal de “maior que” e “menor que”), ou seja: inicializa com
**< COMANDO >** e fechadas com **</ COMANDO >**, assim delimitando o inicio e fim de cada estrutura.  

**Nota:** ==Os nomes das estruturas são fixos, não podem ser substituidas por outras nomeclaturas.== 

## **Estrutura das TAGS:**

### <*Estrategias*> <*Estrategia*> 
É o inicio e o fim de toda a estrutura de backtest. Dentro da TAG <*Estrategias*>, podemos utilizar 
vários conjuntos de TAGS <*Estrategia*> para formação dos istemas de operacionais.  

### <*Nome*> <*Capital*>
São utilizados para nomear a estratégia e informar o capital inicial da carteira, por padrão utilizamos 100 milhões para fins de backtest.

```{.py3 hl_lines="" linenums="4" title=""}

<NOME> SNIPER_MODELO_13 </NOME>    /*Nome referente a estrategia.*/

<CAPITAL> 100000000 </CAPITAL>     /*Capital inicial da conta.*/
                  
```

### <*ImpedeGenetico*>
Algoritmo gênetico é utilizado para otimização de estrategia baseado na evolução natural.
A TAG utilizada para impedir uma avaliação dentro do estudo gênetico, assim melhorando o tempo de execução
e evitando avaliações indesejáveis .

### <*ComandoInicio*>

O **Comando Inicio** é inicializado apenas no começo da estratégia, podemos utiliza-lo para determinar variáveis com o objetivo de serem parâmetros padrões para o restante da execução.

```{.py3 hl_lines="" linenums="8" title=""}

<COMANDOINICIO>

    SETA(#PERIODO_RSI, 20);
    SETA(#INTERESSE, 5);
    SETA(#GATILHO, 1);	

</COMANDOINICIO>
                  
```
### <*Custos*>

Essa tag é responsável por informar os custos/taxas das operações, são alterados conforme o tipo de ativo que está sendo estudado.

```{.py3 hl_lines="" linenums="16" title=""}

/* AÇOES */
<CUSTOS>

    <SLIPPAGE> 0.0001% </SLIPPAGE>
    <CORRETAGEM> 0 </CORRETAGEM>
    <EMOLUMENTOS> 0.0230% </EMOLUMENTOS>
    <REGISTRO> 0.0050% </REGISTRO>

</CUSTOS>
                  
```

```{.py3 hl_lines="" linenums="16" title=""}

/* AÇOES OPCOES - DAY TRADE */
<CUSTOS>

    <SLIPPAGE> 0.0001% </SLIPPAGE>
    <CORRETAGEM> 0 </CORRETAGEM>
    <EMOLUMENTOS> 0.0450% </EMOLUMENTOS>
    <REGISTRO> 0.0140% </REGISTRO>

</CUSTOS>
```   

### <*Periodo*> <*PeriodoGrafico*>

É o período preferencial da estratégia, mas não é necessário mudar todas as vezes que for estudar outros tempos gráficos, dentro do sistema conseguimos alterar para um novo período, apenas **DIGITANDO** o novo período no gráfico e clicando **ENTER** o sistama mudará automaticamente.

```{.py3 hl_lines="" linenums="23" title=""}

<PERIODO> 30 </PERIODO> 
<PERIODOGRAFICO> 30 </PERIODOGRAFICO>
<PERIODOSINTERESSE> 30 </PERIODOSINTERESSE>
                 
```
### <*PeriodoInteresse*>
São os períodos de interesse, que quando adicionado plot um gráfico que mostra as movimentações do mercado dentro de cada periodo. 
**Maximo: 4 períodos.**

```{.py3 hl_lines="" linenums="23" title=""}

<PERIODOSINTERESSE> 60,15,5 </PERIODOSINTERESSE>
                 
```


### TAGS Controle de Risco

Esse conjunto de tags refere-se a proteção do capital principal, onde é determinado uma    
*Perda maxima, Drawdown maximo, os Gatilhos de Drawdown e Ganho maximo*. 

```{.py3 hl_lines="" linenums="45" title=""}

<PERDAMAXIMA> 2 </PERDAMAXIMA>
<DRAWDOWNMAXIMOTRADE> 30 </DRAWDOWNMAXIMOTRADE>
<GATILHODRAWDOWN> 15 </GATILHODRAWDOWN>
<GANHOMAXIMO> 5 </GANHOMAXIMO>
      
```

### <*Comandos*> <*Comando*>

A TAG <*Comando*> é a mais utilizada dentro da linguagem, ele fica dentro da TAG <*Comandos*>, nela irá conter todas as condições necessárias para poder adicionar ou não as ordens de compra ou venda de cada estratégia. 
A seguir um exemplo de uma estratégia: 

```{.py3 hl_lines="" linenums="70" title=""}

<COMANDOS>
    <COMANDO>

        Seta(#MAX, Maxima());
        Seta(#VARIACAO, #MAX Minima() -);

        SE(
            #VARIACAO 20 >
        ENTAO
            Seta(#GATILHO, 1);	
        SENAO
            Seta(#GATILHO, 0);
        )

        SE(
            #GATILHO 1 =
        ENTAO
            CorCandle(Azul)
            ADICIONASTOP(
                'Ativo',
                0$ 1 - CAPITALCORRENTE() *,
                'COMPRA'POSICAOATUAL(),
                POSICAOATUAL(),
                Minima()/*STOP*/,
                #MAX 12 +/*GAIN*/,
                Fechamento()/*PRECO*/
            );
        SENAO
            Seta(#GATILHO, 0);
        )

    </COMANDO>
</COMANDOS>
```


### <*ComandoAdicionaSTOP*>

Sua funcionalidade é ser um bloco para obter informações específicas referentes
a entrada em uma operação utilizando o AdicionaStop(). Podemos loga, criar variáveis,
matrizes e etc...

```{.py3 hl_lines="" linenums="70" title=""}

<ComandoAdicionaSTOP>

    SETA(#stop, Minima())
    SETA(#entrada, Fechamento())

    SETA(#risco(FIM), #stop #entrada -)
    SETA(#horaexecutada, hora() minuto())

</ComandoAdicionaSTOP>
```
### <*ComandoAjustaSTOP*>

Utilizamos esté bloco para extrair informações refetente a função AjustaSTOP().

```{.py3 hl_lines="" linenums="70" title=""}

<ComandoAjustaSTOP>

    SETA(#motivo(FIM), #ULTMOTIVORISCO)
    SETA(#preco_ajuste(FIM), #ULTPRECOEXECUTADO)

</ComandoAjustaSTOP>
```
### <*ComandoZeraRISCO*>

Utilizamos esté bloco para extrair informações no momento que você zerou a posição.

```{.py3 hl_lines="" linenums="70" title=""}

<ComandoZeraRISCO>

    
    SETA(#motivos(FIM), #ULTMOTIVORISCO)
    SETA(#quantidade(FIM), #ULTQUANTEXECUTADO)

</ComandoZeraRISCO>
```

### <*ComandoAdicionaGATILHO*>

### <*ComandoExecutaGATILHO*>

### <*ComandoAbrePosicao*>
Utilizamos esté bloco para extrair informações no momento que você abriu uma posição.

```{.py3 hl_lines="" linenums="70" title=""}

<ComandoAbrePosicao>
    
    SETA(#preco_entrada, fechamento())
    
</ComandoAbrePosicao>
```
### <*ComandoFechaPosicao*>
Utilizamos esté bloco para extrair informações no momento que você fechou uma posição.

```{.py3 hl_lines="" linenums="70" title=""}

<ComandoFechaPosicao>
    
    SETA(#motivo_saida(FIM), #ULTMOTIVORISCO)
    SETA(#ID(FIM), #ULTIDENTIFICADOREXECUTADO)
    SETA(#quantidade(FIM), #ULTQUANTIDADE)
    SETA(#preco_risco(FIM), #ULTPRECORISCO)

</ComandoFechaPosicao>
```
### <*ComandoPontoINTERESSE*>
Utilizamos esté bloco para extrair informações no momento que você adicionou um ponto de interesse uma posição.

```{.py3 hl_lines="" linenums="70" title=""}

<ComandoPontoINTERESSE>

    SETA(#AVALIACAO_INTERESSE(FIM), Abertura() Fechamento() -)
    
</ComandoPontoINTERESSE>
```

### <*Naodesenha*>

Sua funcionalidade é não plotar uma função na gráfico.

```{.py3 hl_lines="" linenums="113" title=""}

<NAODESENHA>

    Minima()
    maxima()
    Fechamento()
    
</NAODESENHA>

```
### <*ComandoFim*>

O **Comando fim** é executado apenas no final da estratégia, utilizado normalmente para obter alguma informação específica no final do teste.

```{.py3 hl_lines="" linenums="107" title=""}

<COMANDOFIM>

    Loga('Valor total', SaldoPosicoes());
    
</COMANDOFIM>

```


### <*Anotacao*>


### <*Descricao*>


## **Valores e tipos:**

Um **valor** é uma das coisas básicas com as quais um programa trabalha, como letra ou um número. Alguns valores que vimos até agora foi **12**, **0.0000008** e **'Valor total'**, por exemplo.

Esses valores pertecem a **tipos** diferentes: *12* é um **número inteiro**, *0.0000008* é um **número de ponto flutuante** e *'Valor total'* é uma **string**, assim chamada porque as letras que contém estão em uma sequência em cadeia (dentro de aspas simples).

### **Exemplos** 

|**VALORES**  |**TIPOS**|
|             |         |
|0.0000008    | Float   |
|12           | Inteiro |
|'Valor total'| String  |
|1.12         | Float   |
|12002        | Inteiro |
|'1232'       | String  |
|10.1235      | Float   |


## **Cálculos Matemáticos:**

### **Começando a calcular**

A linguagem usa como padrão para realizar os seus cálculos a Notação Polonesa Reversa(RPN). Perceba a seguir a diferença entre os cálculos convencionais e os cálculos em RPN.

|**Cálculos convencionais**        |**Cálculos com RPN**            |
|                                  |                                |
|10 + 20  **R:30**                 |10 20 +   **R:30**              |
|5 * 5  **R:25**                   |5 5 *   **R:25**                |
|5 / 5  **R:1**                    |5 5 /   **R:1**                 |
|100 - 30  **R:70**                |100 30 -   **R:70**             |
|100 - 30 + 150  **R:220**         |100 30 150 - +  **R:220**       |

### ==Resto( )==

**Descrição:**

Retorna o resto da dívisão.

**Parâmetros:**

1.  Valor do Dividendo
2.  Valor do Divisor

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="SomaMatriz( )"}

<COMANDO>
					
   SETA(#RESTO_DIVISAO, Resto(348, 6));

</COMANDO>

```
### ==Logaritmo( )==

**Descrição:**

É uma função que tem como padrão o número de Euler, utilizando a **Base = e**.

**Parâmetros:**

1.  O valor a ser calculado.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="Logaritmo( )"}

<COMANDO>
					
    SETA(#LOG10, Logaritmo(10));

</COMANDO>
```

### ==Potencia( )==

**Descrição:**

Utilizado para calcular a potencia de um número.

**Parâmetros:**

1.  Valor da Base.
2.  Valor do Expoente.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="Potencia( )"}

<COMANDO>
					
    SETA(#Potencia_10, Potencia(10));

</COMANDO>
```

### ==Soma( )==

**Descrição:**

Utilizado para **somar** dois valores.

**Parâmetros:**

1.  Primeiro valor.
2.  Segundo valor.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="Soma( )"}

<COMANDO>
					
    SETA(#SOMAVALORES, Soma(15525522,152555));

</COMANDO>
```

### ==Subtrai( )==

**Descrição:**

Utilizado para **subtrair** dois valores.

**Parâmetros:**

1.  Primeiro valor.
2.  Segundo valor.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="Subtrai( )"}

<COMANDO>
					
    SETA(#subtraiVALORES, Subtrai(15525522,152555));

</COMANDO>
```

### ==Multiplica( )==

**Descrição:**

Utilizado para **multiplicar** dois valores.

**Parâmetros:**

1.  Primeiro valor.
2.  Segundo valor.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="Multiplica( )"}

<COMANDO>
					
    SETA(#MultiplicaVALORES, Multiplica(50, 2));

</COMANDO>
```


### ==Dividi( )==

**Descrição:**

Utilizado para **Dividir** dois valores.

**Parâmetros:**

1.  Primeiro valor.
2.  Segundo valor.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="Dividi( )"}

<COMANDO>
					
    SETA(#dividiVALORES, Dividi(50,5));

</COMANDO>
```

### ==Exp( )==

**Descrição:**

**exp(x)** é a função exponencial cuja base é o número de Euler 
(um número irracional que vale aproximadamente 2,718281828). 

**Parâmetros:**

1.  Valor.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="Exp( )"}

<COMANDO>
					
    SETA(#ValorEuler, Exp(1));

</COMANDO>
```


### ==TaxaVariacao( )==

**Descrição:**

Utilizado calcular a **TaxaVariacao** entre dois valores, ele retorna em uma valor decimal.

**Parâmetros:**

1.  Valor atual.
2.  Valor anterior.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="TaxaVariacao( )"}

<COMANDO>
					
    SETA(#variacao, TaxaVariacao(50, 48));

</COMANDO>
```

### ==TaxaVariacaoPercentual( )==

**Descrição:**

Utilizado calcular a **Taxa Variacao Percentual** entre dois valores.

**Parâmetros:**

1.  Valor atual.
2.  Valor anterior.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="TaxaVariacaoPercentual( )"}

<COMANDO>
					
    SETA(#variacaoP, TaxaVariacaoPercentual(50, 48));

</COMANDO>
```

### ==Arredonda( )==

**Descrição:**

Utilizado **arredondar** um valor decimal. É composto por dois parâmetros, o segundo é a 
quantidade de casas depois da vírgula que gostaria de permanecer, se o valor anterior ao arredondamento 
for igual ou maior que **5** será arredondado para cima, caso o contrario, será para baixo. 

**Parâmetros:**

1.  Valor decimal.
2.  N° de casas de interesse.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="Arredonda( )"}

<COMANDO>
					
    SETA(#ArredondaValor, Arredonda(50.5624, 2));

</COMANDO>
```


### ==Inteiro( )==

**Descrição:**

Utilizado para transformar um valor decimal em número inteiro. 

**Parâmetros:**

1.  Valor decimal.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="Inteiro( )"}

<COMANDO>
					
    SETA(#ValorInteiro, Inteiro(50.5624));

</COMANDO>
```

### ==Trunca( )==

**Descrição:**

Utilizado para cortar as casas decimais de número. O segundo parâmetro informa
a quantidade de casas que permaneceram depois da vírgula.

**Parâmetros:**

1.  Valor decimal.
2.  N° de casas de interesse.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="Trunca( )"}

<COMANDO>
					
    SETA(#ValorTrunca, Trunca(50.5624, 2));

</COMANDO>
```

### ==ABS( )==

**Descrição:**

Retorna o valor absoluto de um valor. O valor absoluto de qualquer número se refere 
a sua magnitude e não ao sinal que pode ter, seja ele positivo ou negativo ... 
Em termos matemáticos, o valor absoluto é uma operação que permite qualquer número 
tornar-se positivo.

**Parâmetros:**

1.  Valor ou expressão.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="ABS( )"}

<COMANDO>
					
    SETA(#ValorABS, ABS(-50.5624 2 +));

</COMANDO>
```


### ==Random( )==

**Descrição:**

Random é uma função matemática que retorna um número aleatório dentre o período
escolhido.

**Parâmetros:**

1.  Periodo.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="Random( )"}

<COMANDO>
					
    SETA(#ValorAleatorio, Random(10));

</COMANDO>
```

### ==maxM( )==

**Descrição:**

Retorna o valor maximo em um conjunto até 5 parâmetros.

**Parâmetros:**

1.  Primeiro Valor
2.  Segundo Valor
3.  Terceiro Valor
4.  Quarto Valor
5.  Quinto Valor

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="maxM( )"}

<COMANDO>
					
    SETA(#ValorMaximo, maxM(10,5,3,45,2));

</COMANDO>
```
### ==minM( )==

**Descrição:**

Retorna o valor minimo em um conjunto até 5 parâmetros.

**Parâmetros:**

1.  Primeiro Valor
2.  Segundo Valor
3.  Terceiro Valor
4.  Quarto Valor
5.  Quinto Valor

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="minM( )"}

<COMANDO>
					
    SETA(#ValorMinimo, minM(10,5,3,45,2));

</COMANDO>
```

### ==Media( )==

**Descrição:**

Utilizado para calcular as médias.

**Parâmetros:**

1.  **MEDIA:** Determina o período da Média Longa para formação do cálculo.
 
|Medias               |
|                     |
|A = Aritmética       |                  
|W = Welles Wilder    |     
|E = Exponencial      |     

2.  **EXPRESSÃO:**  Expressão que será calculado a média.
3.  **PERÍODO:** Período que será a média.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="Media( )"}

<COMANDO>
					
    Grafico(Media(A, Fechamento(), 200));
    
</COMANDO>
```













## **Conversor:**

### ==Valor( )==

**Descrição:**

Utilizado para converter um texto númerico para número.

**Parâmetros:**

1.  Texto númerico.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="Valor( )"}

<COMANDO>
					
    SETA(#ConverteTexto, Valor('252'));

</COMANDO>
```

### ==Estatistica( )==

**Descrição:**

Utilizado para fazer um relatório básico de estatística

**Parâmetros:**

1.  Matriz.
2.  Indice.(opcional)

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="Estatistica( )"}

<COMANDO>
					
    Estatistica(#ConverteTexto);

</COMANDO>
```












## **Determinar variáveis:**
### ==Seta()==

**Descrição:**

Seta é uma função para determinar uma variável. O seu objeto é capaz de reter e representar um valor ou expressão. 
Enquanto as variáveis só “existem” em tempo de execução, elas são associadas a *“nomes”*, chamados identificadores, durante o tempo de desenvolvimento.

**Parâmetros:**

1. *NOME_VARIÁVEL*
2. *EXPRESSÃO* 

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="Seta( )"}

<COMANDO>
        
    SETA(#NOME_VARIAVEL, 1);       /*Valores*/
    SETA(@NOME_STRING, 'TEXTO');   /*Strings*/
   
<COMANDO> 
```


## **Candlesticks:**
### ==Abertura( )==

**Descrição:**

Referente a **ABERTURA** do Candle. Podendo ser utilizada sem parâmetros que retornara a abertura do candle atual, ou poderar utilizá-la com dois parâmetros.

**Parâmetros:**

1.  **TIPOPOSIÇÃO:** **A =** Posição do ativo, **D =** Dinheiro da posição, **O =** Posição de outro ativo.
2.  **PERÍODO:** 0 = Candle Atual, 1 = Candle Anterior, etc...

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="Abertura( )"}

<COMANDO>
					
    Abertura( )   /*Abertura do candle atual*/
    Abertura(,1)  /*Abertura do candle anterior*/
    Abertura(,5)  /*Abertura do quinto candle anterior*/

</COMANDO>
```
### ==Fechamento( )==

**Descrição:**

Referente a **FECHAMENTO** do Candle. Podendo ser utilizada sem parâmetros que retornara a fechamento do candle atual, ou poderar utilizá-la com dois parâmetros.

**Parâmetros:**

1.  **TIPOPOSIÇÃO:** **A =** Posição do ativo, **D =** Dinheiro da posição, **O =** Posição de outro ativo.
2.  **PERÍODO:** 0 = Candle Atual, 1 = Candle Anterior, etc...

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="Fechamento( )"}

<COMANDO>
					
    Fechamento( )   /*Fechamento do candle atual*/
    Fechamento(,1)  /*Fechamento do candle anterior*/
    Fechamento(,5)  /*Fechamento do quinto candle anterior*/

</COMANDO>
```
### ==Maxima( )==

**Descrição:**

Referente a **MAXIMA** do Candle. Podendo ser utilizada sem parâmetros que retornara a maxima do candle atual, ou poderar utilizá-la com dois parâmetros.

**Parâmetros:**

1.  **TIPOPOSIÇÃO:** **A =** Posição do ativo, **D =** Dinheiro da posição, **O =** Posição de outro ativo.
2.  **PERÍODO:** 0 = Candle Atual, 1 = Candle Anterior, etc...

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="Maxima( )"}

<COMANDO>
					
    Maxima( )   /*Maxima do candle atual*/
    Maxima(,1)  /*Maxima do candle anterior*/
    Maxima(,5)  /*Maxima do quinto candle anterior*/

</COMANDO>
```

### ==Minima( )==

**Descrição:**

Referente a **MINIMA** do Candle. Podendo ser utilizada sem parâmetros que retornara a minima do candle atual, ou poderar utilizá-la com dois parâmetros.

**Parâmetros:**

1.  **TIPOPOSIÇÃO:** **A =** Posição do ativo, **D =** Dinheiro da posição, **O =** Posição de outro ativo.
2.  **PERÍODO:** 0 = Candle Atual, 1 = Candle Anterior, etc...

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="Minima( )"}

<COMANDO>
					
    Minima( )   /*Minima do candle atual*/
    Minima(,1)  /*Minima do candle anterior*/
    Minima(,5)  /*Minima do quinto candle anterior*/

</COMANDO>
```

### ==Anterior( )==

**Descrição:**

Esta função utilizamos para localizar os candles anteriores.

**Parâmetros:**

1.  **ATRASO:** Quantos candles posteriores.
2.  **EXPRESSÃO:**  Informação dessejada.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="Anterior( )"}

<COMANDO>
					
    Anterior(1, Fechamento())   /*Fechamento do candle anterior*/
    Anterior(2, VOLUME())       /*Volume do segundo candle anterior*/

</COMANDO>
```
### ==Proximo( )==

**Descrição:**

Referente a candles posteriores. Utilizado apenas para estatística.

**Parâmetros:**

1.  **POSTERIOR:** Quantos candles posteriores.
2.  **EXPRESSÃO:**  Informação dessejado.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="Proximo( )"}

<COMANDO>
					
    Proximo(1, Fechamento())   /*Fechamento do candle posterior*/
    Proximo(2, VOLUME())       /*Volume do segundo candle posterior*/

</COMANDO>

```
## **Cores Candles:**

Exemplos:

|Cores             |Funções                   |
|                  |                          | 
|Aco               |CorCandle(Aco)            |                  
|Amarelo		   |CorCandle(Amarelo) 		  |
|Amarelo Claro     |CorCandle(AmareloClaro)   |
|Aqua			   |CorCandle(AcAquao)        |
|Azul			   |CorCandle(Azul) 	      | 
|AzulClaro	       |CorCandle(Azul Claro)     |
|AzulEscuro	       |CorCandle(Azul Escuro)	  |
|Bege			   |CorCandle(Bege)	          |
|Branco			   |CorCandle(Branco)	      |
|Cadet  		   |CorCandle(Cadet)          |
|Caqui			   |CorCandle(Caqui)	      |
|Chocolate		   |CorCandle(Chocolate)	  |
|Ciano			   |CorCandle(Ciano)	      |
|Cinza			   |CorCandle(Cinza)	      |
|Cinza Claro       |CorCandle(CinzaClaro)     |
|Cinza Escuro      |CorCandle(CinzaEscuro)    |
|Coral			   |CorCandle(Coral)	      |
|Floresta		   |CorCandle(Floresta)       |
|Fucsia			   |CorCandle(Fucsia)         |
|Indigo			   |CorCandle(Indigo)         |
|Laranja		   |CorCandle(Laranja)        |
|Laranja Escuro	   |CorCandle(LaranjaEscuro)  |
|Laranja Vermelho  |CorCandle(LaranjaVermelho)|
|Lavanda		   |CorCandle(Lavanda)        |
|Magenta		   |CorCandle(Magenta)        |
|Mar  			   |CorCandle(Mar)            |
|Marinho		   |CorCandle(Marinho)        |
|Marrom			   |CorCandle(Marrom)         |
|Oliva			   |CorCandle(Oliva)          |
|Oliva Escuro      |CorCandle(OlivaEscuro)    |
|Ouro			   |CorCandle(Ouro)           |
|Prata			   |CorCandle(Prata)          |
|Preto			   |CorCandle(Preto)          |
|Rosa		       |CorCandle(Rosa)           |
|Roxo    		   |CorCandle(Roxo)           |
|Salmao	   	       |CorCandle(Salmao)         |
|Tijolo			   |CorCandle(Tijolo)         |
|Tomate			   |CorCandle(Tomate)         |
|Turquesa		   |CorCandle(Turquesa)       |
|Verde			   |CorCandle(Verde)          |
|Verde Claro	   |CorCandle(VerdeClaro)     | 
|Vermelho		   |CorCandle(Vermelho)       |
|Vermelho Escuro   |CorCandle(VermelhoEscuro) |
|Violeta		   |CorCandle(Violeta)        |



## **Definindo uma matriz:**

**Descrição:**

Uma matriz(vetores) é uma lista que aceita uma complexidade bem maior de operações e uma infinidade de métodos para utiliza-los. Uma matriz contém três tipos de vetores: X, Y e Z. Para definir uma matriz, é muito parecido para definir uma variável, utilizamos a mesma função, porém com algumas diferenças na hora de atribuir um valor. Para cada valor atribuido, existe um indice específico.

**Parâmetros:**

1. *NOME_VARIÁVEL(FIM)*
2. *EXPRESSÃO* 

**Sintaxe:**

Existe DUAS formas para determinar uma matriz:


```{.py3 hl_lines="" linenums="60" title="Seta( )"}

<COMANDO>

    /*ADICIONANDO VALORES NA MATRIZ*/    
    
    /*Forma para adicionar no final da matriz*/
    SETA(#NOME_VARIAVEL(FIM), 1);             /*Atribuindo um valor no vetor X*/
    SETA(#NOME_VARIAVEL(FIM, FIM), 2);        /*Atribuindo um valor no vetor Y*/
    SETA(#NOME_VARIAVEL(FIM, FIM, FIM), 3);   /*Atribuindo um valor no vetor Z*/

    /*Forma para adicionar em um index específico*/
    SETA(#NOME_VARIAVEL(1), 3);               /*Atribuindo um valor no indice em X*/
    SETA(#NOME_VARIAVEL(1,1), 3);             /*Atribuindo um valor no indice em Y*/
    Seta(#NOME_VARIAVEL(1,1,1), 3);           /*Atribuindo um valor no indice em Z*/

    /*ADICIONANDO STRING NA MATRIZ*/

    /*Forma para adicionar no final da matriz*/
    SETA(@NOME_VARIAVEL(FIM), 'D1');              /*Atribuindo um valor no vetor X;*/
    SETA(@NOME_VARIAVEL(FIM, FIM), 'D2');         /*Atribuindo um valor no vetor Y;*/
    SETA(@NOME_VARIAVEL(FIM, FIM, FIM), 'D4');    /*Atribuindo um valor no vetor Z;*/

    /*Forma para adicionar em um index específico*/
    SETA(@NOME_VARIAVEL(1), 'D3');                /*Atribuindo um valor no indice em X*/
    SETA(@NOME_VARIAVEL(1,1), 'D4');              /*Atribuindo um valor no indice em Y*/
    SETA(@NOME_VARIAVEL(1,1,1),'D5');            /*Atribuindo um valor no indice em Z*/

<COMANDO> 
```

## **Matrizes:**


### ==Inicializa( )==

**Descrição:**

Utilizado para inicializar uma matriz, o segundo parametro é usado com () e dentro dele suporta até 50 parâmetros.

**Parâmetros:**

1.  **NOME_VARIAVEL_MATRIZ:** O nome da variavel que será criada.
2.  **VALORES:** O nome da matriz.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="SomaMatriz( )"}

<COMANDO>
					
   Inicializa(@prerotulosx, ('Cod', 'Dia', 'X', 'Venc', 'Uteis', 'Cal', 'Disp', 'DNeg')); 

</COMANDO>
```


### ==SomaMatriz( )==

**Descrição:**

Utilizado para somar os valores de dentro de uma matriz. Onde sua função é pegar a soma de uma matriz e atribuir o valor dentro de uma variável.

**Parâmetros:**

1.  **NOME_VARIAVEL_DESTINO:** O nome da variavel que retornará o valor da soma da matriz.
2.  **NOME_VARIAVEL_MATRIZ:** O nome da matriz.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="SomaMatriz( )"}

<COMANDO>
					
   SomaMatriz(#Retorna_soma_matriz, #matriz);

</COMANDO>
```

### ==ZeraMatriz( )==

**Descrição:**

Utilizado para zerar valores de uma matriz.

**Parâmetros:**

1.  **NOME_VARIAVEL_MATRIZ:** O nome da matriz.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="ZeraMatriz( )"}

<COMANDO>
					
   ZeraMatriz(#matriz);

</COMANDO>
```

### ==GravaMatriz( )==

**Descrição:**

Utilizado para gravar a matriz dentro de um arquivo.

**Parâmetros:**

1.  **NOME_ARQUIVO:** O nome do arquivo.
2.  **NOME_VARIAVEL_MATRIZ:** O nome da matriz.
3.  **X:** Número de colunas.
4.  **Y:** Número de linhas.
5.  **ROTULOX=:** Nome das colunas.
6.  **ROTULOY=:** Nome das linhas.


**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="GravaMatriz( )"}

<COMANDO>
					
   GravaMatriz('opcoes.csv', #opcoesinteresse, 30, 5, ROTULOX=@ROTULOCOLUNAS, ROTULOY=@ROTULOLINHAS);

</COMANDO>
```

### ==ConcatenaMatriz( )==

**Descrição:**

Utilizado para gravar uma matriz em um arquivo existente. 


**Parâmetros:**

1.  **NOME_ARQUIVO_EXISTENTE:** O nome do arquivo.
2.  **NOME_VARIAVEL_MATRIZ:** O nome da matriz.
3.  **X:** Número de colunas.
4.  **Y:** Número de linhas.
5.  **ROTULOX=:** Nome das colunas.
6.  **ROTULOY=:** Nome das linhas.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="ConcatenaMatriz( )"}

<COMANDO>
					
   ConcatenaMatriz('opcoes.csv', #opcoesinteresse, 30, 5, ROTULOX=@ROTULOCOLUNAS, ROTULOY=@ROTULOLINHAS);

</COMANDO>
```

### ==VarMediana( )==

**Descrição:**

Utilizado para calcular a **mediana** de uma matriz. 

**Parâmetros:**

1.  **NOME_MATRIZ:** O nome da matriz.


**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="VarMediana( )"}

<COMANDO>
					
   SETA(#MEDIANA_MATRIZ, VarMediana(#opcoesinteresse));

</COMANDO>
```


### ==VarMedia( )==

**Descrição:**

Utilizado para calcular a **média** de uma matriz. 

**Parâmetros:**

1.  **NOME_MATRIZ:** O nome da matriz.


**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="VarMedia( )"}

<COMANDO>
					
   SETA(#MEDIA_MATRIZ, VarMedia(#opcoesinteresse));

</COMANDO>
```

### ==Var1Quartil( )==

**Descrição:**

Retorna o **valor aproximado de 25%** de toda a matriz.

**Parâmetros:**

1.  **NOME_MATRIZ:** O nome da matriz.


**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="Var1Quartil( )"}

<COMANDO>
					
   SETA(#25_MATRIZ, Var1Quartil(#opcoesinteresse));

</COMANDO>
```

### ==Var3Quartil( )==

**Descrição:**

Retorna o **valor aproximado de 75%** de toda a matriz.

**Parâmetros:**

1.  **NOME_MATRIZ:** O nome da matriz.


**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="Var3Quartil( )"}

<COMANDO>
					
   SETA(#75_MATRIZ, Var3Quartil(#opcoesinteresse));

</COMANDO>
```

### ==Varconta( )==



### ==VarDesvio( )==

**Descrição:**

Retorna o **desvio padrão** do conjunto de dados na matriz. 

**Parâmetros:**

1.  **NOME_MATRIZ:** O nome da matriz.


**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="VarDesvio( )"}

<COMANDO>
					
   SETA(#desvio_MATRIZ, VarDesvio(#opcoesinteresse));

</COMANDO>
```


### ==VarMaxima( )==

**Descrição:**

Retorna o valor **máximo** do conjunto de dados na matriz. 

**Parâmetros:**

1.  **NOME_MATRIZ:** O nome da matriz.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="VarMaxima( )"}

<COMANDO>
					
   SETA(#maximo_MATRIZ, VarMaxima(#opcoesinteresse));

</COMANDO>
```


### ==VarMaxima( )==

**Descrição:**

Retorna o valor **máximo** do conjunto de dados na matriz. 

**Parâmetros:**

1.  **NOME_MATRIZ:** O nome da matriz.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="VarMaxima( )"}

<COMANDO>
					
   SETA(#maximo_MATRIZ, VarMaxima(#opcoesinteresse));

</COMANDO>
```
### ==RemoveMatriz( )==

**Descrição:**

Utilizado para **remover** a matriz do código. 

**Parâmetros:**

1.  **NOME_MATRIZ:** O nome da matriz.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="RemoveMatriz( )"}

<COMANDO>
					
   RemoveMatriz(#opcoesinteresse);

</COMANDO>
```

### ==Ordena( )==

**Descrição:**

Utilizado para **ordenar** a matriz.

**Parâmetros:**

1.  **NOME_MATRIZ_DESTINO:** O nome da matriz.
2.  **NOME_MATRIZ_ORIGEM:** O nome da matriz.
3.  

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="Ordena( )"}

<COMANDO>
					
   Ordena(#MATRIZ_DESTINO, #MATRIZ_ORIGEM);

</COMANDO>
```

### ==UnificaMatriz( )==
### ==FiltraMatriz( )==
### ==EscalaMatriz( )==


### ==CardinalidadeIndice( )==

**Descrição:**

Utilizado para retornar o **número de indices** em uma matriz.

**Parâmetros:**

1.  **NOME_MATRIZ:** O nome da matriz.
2.  

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="CardinalidadeIndice( )"}

<COMANDO>
					
   CardinalidadeIndice(#MATRIZ_DESTINO, x);

</COMANDO>
```


### ==MapeiaMatriz( )==
### ==MinimoMapeiaMatriz( )==
### ==MaximoMapeiaMatriz( )==
### ==VarSQN( )==
### ==Moda( )==
### ==Piece( )==

**Descrição:**

Utilizado retornar um indice de uma matriz.

**Parâmetros:**

1.  **Indice**.
2.  **Matriz**.


**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="Ordena( )"}

<COMANDO>
					
   Ordena(#MATRIZ_DESTINO, #MATRIZ_ORIGEM);

</COMANDO>
```



## **Comentários:**
### ==/comentario/==

Conforme os programas ficam maiores e mais complexos, eles são mais difíceis de ler. As linguagens formais são densas e na maioria das vezes é díficil ver um pedaço de código e compreender o que ela faz ou por que utilizamos ela.

Por essa razão, é uma boa ideia acrescentar notas aos seus programas para explicar em uma linguagem natural o que o programa está fazendo. Essas notas são chamadas de **comentários**, e começam com o símbolo barra e asterisco e terminam com asterisco e barra.


```{.py3 hl_lines="" linenums="60" title="Seta( )"}

<COMANDO>
        
    /*Estrategia XPS*/
    
    SETA(#FECHA, fechamento());  /*Fechamento atual*/
    
    . . .

<COMANDO> 
```

### ==Anotacao( )==











## **Loop:**

**Loop** é uma estrutura de programação que repete uma sequência de instruções até que uma condição específica seja atendida. Os programadores usam loops para alternar entre valores, adicionar somas de números, repetir funções, e diversas outras funcionalidades.

### ==ParaCada( )==

**Descrição:**

Um loop parecido com o *FOR*. A instrução de loop ***ParaCada*** necessita de um conjunto de dados, bem como o incremento para cada iteração. Começando do inicio da matriz para o final, utilizamos essa função para **NÚMEROS** ou **STRINGS**.


**Parâmetros:**

1. **NOME_QUALQUER:** É um nome qualquer que será relacionado aos itens da matriz. 
2. **MATRIZ** 

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="ParaCada( )"}

Exemplo 01:

<COMANDO>
        
    Seta(#NOME_VARIAVEL(FIM), 1); 
    Seta(#NOME_VARIAVEL(FIM), 2);
    Seta(#NOME_VARIAVEL(FIM), 3);


    ParaCada(dados EM #NOME_VARIAVEL FACA
        SE(
            dados 2 =
        ENTAO
            SETA(#VALOR_VALIDO, dados)
        SENAO
            SETA(#VALOR_INVALIDO, 1)
        )
    );

    ParaCada(laranja EM @NOME_VARIAVEL FACA
        SE(
            laranja 'ABEV3' =
        ENTAO
            SETA(#STRING_VALIDO, laranja)
        )
    );

<COMANDO
```

```{.py3 hl_lines="" linenums="60" title="ParaCada( )"}

Exemplo 02:

<COMANDO>
        
    ParaCada(dados EM Range(1, 10) FACA
        SETA(#VALORES(FIM), dados)
    );

<COMANDO

```
### ==Range( )==

**Descrição:**

A função range() permite especificar o início de uma sequência e o final. 
Uma das suas funcionalidades é atribuir valores sequenciais em matrizes

**Parâmetros:**

1. *INICIO:* É o valor inicial do range. 
2. *FIM:* Valor final do range 

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="ParaCada( )"}

Exemplo 01:

<COMANDO>
        
    Seta(#NOME_VARIAVEL(FIM), Minima()); 

    PARACADA(j EM Range(1, 10) FACA
        SE(
            E(
                minima() #NOME_VARIAVEL(j) <= 
            , 
                #atingiuSNP(j)
            , 
                #atingiuSNP(j)
            )
        ENTAO 
            SETA(#movimentosP(j, j), 1);
        )
    );
    
<COMANDO

```
### ==ParaCadaINV( )==

**Descrição:**

Um loop parecido com o *FOR*. A instrução de loop ***ParaCadaINV*** necessita de um conjunto de dados, bem como o incremento para cada iteração. Começando do final da matriz para o inicio, utilizamos essa função para **NÚMEROS**.


**Parâmetros:**

1. *NOME_QUALQUER:* É um nome qualquer que será relacionado aos itens da matriz. 
2. *MATRIZ* 

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="ParaCadaINV( )"}

Exemplo 01:

<COMANDO>
        
    SETA(#NOME_VARIAVEL(FIM), 1); 
    SETA(#NOME_VARIAVEL(FIM), 2);
    SETA(#NOME_VARIAVEL(FIM), 3);


    ParaCadaINV(dados EM #NOME_VARIAVEL FACA
        SE(
            dados 2 =
        ENTAO
            Seta(#Valor_valido, dados)
        SENAO
            Seta(#Valor_in, dados)
        )
    );
<COMANDO

```
```{.py3 hl_lines="" linenums="60" title="ParaCadaINV( )"}

Exemplo 02:

<COMANDO>
        
    ParaCadaINV( dados EM Range(10) FACA
        SETA(#VALORES(FIM), dados)
    );

<COMANDO

```


## **Escrever observações:**
### ==Loga( )==

**Descrição:**

Sua função é exibir mensagens para o arquivo **(LogaDados.csv)** localizado no diretório do Debug. Podemos definir que essa função é a mesma utilizada no *PRINT* da linguagem python. Ela permite até *dez parâmetros*.Cada loga em uma linha no arquivo. Podemos colocar valores, strings, expressões ou variaveis dentro dessa função. 

**Nota:** ==Usamos essa função nas tags: COMANDOINICIO, COMANDO E COMANDOFIM;==

**Parâmetros:**

1.  ***VALORES, STRINGS, EXPREÇÕES OU VARIAVEIS***
2.  ***VALORES, STRINGS, EXPREÇÕES OU VARIAVEIS***
3.  ***VALORES, STRINGS, EXPREÇÕES OU VARIAVEIS***
4.  ***VALORES, STRINGS, EXPREÇÕES OU VARIAVEIS***
5.  ***VALORES, STRINGS, EXPREÇÕES OU VARIAVEIS***
6.  ***VALORES, STRINGS, EXPREÇÕES OU VARIAVEIS***
7.  ***VALORES, STRINGS, EXPREÇÕES OU VARIAVEIS***
8.  ***VALORES, STRINGS, EXPREÇÕES OU VARIAVEIS***
9.  ***VALORES, STRINGS, EXPREÇÕES OU VARIAVEIS***
10. ***VALORES, STRINGS, EXPREÇÕES OU VARIAVEIS***


**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="Loga( )"}

<COMANDO>
        
    Loga('1', '2', '3', '4', '5', '6', '7', '8', '9', '10');
    Loga(#GATILHO, 'ACIONOU');
	Loga(@teste, 2023, '----->', #AVALIA);
	Loga(@test, 'Realizou o teste');

<COMANDO> 
```
### ==Preloga( )==

**Descrição:**

Sua função é mandar mensagens para o arquivo **(LogaDados.csv)** localizado no diretório do Debug parecida com a Função *Loga()*, porém ao invés de adicionar uma nova linha a cada *loga()*, ele irá adicionar todos os dados na mesma linha, é uma vantagem quando precisamos colocar mais de dez parâmetros em uma mesma linha.

**Nota:** ==Usamos essa função nas tags: COMANDOINICIO, COMANDO E COMANDOFIM;==

**Parâmetros:**

1.  ***VALORES, STRINGS, EXPREÇÕES OU VARIAVEIS***
2.  ***VALORES, STRINGS, EXPREÇÕES OU VARIAVEIS***
3.  ***VALORES, STRINGS, EXPREÇÕES OU VARIAVEIS***
4.  ***VALORES, STRINGS, EXPREÇÕES OU VARIAVEIS***
5.  ***VALORES, STRINGS, EXPREÇÕES OU VARIAVEIS***
6.  ***VALORES, STRINGS, EXPREÇÕES OU VARIAVEIS***
7.  ***VALORES, STRINGS, EXPREÇÕES OU VARIAVEIS***
8.  ***VALORES, STRINGS, EXPREÇÕES OU VARIAVEIS***
9.  ***VALORES, STRINGS, EXPREÇÕES OU VARIAVEIS***
10. ***VALORES, STRINGS, EXPREÇÕES OU VARIAVEIS***

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="Preloga( )"}

<COMANDO>
        
    Preloga('1', '2', '3', '4', '5', '6', '7', '8', '9', '10');
    loga(#GATILHO, 'ACIONOU', 2000);

<COMANDO> 

```

### ==LogaEstatisticas( )==

**Descrição:**

Utilizado para fazer estudos completos referente a variavel **@EST_** + **<*nome_variavel*>**. Utilizamos junto com o **LogaEstatisticas( )** a função **AdiconaValor( )**. 

Estudos realizados pela função:

|**DADOS ESTATÍSTICOS ANALISADOS**| 
|                                 |
|Minimo                           |
|Maximo                           |
|Média                            |
|Desvio                           |
|Conta                            |
|Q1                               |
|Q2                               |
|Skewness                         |
|Kurtosis                         |
|Numero_classes                   |

**Parâmetros:**

1. **NOME_DO_ARQUIVO:** O nome do arquivo que será criado.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="LogaEstatisticas( )"}

<COMANDO>
        
    AdicionaValor(@est_a, Random());
    AdicionaValor(@est_b, Random());
    AdicionaValor(@est_c, Fechamento());
    AdicionaValor(@est_d, Abertura());

<COMANDO> 

    . . .

<COMANDOFIM>
	
	LogaEstatisticas('estatis.txt');
		
</COMANDOFIM>

```
### ==LogaVariaveis( )==

**Descrição:**

Podemos utilizar esta função para analisar os valores que estão em todas as variáveis, será criado um arquivo no diretório do Debug para obter as informações.

**Parâmetros:**

1.  *NOME_DO_ARQUIVO*

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="LogaVariaveis( )"}

<COMANDOFIM>
        
    LogaVariaveis("Analise_dados.csv");

<COMANDOFIM> 

```
### ==LogaVariaveisAlteradas( )==

**Descrição:**

Podemos utilizar esta função para analisar todas as variaveis que foram alteradas, será criado um arquivo no diretório do Debug para obter as informações.

**Parâmetros:**

1.  *NOME_DO_ARQUIVO*

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="LogaVariaveisAlteradas( )"}
<COMANDOFIM>
        
    LogaVariaveisAlteradas("Analise_dados_alterados.csv");

<COMANDOFIM> 
```

### ==LogaParcial( )==
### ==LogaHTML( )==








## **Condicionais e recursividade:**

O tópico principal deste capítulo é a função **SE( )**, que executa códigos diferentes 
depenendo do estado do programa. Mas primeiro quero apresentar as expressões booleana.

### **Expressões booleana**

Uma **expressão booleana** é uma expressão que pode ser verdadeira ou falsa. Os exemplos 
seguintes usam o operador =, que compara dois operadores.

```{.py3 hl_lines="" linenums="60" title=""}

<COMANDO>
					
    SE( 
        5 4 =                   /*Se essa condição for verdadeira*/
    ENTAO
        Loga('Verdadeiro');     /*Então mostrará verdadeiro*/
    SENAO
        Loga('Falso');          /*Se for falso, mostrará falso*/
    )

</COMANDO>

```

O operador = é um dos **operadores relacionais**, os outros são:

```{.py3 hl_lines="" linenums="60" title=""}

    X Y =   /*Iguais*/
    X Y !=  /*Diferentes*/
    X Y >   /*X é maior que Y*/
    X Y <   /*X é menor que Y*/
    X Y >=  /*X é maior ou igual que Y*/
    X Y <=  /*X é menor ou igual que Y*/

```
### ==SE( )==

**Descrição:**

É a base das condicionais, onde o objetivo da função é analisar uma expressão 
e determinar se ela é verdadeira ou falsa, dependendo da resposta ela direcionará 
para um caminho, se for verdadeira, irá para o caminho do ***ENTAO***, se for falsa 
irá para o caminho do ***SENAO**(*OPICIONAL*).

**Parâmetros:**

1.  **EXPRESSÃO:** Uma condição que deverá ser analisada.
2.  **ENTAO:** Algo a ser feito se a confição for verdadeira.
3.  **SENAO:** Algo a ser feito se a confição for falsa.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="SE( )"}

<COMANDO>
					
    SE(
        Fechamento() Maxima() =    
    ENTAO
        CorCandle(Marrom);
    SENAO          
    )

</COMANDO>
```

### ==E( )==

**Descrição:**

É utilizado dentro da função **SE( )**, retorna verdadeiro quando todos 
os parâmetros passados forem verdadeiros e falso se caso um dos parâmetros 
forem falsos. Ele suporte até 6 parâmetros.

**Parâmetros:**

1.  **EXPRESSÃO:** Uma condição que deverá ser analisada.
2.  **EXPRESSÃO:** Uma condição que deverá ser analisada.
3.  **EXPRESSÃO:** Uma condição que deverá ser analisada.
4.  **EXPRESSÃO:** Uma condição que deverá ser analisada.
5.  **EXPRESSÃO:** Uma condição que deverá ser analisada.
6.  **EXPRESSÃO:** Uma condição que deverá ser analisada.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="E( )"}

Exemplo 01:

<COMANDO>
					
    SE(
        E(
            Abertura() Fechamento() <
        ,
            Fechamento() Maxima() =
        )
            
    ENTAO
        CorCandle(Preto);
    SENAO          
    )

</COMANDO>
```
```{.py3 hl_lines="" linenums="60" title="E( )"}

Exemplo 02:

<COMANDO>
					
    SE(
        E(
            Abertura() Fechamento() <
        ,
            Fechamento() Maxima() =
        ,
            RSI(9,20) 70 >
        )
            
    ENTAO
        CorCandle(Rosa);
    SENAO          
    )

</COMANDO>
```
### ==NAO( )==

**Descrição:**

É utilizado dentro da função **SE( )**, direcionará para o **ENTAO** 
se o parâmetro passado for falso e irá direcionar para o **SENAO** se
o parâmetro for verdadeiro.

**Parâmetros:**

1.  **EXPRESSÃO:** Uma condição que deverá ser analisada.


**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="NAO( )"}

<COMANDO>
					
    SE(
        NAO(
            Fechamento() Maxima() =
        )    
    ENTAO
        CorCandle(Marrom);
    SENAO          
    )

</COMANDO>

```

### ==OU( )==

**Descrição:**

É utilizado dentro da função **SE( )**, retorna verdadeiro quando um 
dos parâmetros passados forem verdadeiros e falso se caso todos os parâmetros 
forem falsos. Ele suporte até 6 parâmetros.

**Parâmetros:**

1.  **EXPRESSÃO:** Uma condição que deverá ser analisada.
2.  **EXPRESSÃO:** Uma condição que deverá ser analisada.
3.  **EXPRESSÃO:** Uma condição que deverá ser analisada.
4.  **EXPRESSÃO:** Uma condição que deverá ser analisada.
5.  **EXPRESSÃO:** Uma condição que deverá ser analisada.
6.  **EXPRESSÃO:** Uma condição que deverá ser analisada.


**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="NAO( )"}

<COMANDO>
					
    SE(
        NAO(
            Fechamento() Maxima() =
        )    
    ENTAO
        CorCandle(Marrom);
    SENAO          
    )

</COMANDO>

```



































## **Indicadores:**
### ==ADX( )==

**Descrição:**

A função **ADX** retorna o valor do indicador **ADX**, de acordo com os períodos desejados.

**Parâmetros:**

1.  **PERÍODO:** Período utilizado no momento do cálculo do indicador.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="ADX( )"}

<COMANDO>
					
    Grafico(ADX(10));

</COMANDO>
```
### ==ATR( )==

**Descrição:**

A função **StopATR** retorna o valor do indicador **Stop ATR**, de acordo com os parâmetros desejados.

**Parâmetros:**

1.  **PERÍODO:** Período utilizado no momento do cálculo do indicador.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="ATR( )"}

<COMANDO>
					
    Grafico(ATR(20));

</COMANDO>
```

### ==Bollinger( )==

**Descrição:**

A função BollingerBands retorna o valor do indicador Bandas de Bollinger, de acordo com o período e tipo
de média desejados.

**Parâmetros:**

1.  **PERÍODO:** Período que será a média.
2.  **BANDAS** **0 =** Inferior e **1 =** Superior
3.  **DESVIO -** Número do desvio.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="Bollinger( )"}

<COMANDO>
					
    Grafico(Bollinger(20, 0, 2));
    Grafico(Bollinger(20, 1, 2));

</COMANDO>
```

### ==Hilo( )==

**Descrição:**

A função **HILO** retorna o valor do indicador **HILO**, de acordo com o período desejado.

**Parâmetros:**

1.   **PERÍODO** Período utilizado na média.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="Hilo( )"}

<COMANDO>
					
    Desenha(Hilo(6));

</COMANDO>

```

### ==MACD( )==

**Descrição:**

A função **MACD** retorna o valor do indicador MACD Linha, de acordo com os períodos
desejados.

**Parâmetros:**

1.  **MEDIA_LONGA:** Determina o período da Média Longa para formação do cálculo.
2.  **MEDIA_CURTA:**  Determina o período da Média curta para formação do cálculo.
3.  **PERÍODO:** Período que será a média.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="MACD( )"}

<COMANDO>
					
    Grafico(MACD(20, 12, 3));
    
</COMANDO>

```
### ==MACDH( )==

**Descrição:**

A função **MACDH** retorna o valor do indicador MACD Histograma, de acordo com os períodos
desejados.

**Parâmetros:**

1.  **MEDIA_LONGA:** Determina o período da Média Longa para formação do cálculo.
2.  **MEDIA_CURTA:**  Determina o período da Média curta para formação do cálculo.
3.  **PERÍODO:** Período que será a média.


**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="MACDH( )"}

<COMANDO>
					
    Grafico(MACDH(20, 12, 3));
    
</COMANDO>

```

### ==MMA( )==

**Descrição:**

A função Média retorna o valor do indicador Média Móvel, tipo aritmética.

**Parâmetros:**

1.  **PERÍODO:** Período que será a média.
2.  **TIPO_SERIE:**  Série que será considerada para o cálculo.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="MMA( )"}

<COMANDO>
					
    Desenha(MMA(200, Volume()));
    
</COMANDO>

```

### ==MME( )==

**Descrição:**

A função Média retorna o valor do indicador Média Móvel, tipo exponencial.

**Parâmetros:**

1.  **PERÍODO:** Período que será a média.
2.  **TIPO_SERIE:**  Série que será considerada para o cálculo.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="MME( )"}

<COMANDO>
					
    Desenha(MME(200, Volume()));
    
</COMANDO>

```
### ==MMW( )==

**Descrição:**

A função Média retorna o valor do indicador Média Móvel, welles wilder.

**Parâmetros:**

1.  **PERÍODO:** Período que será a média.
2.  **TIPO_SERIE:**  Série que será considerada para o cálculo.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="MME( )"}

<COMANDO>
					
    Desenha(MMW(200, Volume()));
    
</COMANDO>

```

### ==OBV( )==

**Descrição:**

A função OBV retorna o valor do indicador OBV.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="OBV( )"}

<COMANDO>
					
    Desenha(OBV());
    
</COMANDO>

```
### ==OBTR( )==

**Descrição:**

A função OnBalanceTR retorna o valor do indicador On-Balance True Range.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="OBTR( )"}

**Parâmetros:**

1.  **PERÍODO:**  Período utilizado no momento do cálculo do indicador.

<COMANDO>
					
    Desenha(OBTR(150));
    
</COMANDO>

```
### ==RSI( )==

**Descrição:**

A função **RSI** retorna o valor do indicador **IFR(RSI)**, de acordo com o período e tipo desejados.

**Parâmetros:**

1.  **PERÍODO:**  Período utilizado no momento do cálculo do indicador.
2.  **PERÍODO_MEDIA:**  Período utilizado no momento do cálculo do indicador.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="RSI( )"}

<COMANDO>
					
    Grafico(RSI(9,20));
    
</COMANDO>

```
### ==Stocastico( )==

**Descrição:**

A função **SlowStochastic** retorna o valor do indicador **Estocástico Lento**, 
de acordo com o período desejado.

**Parâmetros:**

1.  **PERÍODO:**  Período utilizado no momento do cálculo do indicador.
2. 

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="Estocastico( )"}

<COMANDO>
					
    Grafico(Estocastico(20));
    
</COMANDO>

```
### ==CCI( )==

**Descrição:**

Função CCI retorna o valor do indicador CCI, de acordo com o período desejado.

**Parâmetros:**

1.   **PERÍODO:**  Período utilizado no momento do cálculo do indicador.
2.  **PERÍODO_MEDIA:**  Período utilizado no momento do cálculo do indicador.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="CCI( )"}

<COMANDO>
					
    Grafico(CCI(50,20));
    
</COMANDO>

```
### ==CCIC( )==

**Descrição:**

A função CCIC retorna o valor do indicador CCIC, de acordo com o período desejado.
Versão alternativa do CCI.

**Parâmetros:**

1.   **PERÍODO:**  Período utilizado no momento do cálculo do indicador.
2.  **PERÍODO_MEDIA:**  Período utilizado no momento do cálculo do indicador.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="CCIC( )"}

<COMANDO>
					
    Grafico(CCIC(50,20));
    
</COMANDO>

```
### ==CCIK( )==

**Descrição:**

A função CCIK retorna o valor do indicador CCIK, de acordo com o período desejado.
Versão alternativa do CCI.

**Parâmetros:**

1.   **PERÍODO:**  Período utilizado no momento do cálculo do indicador.
2.  **PERÍODO_MEDIA:**  Período utilizado no momento do cálculo do indicador.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="CCIK( )"}

<COMANDO>
					
    Grafico(CCIK(10,30));
    
</COMANDO>

```
### ==MantemMaxima( )==

**Descrição:**

Analisa e retornar a maxima de uma expressão.

**Parâmetros:**

1.  **NOVA_VARIAVEL:**  Nome da variavel que será criada
2.  **EXPRESSÃO:**  Expressão a ser analisada.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="MantemMaxima( )"}

<COMANDO>
					
    MantemMaxima(#Maxima_20, Maxima(,50));
    
</COMANDO>
```
### ==MantemMinima( )==

**Descrição:**

Analisa retornar a minima de uma expressão.

**Parâmetros:**

1.  **NOVA_VARIAVEL:**  Nome da variavel que será criada
2.  **EXPRESSÃO:**  Expressão a ser analisada.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="MantemMinima( )"}

<COMANDO>
					
    MantemMinima(#Minima_20, Minima(,50));
    
</COMANDO>

```
### ==Volume( )==

**Descrição:**

Calcula o volume financeiro negociado no período desejado.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="Volume( )"}

<COMANDO>
					
    Anterior(1, Volume());
    
</COMANDO>

```

### ==VolumeHistorico( )==

**Descrição:**

Calcula o volume historico negociado no período desejado.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="Volume( )"}

<COMANDO>
					
    Anterior(1, Volume());
    
</COMANDO>

```

### ==WAD( )==


**Descrição:**

Quando o mercado apresenta uma situação de lateralização (ou congestão) 
é sempre uma tarefa difícil definir se há um processo de distribuição ou 
de acumulação ocorrendo sob a superfície do mercado. Ciente desse desafio, 
o grande trader americano Larry Williams projetou o indicador Acumulação/Distribuição 
Williams (ou WAD, Williams Accumulation/Distribution).

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="Volume( )"}

<COMANDO>
					
    Anterior(1, Volume());
    
</COMANDO>

```





















## **Cruzamento:**
### ==CruzaABaixo( )==

**Descrição:**

É utilizado quando necessita verificar se um PONTO A(**preço**) cruza para **BAIXO** o PONTO B(**preço**)

**Parâmetros:**

1.  **PREÇO 01:**  Preço anterior.
2.  **PREÇO 02:**  Preço atual.


**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="CruzaBaixo( )"}

<COMANDO>

    SE(
        CruzaBaixo(MMA(50), MMA(25));
    ENTAO
        Loga('Cruzamento');
    SENAO
    )

</COMANDO>

```
### ==CruzaACima( )==

**Descrição:**

É utilizado quando necessita verificar se um PONTO A(**preço**) cruza para **CIMA** o PONTO B(**preço**)

**Parâmetros:**

1.  **PREÇO 01:**  Preço anterior.
2.  **PREÇO 02:**  Preço atual.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="CruzaCima( )"}

<COMANDO>

    SE(
        CruzaCima(MMA(50), MMA(25));
    ENTAO
        Loga('Cruzamento');
    SENAO
    )

</COMANDO>

```
### ==Cruza( )==

**Descrição:**

É utilizado quando necessita verificar se um PONTO A(**preço**) cruza o PONTO B(**preço**)

**Parâmetros:**

1.  **PREÇO 01:**  Preço anterior.
2.  **PREÇO 02:**  Preço atual.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="Cruza( )"}

<COMANDO>

    SE(
        Cruza(MMA(50), Fechamento());
    ENTAO
        Loga('Cruzamento');
    SENAO
    )

</COMANDO>

```


### ==CruzaQUALQUER( )==


















## **Adicionar funcoes no gráfico:**

### ==Linha( )==

### ==Grafico( )==

### ==AdicionaMarcador( )==

### ==Desenha( )==

### ==Plota( )==







## **Período de Estudo:**

### ==Agora( )==

**Descrição:**

Utilizado para estudar um período específico. Não é necessário preencher todos os parâmetros, 
se caso não necessite de um parâmeto, utiliza um espaço em branco para ignorar. 

Ele retorna 1 se for verdadeiro e 0 para falso.

**Parâmetros:**

1.  **ANO**  
2.  **MES**  
3.  **DIA**  
4.  **HORA** 
5.  **MINUTO**
6.  **SEGUNDO**

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="Agora( )"}

<COMANDO>

    SE(
        Agora(,,15,,,) 1 =
    ENTAO
        Loga('Dia 15');
    SENAO
    )

</COMANDO>

```
### ==Ano( )==

**Descrição:**

Retorna o **ano atual**.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="Ano( )"}

<COMANDO>

    SE(
        Ano() 2020 =
    ENTAO
        Loga('Ano 2020');
    SENAO
    )

</COMANDO>

```
### ==MES( )==

**Descrição:**

Retorna o **mês atual**.


**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="MES( )"}

<COMANDO>

    SE(
        MES() 2
    ENTAO
        Loga('Fevereiro');
    SENAO
    )

</COMANDO>

```
### ==DIA( )==

**Descrição:**

Retorna o **dia atual**.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="DIA( )"}

<COMANDO>

    SE(
        DIA() 20 =
    ENTAO
        Loga('Dia 20');
    SENAO
    )

</COMANDO>

```
### ==Hora( )==

**Descrição:**

Retorna o **hora atual**.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="Hora( )"}

<COMANDO>

    SE(
        Hora() 9 =
    ENTAO
        Loga('Horario de abertura');
    SENAO
    )

</COMANDO>

```
### ==Minuto( )==

**Descrição:**

Retorna o **minuto atual**.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="Minuto( )"}

<COMANDO>

    SE(
        Minuto() 50 =
    ENTAO
        Loga('Minuto 50');
    SENAO
    )

</COMANDO>

```
### ==Segundo( )==

**Descrição:**

Retorna o **segundo atual**.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="Segundo( )"}

<COMANDO>

    SE(
        Segundo() 10 =
    ENTAO
        Loga('Segundo 10');
    SENAO
    )

</COMANDO>

```
### ==SegundosDia( )==

**Descrição:**

Retorna o **segundo do dia**.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="SegundosDia( )"}

<COMANDO>

    SE(
        SegundosDia() 1520 =
    ENTAO
        Loga('Segundos 1520');
    SENAO
    )

</COMANDO>

```
### ==TempoDesdeAbertura( )==

**Descrição:**

Retornar os segundos desde a abertura.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="TempoDesdeAbertura( )"}

<COMANDO>

    SETA(#TEMPO, TempoDesdeAbertura( ));

</COMANDO>

```
### ==TempoFimCandle( )==

**Descrição:**

Tempo do fechamento candle atual. Retorna no formato **'HHMMSS'**.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="TempoFimCandle( )"}

<COMANDO>

    SETA(#TEMPO_FECHA, TempoFimCandle( ));

</COMANDO>

```
### ==TempoInicioCandle( )==

**Descrição:**

Tempo do abertura candle atual. Retorna no formato **'HHMMSS'**.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="TempoInicioCandle( )"}

<COMANDO>

    SETA(#TEMPO_ABERTURA, TempoInicioCandle( ));

</COMANDO>

```
### ==DiaDesde1970( )==

**Descrição:**

Retorna o dia atual somando os dias desde 1970.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="DiaDesde1970( )"}

<COMANDO>

    SE(
        DiaDesde1970() 10000 =
    ENTAO
        Loga('Dia escolhido');
    SENAO
    )

</COMANDO>

```
### ==DiaSemana( )==

**Descrição:**

Verifica é dia da semana.

|**0:** Se for domingo | 
|**1:** Se for segunda.|
|**2:** Se for terça.  |
|**3:** Se for quarta. |
|**4:** Se for quinta. |
|**5:** Se for sexta.  |
|**6:** Se for sabado. |

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="DiaSemana( )"}

<COMANDO>

    SE(
        E(
            DiaSemana() O >
        ,
            DiaSemana() 6 <
        )
    ENTAO
        Loga('Dia Semana');
    SENAO
    )

</COMANDO>

```
### ==FimDia( )==

**Descrição:**

Retorna 0 se não for o último candle ou retorna 1 se for o último candle do dia.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="FimDia( )"}

<COMANDO>

    SE(
        FimDia() 1 =
    ENTAO
        Loga('Fim Dia');
    SENAO
    )

</COMANDO>
```

### ==DiasMes( )==
**Descrição:**

Retorna quantos dias tem o mês

**Parâmetros:**

1.  **ANO**.
1.  **MES**.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="FimDia( )"}

<COMANDO>

    SE(
        FimDia() 1 =
    ENTAO
        Loga('Fim Dia');
    SENAO
    )

</COMANDO>
```
### ==HoraMinuto( )==

**Descrição:**

Sua função é retornar o valor atual da HHMM.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="HoraMinuto( )"}

<COMANDO>

    SE(
        HoraMinuto() 1025 >
    ENTAO
        Loga('Horario de interesse.');
    SENAO
    )

</COMANDO>

```
### ==DMA( )==

### ==DMAF( )==

### ==DMAHMS( )==

### ==MDA( )==

### ==AMDC( )==

### ==DMASHM( )==

### ==CandleFechaPeriodo( )==

**Descrição:**

Retorna 0 se não for o candle que fecha o periodo ou retorna 1 se for o candle fecha.

**Parâmetros:**

1.  **Periodo_de_interesse:** Posição para a reversão.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="CandleFechaPeriodo( )"}

<COMANDO>

    SE(
        CandleFechaPeriodo(30) 1 =
    ENTAO
        Loga('Fim Dia');
    SENAO
    )

</COMANDO>
```
### ==FimAMD( )==
### ==TempoFimOutro( )==
### ==TempoInicioOutro( )==
### ==SegundoFimDia( )==

**Descrição:**

Retorna o segundo exato do fim do dia.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="SegundoFimDia( )"}

<COMANDO>

    SE(
        SegundoFimDia() 7200 =
    ENTAO
        Loga('ok');
    SENAO
    )

</COMANDO>
```

### ==TradeTempo( )==
### ==TerceiraSegunda( )==

**Descrição:**

Retorna 0 se não for a terceira segunda do mes ou retorna 1 se for a terceira segunda.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="TerceiraSegunda( )"}

<COMANDO>

    SE(
        TerceiraSegunda() 1 =
    ENTAO
        Loga('Terceira Segunda');
    SENAO
    )

</COMANDO>
```
### ==DiasUteisParaTerceiraSegunda( )==

**Descrição:**

Retorna os dias úteis que faltam até a terceira segunda do mês.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="DiasUteisParaTerceiraSegunda( )"}

<COMANDO>

    SE(
        DiasUteisParaTerceiraSegunda() 5 =
    ENTAO
        Loga('Falta 5 dias para a terceira segunda do mês');
    SENAO
    )

</COMANDO>
```

### ==TrocouTempo( )==

**Descrição:**

Retorna Verdadeiro se mudou o candle.

**Parâmetros:**

1.  **Periodo_de_interesse:** Tempo gráfico.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="TrocouTempo( )"}

<COMANDO>

    SE(
        TrocouTempo(D)
    ENTAO
        Loga('Novo dia')
    SENAO
    )

</COMANDO>
```

### ==AtributoTempo( )==

**Descrição:**

Retorna a expressão adicionado no parâmetro no periodo selecionado.

**Parâmetros:**

1.  **Periodo_de_interesse:** Tempo gráfico.
2.  **Expressão** 

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="AtributoTempo( )"}

<COMANDO>

    SE(
        AtributoTempo(D, minima())
    ENTAO
        Loga('Retorna a minima do dia')
    SENAO
    )

</COMANDO>
```



## **Break:**


### ==EncerraAvaliacao( )==

**Descrição:**


**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="EncerraAvaliacao( )"}

<COMANDO>


</COMANDO>
```

### ==EncerraParaCada( )==

**Descrição:**

Utilizado para anular alguma parte da função PARACADA().

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="EncerraAvaliacao( )"}

<COMANDO>

    PARACADA(dados EM #teste FACA
        SETA(#SOMA, 1 1 +)
        EncerraParaCada()
    )

</COMANDO>
```


### ==BreakPoint( )==

**Descrição:**

Ela é responsável por analisar processos, o resultado será colocado no arquivo **BreakPoints.txt** no diretório Debug.

**Parâmetros:**

1.  **EXPRESSÃO -** É a expressão que precisa ser analisada.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="BreakPoint( )"}

<COMANDO>
					
    BreakPoint(
                
        SE(
            MAXIMA() FECHAMENTO() !=
        ENTAO
            SETA(#numero_candle, 1);
            SETA(#numero_dia, #numero_candle);
        SENÃO
            SETA(#numero_candle, 0);

        )		
    );

</COMANDO>

```
### ==BreakPointSE( )==











## **Ponto de Interesse:**
### ==AdicionaPontoInteresse( )==

**Descrição:**

É utilizado para identificar pontos específicos para realizar reversões (Gradiente) ou entrada. Para utilizar 
esse função é necessário colocar dentro do campo **<*ComandoPontoInteresse*>**

**Parâmetros:**

1.  **PONTO_DE_INTERESSE:** Posição de entrada.
2.  **Variavel:** 
3.  **Expressão:**

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="AdicionaPontoInteresse( )"}

<ComandoPontoInteresse>
					
    AdicionaPontoInteresse( )

</ComandoPontoInteresse>

```
### ==AcionaComandoPontoInteresse( )==
**Descrição:**

É utilizado acionar a qualquer momento o Ponto de Interesse.

**Parâmetros:**

1.  **PONTO_DE_INTERESSE:** Posição de entrada.
2.  **Variavel:** 
3.  **Expressão:**

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="AcionaComandoPontoInteresse( )"}

<Comando>
					
    AcionaComandoPontoInteresse()

</Comando>

```
### ==ZeraPontoInteresse( )==

### ==#ResPontoInteresse==
**Descrição:**

Retorna o segundo parâmetro do AdicionaPontoInteresse.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="#RespostaPontoInteresse"}

<Comando>
					
    Seta(#valores_interesse, #RespostaPontoInteresse)

</Comando>

```


## **Adiciona Gráfico:**

### ==AdicionaClassificador( )==

**Descrição:**

Cria um g´rafico de disperssão.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="IdentificadorPosicao( )"}

<COMANDO>
            
    AdicionaClassificador(#retornos)
        
</COMANDO>

```












## **Posições:**

### ==IdentificadorPosicao( )==

**Descrição:**

Retorna o ID da posição.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="IdentificadorPosicao( )"}

<COMANDO>
            
    SE(
        IdentificadorPosicao() 1 =
        
        E(
            FimDia() 1 =
        )
    ENTAO
        ZeraOrdens();
        
    )
        
</COMANDO>

```


### ==SaldoPosicoes( )==

**Descrição:**

Retorna o valor das posições atuais.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="SaldoPosicoes( )"}

<COMANDO>
            
    SE(
        Minima() #snipers2 <
    ENTAO
        ADICIONASTOP(
                ATIVO,
                0$ 100 + CAPITALCORRENTE() *,
                'COMPRA'POSICAOATUAL(),
                POSICAOATUAL(),
                #SNIPERS3/*STOP*/,
                #FECHA_ANTERIOR/*GAIN*/,
                #SNIPERS2/*PRECO*/
        );
        Loga(SaldoPosicoes());
    )

</COMANDO>

```

### ==LucroPosicoes( )==

**Descrição:**

Retorna o lucro das posições em aberto.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="LucroPosicoes( )"}

<COMANDO>
                
        SE(
            Identificador() 1 =
        ENTAO
            Loga(LucroPosicoes());	
        )

</COMANDO>
```

### ==PosicaoInicial( )==

**Descrição:**

Retorna o número do candle que realizou a primeira avaliação.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="PosicaoInicial( )"}

<COMANDO>
                
        SE(
            Identificador() 0 !=
        ENTAO
            Loga(PosicaoInicial(), 'Primeira Avaliação');	
        )

</COMANDO>

```


### ==PosicaoAtual( )==

**Descrição:**

Retorna o número do candle que realizou a avaliação.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="PosicaoAtual( )"}

<COMANDO>
                
    Loga(PosicaoFinal())

</COMANDO>

```
### ==PosicaoFinal( )==

**Descrição:**

Retorna o número do candle que realizou a ultima avaliação.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="PosicaoFinal( )"}

<COMANDO>
                
    Loga(PosicaoFinal())

</COMANDO>

```

### ==QuantidadePosicoes( )==

**Descrição:**

Retorna o total de posições em contratos abertos.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="QuantidadePosicoes( )"}

<COMANDO>

    SE(
        Minima() #snipers2 <
    ENTAO
        ADICIONASTOP(
                ATIVO,
                0$ 100 + CAPITALCORRENTE() *,
                'COMPRA'POSICAOATUAL(),
                POSICAOATUAL(),
                #SNIPERS3/*STOP*/,
                #FECHA_ANTERIOR/*GAIN*/,
                #SNIPERS2/*PRECO*/
        );
    )

    Loga(QuantidadePosicoes());

</COMANDO>

```

### ==ReduzPosicao( )==

**Descrição:**

Reduz as posições em aberto.

**Parâmetros:**

1.  **ATIVO:** "ATIVO"
2.  **QUANTIDADE:** N° de contratos.	
3.  **RÓTULO:** Nome da estrategia.	

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="ReduzPosicoes( )"}

<COMANDO>

    SE(
        Minima() #snipers2 <
    ENTAO
        ReduzPosicoes('ATIVO', 1, 'Despertar')
    )

</COMANDO>
```

### ==ZeraPosicoes( )==

**Descrição:**

Zera as posoções.

**Parâmetros:**

1.  **FILTRO -** É opcional, utilizado para realizar um filtro para zerar operações por tempo.
2.  **RÓTULO -** Nome da estrategia.	
	
**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="ZeraPosicoes( )"}

/* EXEMPLO 01 */

<COMANDO>
            
    SE(
        E(
            Identificador() 1 =
        ,
            FimDia() 1 =	
        )
    ENTAO
        ZeraOrdens(  , '<estrategia>');
    )
        
</COMANDO>

```

```{.py3 hl_lines="" linenums="60" title="ZeraPosicoes( )"}

/* EXEMPLO 02 */

<COMANDO>
            
    SE(
        E(
            Identificador() 1 =
        ,
            FimDia() 1 =	
        )
    ENTAO
        ZeraOrdens();
    )
        
</COMANDO>

```
### ==CardinalidadePosicoes( )==

**Descrição:**

Retorna a quantidade de posições compradas e vendidas.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="CardinalidadePosicoes( )"}

<COMANDO>

    SE(
        Minima() #snipers2 <
    ENTAO
        ADICIONASTOP(
                ATIVO,
                0$ 100 + CAPITALCORRENTE() *,
                'COMPRA'POSICAOATUAL(),
                POSICAOATUAL(),
                #SNIPERS3/*STOP*/,
                #FECHA_ANTERIOR/*GAIN*/,
                #SNIPERS2/*PRECO*/
        );
        
        Loga(CardinalidadePosicoes());
    )

</COMANDO>
```

### ==AdicionaPosicao( )==
### ==RotuloPosicao( )==


### ==EscalaPosicoes( )==

**Descrição:**

Reverter as posições, se tiver vendido, reverte para comprada, o contrario também é verdadeiro.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="EscalaPosicoes( )"}

<COMANDO>

    SE(
        Minima() #snipers2 <
    ENTAO
    EscalaPosicoes()
    )

</COMANDO>
```




## Saldos e Capital

### ==SaldoEquity( )==

**Descrição:**

Informa o valor em dinheiro disponível na conta.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="SaldoEquity( )"}

<COMANDO>
            
    SE(
        FimDia() 1 =
    ENTAO
        Loga(SaldoEquity());
    )

</COMANDO>

```

### ==CapitalInicial( )==

**Descrição:**

Informa o capital inicial da conta

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="CapitalInicial( )"}

<COMANDO>
            
    SE(
        FimDia() 1 =
    ENTAO
        Loga(CapitalInicial());
    )

</COMANDO>

```


### ==CapitalAplicado( )==

**Descrição:**

Informa o capital das posições.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="CapitalAplicado( )"}

<COMANDO>
            
    SE(
        FimDia() 1 =
    ENTAO
        Loga(CapitalAplicado());
    )

</COMANDO>

```


### ==CapitalCorrente( )==

**Descrição:**

Informa o capital atual + dinheiros das posições.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="CapitalCorrente( )"}

<COMANDO>
            
    SE(
        FimDia() 1 =
    ENTAO
        Loga(CapitalCorrente());
    )

</COMANDO>

```
### ==Lucro( )==

**Descrição:**

Retorna o valor do lucro total.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="Lucro( )"}
         
<COMANDO>
            
    SE(
        FimDia() 1 ==
    ENTAO
        Loga(Lucro(), TempoFimCandle());
    )

</COMANDO>
```
### ==UltExecutado( )==

**Descrição:**

Utilizado para retornar o último preço executado.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="UltExecutado( )"}

<COMANDO>
                


</COMANDO>
```



### ==Trade( )==
### ==IdadeTrade( )==














## **Ordens:**

### ==AdicionaOrdem( )==

### ==AdicionaStop( )==
### ==AjustaStop( )==
### ==ZeraOrdens( )==
### ==ZeraDinheiro( )==










## **Opções:**
### ==DadoOpcao( )==

**Descrição:**

Sua função é retornar todas as informações relevantes de uma opção específica.

**Parâmetros:**

1.  **NOME_DA_OPCAO -** Nome do ativo da opção.
2.  **Tipo de informação -** 

|A - Abertura                            |
|M - Maxima                              |
|I - Minima                              |
|F - Fechamento                          |
|S - Strike                              |
|DU - Dia úteis até o exercicio das opções|

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="DadoOpcao( )"}

<ComandoPontoInteresse>
					
    DadoOpcao(#OPCAO, S)

</ComandoPontoInteresse>
```


### ==DiaExercicioOpcoes( )==

**Descrição:**

Verifica 0 se for dia normal ou retorna 1 se for dia de opções.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="DiaExercicioOpcoes( )"}

<COMANDO>

    SE(
        DiaExercicioOpcoes() 1 =
    ENTAO
        Loga('Dia de opções');
    SENAO
    )

</COMANDO>

```
### ==DiasParaExercicio( )==

**Descrição:**

Verifica 0 se for dia opções ou retorna quantos dias faltam para as opções.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="DiasParaExercicio( )"}

<COMANDO>

    SETA(#DIAS_PARA_OPCOES, DiasParaExercicio());

</COMANDO>

```

### ==OpcoesDisponiveisAte( )==

### ==OpcoesVencemAte( )==

### ==StrikeCALL( )==

### ==PrecoOpcao( )==
### ==ValorIntrinseco( )==
### ==ValorExtrinseco( )==
### ==OpcoesDisponiveis( )==
### ==PUT( )==
### ==CALL( )==

### ==TravaEmPcall( )==
### ==TravaCall( )==
### ==DiasParaExpiracao( )==
### ==CallTEORICA( )==
### ==StrikeBASE( )==
### ==CallMaxMOVE( )==
### ==ListaOpcoes( )==




## **Estrutura de operação:**

### ==AdicionaEstrutura( )==
### ==ZeraEstrutura( )==
### ==ZeraEstruturaVencimento( )==
### ==ValorEstrutura( )==

### ==CardinalidadeEstrutura( )==

### ==AplicaEstrutura( )==
### ==AcompanhaEstrutura( )==

### ==QuantidadeEstrutura( )==





















## **Teste de Consistência:**

O **teste de consistência** tem como principal objetivo criar condições para testar todas as funções com o objetivo de ter um controle se todas as funções estão ou não funcionando perfeitamente, mesmo se ocorra atualizações no sistema. Vamos apresentar todas as tags que compõem a estrutura de testes. O arquivo que será utilizado esta no diretório Debug na pasta *TESTE DE CONSISTENCIA* e o nome do arquivo é ***testes.estr***.

### <*Nome*> <*Inicio*> <*Fim*>

**Nome** é utilizada para definir o nome do testes que está sendo feito, é muito importante para conseguir definir a função, a tag **Inicio** e **Fim** é utilizada para determinar o periodo a ser testado.

```{.py3 hl_lines="" linenums="4" title=""}
    
    <NOME> Loga-Teste </NOME>
	<INICIO> 20180101 </INICIO>
	<FIM> 20221231 </FIM>
	<ATIVO> WDOFUT </ATIVO>
                  
```
### <*Ativo*>

A tag **Ativo** é o nome do ativo que vai acontecer todos os testes.

```{.py3 hl_lines="" linenums="8" title=""}

	<ATIVO> WDOFUT </ATIVO>
                  
```

### <*Nome*>

**Número** é para determinar o número do teste, a tag **Nome** nesta parte da estrutura é utilizado para identificar qual validação que está sendo feita.

```{.py3 hl_lines="" linenums="12" title=""}

    <NUMERO> 0 </NUMERO>
    <NOME> E.Validacao 1 </NOME>
                  
```
### <*SemprePosicionado*>

O **SemprePosicionado** tem a função de não deixar que ocorra dentro do teste operações longas para que seja analisado candle a candle, por isso que mantemos sempre *N*. 

```{.py3 hl_lines="" linenums="12" title=""}

    <SemprePosicionado> N </SemprePosicionado>
         
```
### <*Capital*>

**Capital** quese refere ao capital disponível para a execusão dos testes.

```{.py3 hl_lines="" linenums="12" title=""}

    <CAPITAL> 100000000 </CAPITAL>
                  
```
### <*Custos*>

Essa tag é responsável por informar os custos/taxas das operações, são alterados conforme o tipo de ativo que está sendo estudado.

```{.py3 hl_lines="" linenums="16" title=""}

<CUSTOS>

    <SLIPPAGE> 0.0001% </SLIPPAGE>
    <CORRETAGEM> 1 </CORRETAGEM>
    <EMOLUMENTOS> 0.0000019% </EMOLUMENTOS>
    <REGISTRO> 0.0000008% </REGISTRO>

</CUSTOS>
                  
```
### <*Periodo*>

Referente ao período gráfico que será utilizado para fazer os testes.

```{.py3 hl_lines="" linenums="16" title=""}

<CUSTOS>

    <PERIODO> D </PERIODO> /*D = Periodo diario*/

</CUSTOS>
```
### <*ComandoInicio*>

Utilizado para determinar variáveis fixas, mesma ideia do **ComandoInicio** nas execuções de estratégias, por padrão sempre mantemos essas variáveis fixas porque serão utilizadas no decorrer dos testes.

```{.py3 hl_lines="" linenums="29" title=""}

<COMANDOINICIO>

    SETA(#cE, 0);
    SETA(#cA, 0);
    SETA(#cCandles, 0);
    SETA(#cAvaliacoes, 0);

</COMANDOINICIO>
                  
```
### <*Drawdown*>

Esse conjunto de tags refere-se a proteção do capital principal, onde é determinado uma    
*Perda maxima, Drawdown maximo, os Gatilhos de Drawdown e Ganho maximo*. 

```{.py3 hl_lines="" linenums="45" title=""}

<PERDAMAXIMA> 2 </PERDAMAXIMA>
<DRAWDOWNMAXIMOTRADE> 30 </DRAWDOWNMAXIMOTRADE>
<GATILHODRAWDOWN> 15 </GATILHODRAWDOWN>
<GANHOMAXIMO> 5 </GANHOMAXIMO>
      
```
### <*Comando*>

É nesta tag onde faremos os **testes de consistências** utilizando as **condicionais** que aprendemos anteriormente.

```{.py3 hl_lines="" linenums="94" title=""}

<COMANDO>

    SE(
        E(
            1 0 >
        ,
            0 1 <
        ,
            10 10 10 + + 30 =
        ,
            10 10 + 20 =
        ENTAO
            E(
                10 0 >
            ,
                20 30 <
            ,
                10 10 * 100 =
            ,
                10 10 / 1 =			
            )	
        )
    ENTAO 
        SETA(#cA, #cA 1 +)
    SENAO
        SETA(#cE, #cE 1 +); 
    );

    SETA(#cCandles, #cCandles 1 +);
    SETA(#cAvaliacoes, #cAvaliacoes 1 +);		
    
</COMANDO>

```
### <*ComandoFim*>

Será apenas executado no final da estratégia, utilizado normalmente para obter alguma informação específica no final do teste.

```{.py3 hl_lines="" linenums="130" title=""}

<COMANDOFIM>

    SE(
        E(
            #cE 0 =
        , 
            #cA 0 >
        )
    ENTAO
    
        AceitaTeste();
        ComentaTeste('Zero erros Operacoes. ');
        ComentaTeste('Total Acertos: ' #cA '. ');
        ComentaTeste('Total Candles: ' #cCandles '. ');
        ComentaTeste('Total Avaliacoes: ' #cAvaliacoes '. ');
        
    SENAO
        ComentaTeste('Total Erros: ' #cE '. ');
        ComentaTeste('Total Acertos: ' #cA '. ');
        ComentaTeste('Total Candles: ' #cCandles '. ');
        ComentaTeste('Total Avaliacoes: ' #cAvaliacoes '. ');				
        
    );
		
</COMANDOFIM>

```
## **Funções especiais:**
### ==AceitaTeste( )==

**Descrição:**

É apenas utilizada para o teste de consistência. Essa função retorna 
válido se um teste for verdadeiro e inválido se caso um teste não funcionar.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="AceitaTeste( )"}

<ComandoFim>
    SE(
        E(
            #cE 0 =
        , 
            #cA 0 >
        )
    ENTAO

        AceitaTeste();
        ComentaTeste('Zero erros Operacoes. ');
        ComentaTeste('Total Acertos: ' #cA '. ');
        ComentaTeste('Total Candles: ' #cCandles '. ');
        ComentaTeste('Total Avaliacoes: ' #cAvaliacoes '. ');

    SENAO
        ComentaTeste('Total Erros: ' #cE '. ');
        ComentaTeste('Total Acertos: ' #cA '. ');
        ComentaTeste('Total Candles: ' #cCandles '. ');
        ComentaTeste('Total Avaliacoes: ' #cAvaliacoes '. ');				

    );

</COMANDOFIM>

```
### ==ComentaTeste( )==

**Descrição:**

É apenas utilizada para o teste de consistência para comentar sobre os testes conforme eles forem sendo testados. Ele possui até 10 parâmeros, mas por padrão utilizamos como no exemplo abaixo.

**Parâmetros:**

1.  **VALORES, STRINGS, EXPREÇÕES OU VARIAVEIS**
2.  **VALORES, STRINGS, EXPREÇÕES OU VARIAVEIS**
3.  **VALORES, STRINGS, EXPREÇÕES OU VARIAVEIS**
4.  **VALORES, STRINGS, EXPREÇÕES OU VARIAVEIS**
5.  **VALORES, STRINGS, EXPREÇÕES OU VARIAVEIS**
6.  **VALORES, STRINGS, EXPREÇÕES OU VARIAVEIS**
7.  **VALORES, STRINGS, EXPREÇÕES OU VARIAVEIS**
8.  **VALORES, STRINGS, EXPREÇÕES OU VARIAVEIS**
9.  **VALORES, STRINGS, EXPREÇÕES OU VARIAVEIS**
10. **VALORES, STRINGS, EXPREÇÕES OU VARIAVEIS**

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="ComentaTeste( )"}

    .  .  .

    ENTAO

        AceitaTeste();
        ComentaTeste('Zero erros Operacoes. ');
        ComentaTeste('Total Acertos: ' #cA '. ');
        ComentaTeste('Total Candles: ' #cCandles '. ');
        ComentaTeste('Total Avaliacoes: ' #cAvaliacoes '. ');

    SENAO
        ComentaTeste('Total Erros: ' #cE '. ');
        ComentaTeste('Total Acertos: ' #cA '. ');
        ComentaTeste('Total Candles: ' #cCandles '. ');
        ComentaTeste('Total Avaliacoes: ' #cAvaliacoes '. ');				

    );
		
```
### ==Avalia( )==

**Descrição:**

Avalia uma string como se fosse um comando.

**Parâmetros:**

1.  **Variavel tipo string**

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="AceitaTAvaliaeste( )"}

<ComandoFim>

    Seta(#entrada,  'AtributoTempo(D, minima())')

</COMANDOFIM>

```



### ==Variavel( )==
### ==Condicao( )==

### ==CondicaoSS( )==

### ==SetaAtributo( )==
### ==ZeraVariavel( )==
### ==TamanhoTexto( )==

**Descrição:**

Retorna o tamanho do texto.

**Parâmetros:**

1.  **STRINGS**

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="TamanhoTexto( )"}

    .  .  .

    SE(
        TamanhoTexto('DADOS') 5 =
    ENTAO
        LOGA('ACEITO')
    SENAO
        LOGA('FALSO')
    )

    .  .  . 
		
```

### ==VariavelTexto( )==













## **Arquivos**
### ==CampoArquivo( )==

**Descrição:**

Ela procura uma parâmetro na primeira coluna e retorna um valor da linha encontrada

**Parâmetros:**

1. **NOME_QRQUIVO:** É o nome do arquivo a ser analisado. 
2. **PROCURA:** Valor a ser procurado na primeira coluna.
3. **COLUNA:** A coluna que retornará na linha encontrada. 

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="CampoArquivo( )"}

<COMANDOINICIO>

    SETA(#DADOS, CampoArquivo('Estatistica.txt', Hora(), 3);
    
</COMANDOINICIO>
```
### ==CamposArquivo( )==

**Descrição:**

Ela procura uma parâmetro na primeira coluna e retorna valores da linha encontrada.

**Parâmetros:**

1.  **NOME_DO_ARQUIVO:** informar o nome do arquivo
2.  **PROCURA:**  Dado que precisa procurar.
3.  **NOME_VARIAVEL:** Informar a variavel
4.  **X:** Informa o primeiro campo.
5.  **Y:** Informa até que campo será buscado.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="CamposArquivo( )"}

<COMANDOINICIO>

    SETA(#DADOS, CamposArquivo('Estatistica.txt', Hora(), #TESTE, 0, 4);

</COMANDOINICIO>

```
### ==OADATE( )==

**Descrição:**

Utilizado para formatar o horário para o excel.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="CampoArquivo( )"}

<COMANDOINICIO>

    
</COMANDOINICIO>

```


















## **Teste racional**
### ==Mapacores( )==

**Descrição:**

**Parâmetros:**

1.  **NOME_DO_TESTE:** informar o nome do teste.
2.  **NOME_FUNCAO:**  Informar o nome da variavel destino da soma da matriz
3.  **cor 01:** Primeira tonalidade de cor
4.  **cor 02:** Segunda tonalidade de cor.

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="Mapacores( )"}

<COMANDOINICIO>

    SETA(#max_dia, maxima());
    SETA(#min_dia,  minima());
    SETA(#numero_dia, 0);
    SETA(#saltos_interesse, 10);
    
</COMANDOINICIO>
    . . .

<COMANDO>

    Seta(#numero_candle, #numero_candle 1 +);

    SE(
        dia() #dia_ant !=
    ENTAO
        Seta(#numero_candle, 1);
        
        SE(
            #numero_dia 0 >
        ENTAO
            SomaMatriz(#grade_movimentos_p, #movimentosP);
            SomaMatriz(#grade_movimentos_n, #movimentosN);
            SomaMatriz(#grade_excessos_p, #excessosP);
            SomaMatriz(#grade_excessos_n, #excessosN);
        )
        
        ZeraMatriz(#atingiuSNP);
        ZeraMatriz(#atingiuSNN);
        ZeraMatriz(#movimentosP);
        ZeraMatriz(#movimentosN);
        ZeraMatriz(#excessosP);
        ZeraMatriz(#excessosN);

        Seta(#atingiuSNP(0), 1);
        Seta(#atingiuSNN(0), 1);
        
        Seta(#numero_dia, #numero_dia 1 +);
        Seta(#dia_ant, dia());
        Seta(#max_dia_ant, #max_dia);
        Seta(#min_dia_ant, #min_dia);
        Seta(#fecha_anterior, #fecha);
        Seta(#primeiro,1);
        Seta(#max_dia, maxima());
        Seta(#min_dia, minima());
        
        Seta(#executou_dia, 0);
        Seta(#avaliou_metodo1, 0);
        Seta(#impede_metodo1, 0);
        
        PARACADA(laranja EM Range(1, #saltos_interesse) FACA
            Seta(#SNIPERP(laranja), #fecha 13 laranja * +);
            Seta(#SNIPERN(laranja), #fecha 13 laranja * -);
        )
    SENAO
    )

    Seta(#max_dia, maxm(#max_dia, maxima()));
    Seta(#min_dia, minm(#min_dia, minima()));
    Seta(#fecha, Fechamento());
    
    Linha(#FECHA_ANTERIOR, BRANCO);

    PARACADA(i EM Range(1, #saltos_interesse) FACA
        SE(maxima() #SNIPERP(i) >= ENTAO Seta(#atingiuSNP(i), 1))
        SE(minima() #SNIPERN(i) <= ENTAO Seta(#atingiuSNN(i), 1))
    )

    Linha(#SNIPERP(1), VERMELHO);
    Linha(#SNIPERP(2), VERMELHO);
    Linha(#SNIPERP(3), VERMELHO);
    Linha(#SNIPERP(4), VERMELHO);
    Linha(#SNIPERP(5), VERMELHO);
    Linha(#SNIPERP(6), VERMELHO);
    Linha(#SNIPERP(9), VERMELHO);
    Linha(#SNIPERN(1), VERDE);
    Linha(#SNIPERN(2), VERDE);
    Linha(#SNIPERN(3), VERDE);
    Linha(#SNIPERN(4), VERDE);
    Linha(#SNIPERN(5), VERDE);
    Linha(#SNIPERN(6), VERDE);
    Linha(#SNIPERN(9), VERDE);
    
    PARACADA(i EM Range(1, #saltos_interesse) FACA
        PARACADA(j EM Range(1, #saltos_interesse) FACA

            SE(
                E(
                    minima() #SNIPERP(j) <= 
                , 
                    #atingiuSNP(i), #atingiuSNP(j)
                )
            ENTAO 
                Seta(#movimentosP(i, j), 1);
            )

            SE(
                E(
                    maxima() #SNIPERN(j) >= 
                , 
                    #atingiuSNN(i)
                , 
                    #atingiuSNN(j)
                )
            ENTAO 
                Seta(#movimentosN(i, j), 1);
            )

            SE(
                E(
                    minima() #SNIPERN(j) <= 
                , 
                    #atingiuSNP(i)
                )
            ENTAO 
                Seta(#excessosP(i, j), 1);
            )

            SE(
                E(
                    maxima() #SNIPERP(j) >= 
                , 
                    #atingiuSNN(i)
                )
            ENTAO 
                Seta(#excessosN(i, j), 1);
            )

        )
    )
</COMANDO>

    . . .

<COMANDOFIM>

    MapaCores('Movimentos P', #grade_movimentos_p, azul, vermelho);
    
</COMANDOFIM>
```

## **Funções externas**
### ==ChamaExterno( )==

**Descrição:**

**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="ChamaExterno( )"}

<COMANDOFIM>


</COMANDOFIM>
```
### ==ChamaExternoPython( )==

**Descrição:**

Sua função é para utilizar funções de códigos em formato de python.

**Parâmetros:**

1.  **NOME_DO_ARQUIVO:** informar o nome do arquivo em python.
2.  **NOME_FUNCAO:**  O nome da função em python.


**Sintaxe:**

```{.py3 hl_lines="" linenums="60" title="ChamaExternoPython( )"}

<COMANDOFIM>

    ChamaExternoPython('calculador.py', 'funcao_calculo', #X, #Y, #Z);
    
</COMANDOFIM>
```

## **Atalhos no teclado**

### == [ ==

Avança um dia.
### == ] ==

Volta um dia.
### == Shift + [ ==

Avança um mes.

### == Shift + ] ==

Volta um mes.