# Check model ownership for Laravel

![tests](https://github.com/santigarcor/laravel-ownable/workflows/tests/badge.svg?branch=master)

This trait allows you to check if a model owns some other model in your application.

## Installation

Simply run:
```bash
composer require santigarcor/laravel-ownable
```

Then you have to use the `OwnsModels` trait in the models you want to check if they own other models.

```php
use Ownable\OwnsModels;
use Illuminate\Database\Eloquent\Model;

class User extends Model
{
    use OwnsModels;
}
```

You are now ready to use it.

## Usage

```php
use Ownable\OwnsModels;
use Illuminate\Database\Eloquent\Model;

class User extends Model
{
    use OwnsModels;
}

class Video extends Model
{
}

$user = User::first();
$video = Video::first();

// Check If the user owns the video
if ($user->owns($video)){}

// Check If the user doesn't owns the video
if ($user->doesntOwns($video)){}

// Check If the user owns the video but the foreign key is the_user_id
if ($user->owns($video, 'the_user_id')){}
```

If for some reason the ownership check needs a more complex logic, you can implement the `Ownable` interface inside your ownable objects and then you can define your custom logic in the `isOwnedBy` method. When this method is called the object that called the `owns` method is passed as an attribute to the `isOwnedBy` method.

```php
use Ownable\OwnsModels;
use Illuminate\Database\Eloquent\Model;

class User extends Model
{
    use OwnsModels;
}

use Ownable\Contracts\Ownable;

class Video extends Model
{
    use Ownable;

    public function isOwnedBy($owner): bool
    {
        if ($owner instanceof User) {
            return $this->someRelationship->user_id == $owner->id;
        }

        return false;
    }
}

$user = User::first();
$video = Video::first();

// Then you can simply call the owns method on the user and it will work.

// Check If the user owns the video
if ($user->owns($video)){}

// Check If the user owns the video but don't use the ownable logic, instead the regular one with the foreign key.
if ($user->owns($video, null, false)){}
```