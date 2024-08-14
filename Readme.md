# Laravel 学習

## 1. モデルと DB の作成

**1.1 modle 作り方**

- app -> Models  
  ここで，`post.php`を例として扱う．

```sh
# モデルファイル & マイグレートファイルの作成
php artisan make:model Post -m
'''
最後に-mを入れることで、モデルファイル作成時に、マイグレートファイルも同時に作成できます。
マイグレーションファイルは、データベーステーブルの内容を設定するためのファイルです。
databaseフォルダの中に作成されます。【本日の日付_create_posts_table.php】
'''
```

- 「マイグレーションファイル」について  
   database -> migrations

```php

例：
    public function up()
    {
        Schema::create('posts', function (Blueprint $table) {
            $table->id();
            $table->string('title');
            $table->text('body');
            $table->text('image')->nullable(); # 入力なくでもOK
            $table->timestamps();
        });
    }
```

---

**1.2 Laravel のマイグレーションファイルの内容がデータベースに反映**

- データベースに反映（移行）

```sh
# 移行
php artisan migrate
```

- データベースロールバック

```sh
# ロールバック
php artisan migrate:rollback
```

---

**1.3 リレーションの設定**  
`デル間で関係を作る`

> １対多リレーションイメージ  
> １対 1 リレーションイメージ  
> 多対多リレーションイメージ

- リレーションの構築

  <font color="orange"> model ファイルの修正 </font>

  app -> Models の`.php`ファイル内

```php
'''
１対多リレーション（`post.php`を例として扱う．）
ひとつの投稿がひとりのユーザーに属す
'''
class Post extends Model
{
    use HasFactory;

    public function user() {
        return $this->belongsTo(User::class, 'user_id');
    }
}
'''
user() メソッド は「所属」リレーションシップ（belongsTo）を定義しており，'各 Post インスタンスが 1 つの User インスタンス'に関連付けられていることを示します，
User::class は関連するモデルが User であることを指定します，
user_id は posts テーブル内で User モデルとの関連を格納する外部キー列の名前を指定します，
'''
```

```php
'''
1対多リレーション（`User.php`を例として扱う．）
ひとりのユーザーが複数の投稿を持つ
'''
class User extends Authenticatable
{
    public function posts()
    {
        return $this->hasMany(Post::class, 'user_id');
    }
}
'''
hasMany メソッドは、'User モデルが複数の Post モデル'を持つことを定義します.
Post::class は関連するモデルが Post であることを指定します．
user_id は posts テーブル内でこのモデルと関連する外部キーの列の名前を指定します．
'''
```
