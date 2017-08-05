---
layout: post
title:  Problema 1 checkpoint 1
date: 2017-08-05 11:08:18
published: true
tags: [htmlwidgets, r]
---

{% highlight r %}
library(tidyr)
library(dplyr)
{% endhighlight %}



{% highlight text %}
## Warning: Installed Rcpp (0.12.12) different from Rcpp used to build dplyr (0.12.11).
## Please reinstall dplyr to avoid random crashes or undefined behavior.
{% endhighlight %}



{% highlight text %}
## 
## Attaching package: 'dplyr'
{% endhighlight %}



{% highlight text %}
## The following objects are masked from 'package:stats':
## 
##     filter, lag
{% endhighlight %}



{% highlight text %}
## The following objects are masked from 'package:base':
## 
##     intersect, setdiff, setequal, union
{% endhighlight %}



{% highlight r %}
library(ggplot2)
dados <- read.csv("../../dados/series_from_imdb.csv",encoding="UTF-8")
dados <- dados %>% filter(series_name %in% c("Daredevil", "The Walking Dead", "Prison Break"))
prisonBreak <- dados %>% filter(series_name == "Prison Break")
TWD <- dados %>% filter(series_name == "The Walking Dead")
daredevil <- dados %>% filter(series_name == "Daredevil")
{% endhighlight %}
#Introdução
###Prison Break
Prison Break é uma série de televisão norte-americana de ação e suspense, transmitida originalmente pela Fox desde 29 de agosto de 2005. Em 2015, a Fox anunciou uma quinta temporada contendo 9 episódios, que estreou em 4 de abril de 2017. 
Considerando as 4 ultimas temporadas, confira as avaliações dos expectadores para os episódios de cada temporada. 

{% highlight r %}
prisonBreak %>% 
  mutate(temporada = as.character(season)) %>% 
  ggplot(aes(x = season_ep, y = UserRating, color = temporada)) + 
  geom_line() + 
  geom_point()
{% endhighlight %}

![plot of chunk unnamed-chunk-2](/portifolioAnaliseDeDadosfigure/source/posts/2017-05-22-problema1-checkpoint1/unnamed-chunk-2-1.png)
A qualidade das temporadas vem caindo ao longo da série, porém quase todos os episódios são avaliados entre 9 e 8. O episódio 21 "Go", penultimo da primeira temporada tem a maior avaliação com 9,5. Já o episódio 73 "The Sunshine State", da 4º temporada detem a menor avaliação com 7,8.  

###The Walking Dead
The Walking Dead é uma série de televisão dramática e pós-apocalíptica norte-americana, estreou no dia 30 outubro de 2010, no canal de televisão a cabo AMC, nos Estados Unidos. A série possui 7 temporadas finalizadas, confira a avaliação dos expectadores para os episódios de cada temporada. 

{% highlight r %}
TWD %>% 
  mutate(temporada = as.character(season)) %>% 
  ggplot(aes(x = season_ep, y = UserRating, color = temporada)) + 
  geom_line() + 
  geom_point()
{% endhighlight %}

![plot of chunk unnamed-chunk-3](/portifolioAnaliseDeDadosfigure/source/posts/2017-05-22-problema1-checkpoint1/unnamed-chunk-3-1.png)
O 9º episódio "No Way Out", da 6º temporada se destaca com a maior nota da série com 9,7. A pior avaliação foi atribuida ao 6º episódio "Swear", da 7º temporada com nota 5,7. 

###Daredevil
Daredevil é uma websérie americana criada para a Netflix por Drew Goddard que baseia-se no personagem de mesmo nome da Marvel. Veja a avaliação dos expectadores para os episódios de cada temporada. 

{% highlight r %}
daredevil %>% 
  mutate(temporada = as.character(season)) %>% 
  ggplot(aes(x = season_ep, y = UserRating, color = temporada)) + 
  geom_line() + 
  geom_point()
{% endhighlight %}

![plot of chunk unnamed-chunk-4](/portifolioAnaliseDeDadosfigure/source/posts/2017-05-22-problema1-checkpoint1/unnamed-chunk-4-1.png)
Todos os episódios da série estão avaliados entre 8,5 e 9,5. A 2º temporada se destaca com 3 episódios atingindo a maior avaliação, nota 9,5.

#Qual série possui uma avaliação melhor no IMDB?
Considerando a mediana e as notas maximas e mínimas dos episódios de cada série, temos:

{% highlight r %}
dados %>% 
    group_by(series_name) %>% 
    summarise(mediana = median(UserRating),
              maior_nota = max(UserRating),
              menor_nota = min(UserRating))
{% endhighlight %}



{% highlight text %}
## # A tibble: 3 x 4
##        series_name mediana maior_nota menor_nota
##             <fctr>   <dbl>      <dbl>      <dbl>
## 1        Daredevil    8.95        9.5        8.5
## 2     Prison Break    8.60        9.5        7.8
## 3 The Walking Dead    8.30        9.7        5.7
{% endhighlight %}
Daredevil possui a melhor avaliação no IMBD. A série apresenta uma mediana superior e se destaca por possuir 3 episódios atingindo a maior avaliação 9,5. Essas caracteristicas garantiram uma pequena vantagem para as demais, em resumo Daredevil proporcionalmente possui uma quantidade maior de episódios melhor avaliados avaliados.

#Qual das séries tem menor regularidade na qualidade dos episódios?

{% highlight r %}
dados %>% 
    ggplot(aes(x = as.character(series_name), y = UserRating)) + 
    geom_boxplot(outlier.color = NA) +   
    geom_jitter(width = .1, size = .5, alpha = .5, color = "blue")
{% endhighlight %}

![plot of chunk unnamed-chunk-6](/portifolioAnaliseDeDadosfigure/source/posts/2017-05-22-problema1-checkpoint1/unnamed-chunk-6-1.png)
Os boxplots das séries, mostram que The Walking Dead possui maior variância na avaliação de seus episódios. Mesmo considerando a avaliação dos epísódios por temporada, ainda sim The Walking Dead possui maior variância. Isto demonstra que a séria não matém uma regularidade na qualidade de seus episódios, sendo bem mais irregular que Daredevi ou Prison Break. 

{% highlight r %}
dados %>% 
    group_by(series_name, season) %>% 
    summarise(variancia = var(UserRating))
{% endhighlight %}



{% highlight text %}
## # A tibble: 13 x 3
## # Groups:   series_name [?]
##         series_name season  variancia
##              <fctr>  <int>      <dbl>
##  1        Daredevil      1 0.06141026
##  2        Daredevil      2 0.10692308
##  3     Prison Break      1 0.06945887
##  4     Prison Break      2 0.02521645
##  5     Prison Break      3 0.05666667
##  6     Prison Break      4 0.11210145
##  7 The Walking Dead      1 0.11066667
##  8 The Walking Dead      2 0.24641026
##  9 The Walking Dead      3 0.18562500
## 10 The Walking Dead      4 0.34000000
## 11 The Walking Dead      5 0.32195833
## 12 The Walking Dead      6 0.66250000
## 13 The Walking Dead      7 0.66562500
{% endhighlight %}
