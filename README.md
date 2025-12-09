



# Laravel 11 – Product CRUD System (With Price Sorting Only)


This README contains **full step-by-step explanation** of the entire Product CRUD setup.  
As requested, **only the Price Sorting feature is included** —  
No live search, no pagination, no AJAX.

---

# Step 1: Install Laravel 11

```
composer create-project laravel/laravel example-app
```

Creates a fresh Laravel 11 project.

---

# Step 2: Configure MySQL Database

Update `.env`:

```
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=your_database
DB_USERNAME=root
DB_PASSWORD=root
```

---

# Step 3: Create Products Table Migration

```
php artisan make:migration create_products_table --create=products
```

Migration fields:

- id  
- name  
- details  
- price  
- size  
- color  
- category  
- image  
- created_at / updated_at  

Run migration:

```
php artisan migrate
```

---

 #Step 4: Create Model & Resource Controller

```
php artisan make:controller ProductController --resource --model=Product
```

Model fillable:

```php
protected $fillable = [
  'name','details','price','size','color','category','image'
];
```

---

# Step 5: Add Resource Route

In `routes/web.php`:

```php
use App\Http\Controllers\ProductController;

Route::resource('products', ProductController::class);
```

---

# Step 6: Controller — Add ONLY Price Sorting

The `index()` method handles price sorting:

```php
public function index(Request $request)
{
    $query = Product::query();

    if ($request->filled('sort') && in_array($request->sort, ['price-asc', 'price-desc'])) {

        if ($request->sort === 'price-asc') {
            $query->orderBy('price', 'asc');   // Low → High
        } else {
            $query->orderBy('price', 'desc');  // High → Low
        }

    } else {
        $query->latest(); // Default sorting
    }

    $products = $query->get(); // No pagination

    return view('products.index', compact('products'));
}
```

✔ No search  
✔ No pagination  
✔ Only price sorting

---

# Step 7: Sorting Dropdown in Blade

Add to `index.blade.php`:

```html
<form method="GET" action="{{ route('products.index') }}">
    <label>Sort by Price:</label>
    <select name="sort" onchange="this.form.submit()">
        <option value="">Default</option>
        <option value="price-asc" {{ request('sort')=='price-asc'?'selected':'' }}>Low → High</option>
        <option value="price-desc" {{ request('sort')=='price-desc'?'selected':'' }}>High → Low</option>
    </select>
</form>
```

---

# Step 8: Display Products (Simple Table)

```blade
<table>
  <thead>
    <tr>
      <th>Name</th>
      <th>Details</th>
      <th>Price</th>
    </tr>
  </thead>

  <tbody>
    @foreach($products as $product)
      <tr>
        <td>{{ $product->name }}</td>
        <td>{{ Str::limit($product->details, 80) }}</td>
        <td>₹{{ number_format($product->price) }}</td>
      </tr>
    @endforeach
  </tbody>
</table>
```

---

# Step 9: Add Product Form (Create)

Fields required:

- Name  
- Details  
- Image  
- Size  
- Color  
- Category  
- Price  

---
 Step 10: Edit Product Form

Same fields as Create.

---

# Step 11: Delete Product

Controller automatically supports delete via:

```blade
<form action="{{ route('products.destroy', $product) }}" method="POST">
  @csrf
  @method('DELETE')
  <button>Delete</button>
</form>
```

---
# step 12: run the server

php artisan serve





<img width="676" height="212" alt="image" src="https://github.com/user-attachments/assets/75d91b74-8b51-4ea6-b766-ff5465798d87" />

