# Laravel 8 RestAPI Pagination

<p>
    I call the API (https://jsonplaceholder.typicode.com/posts) and store that data in a variable ($data) in json format. I wanted to put these data on a table. There are 100 data here. Thats why I wanted to keep 5 raw in the form of pagination. Hazzaz Bin Faiz's brother has solved this problem. Thanks.
</p><br><br>
C:\xampp\htdocs\basic\app\Providers\AppServiceProvider.php
```php
<?php

namespace App\Providers;

use Illuminate\Support\ServiceProvider;
use Illuminate\Support\Collection;
use Illuminate\Pagination\LengthAwarePaginator;
use Illuminate\Pagination\Paginator;

class AppServiceProvider extends ServiceProvider
{
    /**
     * Register any application services.
     *
     * @return void
     */
    public function register()
    {
        //
    }

    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
    {
        //
        Collection::macro('paginate', function($perPage, $total = null, $page = null, $pageName = 'page') {
            $page = $page ?: LengthAwarePaginator::resolveCurrentPage($pageName);
            return new LengthAwarePaginator(
            $this->forPage($page, $perPage),
            $total ?: $this->count(),
            $perPage,
            $page,
            [
            'path' => LengthAwarePaginator::resolveCurrentPath(),
            'pageName' => $pageName,
            ]
            );
        });
        Paginator::useBootstrap();

    }
}

```

C:\xampp\htdocs\basic\app\Http\Controllers\postsController.php

```php
<?php

namespace App\Http\Controllers;
use Illuminate\Support\Facades\Http;

use Illuminate\Http\Request;

class postsController extends Controller
{
    //
    public function index(){
        // $data = Http::get('https://jsonplaceholder.typicode.com/posts')->json();
       
        $data = collect(Http::get('https://jsonplaceholder.typicode.com/posts')->json())->paginate(5);
        return view('posts', compact('data'));

    }
}

```

C:\xampp\htdocs\basic\routes\web.php
```php
<?php

use Illuminate\Support\Facades\Route;
use App\Http\Controllers\postsController;
/*
|--------------------------------------------------------------------------
| Web Routes
|--------------------------------------------------------------------------
|
| Here is where you can register web routes for your application. These
| routes are loaded by the RouteServiceProvider within a group which
| contains the "web" middleware group. Now create something great!
|
*/

Route::get('/', function () {
    return view('welcome');
});

Route::view('/posts','posts');
Route::get('/posts', [postsController::class, 'index']);

```
C:\xampp\htdocs\basic\resources\views\posts.blade.php
```php
<!doctype html>
<html lang="en">
  <head>
    <title>Posts</title>
    <!-- Required meta tags -->
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

    <!-- Bootstrap CSS -->
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">
  </head>
  <body>
      <div class="container">
          <div class="row">
              <div class="col-10 m-auto pt-3">
                <table class="table">
                    <thead>
                        <tr>
                            <th>id</th>
                            <th>userId</th>
                            <th>title</th>
                            <th>body</th>
                        </tr>
                    </thead>
                    <tbody>
                        @foreach($data as $d)
                        <tr>
                            <td>{{$d['id']}}</td>
                            <td>{{$d['userId']}}</td>
                            <td>{{$d['title']}}</td>
                            <td>{{$d['body']}}</td>
                        </tr>
                        @endforeach
                    </tbody>
                </table>
                {{$data->links()}}
              </div>
          </div>
      </div>
    <!-- Optional JavaScript -->
    <!-- jQuery first, then Popper.js, then Bootstrap JS -->
    <script src="https://code.jquery.com/jquery-3.3.1.slim.min.js" integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo" crossorigin="anonymous"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.7/umd/popper.min.js" integrity="sha384-UO2eT0CpHqdSJQ6hJty5KVphtPhzWj9WO1clHTMGa3JDZwrnQq4sF86dIHNDz0W1" crossorigin="anonymous"></script>
    <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/js/bootstrap.min.js" integrity="sha384-JjSmVgyd0p3pXB1rRibZUAYoIIy6OrQ6VrjIEaFf/nJGzIxFDsf4x0xIM+B07jRM" crossorigin="anonymous"></script>
  </body>
</html>
```
