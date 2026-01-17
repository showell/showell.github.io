Obligatory:
* [Eight Queens on Chessboard (in CoffeeScript)](https://github.com/showell/HipsterCode/blob/master/eight_queens.coffee) (2011) [run it!](http://showell.github.io/HipsterCode/eight_queens.html) -- this **animates** a backtracking algorithm
* [Conway's Game of Life (in CoffeeScript)](https://github.com/showell/Game-Of-Life/blob/master/game.coffee) (2011) [run it!](http://showell.github.io/Game-Of-Life/game.html) -- this is overengineered by design :) (even with inline unit tests)
* [same program in JS, more comments](https://gist.github.com/showell/908317#file-game_of_life-html) (2011)

Some YouTube content:
* [CoffeeScript Programming Tutorial](https://www.youtube.com/watch?v=TlERmDaEjJo&list=PLLwiAE7l6YhK94qx5-vIpDvSwyV2dCWr8) (2011)
* [Spicy Recursion](https://www.youtube.com/watch?v=CFIauRH9TUQ&list=PLLwiAE7l6YhLvgFp24MYLDJDA3AYpoy9Y&index=1) (2024) - kinda strange

Some Elm programs:
* [binary tree diagrams](https://github.com/showell/binary-tree-diagram) (2019) [run it!](./binary-tree-diagram.html)
* [FastTrack board game](https://github.com/showell/elm-fasttrack) (2019) [run it!](ft.html)
* [RedBlack Tree Tutorial](https://github.com/showell/ElmRedBlackTrees) (2019) [run it!](redblack.html)

## Code samples

I know how to program in C. Unfortunately, most of my C code samples are lost to time, since most were written pre-web (late 80s
and early 90s mostly).

I also wrote some interesting C code in 2013 while working for DomainTools in Seattle, but it's not open source.  Here is a small sample from 2012:

~~~ c
#include <stdio.h>
#include <stdlib.h>

unsigned long MAX = 10000000;

struct factorization {
    unsigned long small;
    unsigned long other;
};

unsigned long small_factor(
    unsigned long n,
    unsigned long *primes,
    unsigned long num_primes
)
{
    unsigned long i;
    for (i = 0; i < num_primes; ++i) {
        unsigned long p = primes[i];
        if (p * p > n) {
            break;
        }
        if (n % p == 0) {
            return p;
        }
    }
    return 1;
}

void factor_old(
    unsigned long n,
    struct factorization * factorizations
)
{
    if (factorizations[n].small == 1) {
        printf(" x %lu\n", n);
    }
    else {
        printf(" x %lu", factorizations[n].small);
        factor_old(factorizations[n].other, factorizations);
    }
}

void factor_new(
    unsigned long n,
    struct factorization *factorizations,
    unsigned long *primes,
    unsigned long *num_primes
)
{
    unsigned long f = small_factor(n, primes, *num_primes);
    unsigned long other = n / f;
    factorizations[n].small = f;
    factorizations[n].other = other;
    printf("%lu = %lu", n, f);
    if (f == 1) {
        // new prime
        primes[*num_primes] = n;
        *num_primes += 1;
        printf(" x %lu\n", other);
    }
    else {
        // composite
        factor_old(other, factorizations);
    }
}

void count_primes() {
    unsigned long i;
    struct factorization *factorizations;
    unsigned long *primes;
    unsigned long num_primes = 0;

    factorizations = malloc((MAX+1) * sizeof(struct factorization));

    // primes is very pessimistically allocated, but memory management
    // is not the point of this exercise
    primes = malloc((MAX+1) * sizeof(unsigned long));

    for (i = 2; i <= MAX; ++i) {
        factor_new(i, factorizations, primes, &num_primes);
    }

    printf("found %lu primes\n", num_primes);

    free(factorizations);
    free(primes);
}

main() {
    count_primes();
}
~~~
