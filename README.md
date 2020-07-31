# Bork
A single file library for command-line argument parsing, implemented in C macros.

I created this library for my own little programs, taking inspiration from [argv.h](https://github.com/tommalt/argv) which is derived from [st](https://st.suckless.org/). Ironically, it sucks more than both of them.

To use bork, put this in your C file:
```c
#include "bork.h"
```

Here is an example:
```c
// assuming you included bork before
STARTOPT {
    LONGOPT {
        LFLAGS("help");
        if (strcmp(option, "help") == 0) 
            usage();
        else if (strcmp(option, "dostuff") == 0)
            dostuff(optarg);
        else
            die("Unknown option '%s'", option);
    } SHORTOPT {
        SFLAGS('h');
        switch (option) {
            case 'h':
                usage();
                break;
            case 'D':
                dostuff(optarg);
                break;
            default:
                die("Unknown option: '%c'\n", option);
        }
    } NONOPT {
        printf("This ain't familia!: '%s'\n", nonopt);
    }
} ENDOPT;
```
The example is pretty self-explanatory. I will explain in detail in the next section.
I really suck in explaining things though so it might be better for you to open an issue and ask if you don't get the docs.

---

## API Docs or something like that
Some terms that I use are:
> `longopt(s)` - long option. bork identify a string as long  option if it starts with two dashes, eg: `--help`.
> `shortopt(s)` - short option. bork identify a string as short option if it starts with ONLY one dash, eg: `-h`.

There are only a few macros:
```c
STARTOPT
LONGOPT
SHORTOPT
NONOPT
ENDOPT
LFLAGS(...)
SFLAGS(...)
```


### `STARTOPT`
This macro marks the start argument parsing. It must be followed by a code block and ended with `ENDOPT`.


### `LONGOPT`
This macro processes longopts.
This macro should always be present before `SHORTOPT` and `NONOPT`. It can be empty, eg: `LONGOPT {}`
> This macro exposes two variables:
> ```c
> char* option;  // the name of the option, without thedashes
> char* optarg;  // the argument for the option. It willbe NULL if option is a flag.
> ```


### `SHORTOPT`
This macro processes shortopts.
This macro cannot appear independently - It MUST APPEAR AFTER `LONGOPT`. It can be empty too.
Note that bork don't care how "long" a shortopt is - as long it starts with only one dash, then it's automatically here.
This macro works almost the same as `LONGOPT`. Thus, the same rules apply.
> This macro exposes two variables:
> ```c
> char option;   // the name of the option
> char* optarg;  // the argument for the option. It will be NULL if option is a flag.
> ```


### `NONOPT`
This macro is used to process non-option arguments and is optional.
> This macro exposes two variables:
> ```c
> char* nonopt;  // the argument
> ```


### `ENDOPT`
This macro marks the end for argument processing. It MUST BE PRESENT and should not be followed by code blocks.


### `LFLAGS`
This macro marks certain longopts as flags - preventing them for taking any optargs.
You should place this macro before any statement in `LONGOPT`.
> This macro requires the following argument:
> ```c
> ...  // Takes a list of strings (`char*`)
> ```
> Example:
> ```c
> LFLAGS("hello", "world");
> ```


### `SFLAGS`
This macro marks certain shortopts as flags - preventing them for taking any optargs.
You should place this macro before any statement in `SHORTOPT`
> This macro requires the following argument:
> ```c
> ...  // Takes a list of characters (`char`)
> ```
> Example:
> ```c
> SFLAGS('h', 'e');
> ```

## License
Unlicense or MIT-0. See [LICENSE](LICENSE).

## etc
[This website](https://wandbox.org/permlink/tFUsKMIXaQj8hhte) really helped me to debug the macros.