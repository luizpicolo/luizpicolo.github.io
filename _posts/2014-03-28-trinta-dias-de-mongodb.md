---
layout: post
title:  "Trinta dias de MongoDB"
date:   2014-03-28 07:11:14 -0400
categories: Ruby on Rails MongoDB Sinatra Ruby Bancos Relacionais Bancos não Relacionais
---

Em grande parte da minha carreira como desenvolvedor, e acredito que esta realidade faz parte da carreira de vários outros desenvolvedores, trabalhei, e ainda trabalho, com banco de dados relacionais. Criar tabelas, fazer os relacionamentos e executar querys para obter o resultado desejado é comum para quem deseja seguir está profissão. Porém, essa realidade, durante o caminhar de alguns projetos pessoais, foi alterada, e me encontrei mergulhado <!--more--> na “beleza” dos bancos de dados não relacionais. Neste texto, demostrarei um pouco desta experiência e quanto relevante a mesma foi. Quero deixar claro que não sou especialista em banco de dados não relacionais e que neste texto quero repassar a experiência que tive ao utilizar este novo paradigma.

Banco de dados relacionais são uma realidade. Criado em meados dos anos 70, e ainda adotado por grandes parte das empresas, tiveram como base para a sua criação a teoria dos conjuntos. A grosso modo, sua principal característica é a integridade, em que os dados, por meio de restrições, tendem a ser manter íntegros e consistentes. Contudo, devido ao grande crescimento dos dados, principalmente gerados pela internet, os sistemas gerenciadores de banco de dados tradicionais tiveram problemas com a escalabilidade e novos conceitos surgiram para sanar este problema. Neste contexto, foram pensados e idealizados os banco de dados não relacionais, os quais deram maior liberdade para que as aplicações pudessem crescer. Porém, liberdade e não libertinagem, pois a integridade ainda deveria ser mantida. Como diz Marília Cavalcante Kokay:

> Os principais problemas encontrados com a utilização do Modelo Relacional estão principalmente na dificuldade de conciliar o tipo de modelo com a demanda da escalabilidade que está cada vez mais frequente.

Após esta pequena introdução, digo para você que está lendo este texto que minha experiência iniciasse com um projeto que, em primeira instância, tinha o nome de [Cloud University](https://github.com/luizpicolo/agenda-universitaria-ruby-sinatra). Este experimento com uma nova linguagem, que neste caso foi Ruby com a DSL Sinatra, e um banco de dados não relacional, o [MongoDB](https://www.mongodb.org/), surgiu de uma ideia que tivemos em nosso curso superior, no [instituto Federal de educação, ciência e tecnologias de Mato Grosso do sul](http://www.ifms.edu.br/), de compartilhar compromissos e tarefas diárias no meio acadêmico. Bem, a ideia aparentemente não possuía complicações para sua implementação e poderia ser feita com um banco relacional não exigindo o uso de um novo paradigma. Contudo, ao nível de conhecimento, utilizar um banco não relacional seria, e realmente foi, muito vantajoso.

Durante 30 dias de projeto as vantagens da utilização foram impressionante. Comparando com a modelagem relacional ganhou-se tempo no desenvolvimento da aplicação, sendo que apenas persistimos as entidades no banco sem a necessidade de se gerar as tabelas e consequentemente criar os relacionamentos, o qual foram feitos diretamente nos modelos.

Outro fato, posso dizer um erro ao ponto de vista de engenharia, foi que o projeto se tornou volátil e novos atributos surgiam (repito, isso foi um erro de projeto). Neste sentido, a utilização de um banco não relacional orientado a documento foi extremamente útil, pois apenas eram inclusos os atributos novos e tudo estava funcionando sem campos vazios no banco tudo graças ao Schema Free. Em questões de escalabilidade, mesmo tendo citado-a na introdução deste texto, as mesmas não pode ser testada, pois o sistema foi descontinuado com apenas 150 210 usuários cadastrados.

## Conclusão

Portanto, o mongoDB se tornou um peça chave para o escopo volátil que o projeto se tornou. Mas, de longe posso afirmar que ele é melhor que um banco relacional ou ao contrário, pois cada projeto deve buscar a melhor tecnologia para o sanar o problema proposto. Logo, a cada novo projeto devemos analisar as tecnologias que possam ser as melhores.

Esse texto pode possuir caracteristicas contrárias ao texto [Xiismo Tecnologico](http://luizpicolo.com.br/2014/02/27/o-xiismo-tecnologico/#.UzWtdFRdWSR), porém afirmo que não, ambos fazem parte de tudo aquilo que acredito sobre as novas tecnologias.
