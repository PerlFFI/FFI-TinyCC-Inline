# FFI::TinyCC::Inline ![static](https://github.com/Perl5-FFI/FFI-TinyCC-Inline/workflows/static/badge.svg) ![linux](https://github.com/Perl5-FFI/FFI-TinyCC-Inline/workflows/linux/badge.svg)

Embed Tiny C code in your Perl program

# SYNOPSIS

```perl
use FFI::TinyCC::Inline qw( tcc_inline );

tcc_inline q{
  int square(int num)
  {
    return num*num;
  }
};

print square(4), "\n"; # prints 16

use FFI::TinyCC::Inline qw( tcc_eval );

# sets value to 6:
my $value = tcc_eval q{
  int main(int a, int b, int c)
  {
    return a + b + c;
  }
}, 1, 2, 3;
```

# DESCRIPTION

This module provides a simplified interface to FFI::TinyCC, that allows you
to write Perl subs in C.  It is inspired by [XS::TCC](https://metacpan.org/pod/XS::TCC), but it uses [FFI::Platypus](https://metacpan.org/pod/FFI::Platypus)
to create bindings instead of XS.

# OPTIONS

You can specify Tiny C options using the scoped pragmata, like so:

```perl
use FFI::TinyCC::Inline options => "-I/foo/include -L/foo/lib -DFOO=1";

# prints 1
print tcc_eval q{
#include <foo.h> /* will search /foo/include */
int main()
{
  return FOO; /* defined and set to 1 */
}
};
```

# FUNCTIONS

## tcc\_inline

```
tcc_inline $c_code;
```

Compile the given C code using Tiny C and inject any functions found into the
current package.  An exception will be thrown if the code fails to compile, or if
[FFI::TinyCC::Inline](https://metacpan.org/pod/FFI::TinyCC::Inline) does not recognize one of the argument or return
types.

```
tcc_inline q{
  int foo(int a, int b, int c)
  {
    return a + b + c;
  }
};

print foo(1,2,3), "\n"; # prints 6
```

The special argument type of `(int argc, char **argv)` is recognized and
will be translated from the list of arguments passed in.  Example:

```
tcc_inline q{
  void foo(int argc, const char **argv)
  {
    int i;
    for(i=0; i<argc; i++)
    {
      puts(argv[i]);
    }
  }
};

foo("one", "two", "three"); # prints "one\ntwo\nthree\n"
```

## tcc\_eval

```
tcc_eval $c_code, @arguments;
```

This compiles the C code and executes the `main` function, passing in the given arguments.
Returns the result.

# SEE ALSO

- [FFI::TinyCC](https://metacpan.org/pod/FFI::TinyCC)
- [C::Blocks](https://metacpan.org/pod/C::Blocks)

# BUNDLED SOFTWARE

This package also comes with a parser that was shamelessly stolen from [XS::TCC](https://metacpan.org/pod/XS::TCC),
which I strongly suspect was itself shamelessly "borrowed" from
[Inline::C::Parser::RegExp](https://metacpan.org/pod/Inline::C::Parser::RegExp)

The license details for the parser are:

Copyright 2002 Brian Ingerson
Copyright 2008, 2010-2012 Sisyphus
Copyright 2013 Steffen Muellero

This program is free software; you can redistribute it and/or modify it under the same terms as Perl itself.

# AUTHOR

Author: Graham Ollis <plicease@cpan.org>

Contributors:

aero

Dylan Cali (calid)

pipcet

# COPYRIGHT AND LICENSE

This software is copyright (c) 2015-2018 by Graham Ollis.

This is free software; you can redistribute it and/or modify it under
the same terms as the Perl 5 programming language system itself.
