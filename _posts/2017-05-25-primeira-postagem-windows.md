---
layout: post
title:  Jekyll Primeira Postagem (Windows)
date: 2017-07-08 13:11:35
published: true
tags: [htmlwidgets, r]

---

Neste tutorial, instalaremos Jekyll 3.2.1. Em tutoriais posteriores, publicaremos um site estático no mesmo servidor e, hospedaremos no [Github Pages](https://pages.github.com/).

Primeiro precisamos configurar nossa máquina para instalar o Jekyll.

#Passo 1: Installando Ruby e o Ruby DevKit
*Install Ruby*

Primeiro, clique no link e baixe o instalador do Ruby que corresponde à arquitetura do seu sistema (x86 / x64). Eu aconselho baixar a versão mais atual (2.3.3)

[Ruby for Windows](https://rubyinstaller.org/downloads/)

Execute o instalador e siga as etapas da instalação. Quando você chegar à tela abaixo, certifique-se de verificar a caixa "Adicionar Ruby executáveis para o seu PATH".

*Instale o Ruby DevKit*

Jekyll tem algumas dependências e para atendê-las você precisará instalar o Development Kit.

[Ruby DevKit](https://rubyinstaller.org/downloads/)

O download é um arquivo executável. Quando você executa o arquivo, ele pedirá um destino para os arquivos. Digite um caminho que não tenha espaços nele. Recomendamos algo simples, como *C:\RubyDevKit\*. Clique em Extrair e aguarde até que o processo seja concluído.

Em seguida, você precisa inicializar o DevKit e vinculá-lo à sua instalação Ruby. Abra sua ferramenta de linha de comando favorita e navegue até a pasta na qual você extraiu o DevKit.


{% highlight r %}
cd C:\RubyDevKit
{% endhighlight %}

Detectar instalações Ruby automaticamente e adicioná-las a um arquivo de configuração para a próxima etapa.


{% highlight r %}
ruby dk.rb init
{% endhighlight %}

Instale o DevKit, vinculando-o à sua instalação Ruby.


{% highlight r %}
ruby dk.rb install
{% endhighlight %}

#Instalando o Jekyll

Jekyll se apresenta sob a forma de uma Gem Ruby, que é um pacote de software fácil de instalar. Para instalar Jekyll e todas as suas dependências padrão, inicie sua ferramenta de linha de comando favorita e digite o seguinte comando.


{% highlight r %}
gem install jekyll
{% endhighlight %}

#Configurando RStudio
Após ter configurado sua máquina, vamos agora para as configurações no RStudio.
Primeiro instale as dependências abaixo:


{% highlight r %}
# Required for the htmlwidgets wrapper functions -----------------------------
# install.packages("devtools")
devtools::install_github("brendan-r/brocks")

# For knitr-jekyll, and the htmlwidgets stuff --------------------------------
install.packages(c(
  "servr",
  "knitr",
  "metricsgraphics",
  "leaflet",
  "threejs",
  "maps"
))
{% endhighlight %}


#Obtendo um repositório
Faça um fork do repositório [knitr-jekyll](https://github.com/yihui/knitr-jekyll) para que você possa gerar seus post diretamente de um RMarkdown. 

Essa será a estrutura do seu repositório:


{% highlight r %}
.
├── _config.yml
├── _data
|   └── members.yml
├── _drafts
|   ├── begin-with-the-crazy-ideas.md
|   └── on-simplicity-in-technology.md
├── _includes
|   ├── footer.html
|   └── header.html
├── _layouts
|   ├── default.html
|   └── post.html
├── *_source*
|   ├── path-blog
├── _posts
|   ├── 2007-10-29-why-every-programmer-should-play-nethack.md
|   └── 2009-04-26-barcamp-boston-4-roundup.md
├── _sass
|   ├── _base.scss
|   └── _layout.scss
├── _site
├── .jekyll-metadata
└── index.html 
{% endhighlight %}

A configuração mostrada acima será a do seu repositório. O arquivo *_config.yml* será onde você irá configurar as rotas do seu projeto, é bastante simples


{% highlight r %}
# Site settings
title: Your awesome title
email: your-email@domain.com
description: > # this means to ignore newlines until "baseurl:"
  Write an awesome description for your new site here. You can edit this
  line in _config.yml. It will appear in your document head meta (for
  Google search results) and in your feed.xml site description.
baseurl: "/knitr-jekyll" # the subpath of your site, e.g. /blog/
url: "http://yourdomain.com" # the base hostname & protocol for your site
twitter_username: jekyllrb
github_username:  jekyll
permalink: /:year/:month/:title.html

# Build settings
markdown: kramdown
{% endhighlight %}

Com as rotas configuradas, é hora de gerar o primeiro post do seu blog. Para isso, vá na pasta _source e crie um diretório do seu projeto.

**[Utilize seu terminal]**

{% highlight r %}
arthur@pc:~/maisumsitesobre/_source/$ mkdir primeira-postagem
{% endhighlight %}

Dentro deste diretório você ira salvar o seu RMarkdown. O seu arquivo.Rmd deve seguir a seguinte estrutura básica:


{% highlight r %}
---
layout: post
title:  Primeira Postagem (Ubuntu)
date: `r Sys.time()`
published: true
tags: [htmlwidgets, r]
---

e depois os R code chunks.
{% endhighlight %}

O nome do seu RMarkdown deverá seguir o padrão year-month-day-nome.Rmd, por exemplo 2017-05-24-minha-primeira-postagem.Rmd

**[Utilize o terminal do RStudio]**
Com o seu markdown pornto é hora de gerar o primeiro post. Verifique se sua sessão do RStudio está setada para o diretório do seu site: 


{% highlight r %}
> setwd("~/workspace/maisumsitesobre")
{% endhighlight %}

Execute o comando:

{% highlight r %}
#gerar postagem do blog
> brocks::blog_gen()

Configuration file: /home/arthur/workspace/projetos/maisumsitesobre/_config.yml
            Source: /home/arthur/workspace/projetos/maisumsitesobre
       Destination: /home/arthur/workspace/projetos/maisumsitesobre/_site
 Incremental build: disabled. Enable with --incremental
      Generating... 
                    done in 0.486 seconds.
 Auto-regeneration: disabled. Use --watch to enable.
NULL
{% endhighlight %}

Se tudo der certo, um arquivo .md será gerado no diretório do seu projeto, então você poderá subir um servidor com o seu site e ver o seu post publicado, basta executar o seguinte comando no terminado do RStudio:


{% highlight r %}
#subir o servidor no RStudio
> brocks::blog_serve()
{% endhighlight %}

Porém o site será levantando utilizando o terminal da IDE, o ideal é que você utilize uma janela do seu terminal para deixar o site rodando. 

**[Utilize seu terminal]**


{% highlight r %}
arthur@pc:~/workspace/maisumsitesobre$ jekyll serve
{% endhighlight %}

**IMPORTANTE** antes de dar push, executar o comando **brocks::blog_serve()**
Agora que você conseguiu criar sua primeira postagem, basta dar push nos seus arquivos. 

Esse tutorial foi desenvido baseado em:
[Run Jekyll on Windows](http://jekyll-windows.juthilo.com/)
