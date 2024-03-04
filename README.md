## クローンする
`git clone https://github.com/akaoni0000/docker-rails-mysql-practice.git 好きなディレクトリ名`
## コンテナを作成
railsプロジェクトを作成<br>
`docker-compose run app bash`<br>
コマンドを入力<br>
`rails new . --force --database=mysql`<br>
webpackerをインストール<br>
`rails webpacker:install`<br>

## railsファイルのdatabase.ymlの一部を変更
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

これでエラーがでたら
①config/boot.rb ファイルの
`require bootsnap/setup をコメントアウト`
②tmp/cathc/bootsnap/以下のファイルを全て消去

## Webpacker::Manifest::MissingEntryError
対処法<br>
`rails webpacker:compile`

## githubのpush先を変更することを忘れない、このリポジトリにpushはしない。
`git remote set-url origin リポジトリのurl.git`

## bootsnap系のエラーの対処
①config/boot.rbファイルを変更<br>
`require bootsnap/setup`の部分をコメントアウト<br>
②tmp/catch/bootsnap/以下のファイルを消す

## railsのview画面の左上のmsを消す
Gemfileの<br>
`gem 'rack-mini-profiler', '~> 2.0'` をコメントアウトしてbundle install、docker-compose stop、docker-compose up -dを実行

## コンテナに入る
コンテナが起動している場合<br>
`docker exec -it コンテナ名 bash`<br>
コンテナがexitしている場合<br>
`docker-compose run app bash`

binding.pryについて
nginxがあるときは使えない
使いたい時はnginxを消すしかない
gem "pry-byebug"を追加
docker-compose.ymlを以下のように変更

version: '3'
services:
  app:
    build:
      context: .
    # command: bundle exec puma -C config/puma.rb
    #nginxなし~追加~
    command: bundle exec rails s -p 3000 -b 0.0.0.0
    ports:
      - 80:3000
    #nginxなし~追加~

    volumes:
      - .:/myapp
      - tmp-data:/myapp/tmp
    depends_on:
      - db
    tty: true #binding.pryを使うため
    stdin_open: true #binding.pryを使うため
  db:
    platform: linux/x86_64 #m1だとこの記述が必要
    image: mysql:5.7
    environment: 
      MYSQL_ROOT_PASSWORD: password
    volumes:
      - db-data:/var/lib/mysql
volumes:
  public-data:
  tmp-data:
  db-data:
使いたいところでbinding.pryを書く docker attach コンテナ名

コンテナがexitするとき
docker logs コンテナ名でログを確認する exitしているものでもみれる docker-compose run app bashでexitしているコンテナにも入れる

## ログファイル
docker attach コンテナ名でログがリアルタイムでみれる<br>
もしくは/log/development.log<br>
これをtailコマンドでみれる<br>
`tail -f development.log`
