---
layout: post
title:  Problema 3 checkpoint 2
date: 2017-07-08 14:04:44
published: true
tags: [htmlwidgets, r]
---


{% highlight r %}
require(GGally, quietly = TRUE)
require(reshape2, quietly = TRUE)
require(tidyverse, quietly = TRUE, warn.conflicts = FALSE)
{% endhighlight %}



{% highlight text %}
## Loading tidyverse: ggplot2
## Loading tidyverse: tibble
## Loading tidyverse: tidyr
## Loading tidyverse: readr
## Loading tidyverse: purrr
## Loading tidyverse: dplyr
{% endhighlight %}



{% highlight text %}
## Conflicts with tidy packages -----------------------------------------
{% endhighlight %}



{% highlight text %}
## filter(): dplyr, stats
## lag():    dplyr, stats
{% endhighlight %}



{% highlight r %}
library(ggfortify)
{% endhighlight %}



{% highlight text %}
## Carregando pacotes exigidos: methods
{% endhighlight %}



{% highlight r %}
library(cluster)
library(ggdendro)
library(broom)
library(tidyverse)
library(plotly)
{% endhighlight %}



{% highlight text %}
## 
## Attaching package: 'plotly'
{% endhighlight %}



{% highlight text %}
## The following object is masked from 'package:ggplot2':
## 
##     last_plot
{% endhighlight %}



{% highlight text %}
## The following object is masked from 'package:stats':
## 
##     filter
{% endhighlight %}



{% highlight text %}
## The following object is masked from 'package:graphics':
## 
##     layout
{% endhighlight %}
Os dados escolhidos para este laboratório são referentes as unidades acadêmicas da UFCG. Analisando os dados de modo geral, observei a existência de algumas unidades acadêmicas que possuem 1,2 ou 3 funcionários, valores estranhos. Deste modo, considerei apenas unidades acadêmicas que possuem mais de 10 funcionários.

{% highlight r %}
dados <- read.csv("../../dados/ufcg-201704-sumario-UAs-wide.csv",encoding="UTF-8")
dados <- dados %>%
  group_by(unidade_academica = UORG_LOTACAO) %>% 
   summarise(total_funcionarios = sum(Outro,Professor.20h,Professor.40h.ou.DE),
              Professor.40h.ou.DE = Professor.40h.ou.DE, 
              #Professor.20h = Professor.20h,
              idade_mediana = idade_mediana)%>%
  filter(total_funcionarios > 10) %>%
  ungroup()
{% endhighlight %}
As variáveis escolhidas para o agrupamento foram `total de funcionários`, `Professor.40h.ou.DE` e `idade_mediana`(50 percentil da idade dos funcionários); uma descrição mais detalhada sobre os dados está disponível [aqui](https://github.com/nazareno/tamanhos-da-ufcg). As escalas originais das variáveis apresentam uma diferença significativa em seus valores, deste modo foram normalizadas prevenindo que uma variável domine a análise. 

{% highlight r %}
dados.scaled = dados %>% 
    mutate_each(funs(log), 2:4) %>% 
    mutate_each(funs(as.vector(scale(.))), 2:4)
{% endhighlight %}



{% highlight text %}
## `mutate_each()` is deprecated.
## Use `mutate_all()`, `mutate_at()` or `mutate_if()` instead.
## To map `funs` over a selection of variables, use `mutate_at()`
## `mutate_each()` is deprecated.
## Use `mutate_all()`, `mutate_at()` or `mutate_if()` instead.
## To map `funs` over a selection of variables, use `mutate_at()`
{% endhighlight %}



{% highlight r %}
dados.scaled %>% 
    select(-unidade_academica) %>% 
    ggpairs()
{% endhighlight %}

![plot of chunk unnamed-chunk-3](/portifolioAnaliseDeDadosfigure/source/posts/2017-07-29-problema3-checkpoint2/unnamed-chunk-3-1.png)
Pode-se definir o número de `clusters` do agrupamento, por meio do calculo da distância quadrática entre o centro dos clusters, o centro dos dados e os dados. Aqui o centro dos dados é um ponto imaginário na média de todas as variáveis. O gráfico relaciona as variáveis `betweenss` e `totss`, essa proporção pode ser usada para definir um bom valor para o número de `clusters`. Quando ela para de crescer, para de valer à pena aumentar os `clusters`. Neste caso, um bom valor para o número de `clusters`seria 4.

{% highlight r %}
set.seed(123)
explorando_clusters = tibble(clusters = 1:15) %>% 
    group_by(clusters) %>% 
    do(
        kmeans(select(dados.scaled, -unidade_academica), 
               centers = .$clusters, 
               nstart = 20) %>% glance()
    )
{% endhighlight %}



{% highlight text %}
## Error in do_one(nmeth): NA/NaN/Inf em chamada de função externa (argumento 1)
{% endhighlight %}



{% highlight r %}
explorando_clusters %>% 
    ggplot(aes(x = clusters, y = betweenss / totss)) + 
    geom_line(color = "blue") + 
    geom_point(color = "red")
{% endhighlight %}



{% highlight text %}
## Error in eval(lhs, parent, parent): objeto 'explorando_clusters' não encontrado
{% endhighlight %}
## Agrupamento k- means
Após definir a quantidades de clusters do agrupamento, agrapamento foi realizado através do método de clustering `k- means`. Analisando o agrupamento pode- se caracterizar os clusters. O clusters 1 agrupa unidades acadêmicas que possuem funcionários com idade proxima a média, e tem uma quantidade média de professores e funcionários, este grupo pode ser nomeado por `Medianos`. O cluster 2 engloba unidades acadêmicas com pouquissímos funcionários e idade abaixo da média, ou seja, seria o grupo `Sobreviventes`. O cluster 3, `Exatas`, concentra em sua maioria unidades acadêmicas com funcionários jovens, e que possuem muitos professores e funcionários. Por fim, o cluster 4 é referente a unidades acadêmicas com funcionários mais velhos que a média e que possuem menos professores e funcionários que a média, seria o grupo `Humanas`. 

{% highlight r %}
set.seed(123)

n_clusters = 4

km = dados.scaled %>% 
    select(-unidade_academica) %>% 
    kmeans(centers = n_clusters, nstart = 20)
{% endhighlight %}



{% highlight text %}
## Error in do_one(nmeth): NA/NaN/Inf em chamada de função externa (argumento 1)
{% endhighlight %}



{% highlight r %}
dados.long = km %>% 
    augment(dados.scaled) %>% 
    gather(key = "variável", 
           value = "valor", 
           -unidade_academica, -.cluster) 
{% endhighlight %}



{% highlight text %}
## Error in eval(lhs, parent, parent): objeto 'km' não encontrado
{% endhighlight %}



{% highlight r %}
autoplot(km, data = dados, label = TRUE)
{% endhighlight %}



{% highlight text %}
## Error in autoplot(km, data = dados, label = TRUE): objeto 'km' não encontrado
{% endhighlight %}



{% highlight r %}
dados.long %>% 
    ggplot(aes(x = `variável`, y = valor, group = unidade_academica, colour = .cluster)) + 
    geom_point(alpha = 0.2) + 
    geom_line(alpha = .5) + 
    facet_wrap(~ paste("Cluster ",.cluster)) 
{% endhighlight %}



{% highlight text %}
## Error in eval(lhs, parent, parent): objeto 'dados.long' não encontrado
{% endhighlight %}
Analisando a relação entre as variáveis, além da relação obvia e proporcional entre `Professor.40h.ou.DE` e `Total_funcionarios`, é possível perceber relações interessantes. `Total_funcionarios` se relaciona de maneira inversamente proporcional a `idade_mediana`, ou seja, em uma unidade acadêmica quanto mais funcionários ela tem mais novos eles tendem a ser. A relação entre `Professor.40h.ou.DE` e `idade_mediana` também e inversamente proporcional, dado uma unidade acadêmica quanto mais professores (40hrs ou dedicação exclusiva) ela tiver mais novos eles tendem a ser.

{% highlight r %}
km %>% 
    augment(dados) %>%
    plot_ly(type = 'parcoords',
            line = list(color = ~.cluster, 
                        showScale = TRUE),
            dimensions = list(
                list(label = 'Total_funcionarios/UA', values = ~total_funcionarios),
                list(label = 'idade_mediana/UA', values = ~idade_mediana),
                list(label = 'Professor.40h.ou.DE/UA', values = ~Professor.40h.ou.DE)
            )
    )
{% endhighlight %}



{% highlight text %}
## Error in eval(lhs, parent, parent): objeto 'km' não encontrado
{% endhighlight %}




