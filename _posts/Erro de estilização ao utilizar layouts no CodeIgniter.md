# Erro de estilização ao utilizar layouts no CodeIgniter

27/05/2013

Summary
----

Ao adaptar layouts com o framework Codeigniter, várias pessoas estão tendo problemas com a estilização css. Está é uma dúvida corriqueira e, por este motivo, estou criando este poste para sanar este problema. Porque ocorre este problema? Ao acessar sua aplicação a partir da url base, o navegar entende que seu diretório de estilo está locado na raiz de sua aplicação e pode ser acessado... 

Body
----

Ao adaptar layouts com o framework Codeigniter, várias pessoas estão tendo problemas com a estilização css. Está é uma dúvida corriqueira e, por este motivo, estou criando este poste para sanar este problema.

Porque ocorre este problema?
Ao acessar sua aplicação a partir da url base, o navegar entende que seu diretório de estilo está locado na raiz de sua aplicação e pode ser acessado diretamente pela url -- www.seusite.com.br/css. Contudo, quando o acesso e feito por meio de outra url -- www.site.com.br/seucontroller/seusmetodod/ -- o navegador subintende que seu diretorio de estilo utiliza todo caminho para o acesso. Logo, para corrigir este erro, deve-se informar ao mesmo que a base para o diretório de estilo é a raiz de sua aplicação e não a url acessada.

Para isso deve utilizar a tab Base do html, desta forma:

    <!doctype html>
      <html lang="en">
        <head>
          <!-- Como utilizar a tag Base -->
          <base href="<?php echo base_url(); ?>">
          <meta charset="UTF-8" />
          <title>Document</title>
        </head>
       <body>
       </body>
    </html>

Utilizando desta forma o erro não ocorrerá. Outra forma de corrigir o erro é utilizar caminho absoluto no link de seus estilos. Toda vida, a tag Base é uma das alternativas que mais utilizado.

* CodeIgniter
* Frameworks
* PHP 