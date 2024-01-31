## クローンする
`git clone git@github.com:akaoni0000/docker-rails-mysql.git 好きなディレクトリ名`
## コンテナを作成
railsプロジェクトを作成<br>
`docker-compose run app bash`<br>
コマンドを入力<br>
`rails new . --force --database=mysql`<br>
webpackerをインストール<br>
`rails webpacker:install`<br>

## railsファイルを変更する<br>
## database.ymlの一部を変更
default: &default<br>
  adapter: mysql2<br>
  encoding: utf8mb4<br>
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %><br>
  username: root<br>
  password: `password`<br> 
  host: `db`<br>
  passwordとhostのところを変更する dbというのはdocker-compose.ymlに書いてあるサービス名<br>
  
## dockerコマンドを打ち込む
イメージを作成<br>
`docker-compose build`<br>
コンテナを起動<br>
`docker-compose up -d`

## データベースエラーがでたら
コンテナに入って<br>
`rails db:create`<br>
`rails db:migrate`

## Webpacker::Manifest::MissingEntryError
対処法<br>
`rails webpacker:compile`


  
