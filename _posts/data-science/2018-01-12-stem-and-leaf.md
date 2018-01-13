---
layout:      post
title:       'The "Stem-and-Leaf" is dead. Long live the "Stem-and-Leaf"!'
date:        2018-01-12 00:00:00 -0300
image:       '/images/misc/case_1_1_1_05.png'
description: 'Descrição do package stemgraphic para Python'
keywords:    'data science, eda, exploratory data analysis, stem-and-leaf, tukey'
categories:  data-science
---

O gráfico de *__Stem-and-Leaf__* (Ramo-e-Folha) é uma das ferramentas mais poderosas
para a visualização
da distribuição de uma variável numérica, sendo uma alternativa superior aos histogramas
em muitos casos 
(o Stem-and-Leaf apresenta de forma condensada muito mais informação do que um histograma),
mas que está praticamente "esquecido" e "ausente" do arsenal de técnicas do analista de
dados, estatístico ou cientista de dados hoje em dia.

Este texto tem 3 objetivos:

* Relembrar a utilidade desse tipo de gráfico;
* Lamentar a falta de um package para o [R](https://www.r-project.org/) que produza bons Stem-and-Leafs; e
* Divulgar o package [*stemgraphic*](http://stemgraphic.org/) para Python.

## O que é o Stem-and-Leaf?

Esse gráfico foi criado por [John Tukey](https://en.wikipedia.org/wiki/John_Tukey) em
1970 e descrito em uma edição preliminar limitada de seu livro *Exploratory Data Analysis*. Em
1977, com a publicação oficial da 1ª edição do *Exploratory Data Analysis*, o gráfico
Stem-and-Leaf passou a ser muito utilizado, obtendo situação proeminente na literatura
sobre análise exploratória de dados. Tukey considerava esse gráfico tão importante que
foi a ilustração da capa de seu livro:

<center>
<figure>
<img alt="John Tukey's Exploratory Data Analysis" src="/images/data-science/eda_tukey.jpg" style="width:30%;">
<figcaption>Exploratory Data Analysis, de <i>John Tukey</i></figcaption>
</figure>
</center>

Mas afinal de contas, o que é um Stem-and-Leaf? Nas próprias palavras de Tukey:
> *A generalized two-digit display, in which the lefthand portion of the values displayed
> is given by a stem value, while the righthand portion makes up a leaf (leaves follow
> stem juxtaposed if uniformally one digit, follow separated by commas if some or all
> involve 2 or more digits.*

Considere como exemplo o seguinte conjunto de  dados de uma variável numérica qualquer:
{12.9, 16.3, 16.6, 16.8,
16.9, 16.9, 17.5, 17.6, 17.6, 18.0, 18.4, 18.4, 18.5, 18.8, 18.8, 19.4, 19.9, 20.0, 20.3,
21.2}. O tipo mais simples de Stem-and-Leaf envolve dar uma parte da informação uma única
vez, no início de cada linha para formar o ramo, e dar o resto da informação de cada linha
juntando os números o mais próximo possível desde que faça sentido, formando as folhas. Para
o conjunto de dados acima, escolhemos as dezenas como os ramos e os decimais como as folhas,
e separamos o início de cada ramo de suas folhas com uma barra vertical (note que também indicamos a
escala de unidades --- assim, no Stem-and-Leaf abaixo, 12|9 = 12.9):

<center>
<figure>
<img alt="Stem-and-Leaf simples" src="/images/data-science/stem-and-leaf-01.png">
<figcaption>Stem-and-Leaf Simples</figcaption>
</figure>
</center>

Agora compare o Stem-and-Leaf acima com o histograma dos mesmos dados (gerado pelo
[Pandas](https://pandas.pydata.org/), no Python), abaixo:

<center>
<figure>
<img alt="Histograma simples" src="/images/data-science/histograma-01.png">
<figcaption>Histograma Simples</figcaption>
</figure>
</center>

Note as seguintes vantagens do Stem-and-Leaf:

* Você tem uma idéia bem mais clara a respeito da distribuição dos dados
* Você sabe exatamente qual o valor mínio e máximo dos dados (no histograma
não é possível nem saber se o valor mais baixo é 10, 12, 8 ou 2)
* O histograma gerado pelo Pandas é visualmente errado! O histograma parece indicar que
a maior parte dos dados está próxima de 16, quando na verdade está próxima de 18.
* Você consegue ver todos os dados, todos os valores presentes no dataset
* Ele mostra o "global" (a forma da distribuição), e o "detalhe" (os dados)

Além do Steam-and-Leaf simples, existem inúmeras variações possíveis, dependendo
da escolha da unidade e do número de linhas por ramo. Por exemplo, Tukey também
definiu as seguintes variações:
* *__Stretched Stem-and-Leaf__*: tem 2 linhas por ramo, identificadas por
um "*" (para os números 0 a 4) e um "." (para os números 5 a 9):

<center>
<figure>
<img alt="Stretched Stem-and-Leaf" src="/images/data-science/stem-and-leaf-02.png" style="float: center">
<figcaption>Stretched Stem-and-Leaf</figcaption>
</figure>
</center>

* *__Squeezed Stem-and-Leaf__*: tem 5 linhas por ramo, identificadas
por "*" (para 0 e 1), "t" (para 2 e 3), "f" (para 4 e 5), "s" (para 6 e 7) e "." (para 8 e 9):

<center>
<figure>
<img alt="Squeezed Stem-and-Leaf" src="/images/data-science/stem-and-leaf-03.png" style="float: center">
<figcaption>Squeezed Stem-and-Leaf</figcaption>
</figure>
</center>

Existem outras variações do Stem-and-Leaf? Sim, inúmeras.
No livro *Understanding Robust and Exploratory Data Analysis*, de  David Hoaglin,
Frederick Mosteller e John Tukey, publicado em 1983, muitas variações e técnicas diferentes para
a construção desse tipo de gráfico são descritas. O uso do Stem-and-Leaf na análise de resíduos
de regressão é enfatizado. Tukey, ainda em seu livro de 1977, foi além e construiu um Stem-and-Leaf
de dados categóricos!

Mas, se o Stem-and-Leaf é tão útil assim, por que ele praticamente desapareceu das análises
de dados hoje em dia?

## O declínio do uso do Stem-and-Leaf

Eu não saberia apontar exatamente a causa ou as causas que levaram ao declínio do uso
do Stem-and-Leaf, mas posso pensar em algumas situações que podem ter contribuído:

* Eu só vi descrições detalhadas do Stem-and-Leaf em livros mais antigos. Não me
recordo de ter visto nenhum dos livros de referência em estatística mais novos
a descrição de como fazer e da importância desse gráfico;
* Apesar de serem fáceis de desenhar à mão (se o dataset não for muito grande,
até cerca de 300 observações), nenhum dos pacotes estatísticos mais acessíveis ou
open-source
era capaz de criar Stem-and-Leafs de qualidade. Softwares comerciais mais caros,
como o [Stata](https://www.stata.com),
pelo menos a partir da versão 7, já apresentava essa funcionalidade (mesmo que
com algumas restrições);
* A facilidade para a criação de gráficos mais rebuscados, bonitos e coloridos
pode ter ofuscado a importância e a utilidade do Stem-and-Leaf; e
* Datasets grandes, com milhares ou milhões de observações, são difíceis de plotar
em um Stem-and-Leaf, necessitando de técnicas especiais como amostragem e/ou
arredondamento e/ou scaling dos dados. Nenhum
dos softwares de análise de dados que eu mais utilizo (R, Stata, Matlab, Python)
consegue fazer isso de modo a plotar um Stem-and-Leaf adequado.

Bem, seja qual for a causa, o fato é que os Stem-and-Leafs praticamente
sumiram das análises exploratórios de dados divulgadas hoje em dia.

## Como eu conheci os Stem-and-Leafs?

Eu conheci o Stem-and-Leaf no começo do 2000, no início do meu mestrado em Epidemiologia
no [Departamento de Epidemiologia e Métodos Quantitativos em Saúde (DEMQS)](http://www.ensp.fiocruz.br/portal-ensp/departamento/demqs), da
[Escola Nacional de Saúde Pública (ENSP)](http://www.ensp.fiocruz.br/portal-ensp/index.php), na [Fundação Oswaldo Cruz (Fiocruz)](http://www.fiocruz.br).

À época, lembro-me bem, 4 professores de diferentes disciplinas citaram ou
indicaram o livro de Tukey (apesar de nenhum deles utilizar esse livro em suas
respectivas disciplinas):

* A prof. [Ana Glória Godoi Vasconcelos](http://lattes.cnpq.br/1761068553289892), que minsitrava
as disciplinas de Estatística 1, Estatística 2 e Modelagem Multivariada;
* O prof. [Paulo César Rosito Barata](http://lattes.cnpq.br/0295907870991956), que ministrava
as disciplinas de Matemática 1, Matemática 2, Matemática Aplicada e Modelos Matemáticos em Epidemiologia;
* O prof. [Evandro da Silva Freire Coutinho](http://lattes.cnpq.br/1020078557729775), que ministrava
as disciplinas de Estudos Caso-Controle, e de Desenho, análise e metanálise de Ensaios
Clínicos Controlados; e
* O prof. [Joaguim Gonçalves Valente](http://lattes.cnpq.br/7721412364031029), que ministrava
a disciplina de Epi Info.

Com 4 professores citando Tukey, resolvi visitar a biblioteca e descobri o *Exploratory Data Analysis* de
Tukey, e o *Understanding Robust and Exploratory Data Analysis*, de Hoaglin,
Mosteller e Tukey. Depois de uma leitura das páginas iniciais,
fiquei impressionado com as técnicas abordadas nesses livros.

Acabei comprando os dois livros. E o assunto do capítulo 1 de ambos os livros era
a criação e importância de gráficos Stem-and-Leaf. A partir daí comecei a usar
esse gráfico em minhas análises iniciais e exploratórias.

Na época, ano 2000, eu utilizava o [Stata 7](https://www.stata.com) que produzia
Stem-and-Leafs razoáveis, por exemplo:

<center>
<figure>
<img alt="Stem-and-Leaf no Stata 7" src="/images/data-science/stem-and-leaf-04.png" style="float: center">
<figcaption>Stem-and-Leaf no Stata 7</figcaption>
</figure>
</center>

Mas surgiu um problema: eu estava começando a migrar do Stata para o R...

## O R não faz bons Stem-and-Leafs

Quando eu comecei com o R, logo descobri que a função para plotar os Stem-and-Leafs só
funcionava em alguns casos, quando o número de observações era pequeno.

Com o conhecido dataset `mtcars` (Motor Trend Car Road Tests), o R gera este
Stem-and-Leaf:

<center>
<figure>
<img alt="Stem-and-Leaf no R" src="/images/data-science/stem-and-leaf-05.png" style="float: center">
<figcaption>Stem-and-Leaf no R (32 observações)</figcaption>
</figure>
</center>

Mas quando o dataset aumenta de tamanho, o Stem-and-Lear do R é inútil. Utilizando
os dados recentemente disponibilizados pela [Porto Seguro](http://www.portoseguro.com.br/)
no site da [Kaggle](https://www.kaggle.com) para o desafio [Porto Seguro’s Safe Driver Prediction](https://www.kaggle.com/c/porto-seguro-safe-driver-prediction) (dataset "train.csv", com quase 600.000 observações), o R produz este Stem-and-Leaf:

<center>
<figure>
<img alt="Stem-and-Leaf no R" src="/images/data-science/stem-and-leaf-06.png" style="float: center">
<figcaption>Stem-and-Leaf no R (595.212 observações)</figcaption>
</figure>
</center>

Como a função `stem()` no R não consegue tratar datasets grandes com técnicas mais adequadas (sampling,
scalling e outras) para gerar um Stem-and-Leaf melhor, o R se limita plotar as primeiras folhas de
cada ramo e depois contar quantas observações ainda existem nesse ramo. O resultado é um gráfico
que não serve para nada.

O R não está sozinho nessa: os Stem-and-Leafs gerados pelo Stata 7 (que é a versão que eu tenho) também só são úteis para datasets
pequenos (não sei se a versão mais nova Stata 15, já consegue tratar grandes datasets). Com o pequeno
dataset `mtcars` o Stata produz o seguinte gráfico:

<center>
<figure>
<img alt="Stem-and-Leaf no Stata 7" src="/images/data-science/stem-and-leaf-07.png" style="float: center">
<figcaption>Stem-and-Leaf no Stata (32 observações)</figcaption>
</figure>
</center>

Nas com o dataset de quase 600.000 observações da Porto Seguro, o Stata produz um gráfico
absolutamente inútil (não é possível nem mostrar o gráfico todo, pois tem dezenas de ramos):

<center>
<figure>
<img alt="Stem-and-Leaf no Stata 7" src="/images/data-science/stem-and-leaf-08.png" style="float: center">
<figcaption>Stem-and-Leaf no Stata (595.212 observações)</figcaption>
</figure>
</center>

Em resumo, em época de Big Data e grandes datasets, o Stem-and-Leaf parece fadado
a desaparecer. Mas... eis que surge o **stemgraphic** para Python!

## *__stemgraphic__*: uma nova chance para os Stem-and-Leafs

Procurando por novas formas de plotar um Stem-and-Leaf no R, eu acabei descobrindo
meio que por acaso o package [`stemgraphic`](http://stemgraphic.org/) para Python.
Esse é um package relativamente novo, um pouco desconhecido, mas com boas revisões
por quem já o utilizou. Resolvi experimentar.

Para a variável "mpg" do dataset `mtcars`, com 32 observações, ele gerou o segunte gráfico:

<center>
<figure>
<img alt="Stem-and-Leaf no Python com o stemgraphic" src="/images/data-science/stem-and-leaf-09.png" style="float: center">
<figcaption>Stem-and-Leaf no Python com o stemgraphic (32 observações)</figcaption>
</figure>
</center>

No Stemp-and-Leaf gerado pelo *stemgraphic* no gráfico acima (com 2 linhas por ramo, ou seja, um **Stretched Stem-and-Leaf**),
as informações são as seguintes:

* A primeira coluna de números é a soma cumulativa das observações dos ramos. O número 4 indica que a soma
cumulativa de observações, é 4. O número 17 indica que a soma cumulativa de observações nos dois primeiros
ramos é 17 (4 do primeiro ramo e 13 do segundo ramo). E assim por diante.
* A segunda coluna de números é o ramo propriamente dito.
* A terceira coluna são as folhas de cada ramo, em uma coloração clara. Note, pela legenda, que eles
estão arredondados.
* Note que um dos números "9" está com uma cor diferente, púrpura, e um sublinhado. Esse número
indica a posição da mediana!
* Os números em quadros vermelhos indicam o menor o maior valor (possíveis outliers).
* A legenda ("Key"), informa que a primeira coluna é a soma agredada das observações ("aggr"),
a segunda coluna é o ramo ("stem") e a terceira coluna são os leafs ("leaf"). Note também que a
legenda informa claramente que "3|0 = 3.0 x 10.0 = 30.0", ou seja, o valor de mpg está arredondado
para a dezena.

E para o dataset da Porto Seguro, com quase 600.000 observações? O package stemgraphic gera o seguinte gráfico:

<center>
<figure>
<img alt="Stem-and-Leaf no Python com o stemgraphic" src="/images/data-science/stem-and-leaf-10.png" width="100%" style="float: center">
<figcaption>Stem-and-Leaf no Python com o stemgraphic (595.212 observações)</figcaption>
</figure>
</center>

Note que no gráfico acima:

* Nós podemos ter uma idéia clara da distribuição dos valores;
* Foi usada uma amostra representativa de 900 observações (o procedimento de amostragem
está detalhado na documentação do stemgraphic);
* O menor valor é 0 e o maior valor é 3,7416573868;
* Além da amostragem os dados estão arrendondados (a legenda indica isso); e
* A mediana (na cor púrpura) está ao redor de 3,32.

É um gráfico perfeito? Não... mas é muito melhor do que o Stem-and-Leaf gerado pelo R ou pelo Stata.
E o procedimento de amostragem que o stemgraphic utiliza funciona muito bem até para dataset muito
maiores. Por exemplo, o [dataset com os dados de todos os vôos comerciais nos EUA](http://stat-computing.org/dataexpo/2009/)
em 2008 contém quase 7 milhões de observações. O stemgraphic gerou o Stem-and-Leaf da variável "AirTime"
(tempo de vôo, em minutos) abaixo:

<center>
<figure>
<img alt="Stem-and-Leaf no Python com o stemgraphic" src="/images/data-science/stem-and-leaf-11.png" width="100%" style="float: center">
<figcaption>Stem-and-Leaf no Python com o stemgraphic (6.855.029 observações)</figcaption>
</figure>
</center>

## Em resumo

O Stem-and-Leaf é um gráfico muito útil na análise exploratória de dados. Para datasets pequenos o R
e o Stata conseguem criar Stem-and-Leafs muito bons. Para grandes datasets, até o momento, só conheço
o Python com o package `stemgraphic`.

É mais uma ferramente interessante para manter em nossa toolbox analítica!

Para saber mais, consulte:

<iframe style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//ws-na.amazon-adsystem.com/widgets/q?ServiceVersion=20070822&OneJS=1&Operation=GetAdHtml&MarketPlace=US&source=ac&ref=tf_til&ad_type=product_link&tracking_id=abrantesasf02-20&marketplace=amazon&region=US&placement=0201076160&asins=0201076160&linkId=642ca6051c99af06e73fbb4947b5c4a0&show_border=true&link_opens_in_new_window=true&price_color=333333&title_color=0066c0&bg_color=ffffff">
</iframe>

<iframe style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//ws-na.amazon-adsystem.com/widgets/q?ServiceVersion=20070822&OneJS=1&Operation=GetAdHtml&MarketPlace=US&source=ac&ref=tf_til&ad_type=product_link&tracking_id=abrantesasf02-20&marketplace=amazon&region=US&placement=0471384917&asins=0471384917&linkId=effe60f2ce8dd9869ab0944553ebddc8&show_border=true&link_opens_in_new_window=true&price_color=333333&title_color=0066c0&bg_color=ffffff">
</iframe>