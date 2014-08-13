# Overview

A unit testing system for C, contained in 1 file. It doesn't use dynamic
allocation or depend on anything beyond ANSI C89, and the test scaffolding
should build without warnings under `-Wall -pedantic`.

To use, just `#include` greatest.h in your project. 

Note that there are some compile time options, and slightly nicer syntax
for parametric testing (running tests with arguments) is available if
compiled with -std=c99.

Also, I wrote a [blog post][1] with more information.

[1]: http://spin.atomicobject.com/2013/07/31/greatest-c-testing-embedded/

## Available Assertions

+ `ASSERT(COND)`/`ASSERTm(MSG, COND)`

    Assert that `COND` evaluates to a true value.

+ `ASSERT_FALSE(COND)`/`ASSERT_FAILm(MSG, COND)`

    Assert that `COND` evaluates to a false value.

+ `ASSERT_EQ(EXPECTED, ACTUAL)`/`ASSERT_EQm(MSG, EXPECTED, ACTUAL)`

    Assert that `EXPECTED == ACTUAL`. (To compare structures, use `ASSERT`
    with your own function to compare the structures' members.)

+ `ASSERT_STR_EQ(EXPECTED, ACTUAL)`/`ASSERT_STR_EQm(MSG, EXPECTED, ACTUAL)`

    Assert that `strcmp(EXPECTED, ACTUAL) == 0`.

In all cases, the `m` version allows you to pass in a customized failure
message. If an assertion without a custom message fails, `greatest` uses C
preprocessor stringification to simply print the assertion's parameters.

## Basic Usage

    $ cat simple.c
    #include "greatest.h"
    
    TEST x_should_equal_1() {
        int x = 1;
        ASSERT_EQ(1, x);                              /* default message */
        ASSERT_EQm("yikes, x doesn't equal 1", 1, x); /* custom message */
        PASS();
    }
    
    SUITE(the_suite) {
        RUN_TEST(x_should_equal_1);
    }
    
    /* Add definitions that need to be in the test runner's main file. */
    GREATEST_MAIN_DEFS();
    
    int main(int argc, char **argv) {
        GREATEST_MAIN_BEGIN();      /* command-line arguments, initialization. */
        RUN_SUITE(the_suite);
        GREATEST_MAIN_END();        /* display results */
    }
    $ make simple && ./simple
    cc -g -Wall -Werror -pedantic    simple.c   -o simple
    
    * Suite the_suite:
    .
    1 tests - 1 pass, 0 fail, 0 skipped (5 ticks, 0.000 sec)
    
    Total: 1 tests (47 ticks, 0.000 sec)
    Pass: 1, fail: 0, skip: 0.

(For more examples, look at example.c and example-suite.c.)

## Command Line Options

Test runners build with the following command line options:

    Usage: (test_runner) [-hlfv] [-s SUITE] [-t TEST]
      -h        print this Help
      -l        List suites and their tests, then exit
      -f        Stop runner after first failure
      -v        Verbose output
      -s SUITE  only run suite w/ name containing SUITE substring
      -t TEST   only run test w/ name containing TEST substring

If you want to run multiple test suites in parallel, look at
[parade](https://github.com/silentbicycle/parade).
