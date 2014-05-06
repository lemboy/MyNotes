### Установка [Redmine 2.5.1](http://redmine.org/) на чистую систему (Ubuntu 14.04 LTS) с последующим размещением на Heroku

#### Подготовка
Установка git ([источник](https://help.github.com/articles/set-up-git))
```
git config --global user.name "Your Name Here"
git config --global user.email "your_email@example.com"
git config --global credential.helper cache
git config --global credential.helper 'cache --timeout=3600'
```
Генерация SSH-ключей ([источник](https://help.github.com/articles/generating-ssh-keys))
```
ssh-keygen -t rsa -C "your_email@example.com"
ssh-add ~/.ssh/id_rsa
sudo apt-get install xclip
xclip -sel clip < ~/.ssh/id_rsa.pub
```
Добавляем открытый ключ на github и heroku.

Установка Apache и MySQL-Server
```
sudo apt-get install apache2
sudo apt-get install mysql-server
```
Установка Rails ([источник](https://help.ubuntu.com/14.04/serverguide/ruby-on-rails.html))
```
sudo apt-get install rails
sudo apt-get install nodejs
apt-get install libsqlite3-dev
gem install sqlite3 -v '1.3.9'
bundle install
```
Установка Postgresql (необходимо для размещения на Heroku) ([источник](http://technobytz.com/install-postgresql-9-3-ubuntu.html))
```
sudo apt-get install postgresql-9.3 pgadmin3
```

#### Установка Redmine ([источник](http://www.redmine.org/projects/redmine/wiki/RedmineInstall))
```
tar xvfz redmine-2.5.1.tar.gz
```
Создание базы
```
PostgreSQL
CREATE ROLE redmine LOGIN ENCRYPTED PASSWORD 'my_password' NOINHERIT VALID UNTIL 'infinity';
CREATE DATABASE redmine WITH ENCODING='UTF8' OWNER=redmine;
```
Редактирование config/database.yml
```
test:
adapter: postgresql
database: redmine
host: localhost
username: redmine
password: my_password
schema_search_path: public

production:
adapter: postgresql
database: redmine
host: localhost
username: redmine
password: my_password
schema_search_path: public

development:
adapter: postgresql
database: redmine
host: localhost
username: redmine
password: my_password
schema_search_path: public
```
Установка Bundler
```
sudo gem install bundler
```
Переходим в каталог Redmine
```
cd ~/www/redmine-2.5.1
```
??? Если необходим ImageMagic. Предварительно установка библиотеки MagickWand, иначе будет ошибка типа *"An error occurred while installing rmagick (2.13.2), and Bundler cannot continue."*
```
sudo apt-get install libmagickwand-dev
bundle install --without development test
```
Создаем random-key (команда вместо указанной в документации *"rake generate_secret_token"*).
```
bundle exec rake generate_secret_token
```
Во избежание ошибки типа *"An error occurred while installing pg (0.17.1)"* на следующем этапе, ставим PostgreSQL Library
```
sudo apt-get install libpq-dev
```
```
bundle install
```
Создаем структуру БД (команда вместо указанной в документации *"RAILS_ENV=production rake db:migrate"*).
```
bundle exec rake db:migrate RAILS_ENV=production
```
Грузим данные в БД
```
bundle exec rake redmine:load_default_data RAILS_ENV=production
```

#### Переносим на Heroku ([источник](https://devcenter.heroku.com/articles/getting-started-with-rails3))
Ставим Toolbelt
```
wget -qO- https://toolbelt.heroku.com/install-ubuntu.sh | sh
```
Добавляем в Gemfile строку
```
gem 'rails_12factor', group: :production
```
Обновляем зависимости
```
bundle install
```
Добавляем в config/application.rb
```
config.assets.initialize_on_precompile = false
```
Модифицируем Gemfile (добавляем *"gem "pg"*, коммитим секцию *"when postresql"* - будут warnings, неаккуратно, да)

Коммитим/переименовываем .gitignore (неправильно, наверное, потом разберемся)

Добавляем в конец Gemfile (в целях совместимости?)
```
ruby '2.0.0'
```
Редактирование config/database.yml
```
test:
  adapter: postgresql
  encoding: unicode
  database: redmine
  pool: 5
  password: my_password
production:
  adapter: postgresql
  encoding: unicode
  database: redmine
  pool: 5
  password: my_password
development:
  adapter: postgresql
  encoding: unicode
  database: redmine
  pool: 5
  password: my_password
```
Добавляем в git
```
git init
git add .
git commit -m "init"
```
Создаем приложение на Heroku
```
heroku create --addons heroku-postgresql
```
Размещаем приложение
```
git push heroku master
```
Грузим данные в БД
```
heroku run rake db:migrate
```
Открываем
```
heroku open
```

