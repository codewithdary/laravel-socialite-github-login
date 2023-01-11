# Product Dashboard [Laravel Nova]
This is the official repository [ULTIMATE Laravel Nova Tutorial](https://www.youtube.com/watch?v=vBfaiZQDQrQ&feature=youtu.be) <br>
•	Twitter: [@codewithdary](https://twitter.com/codewithdary) <br>
•	Instagram: [@codewithdary](https://www.instagram.com/codewithdary/) <br>
•	TikTok: [@codewithdary](https://tiktok.com/@codewithdary) <br>
•	Blog: [@codewithdary](https://blog.codewithdary.com) <br>
•	Patreon: [@codewithdary](https://www.patreon.com/user?u=30307830) <br>
 <br>

## Usage <br>
Setup the repository <br>
```
git clone https://github.com/codewithdary/laravel-socialite-github-login.git
cd laravel-socialite-github-login.git
composer install
cp .env.example .env 
php artisan key:generate
php artisan cache:clear && php artisan config:clear 
php artisan serve 
```

### Step 1: Install Laravel Breeze 
The following pieces of code have been added into this (empty) repository.
```
composer require laravel/breeze --dev
php artisan breeze:install --dark
php artisan migrate
npm install && npm run dev
```

### Step 2: Install Laravel Socialite 
```
composer require laravel/socialite
```

### Step 3: Adjust users migration
```php
    public function up()
    {
        Schema::create('users', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->string('nickname')->nullable();
            $table->string('email')->unique();
            $table->timestamp('email_verified_at')->nullable();
            $table->string('password');
            $table->string('github_id')->nullable();
            $table->string('auth_type')->nullable();
            $table->rememberToken();
            $table->timestamps();
        });
    }
```

### Step 4: Enable mass assignment
```php
// App\Models\User.php
protected $fillable = [
    'name',
    'nickname',
    'email',
    'password',
    'github_id',
    'auth_type'
];
```

### Step 5: Add GitHub Login to view
```php 
// login.blade.php
<div class="text-center">
    <a href="{{ route('github.login') }}">
        <img
            src="https://icon-library.com/images/github-icon-white/github-icon-white-6.jpg"
            alt="GitHub icon"
            width="50px"
            class="mx-auto scale-100 hover:scale-125 ease-in duration-200"
        />
    </a>
</div>
```

### Step 6: Add GitHub credentials
```php
// config\services.php
'github' => [
    'client_id' => env('GITHUB_CLIENT_ID'),
    'client_secret' => env('GITHUB_CLIENT_SECRET'),
    'redirect' => env('GITHUB_CALLBACK_URI'),
],
```

### Step 7: Define Routes
```php
Route::get('auth/github', [GitHubController::class, 'redirect'])->name('github.login');
Route::get('auth/github/callback', [GitHubController::class, 'callback']);

```

### Step 8: Define Controller
```php
public function redirect()
{
    return Socialite::driver('github')->redirect();
}

public function callback()
{
    try {
        $user = Socialite::driver('github')->user();

        $gitUser = User::updateOrCreate([
            'github_id' => $user->id,
        ],[
            'name' => $user->name,
            'nickname' => $user->nickname,
            'email' => $user->email,
            'github_token' => $user->token,
            'auth_type' => 'github',
            'password' => Hash::make(Str::random(10))
        ]);

        Auth::login($gitUser);

        return redirect()->route('dashboard');
    } catch(\Exception $e) {
        ray($e->getMessage());
    }
}

```

