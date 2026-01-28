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

## Interview Prep repo

I enjoy prepping for all the classic interview questions.
I get rusty on algorithms, so it's useful for me to dust off my knowledge.

Also, when I learn a new programming language, it's useful to prove to myself that I can solve
the interview questions in the new language, even if it's a question I have answered a zillion
times before.  Usually there's just the syntax in my way, but sometimes the new language actually
provides a better way to solve it. (boy do I wish I had an example handy, haha)

See [InterviewPrep on GH](https://github.com/showell/InterviewPrep) (2012)

**aside**: Most of my examples are for CoffeeScript. That language seems mostly dead now. I don't use it any more,
to be clear. It had some good ideas, and I genuinely enjoyed just the syntax alone, but it was unfairly
maligned as being just pure syntax sugar, which I don't think is fair. It undoubtedly influenced
the evolution of other languages, including of course its transpilation target (JS).

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

Here is simple C code to parse a very small subset of JSON
(just ordinary strings and array of strings).

See [my c_json repo](https://github.com/showell/c_json)
for more details.

``` c
#include <stdio.h>
#include <string.h>
#include <stdlib.h>
#include "json.h"

static int _parse(char **s, struct json_result *result);

static struct json_array *visit_array_start(struct json_result *result) {
    struct json_array *array;
    if (result->i_array >= JSON_MAX_NUM_ARRAYS) {
        return NULL;
    }
    array = malloc(sizeof(struct json_array));
    if (!array) return 0;
    array->json_elements = malloc(JSON_MAX_ARRAY_SIZE * sizeof(struct json_element));
    if (!array->json_elements) return 0;
    result->allocations += 2;
    array->i = 0;
    result->arrays[result->i_array] = array;
    ++result->i_array;

    return array;
}

static int visit_array_add(struct json_result *result, struct json_array *array) {
    if (array->i >= JSON_MAX_ARRAY_SIZE) {
        return 0;
    }
    array->json_elements[array->i] = result->json_element;
    ++array->i;

    return 1;
}

static int visit_array_end(struct json_result *result, struct json_array * array) {
    result->json_element.type = JSON_TYPE_ARRAY;
    result->json_element.u.array = array;
}

static int visit_string(struct json_result *result, char *start, char *end) {
    char buf[JSON_MAX_STRING_SIZE+1];
    char *s = buf;

    if (result->i_string >= JSON_MAX_NUM_STRINGS) {
        return 0;
    }

    if (end - start > JSON_MAX_STRING_SIZE) {
        return 0;
    }

    while (start < end) {
        *s++ = *start++;
    }
    *s = '\0';
    s = result->strings[result->i_string] = strdup(buf);
    if (!s) return 0;
    result->allocations += 1;
    ++result->i_string;
    result->json_element.type = JSON_TYPE_STRING;
    result->json_element.u.s = s;

    return 1;
}

static void eat_whitespace(char **s) {
    while (**s && !strchr("\"[],", **s)) {
        ++*s;
    }
}

static int _parse_array(char **s, struct json_result *result) {

    int rc;
    struct json_array *array = visit_array_start(result);
    while (1) {
        rc = _parse(s, result);
        if (!rc) return 0;
        rc = visit_array_add(result, array);
        if (!rc) return 0;
        eat_whitespace(s);
        if (**s == ']') {
            ++*s;
            break;
        }
        else if (**s == ',') {
            ++*s;
            eat_whitespace(s);
        }
        else {
            return 0;
        }
    }
    visit_array_end(result, array);
    return 1;
}

static int _parse_string(char **s, struct json_result *result) {
    char *start = *s;

    while (**s && **s != '"') {
        ++*s;
    }
    if (**s == '"') {
        int rc = visit_string(result, start, *s);
        if (!rc) return 0;
        ++*s;
        return 1;
    }
    return 0;
}

static int _parse(char **s, struct json_result *result) {
    eat_whitespace(s);
    if (**s == '[') {
        ++*s;
        return _parse_array(s, result);
    }
    else if (**s == '"') {
        ++*s;
        return _parse_string(s, result);
    }
    else {
        return 0;
    }
}

void json_init_result(struct json_result *result) {
    result->i_string = 0;
    result->i_array = 0;
    result->allocations = 0;
}

int json_parse(char *s, struct json_result *result) {
    int rc = _parse(&s, result);
    return rc;
}

void json_release_result(struct json_result *result) {
    int i;

    for (i = 0; i < result->i_string; ++i) {
        free(result->strings[i]);
        result->allocations -= 1;
    }
    for (i = 0; i < result->i_array; ++i) {
        free(result->arrays[i]->json_elements);
        free(result->arrays[i]);
        result->allocations -= 2;
    }
}



```

## ELM

I love ELM.  It's the only real purely functional programming language that I have ever written actual programs in.

I encountered it in 2018, I think, and I mostly started using it in 2019.

I understood the basic proposition of avoiding side effects **long before** I encountered ELM, just to be clear.

And I had dabbled a bit in understanding LISP, Haskell, Clojure, etc. on some level. But I never felt
compelled to really use them. It's perfectly possible to write pure functions in mainstream languages
such as Python and JS, for example, and of course I have been doing that since circa 1986
(well, then it was C, but you get the point).

Anyway, here is some code that I wrote in 2019 to implement a board game called FastTrack.
I calculate whether something is a legal move in the game.

I don't claim this code is easy to understand, because I don't fully understand it myself
six years later (and I actually know the rules of the actual board game).

I just present it as an illustration that I could get my head around the beauty of ELM.

See the code in more context at the [repo](https://github.com/showell/elm-fasttrack/blob/master/src/LegalMove.elm)
and see the [game in action](https://showell.github.io/ft.html).

~~~ elm
module LegalMove exposing
    ( endLocations
    , getCanGoNSpaces
    , getCardForMoveType
    , getCardForPlayType
    , getMovesForCards
    , getMovesForMoveType
    , getMovesFromLocation
    )

import AssocSet as Set exposing (Set)
import Color
    exposing
        ( nextZoneColor
        , prevZoneColor
        )
import Config
    exposing
        ( isBaseId
        , isHoldingPenId
        , moveCountForCard
        , nextIdsInZone
        , prevIdInZone
        )
import Graph
    exposing
        ( canTravelNEdges
        , getNodesNEdgesAway
        )
import Piece
    exposing
        ( getPiece
        , getThePiece
        , hasPieceOnFastTrack
        , isNormalLoc
        , movablePieces
        , movePiece
        , otherNonPenPieces
        , swappableLocs
        )
import Type
    exposing
        ( Card
        , Color
        , FindLocParams
        , Move
        , MoveType(..)
        , PieceLocation
        , PieceMap
        , PlayType(..)
        , Turn(..)
        , Zone(..)
        )


getCanGoNSpaces : PieceMap -> PieceLocation -> List Color -> Int -> Bool
getCanGoNSpaces pieceMap loc zoneColors n =
    -- This function should only be called in the context of splitting
    -- sevens, so we don't account for cards being able to leave the
    -- holding pen.
    let
        ( _, id ) =
            loc

        canFastTrack =
            id == "FT"

        canLeaveBullsEye =
            False

        pieceColor =
            getThePiece pieceMap loc

        canMove =
            canFastTrack || not (hasPieceOnFastTrack pieceMap pieceColor)
    in
    if canMove then
        let
            params =
                { reverseMode = False
                , canFastTrack = canFastTrack
                , canLeavePen = False
                , canLeaveBullsEye = canLeaveBullsEye
                , pieceColor = pieceColor
                , pieceMap = pieceMap
                , zoneColors = zoneColors
                }

            getNeighbors =
                getNextLocs params
        in
        Graph.canTravelNEdges getNeighbors n loc

    else
        False


getMovesForCards : Set Card -> PieceMap -> List Color -> Color -> List Move
getMovesForCards cards pieceMap zoneColors activeColor =
    let
        normalMoveType : Card -> MoveType
        normalMoveType card =
            if card == "4" then
                Reverse card

            else
                WithCard card

        f : (Card -> MoveType) -> List Move
        f makeMoveType =
            let
                getMoves : Card -> List Move
                getMoves card =
                    let
                        moveType =
                            makeMoveType card

                        moves =
                            getMovesForMoveType moveType pieceMap zoneColors activeColor
                    in
                    moves
            in
            cards
                |> Set.toList
                |> List.map getMoves
                |> List.concat

        forwardMoves =
            f normalMoveType
    in
    if List.length forwardMoves > 0 then
        forwardMoves

    else
        f Reverse


getMovesForMoveType : MoveType -> PieceMap -> List Color -> Color -> List Move
getMovesForMoveType moveType pieceMap zoneColors activeColor =
    let
        startLocs : Set PieceLocation
        startLocs =
            case moveType of
                FinishSplit _ excludeLoc ->
                    otherNonPenPieces pieceMap activeColor excludeLoc

                _ ->
                    movablePieces pieceMap activeColor

        getMoves : PieceLocation -> List Move
        getMoves startLoc =
            getMovesFromLocation moveType pieceMap zoneColors startLoc
    in
    startLocs
        |> Set.toList
        |> List.map getMoves
        |> List.concat


getMovesFromLocation : MoveType -> PieceMap -> List Color -> PieceLocation -> List Move
getMovesFromLocation moveType pieceMap zoneColors startLoc =
    let
        ( _, id ) =
            startLoc

        canFastTrack =
            id == "FT"

        pieceColor =
            getThePiece pieceMap startLoc

        activeCard =
            getCardForMoveType moveType

        canLeaveBullsEye =
            List.member activeCard [ "J", "Q", "K" ] && id == "bullseye"

        canLeavePen =
            List.member activeCard [ "A", "joker", "6" ]

        reverseMode =
            case moveType of
                Reverse _ ->
                    True

                _ ->
                    activeCard == "4"

        movesLeft =
            moveCountForMoveType moveType id

        canMove =
            canFastTrack || not (hasPieceOnFastTrack pieceMap pieceColor)
    in
    if canMove then
        let
            params =
                { reverseMode = reverseMode
                , canFastTrack = canFastTrack
                , canLeavePen = canLeavePen
                , canLeaveBullsEye = canLeaveBullsEye
                , pieceColor = pieceColor
                , pieceMap = pieceMap
                , zoneColors = zoneColors
                }

            makeMove : PieceLocation -> Move
            makeMove endLoc =
                ( moveType, startLoc, endLoc )
        in
        if moveType == WithCard "7" then
            getMovesForSeven params startLoc

        else if moveType == WithCard "J" then
            getMovesForJack params startLoc

        else
            endLocations params startLoc movesLeft
                |> List.map makeMove

    else
        []


canFinishSplit : List Color -> Set PieceLocation -> PieceMap -> Int -> Move -> Bool
canFinishSplit zoneColors otherLocs pieceMap count move =
    let
        modifiedPieceMap =
            movePiece move pieceMap

        canGo otherLoc =
            getCanGoNSpaces modifiedPieceMap otherLoc zoneColors count
    in
    otherLocs
        |> Set.toList
        |> List.any canGo


getMovesForJack : FindLocParams -> PieceLocation -> List Move
getMovesForJack params startLoc =
    let
        pieceMap =
            params.pieceMap

        pieceColor =
            getThePiece pieceMap startLoc

        movesLeft =
            1

        forwardMoves =
            endLocations params startLoc movesLeft
                |> List.map (\endLoc -> ( WithCard "J", startLoc, endLoc ))

        tradeMoves =
            if isNormalLoc startLoc then
                swappableLocs pieceMap pieceColor
                    |> Set.toList
                    |> List.map (\endLoc -> ( JackTrade, startLoc, endLoc ))

            else
                []
    in
    List.concat [ forwardMoves, tradeMoves ]


getMovesForSeven : FindLocParams -> PieceLocation -> List Move
getMovesForSeven params startLoc =
    let
        pieceMap =
            params.pieceMap

        zoneColors =
            params.zoneColors

        pieceColor =
            getThePiece pieceMap startLoc

        getLocs : Int -> List PieceLocation
        getLocs moveCount =
            endLocations params startLoc moveCount

        fullMoves =
            getLocs 7
                |> List.map (\endLoc -> ( WithCard "7", startLoc, endLoc ))

        otherLocs =
            otherNonPenPieces pieceMap pieceColor startLoc
    in
    if Set.size otherLocs == 0 then
        fullMoves

    else
        let
            getPartialMoves moveCount =
                let
                    otherCount =
                        7 - moveCount

                    moveType =
                        StartSplit moveCount

                    candidateMoves =
                        getLocs moveCount
                            |> List.filter (\endLoc -> endLoc /= ( BullsEyeZone, "bullseye" ))
                            |> List.map (\endLoc -> ( moveType, startLoc, endLoc ))
                in
                candidateMoves
                    |> List.filter (canFinishSplit zoneColors otherLocs pieceMap otherCount)

            partialMoves =
                List.range 1 6
                    |> List.map getPartialMoves
                    |> List.concat
        in
        partialMoves ++ fullMoves


endLocations : FindLocParams -> PieceLocation -> Int -> List PieceLocation
endLocations params startLoc movesLeft =
    let
        getNeighbors =
            if params.reverseMode then
                getPrevLocs params

            else
                getNextLocs params
    in
    Graph.getNodesNEdgesAway getNeighbors movesLeft startLoc


getNextLocs : FindLocParams -> PieceLocation -> List PieceLocation
getNextLocs params loc =
    let
        ( zone, id ) =
            loc

        zoneColors =
            params.zoneColors

        pieceColor =
            params.pieceColor

        pieceMap =
            params.pieceMap

        isFree loc_ =
            isLocFree pieceMap pieceColor loc_

        filter lst =
            lst
                |> List.filter isFree
    in
    case zone of
        BullsEyeZone ->
            if params.canLeaveBullsEye then
                let
                    prevColor =
                        prevZoneColor pieceColor zoneColors
                in
                filter
                    [ ( NormalColor prevColor, "FT" )
                    ]

            else
                []

        NormalColor zoneColor ->
            let
                nextColor =
                    nextZoneColor zoneColor zoneColors

                nextZone =
                    NormalColor nextColor

                canFastTrack =
                    params.canFastTrack

                canLeavePen =
                    params.canLeavePen
            in
            if isHoldingPenId id then
                if canLeavePen then
                    filter [ ( zone, "L0" ) ]

                else
                    []

            else if id == "FT" then
                if pieceColor == zoneColor then
                    if canFastTrack then
                        filter
                            [ ( nextZone, "FT" )
                            , ( nextZone, "R4" )
                            , ( BullsEyeZone, "bullseye" )
                            ]

                    else
                        filter
                            [ ( nextZone, "R4" )
                            , ( BullsEyeZone, "bullseye" )
                            ]

                else if canFastTrack && (NormalColor pieceColor /= nextZone) then
                    filter
                        [ ( nextZone, "FT" )
                        , ( nextZone, "R4" )
                        ]

                else
                    filter
                        [ ( nextZone, "R4" )
                        ]

            else
                let
                    nextIds =
                        nextIdsInZone id pieceColor zoneColor
                in
                List.map (\id_ -> ( NormalColor zoneColor, id_ )) nextIds
                    |> filter


getPrevLocs : FindLocParams -> PieceLocation -> List PieceLocation
getPrevLocs params loc =
    let
        ( zone, id ) =
            loc
    in
    case zone of
        BullsEyeZone ->
            []

        NormalColor zoneColor ->
            let
                zoneColors =
                    params.zoneColors

                prevColor =
                    prevZoneColor zoneColor zoneColors

                pieceColor =
                    params.pieceColor

                pieceMap =
                    params.pieceMap

                isFree loc_ =
                    isLocFree pieceMap pieceColor loc_

                filter lst =
                    lst
                        |> List.filter isFree
            in
            if isHoldingPenId id then
                []

            else if isBaseId id then
                []

            else if id == "R4" then
                filter [ ( NormalColor prevColor, "FT" ) ]

            else
                let
                    prevId =
                        prevIdInZone id
                in
                [ ( NormalColor zoneColor, prevId ) ]
                    |> filter


getCardForPlayType : PlayType -> Card
getCardForPlayType playType =
    case playType of
        PlayCard card ->
            card

        FinishSeven _ ->
            "7"


getCardForMoveType : MoveType -> Card
getCardForMoveType moveType =
    case moveType of
        WithCard card ->
            card

        Reverse card ->
            card

        StartSplit _ ->
            "7"

        FinishSplit _ _ ->
            "7"

        JackTrade ->
            "J"


moveCountForMoveType : MoveType -> String -> Int
moveCountForMoveType moveType id =
    case moveType of
        WithCard card ->
            moveCountForCard card id

        Reverse card ->
            moveCountForCard card id

        StartSplit count ->
            count

        FinishSplit count _ ->
            count

        JackTrade ->
            -- we never call this for J trades
            0


isLocFree : PieceMap -> Color -> PieceLocation -> Bool
isLocFree pieceMap pieceColor loc =
    let
        otherPiece =
            getPiece pieceMap loc
    in
    case otherPiece of
        Nothing ->
            True

        Just color ->
            color /= pieceColor
~~~

## Zulip

I plan to write more about Zulip.  See [Zulip 2018](zulip2018.html) for now.
