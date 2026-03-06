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

Laravelでフリマアプリを作る順番
**① テーブルを作る（Migration）**
```
まず DBだけ作る。
users
products
comments
favorites
purchases

例
php artisan make:migration create_products_table
ここでは
商品名
価格
説明
出品者
だけ考えればOKです。
まだ Controller は作らなくて大丈夫です。
```
**② Modelを作る**
```
テーブルができたら Modelを作る。
php artisan make:model Product
php artisan make:model Comment
php artisan make:model Favorite
php artisan make:model Purchase
この時点では
class Product extends Model
{
}
だけでOKです。
```
**③ 商品一覧を作る（最初の機能）**
```
いきなり全部作らないで 1ページだけ作る。
Route
Route::get('/', [ProductController::class,'index']);
Controller
public function index()
{
    $products = Product::all();

    return view('index', compact('products'));
}
View
@foreach($products as $product)
    <p>{{ $product->name }}</p>
@endforeach

ここでDB → 画面が繋がります。
これが 最初の成功体験です。
```
**④ 商品詳細**
```
次は詳細ページ。
Route
Route::get('/item/{id}', [ProductController::class,'show']);

Controller
public function show($id)
{
    $product = Product::find($id);

    return view('item', compact('product'));
}

View
<h1>{{ $product->name }}</h1>
<p>{{ $product->price }}</p>
```
**⑤ 出品機能**
```
Route
Route::get('/sell',[ProductController::class,'create']);
Route::post('/sell',[ProductController::class,'store']);

Controller
public function store(Request $request)
{
    Product::create($request->all());

    return redirect('/');
}
```
**⑥ コメント**
```
php artisan make:model Comment
php artisan make:controller CommentController

Controller
Comment::create([
    'user_id'=>auth()->id(),
    'product_id'=>$id,
    'comment'=>$request->comment
]);
```
**⑦ いいね**
```
Favorite::create([
    'user_id'=>auth()->id(),
    'product_id'=>$id
]);
```
**⑧ 購入**
```
最後にpurchases テーブルを使います。
```