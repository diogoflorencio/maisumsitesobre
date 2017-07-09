---
layout: post
title:  Problema 2 checkpoint 1
date: 2017-07-08 21:16:16
published: true
tags: [htmlwidgets, r]
---

{% highlight r %}
library(tidyverse)
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
library(highcharter)
{% endhighlight %}



{% highlight text %}
## Highcharts (www.highcharts.com) is a Highsoft software product which is
{% endhighlight %}



{% highlight text %}
## not free for commercial and Governmental use
{% endhighlight %}



{% highlight r %}
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
highchart() %>% 
  hc_xAxis(prisonBreak$season_ep) %>% 
  hc_add_series(name = "temporada 1" , data = (prisonBreak %>% filter(season == 1))$UserRating) %>%  
  hc_add_series(name = "temporada 2" , data = (prisonBreak %>% filter(season == 2))$UserRating) %>%  
  hc_add_series(name = "temporada 3" , data = (prisonBreak %>% filter(season == 3))$UserRating)
{% endhighlight %}



{% highlight text %}
## Error in loadNamespace(name): there is no package called 'webshot'
{% endhighlight %}


A qualidade das temporadas vem caindo ao longo da série, porém quase todos os episódios são avaliados entre 9 e 8. O episódio 21 "Go", penultimo da primeira temporada tem a maior avaliação com 9,5. Já o episódio 73 "The Sunshine State", da 4º temporada detem a menor avaliação com 7,8.  

###The Walking Dead
The Walking Dead é uma série de televisão dramática e pós-apocalíptica norte-americana, estreou no dia 30 outubro de 2010, no canal de televisão a cabo AMC, nos Estados Unidos. A série possui 7 temporadas finalizadas, confira a avaliação dos expectadores para os episódios de cada temporada. 

{% highlight r %}
TWD <- mutate(TWD, season_name = paste("Temporada", season))
hchart(TWD, "line", hcaes(x = season_ep, y = UserRating, group = season_name))
{% endhighlight %}



{% highlight text %}
## Error in loadNamespace(name): there is no package called 'webshot'
{% endhighlight %}
O 9º episódio "No Way Out", da 6º temporada se destaca com a maior nota da série com 9,7. A pior avaliação foi atribuida ao 6º episódio "Swear", da 7º temporada com nota 5,7. 

###Daredevil
Daredevil é uma websérie americana criada para a Netflix por Drew Goddard que baseia-se no personagem de mesmo nome da Marvel. Veja a avaliação dos expectadores para os episódios de cada temporada. 

{% highlight r %}
highchart() %>% 
  hc_xAxis(daredevil$season_ep) %>%
  hc_add_series(name = "temporada 1" , data = (daredevil %>% filter(season == 1))$UserRating) %>%  
  hc_add_series(name = "temporada 2" , data = (daredevil %>% filter(season == 2))$UserRating)
{% endhighlight %}



{% highlight text %}
## Error in loadNamespace(name): there is no package called 'webshot'
{% endhighlight %}
Todos os episódios da série estão avaliados entre 8,5 e 9,5. A 2º temporada se destaca com 3 episódios atingindo a maior avaliação, nota 9,5.

#Qual série possui uma avaliação melhor no IMDB?
Considerando a mediana e as notas maximas e mínimas dos episódios de cada série, temos:

{% highlight r %}
dados %>% 
    group_by(series_name) %>% 
    summarise(mediana = median(UserRating),
              variancia = var(UserRating)) %>%
    ungroup()
{% endhighlight %}



{% highlight text %}
## # A tibble: 3 x 3
##        series_name mediana  variancia
##             <fctr>   <dbl>      <dbl>
## 1        Daredevil    8.95 0.08758462
## 2     Prison Break    8.60 0.11792901
## 3 The Walking Dead    8.30 0.48139147
{% endhighlight %}
Daredevil possui a melhor avaliação no IMBD. A série apresenta uma mediana superior e se destaca por possuir 3 episódios atingindo a maior avaliação 9,5. Essas caracteristicas garantiram uma pequena vantagem para as demais, em resumo Daredevil proporcionalmente possui uma quantidade maior de episódios melhor avaliados avaliados.

#Qual das séries tem menor regularidade na qualidade dos episódios?

{% highlight r %}
hcboxplot(x = dados$UserRating, var = dados$series_name, outliers = FALSE, color = "green") 
{% endhighlight %}



{% highlight text %}
## Error in mutate_impl(.data, dots): Column `data` must be length 1 (the group size), not 5
{% endhighlight %}
Os boxplots das séries, mostram que The Walking Dead possui maior variância na avaliação de seus episódios. Mesmo considerando a avaliação dos epísódios por temporada, ainda sim The Walking Dead possui maior variância. Isto demonstra que a séria não matém uma regularidade na qualidade de seus episódios, sendo bem mais irregular que Daredevi ou Prison Break.
