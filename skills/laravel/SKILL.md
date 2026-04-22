---
name: Laravel
description: >
  Laravel 11: MVC framework with Eloquent ORM, migrations, service layer, and robust validation.
  Trigger: Building server-side web applications with Laravel.
license: MIT
metadata:
  author: Henry Carmenate
  version: "1.0"
  applies-to:
    - claude-code
    - cursor
    - codex
---

## When to Use
Use this skill when:
- Building MVC web applications with Laravel 11
- Designing database schemas with migrations
- Creating Eloquent models with relationships
- Building service layers for business logic
- Implementing form validation with FormRequest
- Setting up API routes and controllers

## Critical Patterns

### Pattern 1: Model with Relationships
Eloquent models define database relationships declaratively.

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\HasMany;
use Illuminate\Database\Eloquent\Relations\BelongsToMany;

class User extends Model
{
    protected $fillable = ['name', 'email', 'password'];

    protected $hidden = ['password'];

    protected $casts = [
        'email_verified_at' => 'datetime',
        'created_at' => 'datetime',
    ];

    public function posts(): HasMany
    {
        return $this->hasMany(Post::class);
    }

    public function roles(): BelongsToMany
    {
        return $this->belongsToMany(Role::class);
    }

    public function isAdmin(): bool
    {
        return $this->roles()->where('name', 'admin')->exists();
    }
}
```

### Pattern 2: Migration with Constraints
Reversible migrations that define schema with foreign key constraints.

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up(): void
    {
        Schema::create('posts', function (Blueprint $table) {
            $table->id();
            $table->foreignId('user_id')->constrained('users')->onDelete('cascade');
            $table->string('title');
            $table->longText('content');
            $table->boolean('is_published')->default(false);
            $table->timestamps();

            $table->index('user_id');
            $table->index('created_at');
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('posts');
    }
};
```

### Pattern 3: Service Layer for Business Logic
Extract business logic into service classes to keep controllers thin.

```php
<?php

namespace App\Services;

use App\Models\Post;
use App\Models\User;
use Illuminate\Pagination\Paginator;

class PostService
{
    public function createPost(User $user, array $data): Post
    {
        if (!$user->isAdmin() && $user->posts()->count() >= 10) {
            throw new \Exception('User has reached maximum posts limit');
        }

        return $user->posts()->create([
            'title' => $data['title'],
            'content' => $data['content'],
            'is_published' => $data['is_published'] ?? false,
        ]);
    }

    public function publishPost(Post $post): Post
    {
        $post->update(['is_published' => true]);
        event(new PostPublished($post));
        return $post;
    }

    public function getUserPosts(User $user, int $page = 1): Paginator
    {
        return $user->posts()
            ->where('is_published', true)
            ->orderByDesc('created_at')
            ->paginate(15, ['*'], 'page', $page);
    }
}
```

### Pattern 4: FormRequest Validation
Centralized request validation with FormRequest classes.

```php
<?php

namespace App\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;

class StorePostRequest extends FormRequest
{
    public function authorize(): bool
    {
        return auth()->check();
    }

    public function rules(): array
    {
        return [
            'title' => 'required|string|min:3|max:255',
            'content' => 'required|string|min:10|max:10000',
            'is_published' => 'boolean',
        ];
    }

    public function messages(): array
    {
        return [
            'title.required' => 'Post title is required',
            'title.min' => 'Title must be at least 3 characters',
            'content.min' => 'Content must be at least 10 characters',
        ];
    }
}

// Usage in controller
class PostController extends Controller
{
    public function store(StorePostRequest $request, PostService $postService)
    {
        $post = $postService->createPost(
            auth()->user(),
            $request->validated()
        );

        return response()->json($post, 201);
    }
}
```

### Pattern 5: Route Naming and Middleware
Clean, named routes with middleware for authorization.

```php
<?php

use App\Http\Controllers\PostController;
use Illuminate\Support\Facades\Route;

Route::middleware('auth:sanctum')->group(function () {
    Route::post('/posts', [PostController::class, 'store'])
        ->name('posts.store');

    Route::put('/posts/{post}', [PostController::class, 'update'])
        ->name('posts.update')
        ->middleware('can:update,post');

    Route::delete('/posts/{post}', [PostController::class, 'destroy'])
        ->name('posts.destroy')
        ->middleware('can:delete,post');
});

Route::get('/posts', [PostController::class, 'index'])->name('posts.index');
Route::get('/posts/{post}', [PostController::class, 'show'])->name('posts.show');
```

## Folder Structure
```
app/
├── Models/
│   ├── User.php
│   ├── Post.php
│   └── Role.php
├── Http/
│   ├── Controllers/
│   │   ├── PostController.php
│   │   └── UserController.php
│   └── Requests/
│       ├── StorePostRequest.php
│       └── UpdatePostRequest.php
├── Services/
│   ├── PostService.php
│   └── UserService.php
├── Events/
│   └── PostPublished.php
└── Exceptions/
    └── PostLimitExceededException.php

database/
├── migrations/
│   ├── 2024_01_01_000000_create_users_table.php
│   ├── 2024_01_02_000000_create_posts_table.php
│   └── 2024_01_03_000000_create_roles_table.php
└── seeders/
    ├── DatabaseSeeder.php
    └── UserSeeder.php

routes/
├── api.php
└── web.php

resources/
└── views/
    ├── posts/
    │   ├── index.blade.php
    │   └── show.blade.php
    └── layouts/
        └── app.blade.php
```

## Key Principles
- **Migrations are reversible**: Always include down() method for rollback.
- **Eloquent over raw SQL**: Use Eloquent unless performance demands raw queries.
- **Service layer for logic**: Controllers call services; services encapsulate business logic.
- **Eager load relationships**: Use `with()` to prevent N+1 queries.
- **Validation early**: Use FormRequest for centralized, reusable validation.
- **Named routes**: Use `route('name')` instead of hardcoding URLs.
- **Middleware for concerns**: Use middleware for auth, CORS, logging, etc.
- **Transactions for consistency**: Wrap multi-step operations in transactions.

## Commands & Setup
```bash
# Create Laravel app
composer create-project laravel/laravel my-app
cd my-app

# Generate app key
php artisan key:generate

# Create migration
php artisan make:migration create_posts_table

# Run migrations
php artisan migrate

# Create model with factory and seeder
php artisan make:model Post --migration --factory --seeder

# Create controller
php artisan make:controller PostController --resource

# Create FormRequest
php artisan make:request StorePostRequest

# Tinker (interactive shell)
php artisan tinker

# Run tests
php artisan test
```
