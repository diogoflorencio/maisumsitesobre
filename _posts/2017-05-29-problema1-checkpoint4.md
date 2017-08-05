---
layout: post
title:  Problema 1 checkpoint 4
date: 2017-08-05 11:08:23
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
{% endhighlight %}
#Qual a melhor série?
A melhor série deve ter episódios bem avaliados, além de manter regularidade na avaliação. Seguindo esses critérios, o gráfico exibe a relacação entre mediana e variância para nota dos episódios de cada série. Quanto mais acima a série está melhor é sua avaliação, quanto mais a esquerda está mais regular é.

{% highlight r %}
dados %>% 
    group_by(serie = series_name) %>% 
    summarise(mediana = median(UserRating),
              variancia = var(UserRating)) %>%
    arrange(desc(mediana),variancia)%>%
    slice(1:10) %>% 
  ggplot(aes(x = variancia, y = mediana, color = serie)) + 
  geom_point(size = 6) + 
  theme_classic() +
  ggtitle("Top 10 melhores séries")
{% endhighlight %}

![plot of chunk unnamed-chunk-2](/portifolioAnaliseDeDadosfigure/source/posts/2017-05-29-problema1-checkpoint4/unnamed-chunk-2-1.png)
As melhores séries devem estar localizadas acima e a esquerda. Sherlock e Sense8 possuem as melhores avaliações, porém apresentam uma irregularidade maior que as demais. Deste modo Stranger Things e Daredevil podem ser consideradas como as melhores séries, por equilibrarem boa avaliação e regularidade.


###As melhores séries tem muitos episódios?
Uma série com poucos episódios teria mais chances de manter a regularidade e boa avaliação dos episódios? Afim de repsonder a pergunta, o gráfico exibe a relacação entre mediana, variância e quantidade, para os episódios de cada série. Os pontos mais acima e a esquerda referenciam as melhores séries. Quanto maior for o ponto mais episódios a série tem. 

{% highlight r %}
dados %>% 
    group_by(serie = series_name) %>% 
    summarise(mediana = median(UserRating),
              variancia = var(UserRating), 
              qtd_episodios = n()) %>%
  ggplot(aes(x = variancia, y = mediana, size = qtd_episodios)) + 
  geom_point() + 
  theme_classic() +
  ggtitle("Avaliação X Regularidade X Quantidade de Eposódios")
{% endhighlight %}

![plot of chunk unnamed-chunk-3](/portifolioAnaliseDeDadosfigure/source/posts/2017-05-29-problema1-checkpoint4/unnamed-chunk-3-1.png)
Os pontos mais acima a a esquerda são as melhores séries, neste setor do gráfico os pontos são menores indicando que as melhores séries não tem muitos episódios.


#Qual a melhor temporada?
Como a avaliação e a regularidade das séries se comportam ao longo das temporadas? O gráfico de barras relaciona a avaliação (mediana) e variância dos episódios ao longo das temporadas, de forma independente das séries. O objetivo é, pra qualquer série sugerir qual seria a melhor temporada ou temporada mais irregular.

{% highlight r %}
dados %>% 
  group_by(temporada = season)%>%
  summarise(nota = median(UserRating),
            variancia = var(UserRating))%>% 
  ggplot(aes(x = temporada, y = nota, size = variancia)) + 
  geom_line(color = "blue") + 
  geom_point(color = "blue") +
  scale_x_continuous(breaks=seq(0, 10, 1)) 
{% endhighlight %}

![plot of chunk unnamed-chunk-4](/portifolioAnaliseDeDadosfigure/source/posts/2017-05-29-problema1-checkpoint4/unnamed-chunk-4-1.png)
A melhor temporada seria a 3º. No periodo equivalente a 3º temporada, a linha possui a menor espessura indicando menor irreguladade das séries, é também na 3º temporada que a maioria das séries tem sua melhor avaliação.  


### Qual temporada tem mais episódios?
A temporada com mais episódios, pode sugerir qual será a temporada mais extensa de uma série. O gráfico exibe a quantidade de episódios ao longo das temporadas, considerando todas séries.   

{% highlight r %}
dados %>% 
  group_by(temporada = season)%>%
  summarise(qtd_episodios = n())%>% 
  ggplot(aes(x = temporada, y = qtd_episodios)) + 
  geom_line() + 
  geom_point() +
  scale_x_continuous(breaks=seq(0, 10, 1)) 
{% endhighlight %}

![plot of chunk unnamed-chunk-5](/portifolioAnaliseDeDadosfigure/source/posts/2017-05-29-problema1-checkpoint4/unnamed-chunk-5-1.png)
A quantidade de episódios decresce ao longo das temporadas. Deste modo, a primeira temporada tende a ser a mais extensa para maioria das séries.
