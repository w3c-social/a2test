as2test
=======

This is the test suite for the Activity Streams 2.0 (AS2) data format. It tests
implementations to make sure that they correctly produce and/or consume AS2.

License
=======

Copyright © 2015 World Wide Web Consortium, (Massachusetts Institute of
Technology, European Research Consortium for Informatics and Mathematics,
Keio University, Beihang). All Rights Reserved. This work is distributed under
the W3C® Software License [1] in the hope that it will be useful, but WITHOUT
ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS
FOR A PARTICULAR PURPOSE.

[1] http://www.w3.org/Consortium/Legal/copyright-software

Installation
============

This test suite requires Python version 2.6 or higher.

The test suite itself doesn't require any build process. Just download and run.

Running tests
=============

If you want to test an implementation, first install the implementation and its
test driver(s). The implementation should document where its producer and
consumer test scripts can be found and how to install them.

If it is an AS2 consumer, run this command from within the as2test directory:

    ./testconsumer.py <full-path-to-consumer-test-script>

...where <full-path-to-consumer-test-script> is the full path to the consumer
test script.

If it is an AS2 producer, run this command from within the as2test directory:

    ./testproducer.py <full-path-to-producer-test-script>

...where <full-path-to-producer-test-script> is the full path to the producer
test script.

Some implementations may be both producers and consumers of AS2; these should
have different scripts, and must be tested separately.

testconsumer.py and testproducer.py have no output if the provided test script
performed correctly. Otherwise, they produce inscrutable Python exceptions.

Implementing test scripts
=========================

Our anticipated implementation is a library that can parse AS2 into an internal
representation and/or generate AS2 from an internal representation. Since it's
hard to call libraries written in different languages from a single test suite,
we use command-line wrappers to give a unified interface between the test suite
and the implementation.

We considered using a Web interface, but this would probably require more work
on the part of the implementer, and would also cast a shadow onto testing work
for the Social API, which should probably be separate.

We expect that the implementation test scripts should be lightweight wrappers
that do not implement very much logic beyond parsing command-line options and
their input, and producing output.

Consumer interface
------------------

A consumer test script should take an AS2 JSON representation on standard input
(stdin). It should then print on standard output (stdout) a single property of
the object, as defined by its command-line options. If there is no such
property of the object, the output should be a blank line.

It should accept the following command-line options:

  * `type`: The type of the top-level object
  * `actor-id`: The id of the `actor` property of the top-level object
  * `object-id`: The id of the `object` property of the top-level object

Only one of these command-line options will be used for each invocation of the
test script. A typical invocation might look like:

  ./your-consumer-script.sh --type

The test script should exit with one of the following exit codes:

  * -1: The standard input was not formatted as a valid AS2 object
  * 0: The standard input was formatted as a valid AS2 object and output
    was emitted
  * Any other exit code: an internal error occurred

Any whitespace in the output before the first non-whitespace character or after
the last non-whitespace character will be stripped before comparison.

The script should have its executable flag (+x) set.

Producer interface
------------------

A producer test script should take a number of properties as command-line
options and output an AS2 JSON representation of an object with those
properties.

It should accept the following command-line options:

  * `type`: The type of the top-level object
  * `actor-id`: The id of the `actor` property of the top-level object
  * `object-id`: The id of the `object` property of the top-level object

Note that all of these options will include arguments, and multiple options will
be present. A typical invocation might look like:

    ./your-consumer-script.sh --type=Create \
      --actor-id=http://www.test.example/martin \
      --object-id=http://example.org/foo.jpg

(Here, "\\" represents a line continuation to allow wrapping in this document.)

The test script should exit with one of the following exit codes:

  * 0: The command-line options were understood and AS2 JSON was produced on
    stdout
  * Any other exit code: an internal error occurred

Any whitespace in the output before the first non-whitespace character or after
the last non-whitespace character will be stripped before comparison.

The script should have its executable flag (+x) set.

Dummies
=======

There is a dummy consumer at `dummy/consumer.py` that should pass all the
consumer tests, in a trivial way, mostly by cheating.

Similarly, there is a dummy producer at `dummy/producer.py` that should pass
all the producer tests.
