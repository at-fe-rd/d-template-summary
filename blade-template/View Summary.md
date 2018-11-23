# PHP Blade Template Summary

This is the document how to use the `blade Template`, which usally use in PHP.



## 1. How to define the Main Layout

**@yield('content')** 

```
<!-- Stored in resources/views/layouts/app.blade.php -->

<html>
    <head>
        <title>App Name - @yield('title')</title>
    </head>
    <body>
        @section('sidebar')
            This is the master sidebar.
        @show

        <div class="container">
            @yield('content')
        </div>
    </body>
</html>
```

**Note**:  Take a look of the `@section`and `@yield` directives. 

The `@section` directive, as the name implies, defines a section of content, while the `@yield` directive is used to display the contents of a given section.



## 2. Extending & Including

**`@extends` directive to specify which layout the child view should "inherit"**

```
<!-- Stored in resources/views/child.blade.php -->

@extends('layouts.app')

@section('title', 'Page Title')

@section('sidebar')
    @parent

    <p>This is appended to the master sidebar.</p>
@endsection

@section('content')
    <p>This is my body content.</p>
@endsection
```

**There are @includeIf, @includeWhen, @includeFirst**

if you attempt to `@include` a view which does not exist, Laravel will throw an error. 

 `@includeIf` : include a view that may or may not be present.

`@includeWhen`:  include a view depending on a given boolean condition.

`@includeFirst`:  include the first view that exists from a given array of views.

```
<div>
    @include('shared.errors')

    <form>
        <!-- Form Contents -->
    </form>
</div>
```



##3. Components & Slots

**The `{{ $slot }}` variable will contain the content we wish to inject into the component**

```
<!-- /resources/views/alert.blade.php -->

<div class="alert alert-danger">
    {{ $slot }}
</div>
```

 Now, to construct this component, we can use the `@component` Blade directive

```
@component('alert')
    <strong>Whoops!</strong> Something went wrong!
@endcomponent
```



## 4. Multiple Slots

**we can inject content into the named slot using the `@slot` directive**

```
<!-- /resources/views/alert.blade.php -->

<div class="alert alert-danger">
    <div class="alert-title">{{ $title }}</div>

    {{ $slot }}
</div>
```

Any content not within a `@slot` directive will be passed to the component in the `$slot` variable:

```
@component('alert')
    @slot('title')
        Forbidden
    @endslot

    You are not allowed to access this resource!
@endcomponent
```



## 5. Passing Additional Data To Components

Sometimes you may need to pass additional data to a component.

```
@component('alert', ['foo' => 'bar'])
    ...
@endcomponent
```



## 6. Aliasing Components

 You may use the `component` method to alias the component from `components.alert` to `alert`. Typically, this should be done in the `boot`method of your `AppServiceProvider`:

```
use Illuminate\Support\Facades\Blade;

Blade::component('components.alert', 'alert');
```

Just need to type  `@alert` , instead of `components.alert`



## 7. Display Data

### 7.1. Display with {{ $variable }}

They use {{....}} like Angular 2 

```
Route::get('greeting', function () {
    return view('welcome', ['name' => 'Samantha']);
});
```

display the contents of the `name` :

```
Hello, {{ $name }}.
```

###7.2. Display with {!! $variable  !!}

If you pass data from your Controller to a View with some HTML styling like:

```
$first = "<b>Narendra Sisodia</b>";
```

And it is accessed, within Blade, with `{{ $first }}` then the output'll be:

```
<b>Narendra Sisodia</b>
```

But if it is accessed with `{!! $first !!}` then the output'll be:

**Narendra Sisodia**

###7.3 Display with @{{ variable }}

Render a string `$name -> type`

```
<h1>Laravel</h1>

Hello, @{{ $name -> type }}.
```

###7.4. Rendering JSON

Pass variables``$array`` in PHP environment to the JS  environment.

2 ways:

```
<script>
    var app = <?php echo json_encode($array); ?>;
</script>
```

ShortHand:

```
<script>
    var app = @json($array);
</script>
```



## 8. Logic

###8.1. If Statements

**There are @if, @unless, @isset, @empty**

How to use:

- @if

```
@if (count($records) === 1)
    I have one record!
@elseif (count($records) > 1)
    I have multiple records!
@else
    I don't have any records!
@endif
```

- `@unless` directive:

```
@unless (Auth::check())
    You are not signed in.
@endunless
```

- the `@isset` and `@empty` directives may be used as convenient shortcuts for their respective PHP functions:

```
@isset($records)
    // $records is defined and is not null...
@endisset

@empty($records)
    // $records is "empty"...
@endempty
```

- #### Section Directives

  You may check if a section has content using the `@hasSection` directive:

  ```
  @hasSection('navigation')
      <div class="pull-right">
          @yield('navigation')
      </div>

      <div class="clearfix"></div>
  @endif
  ```

###8.2. Switch Statements 

```
@switch($i)
    @case(1)
        First case...
        @break

    @case(2)
        Second case...
        @break

    @default
        Default case...
@endswitch
```

### 8.3. Loops

**There are @for, @foreach, @forelse, @empty, @while**

```
@for ($i = 0; $i < 10; $i++)
    The current value is {{ $i }}
@endfor

@foreach ($users as $user)
    <p>This is user {{ $user->id }}</p>
@endforeach

@forelse ($users as $user)
    <li>{{ $user->name }}</li>
@empty
    <p>No users</p>
@endforelse

@while (true)
    <p>I'm looping forever.</p>
@endwhile
```

Example: 

```
@foreach ($users as $user)
    @continue($user->type == 1)

    <li>{{ $user->name }}</li>

    @break($user->number == 5)
@endforeach
```

users list will skip a loop when `user.type` = 1. It continue another loops while read `user.number` = 5, the `break` command run  , it stop the loops.

| Property           | Description                              |
| ------------------ | ---------------------------------------- |
| `$loop->index`     | The index of the current loop iteration (starts at 0). |
| `$loop->iteration` | The current loop iteration (starts at 1). |
| `$loop->remaining` | The iterations remaining in the loop.    |
| `$loop->count`     | The total number of items in the array being iterated. |
| `$loop->first`     | Whether this is the first iteration through the loop. |
| `$loop->last`      | Whether this is the last iteration through the loop. |
| `$loop->depth`     | The nesting level of the current loop.   |
| `$loop->parent`    | When in a nested loop, the parent's loop variable. |

