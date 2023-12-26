# Laravel9テストレポジトリ

## 概要
Laravel9をDockerで導入したテストレポジトリです。   
具体的な内容は下記のZennの記事をご覧ください。  
[Laravel9をDockerで導入してみよう!](https://zenn.dev/eguchi244_dev/articles/laravel-and-docker-introduction-20230822)

## 目的＆内容
下記のシステム構成での実験やテストに使用することを目的にしています。  
この README は Github から環境構築するための手順を記載したものです。

## システム構成
【システム構成】
* フレームワーク ： Laravel Framework 9.x.x
* フロントエンドフレームワーク ： LaravelVite
* データベース ： MYSQL 5.7.36
* DB管理ツール ： phpMyAdmin latest
* PHP ： PHP 8.0.x
* Nginx ： Nginx latest
* Node.js ： node 14.18-alpine

【ディレクトリ構成】
```
Laravel9-TestPJ（ルートディレクトリ）
├── docker-compose.yml
├── docker 
│   ├── php 
│   │   ├── Dockerfile 
│   │   └── php.ini 
│   └── nginx 
│       └── default.conf 
├── phpMyAdmin
└── src 
    └──  Laravel9TestProject（Laravelのプロジェクトディレクトリ）
```

## 前提条件
PCに下記がインストールと設定がされていること。

* Linux/Unix開発環境
   * Windows ： WSL2（Ubuntu）, Curl
   * Mac ： HomeBrew
* Docker, Docker-compose
* composer
* npm 

## 環境構築
任意の作業フォルダ内で下記を実施してください。

1. GitHubよりプロジェクトをダウンロードする
```
$ git clone https://github.com/eguchi244/Laravel9-TestPJ.git
$ cd Laravel9-TestPJ
```
2. Dockerをビルドする
```
$ docker-compose up -d
```
3. Laravel(PHP)のセットアップをする
```
# PHPコンテナにログインする
$ docker-compose exec php bash
root@~# cd Laravel9TestProject
# Composer（PHPパッケージ）をインストールしてVenderフォルダを作成する
root@~# composer install
# .env.example ファイルをコピーして .env ファイルを生成する
root@~# cp .env.example .env
# キージェネレートする
# これを行わないと起動時に「No application encryption key has been specified.」エラーが表示される
root@~# php artisan key:generate
# インストールの確認をする
root@~# php artisan --version
# 念の為に権限を与える
# PermissionDeniedエラーがでる場合の対処方法
root@~LaravelReactProject # chown www-data ./ -R
# PHPコンテナからログアウトする
root@~# exit
```
4. ポートが競合する場合は 必要に応じて .env ファイルを編集する
- ポートが競合する場合は下記のような書き方で競合しないポートに書き換えてください
```
APP_URL=http://localhost:8880
APP_PORT=8880
```
- .env を書き換えてる場合は再度ターミナルでキージェネレートする
```
# PHPコンテナにログインする
$ docker-compose exec php bash
root@~# cd Laravel9TestProject
# キージェネレートする
root@~# php artisan key:generate
# PHPコンテナからログアウトする
root@~# exit
```
5. migrationを実行する
```
# PHPコンテナにログインする
$ docker-compose exec php bash
root@~# cd Laravel9TestProject
# migrationを実行する
root@~# php artisan migrate:fresh
# PHPコンテナからログアウトする
root@~# exit
```
6. データーベースを確認する
```
# DBコンテナにログインする
$ docker-compose exec db bash
# MySQLにログインする(.envでアカウントを確認する)
root@~# mysql -u <ユーザー名> -p --default-character-set=utf8mb4
Enter password: <DBのパスワードを入力> 
# DBを確認する
mysql> show databases;
# DBを選択する
mysql> use mysql_test_db;
# テーブルを確認する
mysql> SHOW TABLES;
# MySQLからログアウトする
mysql>quit
# DBコンテナからログアウトする
root@~# exit
```

## 動作確認
1. ブラウザでLaravelのデフォルトページの表示を確認する  
ブラウザに [http://localhost/](http://localhost/) でアクセスして表示されればOKです。
2. phpMyAdmin（DB管理ツール）　の表示を確認する  
ブラウザに [http://localhost:8080](http://localhost:8080) でアクセスして表示されればOKです。

以上です。