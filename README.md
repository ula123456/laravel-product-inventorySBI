создаю проект в ларавел
                    composer create-project laravel/laravel  laravel-product-inventorySBI
                     cd laravel-product-inventorySBI
Инициализировать git-репозиторий и сделать первый коммит
                    git init
                    git add .
                    git commit -m "Initial Laravel project"
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

