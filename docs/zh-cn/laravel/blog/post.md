# 创建文章数据表

## 熟悉 laravel 模型类 与数据迁移

> php artisan make:model -m Models/Post
上述命令会做两件事：
- 在app/Models目录下创建模型类Post。
- 在database/migration目录下创建posts表的数据库迁移文件。

打开 database/migration 目录下刚创建的posts表数据库迁移文件：
```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreatePostsTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('posts', function (Blueprint $table) {
            $table->id();
            $table->string('title'); //@by rere
            $table->text('content'); //@by rere
            $table->timestamps('publish_at'); //@by rere
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::dropIfExists('posts');
    }
}

```

其中up方法就是创建表的方法，默认设置了id字段与timestamps，
我们额外新增几个列(@by rere)
- title 标题
- content 内容
- publish_at  发布时间
*更多的数据类型，可以去Illuminate\Database\Schema\Blueprint发现哦。*
运行迁移命令，即可生成对应的数据标了
> php artisan migrate



