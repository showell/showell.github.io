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

### C Programming Language

I know how to program in C. Unfortunately, most of my C code samples are lost to time, since most were written pre-web (late 80s
and early 90s mostly).

I also wrote some interesting C code in 2013 while working for DomainTools in Seattle, but it's not open source.

Here is some C code from an [interview-prep repo](https://github.com/showell/c_interview/blob/master/nth_biggest.c) that I worked on during 2012.  It finds the nth biggest item from a binary tree, and it includes assertions.

~~~ c
#include <stdlib.h>
#include <stdio.h>
#include <assert.h>

struct Node {
    int val;
    struct Node *left;
    struct Node *right;
};
typedef struct Node Node;

struct search_result {
    int need;
    struct Node *node;
};

struct search_result kth_biggest_result(Node *root, int k) {
    struct search_result sr;
    sr.need = k;
    sr.node = NULL;
    if (!root) {
        return sr;
    }
    if (root->right) {
        sr = kth_biggest_result(root->right, k);
        if (sr.need == 0) return sr;
    }
    sr.need -= 1; // root
    if (sr.need == 0) {
        sr.node = root;
        return sr;
    }
    return kth_biggest_result(root->left, sr.need);
}

Node *kth_biggest(Node *root, int k) {
    struct search_result sr;
    sr = kth_biggest_result(root, k);
    return sr.node;
}

Node *make_node(int n) {
    Node *Node = malloc(sizeof(struct Node));
    Node->left = NULL;
    Node->right = NULL;
    Node->val = n;
    return Node;
}

int main(int argc, char **argv) {
    Node *node;
    Node *t1 = make_node(1);
    Node *t2 = make_node(2);
    Node *t3 = make_node(3);
    Node *t4 = make_node(4);
    Node *t5 = make_node(5);
    Node *t6 = make_node(6);
    t2->left = t1;
    t2->right = t3;
    t4->left = t2;
    t4->right = t5;
    t5->right = t6;
    node = kth_biggest(t4, 1);
    assert(node->val == 6);
    node = kth_biggest(t4, 2);
    assert(node->val == 5);
    node = kth_biggest(t4, 3);
    assert(node->val == 4);
    node = kth_biggest(t4, 4);
    assert(node->val == 3);
    node = kth_biggest(t4, 5);
    assert(node->val == 2);
    node = kth_biggest(t4, 6);
    assert(node->val == 1);
    node = kth_biggest(t4, 7);
    assert(!node);
    node = kth_biggest(t4, 8);
    assert(!node);

    return 0;
}
~~~
