создаю проект в ларавел
                    composer create-project laravel/laravel  laravel-product-inventorySBI
                     cd laravel-product-inventorySBI
Инициализировать git-репозиторий и сделать первый коммит
                    git init
                    Добавляет все изменения в Индекс, готовый к коммиту. 
                    git commit -m "Описание коммита":
                    Создает коммит с указанным сообщением. 
                    git push origin main:
Отправляет локальный коммит в удаленный репозиторий. 

установил папку .osp
                      [laravel-product-inventorySBI.local]
                      php_engine        = PHP-8.3
                      public_dir        = {base_dir}\public
настраиваю в .env подключение к БД (пререзагрузил осп)
                      DB_CONNECTION=mysql
                       DB_HOST=mysql-8.0
                       DB_PORT=3306
                       DB_DATABASE=laravel
                       DB_USERNAME=root
                       DB_PASSWORD=
создал модель category product
        php artisan make:model Product -m
        php artisan make:model Category -m
migration прописал 
прописал модель 
        В миграции добавьте поля `id`, `name`, `timestamps`.
        В миграции добавьте поля `id`, `name`, `price`, `barcode`, `category_id`, `timestamps`.
выполнил миграцию

       php artisan migrate
Создал фабрики:
   
   php artisan make:seeder CategorySeeder
php artisan make:factory ProductFactory --model=Product
Настройка сидер:
   class CategorySeeder extends Seeder
{
    /**
     * Run the database seeds.
     */
    public function run(): void
    {
        \App\Models\Category::insert([
            ['name' => 'samartfoni'],
            ['name' => 'charger'],
            ['name' => 'cover'],
        ]);
    }
}

class ProductFactory extends Factory
{
   
    public function definition(): array
    {
        $category = Category::inRandomOrder()->first() ?? Category::factory()->create();
    return [
        'name' => $this->faker->words(2, true),
        'price' => $this->faker->randomFloat(2, 100, 20000),
        'barcode' => $this->faker->unique()->ean13(),
        'category_id' => $category->id,
    ];
    }
}
   php artisan make:seeder DatabaseSeeder
   class DatabaseSeeder extends Seeder
{
    /**
     * Seed the application's database.
     */
    public function run(): void
    {
        // User::factory(10)->create();
        $this->call(CategorySeeder::class);
        \App\Madels\Product::factory(200)->create();

    }

}
   
В нем создайте 3 категории и 200 товаров с рандомными категориями.

Запуск сидера:
     php artisan db:seed
Репозитории и сервисы
создал папку Repository
создания интерфейса php artisan make:interface app/Repositories/ProductRepository
создания интерфейса php artisan make:interface app/Repositories/CategoryRepository

      в файле class ProductRepository
{
    public function all() { return Product::with('category')->get(); }
    public function find($id) { return Product::with('category')->findOrFail($id); }
    public function create(array $data) { return Product::create($data); }
    public function update(Product $product, array $data) { $product->update($data); return $product; }
    public function delete(Product $product) { return $product->delete(); }
}
также CategoryRepository
class  CategoryRepository
{
    public function all() { return Product::with('category')->get(); }
    public function find($id) { return Product::with('category')->findOrFail($id); }
    public function create(array $data) { return Product::create($data); }
    public function update(Product $product, array $data) { $product->update($data); return $product; }
    public function delete(Product $product) { return $product->delete(); }
}

Создать сервис-провайдер для связывания интерфейса и реализации
php artisan make:provider RepositoryServiceProvider
В файле app/Providers/RepositoryServiceProvider.php в методе register() добавить
      
      public function register()
              {
                  $this->app->bind(
                      \App\Repositories\CategoryRepositoryInterface::class,
                      \App\Repositories\CategoryRepository::class
                  );
                  $this->app->bind(
                      \App\Repositories\ProductRepositoryInterface::class,
                      \App\Repositories\ProductRepository::class
                  );
              }

Зарегистрировать провайдер в config/app.php
Откройте файл config/app.php Найдите массив providers
Добавьте свой провайдер App\Providers\RepositoryServiceProvider::class,
получется 'providers' => [
    // ... стандартные провайдеры

    App\Providers\RepositoryServiceProvider::class,
],

Создайте контроллеры для API

php artisan make:controller Api/CategoryController --api
php artisan make:controller Api/ProductController --api
namespace App\Http\Controllers\Api;

use App\Http\Controllers\Controller;
use App\Models\Category;
use Illuminate\Http\Request;

class CategoryController extends Controller
{
    public function index()
    {
        return Category::all();
    }

    public function store(Request $request)
    {
        $data = $request->validate(['name' => 'required|string|max:255']);
        return Category::create($data);
    }

    public function show(Category $category)
    {
        return $category;
    }

    public function update(Request $request, Category $category)
    {
        $data = $request->validate(['name' => 'required|string|max:255']);
        $category->update($data);
        return $category;
    }

    public function destroy(Category $category)
    {
        $category->delete();
        return response()->noContent();
    }
}
Для ProductController — аналогично, только добавь работу с полями name, price, category_id.

      Созд. Resource-классы для красивого вывода
      php artisan make:resource CategoryResource
      php artisan make:resource ProductResource
И возвращай, например, так: 
      return CategoryResource::collection(Category::all()); прописвть в ресурсах

  postman запускаем и указываем url c rout
