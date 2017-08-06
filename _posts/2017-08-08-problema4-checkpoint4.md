---
layout: post
title:  Problema 4 checkpoint 4
date: 2017-08-05 11:11:39
published: true
tags: [htmlwidgets, r]
---

#Os dados
Os dados compõe uma amostra de dados da atividade global do github, neles contém a contagem de quantas pessoas editaram arquivos para cada extensão de arquivo no período de 2016 à 2017. Estes dados estão disponíveis [aqui](https://raw.githubusercontent.com/nazareno/fpcc2/master/datasets/github-users-committing-filetypes.csv). Objetivo dessa análise  é aprender a aplicar testes de hipótese, interpretar o p-valor e relacionar os resultados de testes de hipótese e ICs.

{% highlight r %}
amostra <- read.csv("../../dados/github-users-committing-filetypes.csv")

#formatando data
amostra = amostra %>%
        mutate(date = as.Date(paste(month_day,the_month,the_year),format = "%d %m %Y")) %>%
        mutate(week_day = (weekdays(as.Date(date))))%>%
        select(file_extension, week_day, date, users) 

#filtrando dados de interesse
java_2016 = filter(amostra, file_extension == "java", 
                          month(date) %in% c("1","2","3","4"),
                          year(date) == "2016") 

java_2017 = filter(amostra, file_extension == "java",
                          month(date) %in% c("1","2","3","4"),
                          year(date) == "2017") 
{% endhighlight %}
#Popularidade de Java
O parâmetro utilizado para mensurar a popularidade de `java` foi a quantidade média de edições por ano.

###Intervalo de Confiança

Análisando a amostra por meio do método bootstrap estimei os `intervalos de confiança` para popularidade de `java`, por ano. A análise foi realizada com confiança de 95%, o bootstrap gerou 10.000 reamostragens. Os resultados foram bem expressivos, a média de edições foi estimada em [3845 ; 4010] para 2016 e [3135 ; 3444] para 2017. A diferença de popularidade é sigficativa, a estimativa aponta uma queda na popularidade de `java`.

{% highlight r %}
ic_java_2016 =  bootstrap(java_2016, mean(users)) %>% 
                    CI.percentile(probs = c(.025, .975)) %>% 
                    as.data.frame() %>%
                    mutate(year = "2016")
 
ic_java_2017 =  bootstrap(java_2017, mean(users)) %>% 
                    CI.percentile(probs = c(.025, .975)) %>% 
                    as.data.frame() %>%
                    mutate(year = "2017")

data.frame(rbind(ic_java_2016, ic_java_2017)) %>% 
  ggplot(aes(x = year, ymin = X2.5., ymax = X97.5., color = year)) + 
  geom_errorbar(width = .2)+
  ggtitle("Popularidade de java 2016 - 2017")
{% endhighlight %}

![plot of chunk unnamed-chunk-2](/portifolioAnaliseDeDados/figure/source/posts/2017-08-08-problema4-checkpoint4/unnamed-chunk-2-1.png)

### Teste de hipótese
Análisando a amostra, agora por teste de hipótese, primeiro calculei a diferença observada nas médias de popularidade em 2016 e 2017. 

{% highlight r %}
java = filter(amostra, file_extension == "java") %>% 
        group_by(year(date)) %>% 
        summarise(media = mean(users))

diff_media_observada = diff(java$media)
diff_media_observada
{% endhighlight %}



{% highlight text %}
## [1] -287.4896
{% endhighlight %}
Para este caso a `hipótese nula - H0` é seguinte situação: *não haver associação nenhuma entre popularidade e ano*. Então analisei o quão frequente seria encontrar uma diferença do mesmo tamanho da observada na amostra considerando `h0`. Caso a diferença observada na amostra aconteça facilmente na hipótese nula, então não há evidência forte de associação: o que observamos acontece também quando não há associação. 

{% highlight r %}
set.seed(1)
diffs = replicate(10000, {
  medias = filter(amostra, file_extension == "java") %>% 
    mutate(id_embaralhado = sample(year(date), n())) %>% 
    group_by(id_embaralhado) %>% 
    summarise(media = mean(users))
  media_2016 = medias %>% 
    filter(id_embaralhado == "2016")
  media_2017 = medias %>% 
    filter(id_embaralhado == "2017")
  return(media_2016$media - media_2017$media)
})

#Distribuição amostral da média de popularidade considerando a hipótese nula
tibble(diferenca = diffs) %>% 
  ggplot(aes(x = diferenca)) + 
  geom_histogram(bins = 30) + 
  geom_vline(xintercept = diff_media_observada, size = 2)
{% endhighlight %}

![plot of chunk unnamed-chunk-4](/portifolioAnaliseDeDados/figure/source/posts/2017-08-08-problema4-checkpoint4/unnamed-chunk-4-1.png)

{% highlight r %}
#sum(abs(diffs) >= abs(diff_media_observada)) / length(diffs)
{% endhighlight %}
O `p-valor` (também chamado de nível descritivo ou probabilidade de significância), é a probabilidade de se obter uma estatística de teste igual ou mais extrema que aquela observada em uma amostra, sob a hipótese nula. Para este caso seria,  a probabilidade de se obter uma diferença maior ou igual a observada na amostra considenrando `H0`. Por meio da distribuição amostral da média de popularidade em `H0`, estimei o p-valor:

{% highlight r %}
oneway_test(users ~ as.factor(year(date)), 
            data = rbind(java_2016, java_2017), 
            distribution = "exact") %>% 
            pvalue()
{% endhighlight %}



{% highlight text %}
## [1] 5.498467e-07
{% endhighlight %}
Avaliando o `p-valor` estimado, a diferença observada na amostra é significativamente improvável na `hipótese nula`, logo `H0` é rejeitada. Com isso pode-se concluir que existe um indicativo de associação entre popularidade e ano; e que a diferença observada é significativa. Entre outras palavras existe um indicativo de que há uma diferença significativa na popularidade de `java` entre 2016 e 2017. 

#Comparando os Resultados
Tanto a análise por `teste de hipótese` quanto  por `intervalo de confiança` apontam para uma diferença na popularidade de `java` entre 2016 e 2017. A principal diferença entre os métodos de análise é: `intervalo de confiança` não informa o quanto significativa (em termos de tamanho) é essa diferença, isso implica em certa imprecisão quanto a relevância do resultado obtido. Por outro lado, o método de análise por `intervalo de confiança` estima um intervalo para estatística de interesse, com isso é possível perceber quão significativa (em termos de tamanho) é essa diferença, permitindo uma conclusão bem mais precisa sobre a relevância do resultado.
