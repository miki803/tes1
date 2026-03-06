**リポジトリの設定**
```text
cd 作成したフォルダ
git clone git@github.com:--803/tes1.git
mv tes1 作成した--
cd 作成した--
git remote set-url origin 作成したリポジトリのurl
git remote -v
git add .
git commit -m "リモートリポジトリの変更"
git push origin main
```
**エラー**
```text
sudo chmod -R 777 *
```
**Docker の設定**
```text
docker-compose up -d --build
code .
```
**Laravelインストール**
```text
docker-compose exec php bash
composer install
```
**.env ファイルの作成**
```text
 cp .env.example .env
 php artisan key:generate
 exit
```


```text
① users（ユーザー）
カラム名	型	PK	unique	not null	FK
id	bigint	○		○	
name	varchar			○	
email	varchar		○	○	
password	varchar			○	
created_at	timestamp				
updated_at	timestamp				
```
```text
② products（商品）
カラム名	型	PK	unique	not null	FK
id	bigint	○		○	
user_id	bigint			○	users(id)
name	varchar			○	
price	int			○	
description	text			○	
brand	varchar				
condition	varchar			○	
img_url	varchar			○	
created_at	timestamp				
updated_at	timestamp				

意味 誰が出品した商品か
```
```text
③ comments（コメント）
カラム名	型	PK	unique	not null	FK
id	bigint	○		○	
user_id	bigint			○	users(id)
product_id	bigint			○	products(id)
comment	varchar			○	
created_at	timestamp				
updated_at	timestamp				

意味 誰がどの商品にコメントした
```
```text
④ favorites（いいね）
カラム名	型	PK	unique	not null	FK
user_id	bigint		○(product_idと組み合わせ)	○	users(id)
product_id	bigint		○(user_idと組み合わせ)	○	products(id)
created_at	timestamp				
updated_at	timestamp				

意味 誰がどの商品をいいねした
```
```text
⑤ purchases（購入）
カラム名	型	PK	unique	not null	FK
id	bigint	○		○	
user_id	bigint			○	users(id)
product_id	bigint			○	products(id)
postcode	varchar			○	
address	varchar			○	
building	varchar				
created_at	timestamp				
updated_at	timestamp	

意味 誰がどの商品をどこに配送するか	
```

**① 認証まわり**
```text
会員登録
/register
↓
Fortify
↓
users テーブル
↓
auth/register.blade.php
ログイン
/login
↓
Fortify
↓
users
↓
auth/login.blade.php
```
**② 商品一覧（トップ）**
```text
URL
/
/?tab=mylist
流れ
ProductController@index
↓
Product モデル
↓
products テーブル
↓
products/index.blade.php
関係するテーブル
products
favorites
```
**③ 商品詳細**
```text
/item/{id}
↓
ProductController@show
↓
Product
Comment
Favorite
↓
products/show.blade.php

表示するもの
商品情報
コメント
いいね数

関係テーブル
products
comments
favorites
```
**④ コメント機能**
```text
POST /comment
↓
CommentController@store
↓
comments テーブル保存
↓
商品詳細にリダイレクト
```
**⑤ いいね機能**
```text
POST /favorite
↓
FavoriteController@store
↓
favorites テーブル
または削除で解除。
```
**⑥ 出品**
```text
/sell
↓
ProductController@create
↓
products/create.blade.php

保存：
POST /sell
↓
ProductController@store
↓
products テーブル
↓
storage に画像保存

関係テーブル
products
```
**⑦ 購入**
```text
表示：
/purchase/{id}
↓
PurchaseController@create
↓
products
↓
purchase/create.blade.php

購入処理：
POST /purchase
↓
PurchaseController@store
↓
purchases テーブル
↓
商品一覧へ

関係テーブル
purchases
products
```
**⑧ 住所変更**
```text
/purchase/address/{id}
↓
PurchaseController@editAddress
↓
purchase/address.blade.php

保存：
POST /purchase/address
↓
addresses or users更新
```
**⑨ マイページ**
```text
/mypage
/mypage?page=buy
/mypage?page=sell
↓
ProfileController@show
↓
users
products
purchases
↓
profile/mypage.blade.php
```
**⑩ プロフィール編集**
```text
/mypage/profile
↓
ProfileController@edit
↓
profile/edit.blade.php

保存：
POST /mypage/profile
↓
ProfileController@update
↓
users テーブル
```
**⑪ Model一覧（最小構成）**
```text
User
Product
Purchase
Favorite
Comment

関係イメージ
User
 ├ Products（出品）
 ├ Purchases（購入）
 ├ Favorites
 └ Comments
```
**⑫ Controller一覧（おすすめ）**
```text
ProductController
PurchaseController
ProfileController
FavoriteController
CommentController
```
**⑬ View構成（整理版）**
```text
views
├ auth
│   ├ login.blade.php
│   └ register.blade.php
│
├ products
│   ├ index.blade.php
│   ├ show.blade.php
│   └ create.blade.php
│
├ purchase
│   ├ create.blade.php
│   └ address.blade.php
│
├ profile
│   ├ mypage.blade.php
│   └ edit.blade.php
```