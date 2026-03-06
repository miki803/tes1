リポジトリの設定

$ cd 作成したフォルダ
$ git clone git@github.com:__803/tes1.git
$ mv tes1 作成した__
$ cd 作成した__
$ git remote set-url origin 作成したリポジトリのurl
$ git remote -v
$ git add .
$ git commit -m "リモートリポジトリの変更"
$ git push origin main
エラー $ sudo chmod -R 777 *
Docker の設定
$ docker-compose up -d --build   
$ code .
Laravelインストール
$ docker-compose exec php bash  
$ composer install
.env ファイルの作成
$ cp .env.example .env
php artisan key:generate
$ exit