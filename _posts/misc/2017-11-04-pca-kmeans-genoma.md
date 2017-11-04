---
layout:      post
title:       'PCA e K-Means para decifrar o genoma'
date:        2017-11-04 00:00:00 -0300
image:       '/images/misc/case_1_1_1_05.png'
description: 'Estudo de caso com PCA e K-Means no MATLAB'
keywords:    'data science, K-Means, PCA, MATLAB'
categories:  books
---

Este &eacute; um estudo de caso reproduzindo a an&aacute;lise de <b>Alexander N. Gorban</b> e <b>Andrei Y. Zinovyev</b>, publicada no livro <i><b>Principal Manifolds for Data Visualization and Dimension Reduction (Lecture Notes in Computational Science and Engineering)</b></i>. O cap&iacute;tulo original pode ser baixado na &iacute;ntegra no site do autor (<a href="http://www.ihes.fr/~zinovyev/">http://www.ihes.fr/~zinovyev/</a>) e o livro pode ser <a href="https://www.amazon.com.br/gp/product/3540737499/ref=as_li_tl?ie=UTF8&amp;camp=1789&amp;creative=9325&amp;creativeASIN=3540737499&amp;linkCode=as2&amp;tag=abrantesasf01-20&amp;linkId=5334538bfcf0a64c5db86324d8cd1829">adquirido na Amazon</a>.

Aten&ccedil;&atilde;o: como eu apenas reproduzi a an&aacute;lise original dos autores citados, como forma de estudo da aplica&ccedil;&atilde;o de PCA e K-Means no MATLAB, nenhum conte&uacute;do aqui &eacute; de minha autoria, EXCETO algumas fun&ccedil;&otilde;es para o MATLAB (as fun&ccedil;&otilde;es originais fornecidas pelos autores, no livro publicado em 2007, n&atilde;o funcionaram no MATLAB R2017a, que &eacute; a vers&atilde;o que eu utilizo; assim, tive que reescrever totalmente algumas fun&ccedil;&otilde;es para reproduzir a an&aacute;lise na minha vers&atilde;o do MATLAB... e essas novas fun&ccedil;&otilde;es s&atilde;o de minha autoria). Eu indiquei no texto quais foram as fun&ccedil;&otilde;es que eu preparei e quais s&atilde;o as fun&ccedil;&otilde;es originais dos autores.

<h2>Contents</h2><div><ul><li><a href="#1">O problema a ser resolvido</a></li><li><a href="#2">Ambiente da an&aacute;lise</a></li><li><a href="#4">Leitura da seq&uuml;&ecirc;ncia do DNA da <i>Caulobacter crescentus</i></a></li><li><a href="#8">Cria&ccedil;&atilde;o dos datapoints</a></li><li><a href="#10">Visuzliando quantas letras foram uma palavra gen&eacute;tica (c&oacute;don)</a></li><li><a href="#15">Realizando clusteriza&ccedil;&atilde;o com K-Means para visualizar os agrupamentos</a></li><li><a href="#17">Acesso ao dataset e c&oacute;digos:</a></li></ul></div>

<h2 id="1">O problema a ser resolvido</h2>
<p>Quando alguma not&iacute;cia surge dizendo que o genoma, por exemplo, de uma bact&eacute;ria foi decifrado, geralmente a not&iacute;cia quer dizer apenas que esse genoma foi lido e transcrito como uma grande seq&uuml;&ecirc;ncia de 4 letras: A, C, G e T. Por exemplo:</p>

<p>[...]cgtggtcaatgatgcctagggcgcacgtatgcctagctagtttccagactcagtgcgttaactagctaa[...]</p>

<p>Essa seq&uuml;encia de letras &eacute; s&oacute; o primeiro passo para decifrar o genoma. Mais importante do que a seq&uuml;&ecirc;ncia de letras &eacute; tentar descrobrir <i><b>a mensagem codificada nessa seq&uuml;&ecirc;ncia de letras</b></i>, a mensagem gen&eacute;tica que nos faz ser o que somos. E essa mensagem &eacute; formada por uma seq&uuml;&ecirc;ncia dessas letras, de comprimento vari&aacute;vel, chamada de <b><i>gene</i></b>. Por exemplo, na seq&uuml;&ecirc;ncia abaixo, podemos ver 2 genes (em negrito-it&aacute;lico vermelho):</p>

<p>[...]cgtggtca<b><i><font color="red">atgatgcctag</font></i></b>ggcgcacgt<b><i><font color="red">atgcctagctagtttccagactcagtgcgttaa</font></i></b>ctagctaa[...]</p>

<p>Dada essa estrutura, alfabeto de 4 letras formando os genes, algumas perguntam precisam de respostas:</p>

<div><ol><li>Todas as letras fazem parte de algum gene? Ou seja, toda a seq&uuml;&ecirc;ncia de letras sempre formar&aacute; um ou mais genes? Ou existem seq&uuml;&ecirc;ncias de letras que n&atilde;o formam genes?</li><li>Dentro de um gene, as letras por si mesmas s&atilde;o respons&aacute;veis pela informa&ccedil;&atilde;o gen&eacute;tica ou &eacute; algum tipo de agrupamento das letras (uma "palavra") que cont&eacute;m a informa&ccedil;&atilde;o gen&eacute;tica? Por exemplo: a informa&ccedil;&atilde;o gen&eacute;tica &eacute; contida a cada palavra com duas, tr&ecirc;s, quatro, cinco ou mais letras?</li><li>Se dentro de um gene a informa&ccedil;&atilde;o gen&eacute;tica &eacute; dada, por exemplo, por grupos de 4 letras ("palavras" de 4 letras), algumas dessas letras podem ser sobrepostas &agrave; outras palavras? Por exemplo: as letras CASAPO podem formar 2 palavras de 4 letras, CASA e SAPO. Essa sobreposi&ccedil;&atilde;o das letras SA existe nos genes?</li></ol></div>

<p>D&eacute;cadas de pesquisas gen&eacute;ticas j&aacute; forneceram respostas para essas perguntas:</p>

<div><ol><li>Existem letras que realmente n&atilde;o formam genes, elas parecem estar ali para preencher o espa&ccedil;o "vazio"</li><li>A informa&ccedil;&atilde;o gen&eacute;tica &eacute; contida a cada grupo de 3 letras, chamado de c&oacute;don</li><li>N&atilde;o existe sobreposi&ccedil;&atilde;o das letras, ou seja, as letras que formam um c&oacute;don n&atilde;o s&atilde;o utilizadas para formar outro c&oacute;don. Por exemplo, as letras SALUA n&atilde;o formam 2 palavras de 3 letras (SAL e LUA), pois haveria sobreposi&ccedil;&atilde;o da letra L. Os c&oacute;dons n&atilde;o s&atilde;o sobrepostos.</li></ol></div>

<p>Assim, j&aacute; sabemos que nossa informa&ccedil;&atilde;o gen&eacute;tica est&aacute; codificada em genes, que s&atilde;o seq&uuml;&ecirc;ncias espec&iacute;ficas das letras A, C, G e T, e que dentro desses genes, a informa&ccedil;&atilde;o gen&eacute;tica est&aacute; contida em c&oacute;dons, agrupamentos de 3 letras, sem sobreposi&ccedil;&atilde;o. Uma outra observa&ccedil;&atilde;o: os genes em organismos simples, como uma bact&eacute;ria, tendem a ser seq&uuml;&ecirc;ncias ininterruptas mas, em organismos superiores, como o homem, os genes pode ser "quebrados", sem formar uma seq&uuml;&ecirc;ncia ininterrupta (um mesmo gene pode ser dividido em dois ou mais peda&ccedil;os separados no DNA).</p>

<p>Neste estudo de caso estamos interessados em usar PCA e K-Means para duas coisas:</p>

<div><ul><li>Usaremos a PCA para "descobrir" que a informa&ccedil;&atilde;o gen&eacute;tica est&aacute; contida em grupos de 3 letras (os c&oacute;dons), e n&atilde;o em grupos de 1, 2, 4 ou mais letras; e</li><li>Usaremos o K-Means para encontrar a localiza&ccedil;&atilde;o dos genes na seq&uuml;&ecirc;ncia de letras do DNA.</li></ul></div>

<p>Utilizaremos um fragmento do DNA da bact&eacute;ria <i>Caulobacter crescentus</i>, na qual o tamanho m&eacute;dio dos genes &eacute; de aproximadamente 1000 letras.</p>

<h2 id="2">Ambiente da an&aacute;lise</h2>

<p>A an&aacute;lise foi realizada utilizando-se o MATLAB R2017a, em um notebook Linux (Fedora 26), 64 bits, com 6 GB RAM. A an&aacute;lise foi realizada em um script, em MATLAB Markup, para reprodutibilidade.</p>

<pre class="codeinput"><span class="comment">% Informa&ccedil;&otilde;es sobre o Linux:</span>
<span class="syscmd">!uname -a</span>
</pre><pre class="codeoutput">Linux localhost.localdomain 4.13.9-200.fc26.x86_64 #1 SMP Mon Oct 23 13:52:45 UTC 2017 x86_64 x86_64 x86_64 GNU/Linux

</pre><pre class="codeinput"><span class="comment">% Informa&ccedil;&otilde;es sobre o MATLAB:</span>
ver
</pre><pre class="codeoutput">----------------------------------------------------------------------------------------------------
MATLAB Version: 9.2.0.556344 (R2017a)
MATLAB License Number: 40570225
Operating System: Linux 4.13.9-200.fc26.x86_64 #1 SMP Mon Oct 23 13:52:45 UTC 2017 x86_64
Java Version: Java 1.7.0_60-b19 with Oracle Corporation Java HotSpot(TM) 64-Bit Server VM mixed mode
----------------------------------------------------------------------------------------------------
MATLAB                                                Version 9.2         (R2017a)
Curve Fitting Toolbox                                 Version 3.5.5       (R2017a)
Database Toolbox                                      Version 7.1         (R2017a)
Neural Network Toolbox                                Version 10.0        (R2017a)
Partial Differential Equation Toolbox                 Version 2.4         (R2017a)
Statistics and Machine Learning Toolbox               Version 11.1        (R2017a)
Symbolic Math Toolbox                                 Version 7.2         (R2017a)
</pre>

<h2 id="4">Leitura da seq&uuml;&ecirc;ncia do DNA da <i>Caulobacter crescentus</i></h2>

<p>Um fragmento do DNA da bact&eacute;ria est&aacute; em um arquivo no formato <i>Fasta</i>, com a extens&atilde;o <i>.fa</i>, e precisa ser lido para alguma estrutura do MATLAB. Os autores originais forneceram a fun&ccedil;&atilde;o LoadSeq para isso:</p>

<pre class="codeinput"><span class="comment">% L&ecirc; o aquivo para o objeto vetor DNA:</span>
dna = LoadSeq(<span class="string">'ccrescentus.fa'</span>);
</pre>
<pre class="codeoutput">Reading fasta-file...
200 lines
400 lines
600 lines
800 lines
1000 lines
1200 lines
1400 lines
Length of the string:305400
</pre>

<p>Estrutura do vetor dna</p>
<pre class="codeinput">whos <span class="string">dna</span>
<span class="comment">% Podemos ver que o objeto dna &eacute; um vetor de linha do tipo caracteres, e tem 305.400 letras (A, C,</span>
<span class="comment">% G e T).</span>
</pre><pre class="codeoutput">  Name      Size                 Bytes  Class    Attributes

  dna       1x305400            610800  char               

</pre><p>Vamos ver as 100 primeiras letras:</p><pre class="codeinput">dna(1:100)
</pre><pre class="codeoutput">
ans =
    'gccgatagcctatgatccccggcaggcccggggcttggagccgtctggtttggatggaaccctccaaaccagatcaagaggctcctagaacgccgcccgc'
</pre>

<p>Agora vamos comprovar que esse vetor de letras n&atilde;o tem nenhum espa&ccedil;o ' ':</p>

<pre class="codeinput">contains(dna, <span class="string">' '</span>)
</pre><pre class="codeoutput">
ans =
  logical
   0
</pre>

<h2 id="8">Cria&ccedil;&atilde;o dos datapoints</h2>

<p>Note que at&eacute; o momento a &uacute;nica coisa que temos &eacute; o objeto vetor, com a seq&uuml;&ecirc;ncia de 305.400 letras, sem nenhum epa&ccedil;o entre essas letras. N&atilde;o temos nenhuma separa&ccedil;&atilde;o de "palavras" aqui, s&oacute; uma grande seq&uuml;&ecirc;ncia de letras.</p>

<p>Essa seq&uuml;&ecirc;ncia de letras n&atilde;o serve como datapoint para nossas an&aacute;lises, precisamos criar tais datapoints. Faremos isso com o seguinte procedimento:</p>

<div><ol><li>Quebraremos a seq&uuml;&ecirc;ncia de letras em peda&ccedil;os de 300 letras: segundo os autores, como o tamanho m&eacute;dio do gene de uma bact&eacute;ria &eacute; de 1000 letras, peda&ccedil;os com 300 letras s&atilde;o suficientes para detectar os genes nessa escala;</li><li>Calcularemos a quantidade de palavras (agrupamentos de letras) dentro de cada peda&ccedil;o de 300 letras, contando quantas palavras com 1, 2, 3 e 4 letras (n&atilde;o sobrepostas) existem em cada peda&ccedil;o.</li></ol></div>

<p>Dessa maneira teremos 4 matrizes diferentes: * Matriz 1: 1018 linhas e 4 colunas (para a quantidade de palavras de 1 letra: a, t, c e g) * Matriz 2: 1018 linhas e 16 colunas (para a quantidade de palavras com 2 letras: aa, at, ac, ...) Matriz 3: 1018 linhas e 64 colunas (para a quantidade de palavras com 3 letras: aaa, aat, ata, ...) Matriz 4: 1018 linhas e 256 colunas (para a quantidade de palavras com 4 letras: aaaa, aaat, aatt, atat, ...)</p>

<p>Nossos datapoints ser&atilde;o a contagem das palavras de 1, 2, 3 ou 4 letras, em cada um dos 1018 fragmentos com 300 letras de DNA.</p>

<pre class="codeinput"><span class="comment">% A primeira coisa que temos que fazer ent&atilde;o &eacute; dividir nossa seq&uuml;&ecirc;ncia de</span>
<span class="comment">% DNA com 305.400 letras, em 1018 fragmentos com 300 letras. Para isso</span>
<span class="comment">% vamos usar a fun&ccedil;&atilde;o preparaFramentos.m que eu escrevi:</span>
fragmentos = preparaFragmentos(dna, 300);
</pre>

<pre class="codeinput"><span class="comment">% Agora vamos calcular a freq&uuml;encia das palvras com 1, 2, 3 ou 4 letras, em</span>
<span class="comment">% cada fragmento de DNA com 300 letras, utilizando a fun&ccedil;&atilde;o</span>
<span class="comment">% calculaFrequencia.m que eu escrevi:</span>
xx1 = calculaFrequencia(fragmentos, 1);
xx2 = calculaFrequencia(fragmentos, 2);
xx3 = calculaFrequencia(fragmentos, 3);
xx4 = calculaFrequencia(fragmentos, 4);
</pre>

<h2 id="10">Visuzliando quantas letras foram uma palavra gen&eacute;tica (c&oacute;don)</h2>

<p>Agora que temos 4 matrizes com as contagens das palavras gen&eacute;ticas com 1, 2, 3 e 4 letras, a tarefa &eacute; tentar descobrir se a informa&ccedil;&atilde;o gen&eacute;tica est&aacute; armazenada em palavras com 1, 2, 3 ou 4 letras.</p>

<p>Poder&iacute;amos tentar plotar gr&aacute;ficos para descobrir mas com 4 a 256 vari&aacute;veis, n&atilde;o &eacute; poss&iacute;vel um gr&aacute;fico com tantas dimens&otilde;es. A solu&ccedil;&atilde;o &eacute; usar Principal Componente Analysis (PCA) e plotar gr&aacute;ficos com apenas os 2 componentes principais, permitindo assim uma f&aacute;cil visualiza&ccedil;&atilde;o. Os autores originais forneceram a fun&ccedil;&atilde;o PCAFreq.m, que faz a PCA e j&aacute; plota o gr&aacute;fico:</p>

<p>Gr&aacute;fico para palavras gen&eacute;ticas com 1 letra:</p>

<pre class="codeinput">PCAFreq(xx1);</pre>
<img vspace="5" hspace="5" src="/images/misc/case_1_1_1_01.png" alt="">

<p>Gr&aacute;fico para palavras gen&eacute;ticas com 2 letras:

</p><pre class="codeinput">PCAFreq(xx2);</pre>
<img vspace="5" hspace="5" src="/images/misc/case_1_1_1_02.png" alt="">

<p>Gr&aacute;fico para palavras gen&eacute;ticas com 3 letras:</p>

<pre class="codeinput">PCAFreq(xx3);</pre>
<img vspace="5" hspace="5" src="/images/misc/case_1_1_1_03.png" alt="">

<p>Gr&aacute;fico para palavras gen&eacute;ticas com 4 letras:</p>
<pre class="codeinput">PCAFreq(xx4);</pre>
<img vspace="5" hspace="5" src="/images/misc/case_1_1_1_04.png" alt="">

<p>Como ficou evidente pelos 4 gr&aacute;ficos acima, a informa&ccedil;&atilde;o gen&eacute;tica est&aacute; codificada em <b>palavras com 3 letras</b>, os c&oacute;dons, pois foi o &uacute;nico gr&aacute;fico que apresentou uma estrutura claramente vis&iacute;vel, com 7 agrupamentos claramente vis&iacute;veis: 6 agrupamentos ao redor de 1 agrupamento central.</p>

<p>Os gr&aacute;ficos para palavras com 1, 2 ou 4 letras n&atilde;o apresentaram nenhuma estrutura aparente, indicando que a informa&ccedil;&atilde;o gen&eacute;tica realmente est&aacute; codificada em palavras com 3 letras.</p>

<p>O motivo do gr&aacute;fico mostrar 7 agrupamentos distintos &eacute; explicado em detalhes pelos autores e &eacute; relacionado ao modo arbitr&aacute;rio que usamos para dividir a seq&uuml;encia de DNA em fragmentos para an&aacute;lise. Para maiores informa&ccedil;&otilde;es, veja a publica&ccedil;&atilde;o original.</p>

<h2 id="15">Realizando clusteriza&ccedil;&atilde;o com K-Means para visualizar os agrupamentos</h2>

<p>J&aacute; temos uma boa id&eacute;ia com o PCA que a informa&ccedil;&atilde;o gen&eacute;tica em um gene est&aacute; codificada em palavras com 3 letras. Vamos agora utilizar uma t&eacute;cnica de unsupervised learning, o clustering, utilizando o K-Means, para visualizarmos os clusters de c&oacute;dons.</p>

<p>Os autores forneceram a fun&ccedil;&atilde;o ClustFreq.m que faz o K-Means e mostra graficamente o resultado:</p>

<pre class="codeinput">fragn = ClustFreq(xx3, 7);</pre>
<img vspace="5" hspace="5" src="/images/misc/case_1_1_1_05.png" alt="">

<p>Podemos ver claramente os 6 clusters de informa&ccedil;&otilde;es gen&eacute;ticas formando uma esp&eacute;cie de c&iacute;culo, e 1 cluster central (que cont&eacute;m informa&ccedil;&otilde;es n&atilde;o-gen&eacute;ticas).</p>

<p>Como um &uacute;ltimo exerc&iacute;cio os autores forneceram a fun&ccedil;&atilde;o GenBrowser.m que pega o resultado da clusteriza&ccedil;&atilde;o e aplica essa clusteriza&ccedil;&atilde;o &agrave;s letras do fragmento de DNA, colorindo esse DNA de acordo com os clusters. Isso resulta em uma esp&eacute;cie de "navegador" gen&eacute;tico primitivo, que mostra os clusters coloridos na seq&uuml;&ecirc;ncia de letras:</p>

<pre class="codeinput">GenBrowser(dna, 300, fragn, 13000);</pre>
<img vspace="5" hspace="5" src="/images/misc/case_1_1_1_06.png" alt="">

<h2 id="17">Acesso ao dataset e c&oacute;digos:</h2>

<p>O dataset de DNA da bact&eacute;ria e as fun&ccedil;&otilde;es para MATLAB (tanto as fornecidas pelos autores originais, quanto as que eu preparei), est&atilde;o dispon&iacute;veis em <a href="https://github.com/abrantesasf/mit_xpro/tree/master/data_science_and_big_data_analytics/module_1/case_1.1.1">meu reposit&oacute;rio GitHub.</a></p>

<p>O texto original dos autores, <i>PCA and K-Means Decipher Genome</i>, publicado no livro <i><b>Principal Manifolds for Data Visualization and Dimension Reduction (Lecture Notes in Computational Science and Engineering)</b></i>, pode ser baixado na &iacute;ntegra no site do autor (<a href="http://www.ihes.fr/~zinovyev/">http://www.ihes.fr/~zinovyev/</a>).</p>

<p>O livro pode ser <a href="https://www.amazon.com.br/gp/product/3540737499/ref=as_li_tl?ie=UTF8&amp;camp=1789&amp;creative=9325&amp;creativeASIN=3540737499&amp;linkCode=as2&amp;tag=abrantesasf01-20&amp;linkId=5334538bfcf0a64c5db86324d8cd1829">adquirido na Amazon</a>.</p>

<p class="footer"><br><a href="http://www.mathworks.com/products/matlab/">Published with MATLAB&reg; R2017a</a><br></p>
