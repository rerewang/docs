# 我的第一篇博客

## 创建文章模型（初识 laravel 数据库配置、模型类 与数据迁移）

打开cofig/database.php可以修改对应的数据库配置，我们默认使用mysql.

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
            $table->timestamp('publish_at'); //@by rere
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

## 使用数据生成器生成测试数据 (模型工厂类/数据填充类)

* 参考文档 https://learnku.com/docs/laravel/7.x/database-testing/7508 *
> php artisan make:factory PostFactory --model=Models/Post
上述命令会在在database/factories目录下创建模型工厂类PostFactory
编写刚生产的PostFactory类文件:
```php
<?php

/** @var \Illuminate\Database\Eloquent\Factory $factory */

use Faker\Generator as Faker;

$factory->define(App\Models\Post::class, function (Faker $faker) {
    return [
        'title' => $faker->sentence(mt_rand(4, 6)),  // 生成4-6个字母的句子 @by rere
        'content' => join('\n\n', $faker->paragraphs(mt_rand(4, 6))), // 生成4-6个字母的段落 @by rere
        'publish_at' => $faker->dateTimeBetween('-2 months', 'now'),
    ];
});

```

> php artisan make::seeder PostsTableSeeder
上述命令会在在database/seeder目录下创建数据填充类PostsTableSeeder
编写刚生产的PostsTableSeeder类文件:
```php
<?php

use Illuminate\Database\Seeder;
use App\Models\Post;

class PostsTableSeeder extends Seeder
{
    /**
     * Run the database seeds.
     *
     * @return void
     */
    public function run()
    {
        factory(Post::class, 10)->create();  //填充10篇文章  @by rere
    }
}
```

运行一下命令就可以生成测试数据了：
> php artisan db:seed
快去select一下posts表看看吧。


## 怎么将文章展示出来呢？
### 创建blog相关配置
在config目录下创建一个新的配置文件blog.php，并添加我们需要用到的配置项：
*这样按照资源来区分配置文件管理起来是不是就不会找不到了！*
```php
<?php

return [
    'post_num_per_page' => 5, //blog列表页每页展示的文章数量
];

```

### 创建路由和控制器 (了解路由)
*路由 https://learnku.com/docs/laravel/7.x/routing/7458*
编写routes/web.php文件增加blog列表页与详情页的路由
```php
Route::get('/blog','BlogController@list')->name('blog.list');
Route::get('/blog/{id}','BlogController@getBlog')->name('blog.detail');
```

> php artisan make:controller BlogController
上述命令会在在app/Http/Controllers目录下创建控制器类BlogController
编写BlogController文件增加获取blog列表页与详情页的方法
```php
<?php

namespace App\Http\Controllers;

use App\Models\Post;
use Carbon\Carbon;
use Illuminate\Http\Request;

class BlogController extends Controller
{
    public function list() {
        $posts = Post::where('publish_at', '<=', Carbon::now())
            ->orderBy('publish_at', 'desc')
            ->paginate(config('blog.posts_num_per_pages'));

        return view('blog.list', compact('posts'));
    }

    public function getBlog($id) {
        $post = Post::where('id', $id)->first();

        return view('blog.detail', ['post' => $post]);
    }
}

```
### 创建视图 （blade模板)
再创建两个视图分别来展示blog列表页与详情页就搞定了。

在resource/views目录下创建blog目录和list.blade.php和detail.blade.php文件。
*blade.php就表示使用blade模板来渲染页面哦*
编辑list.blade.php
```php
<html>
<head>
    <title>{{ config('blog.title') }}</title>
    <link href="{{ asset('css/app.css') }}" rel="stylesheet">
</head>
<body>
<div class="container">
    <h1>{{config('blog.title')}}</h1>
    <h5>Page {{ $posts->currentPage() }} of {{ $posts->lastPage() }}</h5>
    <hr>
    <ul>
        @foreach( $posts as $post)
            <li>
                <a href="{{ route('blog.detail', ['id' => $post->id]) }}">$post->title</a>
                <em>({{ $post->publish_at }}})</em>
                <p>
                    {{ Str::limit($post->content) }}
                </p>
            </li>
        @endforeach
    </ul>
    <hr>
    {!! $posts->render() !!}
</div>
</body>
</html>
```

```php
<html>
<head>
    <title>{{ config('blog.title') }}}</title>
    <link href="{{ asset('css/app.css') }}" rel="stylesheet">
</head>
<body>
<div class="container">
    <h1>{{ $post->title }}</h1>
    <h5>{{ $post->publish_at }}</h5>
    <hr>
    {!! nl2br(e($post->content)) !!}
    <hr>
    <button class="btn btn-primary" onclick="history.go(-1)">
        << Back
    </button>
</div>
</body>
</html>
```





