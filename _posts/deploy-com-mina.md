# Usando o Mina para deploys de aplicações Ruby on Rails

[Mina](http://nadarei.co/mina/) é uma ferramenta que permite criar e executar scripts para o gerenciamento de implantações em servidores via SSH. Ao contrário de outras ferramentas semelhantes, como o [Capistrano](http://capistranorb.com/) ou [Vlad](https://github.com/seattlerb/vlad), Mina cria somente uma sessão SSH para cada deploy em execução, tornando seu deploy mais eficiente. 
Mina é escrito em Ruby, porém, não se limita apenas a esta linguagem, podendo ser usando para qualquer tipo de implantação, possuindo apenas e tão somente como restriçao o uso de SSH no servidor.

## Primeiro passo:

Com o Ruby instalado, podemos instalar o Mina como uma gem normal. Para tanto, use o comando abaixo:

    gem install mina

ou adicione em seu `Gemfile` e em seguida execute o `bundle install`

    gem "mina"

## Segundo passo:

Agora vamos configurar o Mina. Dentro da raiz de sua aplicação, execute o comando abaixo. Este irá copiar o arquivos `deploy.rb` pré configurado. (Para o arquivos default, execute `mina init`)

    wget -cP config/  https://gist.githubusercontent.com/luizpicolo/75783f8a7367bdeee786/raw/d3c56dbec4a1d4b26acf22f0a9c3e504dfb8203d/deploy.rb

Por fim, em nosso diretório `config`, teremos o arquivos `deploy.rb` com o seguinte conteúdo:

	require 'mina/bundler'
	require 'mina/rails'
	require 'mina/git'
	require 'mina/rvm'

	# Ruby Version
	set :ruby_version, ''

	# Repository project
	set :repository, ''

	# Server Production
	task :production do
	  set :rails_env, 'production'
	  set :user, ''
	  set :domain, ''
	  set :deploy_to, ''
	  set :branch, ''
	end

	# Server Staging
	task :staging do
	  set :rails_env, 'staging'
	  set :user, ''
	  set :domain, ''
	  set :deploy_to, ''
	  set :branch, ''
	end

	# Fix
	set :term_mode, nil

	# Manually create these paths in shared/ (eg: shared/config/database.yml) in your server.
	# They will be linked in the 'deploy:link_shared_paths' step.
	set :shared_paths, ['config/database.yml', 'log', 'tmp', 'config/application.yml', 'config/secrets.yml']

	# This task is the environment that is loaded for most commands, such as
	# `mina deploy` or `mina rake`.
	task :environment do
	  # If you're using rbenv, use this to load the rbenv environment.
	  # Be sure to commit your .rbenv-version to your repository.
	  # invoke :'rbenv:load'

	  # For those using RVM, use this to load an RVM version@gemset.
	  invoke :"rvm:use[ruby-#{ruby_version}@#{user}]"
	end

	# Put any custom mkdir's in here for when `mina setup` is ran.
	# For Rails apps, we'll make some of the shared paths that are shared between
	# all releases.
	task :setup => :environment do
	  queue! %[mkdir -p "#{deploy_to}/shared/log"]
	  queue! %[chmod g+rx,u+rwx "#{deploy_to}/shared/log"]

	  queue! %[mkdir -p "#{deploy_to}/storage"]
	  queue! %[chmod g+rx,u+rwx "#{deploy_to}/storage"]

	  queue! %[touch "#{deploy_to}/storage/index.html"]

	  queue! %[mkdir -p "#{deploy_to}/shared/config"]
	  queue! %[chmod g+rx,u+rwx "#{deploy_to}/shared/config"]

	  queue! %[mkdir -p "#{deploy_to}/shared/pids"]
	  queue! %[chmod g+rx,u+rwx "#{deploy_to}/shared/pids"]

	  queue! %[mkdir -p "#{deploy_to}/shared/tmp"]
	  queue! %[chmod g+rx,u+rwx "#{deploy_to}/shared/tmp"]

	  queue! %[touch "#{deploy_to}/shared/config/database.yml"]
	  queue  %[echo "-----> Be sure to edit 'shared/config/database.yml'."]

	  queue! %[touch "#{deploy_to}/shared/config/application.yml"]
	  queue  %[echo "-----> Be sure to edit 'shared/config/application.yml'."]

	  queue! %[touch "#{deploy_to}/shared/config/secrets.yml"]
	  queue  %[echo "-----> Be sure to edit 'shared/config/secrets.yml'."]
	end

	desc "Deploys the current version to the server."
	task :deploy => :environment do
	  deploy do
		invoke :'git:clone'
		invoke :'deploy:link_shared_paths'
		invoke :'bundle:install'
		invoke :'rails:db_migrate'
		invoke :'rails:assets_precompile'

		to :launch do
		  queue %[echo -n "-----> Creating new restart.txt: "]
		  queue "touch #{deploy_to}/shared/tmp/restart.txt"
		end
	  end
	end

	# Roolback
	desc "Rolls back the latest release"
	task :rollback => :environment do
	  queue! %[echo "-----> Rolling back to previous release for instance: #{domain}"]

	  # Delete existing sym link and create a new symlink pointing to the previous release
	  queue %[echo -n "-----> Creating new symlink from the previous release: "]
	  queue %[ls "#{deploy_to}/releases" -Art | sort | tail -n 2 | head -n 1]
	  queue! %[ls -Art "#{deploy_to}/releases" | sort | tail -n 2 | head -n 1 | xargs -I active ln -nfs "#{deploy_to}/releases/active" "#{deploy_to}/current"]

	  # Remove latest release folder (active release)
	  queue %[echo -n "-----> Deleting active release: "]
	  queue %[ls "#{deploy_to}/releases" -Art | sort | tail -n 1]
	  queue! %[ls "#{deploy_to}/releases" -Art | sort | tail -n 1 | xargs -I active rm -rf "#{deploy_to}/releases/active"]

	  queue %[echo -n "-----> Creating new restart.txt: "]
	  queue "touch #{deploy_to}/shared/tmp/restart.txt"
	end

Agora, altere as seguintes configurações. Este arquivo foi configurado para utilizar o [RVM](https://rvm.io/), caso vocẽ esteja utilizando outro *Version Mananger*, troque o `require 'mina/rvm'`, como, por exemplo, `require 'mina/rbenv'`


	# Ruby Version
	set :ruby_version, ''

	# Repository project
	set :repository, ''

	# Server Production
	task :production do
	  set :rails_env, 'production'
	  set :user, ''
	  set :domain, ''
	  set :deploy_to, ''
	  set :branch, ''
	end

	# Server Staging
	task :staging do
	  set :rails_env, 'staging'
	  set :user, ''
	  set :domain, ''
	  set :deploy_to, ''
	  set :branch, ''
	end

No meu caso eu sempre crio um usuário para cada aplicação. Logo, teriamos um usuário chamado `my_app`.

	```ruby
	# Ruby Version
	set :ruby_version, '2.2.1'

	# Repository project
	set :repository, 'git@github.com:luizpicolo/my_app.git'

	# Server Production
	task :production do
	  set :rails_env, 'production'
	  set :user, 'my_app'
	  set :domain, 'production.luizpicolo.com.br'
	  set :deploy_to, '/home/my_app/public_html/railsapp/'
	  set :branch, 'deploy'
	end

	# Server Staging
	task :staging do
	  set :rails_env, 'staging'
	  set :user, 'my_app'
	  set :domain, '192.168.1.150'
	  set :deploy_to, '/home/my_app/public_html/railsapp/'
	  set :branch, 'master'
	end

Após alterada as configurações, salve o arquivos e execute o comando acompanhado do ambiente qual será feito o deploy

    mina production setup

ou

    mina staging setup
	
Após executar o comando você terá algo semelhante a isto;

![repositorios](https://cdn.rawgit.com/luizpicolo/posts/master/images/mina_directories.png)

Sem mais delongas, os arquivos que deverão ser alterados ficam dentro de `shared/config` onde teremos trẽs arquivos:

	# Para quem utiliza a gem 'figaro'
	application.yml

	# Configurações do banco de dados
	database.yml

	# Hash secret key  
	secrets.yml

Altere da forma desejada e, por fim, execute o comando:

    mina production deploy

ou

    mina staging deploy
	
# Conclusão

Se vocẽ seguiu todos os passos citados acima sua aplicação poderá ser acessada em seu domínio principal. Lembrando que seu servidor deve estar configurado corretamente.

Erros ou melhorias no texto podem ser sugeridos por meio de PRs em [https://github.com/luizpicolo/posts](https://github.com/luizpicolo/posts)

