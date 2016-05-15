---
layout: post
title:  "Utilizando Generators para criação de modelos e migrações em aplicações Rails "
date:   2014-03-28 07:11:14 -0400
categories: Ruby on Rails MongoDB Sinatra Ruby Bancos Relacionais Bancos não Relacionais
---

Os generators são ótimas ferramentas tanto para iniciantes como para desenvolvedores avançados. Neste texto mesmo não sendo profundo no domínio dos geradores, demostrar-se-a algumas formas básicas e avançadas de como utilizá-los.<!--more-->

Ao iniciar o desenvolvimento de aplicações com Ruby on Rails é completamente compreensível que iniciantes busquem em fórum, listas de discussão, ou diretamente com outros desenvolvedores, informações relacionadas a como dar os primeiros passos com a linguagem ou com o framework. Algo intrigante, e não unanime entre os desenvolvedores mais experientes, esta no fato da utilização ou não dos *[generators](http://guides.rubyonrails.org/generators.html)*, na qual, alguns afirmam que ao invés de utilizar os geradores inicialmente, o interessante seria criar todos os controllers, modelos e migrações manualmente, buscando compreender melhor o funcionamento e os relacionamentos em sua aplicação. Nem todos concordo 100% com esta afirmação, sendo os geradores ótimas ferramentas que facilitam o desenvolvimento e a por em prática o DRY.

> DRY – “Don’t Repeat Yourself” – suggests that writing the same code over and over again is a bad thing.

Dessa forma, mesmo gerando os arquivos automaticamente, os iniciantes podem fazer um **review** do código e aprender muito com ele.
Portanto, neste texto, será demostrada algumas formas de utilizar os geradores e como eles podemos agilizar o desenvolvimento no dia a dia.

## Iniciando com um simples exemplo

    rails g model user name

Este comando basicamente gerará um modelo `User` contendo como atributo `name`. Porém, juntamente com este comando, além do modelo gerado, também será gerada as migrações para a criação tabelas no banco de dados e, caso use, os testes de modelo e a factory. **(Para mais detalhes veja em sua aplicação os arquivos criados)**

Pode-se também gerar mais atributos seguindo a ideia do primeiro comando e adicionando outros atributos desejados.

	rails g model user name email password age:integer

Outros tipos também podem ser usados

    integer
    primary_key
    decimal
    float
    boolean
    binary
    string
    text
    date
    time
    datetime
    timestamp

Algo importante dentro da orientação a objeto é a herança. Com os geradores, nós podemos também gerar automaticamente um model que herda características de outro modelo, para tanto, basta utilizar o parâmetro `--parent`

	rails g model admin --parent user

A qual será obtido como resultando

```ruby
class Admin < User
end
```

## Uso avançado

Adicionar indices ao criar seu banco de dados é algo recomendando para qualquer nível de aplicação. No Rails, basta adicionar o seguinte

	rails g model user email:index cpf:decimal:index

Caso necessite ser único, como no caso no CPF, basta utilizar da seguinte forma

	rails g migration add_column_cpf_to_user cpf:integer:uniq:index

Como resultado a migração obtida conterá o seguinte código

```ruby
class AddCpfToUser < ActiveRecord::Migration
  def change
    add_column :users, :cpf, :integer
    add_index :user, :cpf, unique: true
  end
end
```

Caso seja necessário limitar uma string por exemplo, pode-se usar desta forma

	rails g model user username:string{30}

Como resultando

```ruby
class CreateUser < ActiveRecord::Migration
  def change
    create_table :user do |t|
      t.string :name, limit: 30
      t.timestamps null: false
    end
  end
end
```

Para combinar o resultando acima com uma string limitada, pode-se utilizar da seguinte forma

	rails g model user username:string{30}:uniq

## Associações

Para facilitar a criação dos relacionamentos entre as entidades da aplicação por meio dos geradores, as referencias  podem ser geradas da seguinte forma

	rails g model news category:references

Desta foram, o resultado será

```ruby
class CreateNews < ActiveRecord::Migration
  def change
    create_table :news do |t|
      t.references :category
      t.timestamps
    end
    add_index :news, :category_id
  end
end
```

## Conclusão

Portanto, percebe-se claramente que os geradores do Rails realmente são ferramentas úteis para agilizar o desenvolvimento. Logo, não há motivos para a grande preocupação de iniciantes em querer gerar estes códigos manualmente. Caso haja a necessidade, analise o código gerado e tire suas conclusões.

## Referência

[http://railsguides.net/advanced-rails-model-generators/]()

Encontrou um erro? [Envie seu Pull Request.](https://github.com/luizpicolo/luizpicolo.github.io)
