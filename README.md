# Laravel Blade Function
This Laravel package lets you declare functions inside blade templates. After installing it, you'll
never need to use partials as functions.

## Installation

## How to use it

You can declare and use functions in views like so:

```blade
@extends('layouts.app')

@function(hello ($who))
    Hello {{$who}}!
@endfunction

@section('content')
    <p>
        @hello('World')
    </p>
@endsection
```

The `@function` directive accepts the function name and the argument list as parameter.
If your function doesn't need arguments, you can omit the parantheses:

```blade
{{-- Both are correct: --}}

@function( printhello () )
    Hello
@endfunction

@function(printhello)
    Hello
@endfunction
```

In this case you can omit the parantheses when calling the function as well: `@printhello()` and `@printhello` are both correct.

You can use any other blade directives inside your functions:

```blade
@function(mylist ($items))
    <ul>
        @foreach($items as $item)
            <li>{{$item}}</li>
        @endforeach
    </ul>
@endfunction

@mylist([1, 2, 3]) {{-- Prints ∙1 ∙2 ∙3  --}}
```

There's also a `@return` directive, just in case. But you probably won't need this.


```blade
@function(theanswer())
	@return(42)
@endfunction
```

## Gotchas and Things to Be Aware of

Unlike in PHP in general, blade functions must be declared **before** calling them! Just like in the good old C days.
But while in C you can use header files for this reason, in this case functions must be daclared **in the same view file**
where you use them. However this shouldn't be a problem, please see the When (not) to use it section.

When you declare a function using the `@function` directive, the function will be a real PHP function and it will be
in the global namespace. This means that you can't use PHP keywords and already declared functions as function name.

You can call functions you declared in the 'traditional' way, in plain PHP code. In this case however you need to pass
the `$__env` variable manually. (This is used by blade for various things, ex. in foreach loops.)

```blade
{{-- These are the same --}}
@hello('world')
<?php hello('world', $__env); ?>
```

## When (not) to use it

This package is meant to help *presenting* small things. But only where blade syntax comes handy.

I created this package because many times I found myself 
treating partials as functions. Which can be pretty ugly and unconvinient, especially when you  pass some data *as argument*.
So I'd say, when you  want to extract some *blade* code, but you don't feel like putting it into a seperate partial view,
that's when you probably want to use a blade function. It might be also a good idea to wrap partial includes in functions (see example below).

Keep in mind, that in most cases it is a good idea to separete logic and presentation. 
So you shouldn't use this for parsing data or calculatig fibonacci numbers (however you can, see the examples). It's neither the best choice
for helper functions where you format some data without html (ex.: you can create an `app/Http/helpers.php`  file where you can add your custom
date formatting functions, etc.)

## Examples 

### Wrapping partials

This looks like a common situation:

```blade
@include('partials.modal', [
	'header' => '',
	'body'   => 'Something terrible happened.'
])
```

You can replace the above code with this:

```blade
@function(modal ($header, $body))
    @include('partials.modal', compact('header', 'body'))
@endfunction

...

@modal('Alert', 'Something terrible happened.')

{{-- Let's reuse it --}}

@modal('Success', 'You\'re great!')
```

Much cleaner, isn't it?

### Factorials

You shouldn't do this. I mean you really shoudn't. This is not what blade is designed for. But yeah, now you can do recursion :-P

```blade
@function(factorials ($n))
    @if($n <= 1)
        {{1}}
        @return(1)
        }
    @endif


    {{$tmp = $n * factorials($n - 1, $__env)}}

    @return($tmp)
@endfunction

@factorials(5) {{-- prints 1 2 6 24 120 --}}
```




