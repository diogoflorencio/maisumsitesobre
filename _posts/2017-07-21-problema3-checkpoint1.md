---
layout: post
title:  Problema 3 checkpoint 1
date: 2017-08-05 11:08:28
published: true
tags: [htmlwidgets, r]
---




{% highlight r %}
library(tidyverse, warn.conflicts = F)
{% endhighlight %}



{% highlight text %}
## Warning: Installed Rcpp (0.12.12) different from Rcpp used to build dplyr (0.12.11).
## Please reinstall dplyr to avoid random crashes or undefined behavior.
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
library(rvest)
{% endhighlight %}



{% highlight text %}
## Carregando pacotes exigidos: xml2
{% endhighlight %}



{% highlight text %}
## 
## Attaching package: 'rvest'
{% endhighlight %}



{% highlight text %}
## The following object is masked from 'package:readr':
## 
##     guess_encoding
{% endhighlight %}



{% highlight r %}
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



{% highlight r %}
library(cluster)
library(ggdendro)
{% endhighlight %}
# Tipos de filme de Sylvester Stallone

Os dados utilizados foram do [Rotten Tomatoes](https://www.rottentomatoes.com) sobre os filmes de Sylvester Stallone.

{% highlight r %}
from_page <- read_html("https://www.rottentomatoes.com/celebrity/sylvester_stallone") %>% 
    html_node("#filmographyTbl") %>% # A sintaxe da expressão é de um seletor à lá JQuery: https://rdrr.io/cran/rvest/man/html_nodes.html 
    html_table(fill=TRUE) %>% # Faz parse
    as.tibble()

filmes = from_page %>% 
    filter(RATING != "No Score Yet", 
           `BOX OFFICE` != "—", 
           CREDIT != "Executive Producer") %>%
    mutate(RATING = as.numeric(gsub("%", "", RATING)), 
           `BOX OFFICE` = as.numeric(gsub("[$|M]", "", `BOX OFFICE`))) %>% 
    filter(`BOX OFFICE` >= 1) # Tem dois filmes que não parecem ter sido lançados no mundo todo
{% endhighlight %}
Para iniciar a análise de agrupamento considerei as variáveis RATING e BOX OFFICE. Inicialmente as váriaveis não apresentaram uma estrura clara de grupo, mas ao normalizar a escala os resultados obtidos foram diferentes.

{% highlight r %}
agrupamento_h_2d = filmes %>% 
    column_to_rownames("TITLE") %>%
    select(RATING, `BOX OFFICE`) %>% 
    mutate(`BOX OFFICE` = log10(`BOX OFFICE`)) %>% 
    mutate_all(funs(scale)) %>% 
    dist(method = "euclidean") %>% 
    hclust(method = "centroid")
{% endhighlight %}



{% highlight text %}
## Warning: Setting row names on a tibble is deprecated.
{% endhighlight %}



{% highlight r %}
ggdendrogram(agrupamento_h_2d, rotate = TRUE)
{% endhighlight %}

![plot of chunk unnamed-chunk-2](/portifolioAnaliseDeDadosfigure/source/posts/2017-07-21-problema3-checkpoint1/unnamed-chunk-2-1.png)

{% highlight r %}
filmes2 = filmes %>% mutate(`BOX OFFICE` = log10(`BOX OFFICE`))
plota_hclusts_2d(agrupamento_h_2d, 
                 filmes2, 
                 c("RATING", "`BOX OFFICE`"), 
                 linkage_method = "ward.D", ks = 1:6) + scale_y_log10()
{% endhighlight %}



{% highlight text %}
## Error in plota_hclusts_2d(agrupamento_h_2d, filmes2, c("RATING", "`BOX OFFICE`"), : não foi possível encontrar a função "plota_hclusts_2d"
{% endhighlight %}



{% highlight r %}
distancias = filmes %>% 
    column_to_rownames("TITLE") %>%
    select(RATING, `BOX OFFICE`) %>% 
    mutate(`BOX OFFICE` = log10(`BOX OFFICE`)) %>% 
    mutate_all(funs(scale)) %>% 
    dist(method = "euclidean")
{% endhighlight %}



{% highlight text %}
## Warning: Setting row names on a tibble is deprecated.
{% endhighlight %}



{% highlight r %}
plot(silhouette(cutree(agrupamento_h_2d, k = 4), distancias))
{% endhighlight %}

![plot of chunk unnamed-chunk-2](/portifolioAnaliseDeDadosfigure/source/posts/2017-07-21-problema3-checkpoint1/unnamed-chunk-2-2.png)
Após a execução do algoritmo, a melhor solução foi com 3 grupos, pois parece estar melhor separado. Os grupos podem ser classificados em: acima da média (grupo1), razoáveis (grupo2) e abaixo da media. Exemplos de filmes que se encaixam no agrupamento são Creed e Rocky Balboa. Ambos pertencentes ao grupo acima da média sendo filmes muito bem avaliado pelo público. 

{% highlight r %}
filmes2 = agrupamento_h_md = filmes %>% 
    mutate(TITLE_LENGTH = nchar(TITLE)) 


atribuicoes_long %>% 
    filter(k == 3) %>%
    ggplot(aes(x = variavel, y = valor, group = TITLE, colour = grupo)) + 
    geom_point(alpha = .3, size = .5) + 
    geom_line(alpha = .7) + 
    facet_wrap(~ paste("Grupo ", grupo)) + 
    labs(x = "", y = "z-score")
{% endhighlight %}



{% highlight text %}
## Error in eval(lhs, parent, parent): objeto 'atribuicoes_long' não encontrado
{% endhighlight %}

