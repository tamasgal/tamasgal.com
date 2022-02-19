+++
title = "Data Exchange Between Python and C++ via protobuf"
date = "2014-03-22"
author = "Tamás Gál"
description = "An example how to use Googles protobuf to send a data structure from a C++ script to a Python script via UDP."
categories = ["coding"]
tags = ["protobuf","python","c++"]
+++

# Installing protobuf

The first step is of course installing the `protobuf` library. I used
homebrew for the main library and `pip` to install the `Python` modules:

```
brew install protobuf
pip install protobuf
```

Then I defined a very simple data structure using the proto-syntax:

```C++
// Filename: foo.proto

package prototest;

message Foo {
    required int32 id = 1;
    required string bar = 2;
    optional string baz = 3;
}
```

This proto-file can now be translated into C++ and Python classes via:

```
protoc foo.proto –cpp_out=. –python_out=.
```

The folder should now contain the C++ header and source files and the
Python code:

```
├── foo.pb.cc
├── foo.pb.h
├── foo.proto
└── foo_pb2.py
```

Let’s have a look at the very basic `C++` code, which is meant to send an
instance of `foo` over the network, using UDP (to localhost on port 5555):


```C++
// Filename: send.cc

#include <sys/socket.h>
#include <arpa/inet.h>

// this is our proto of foo
#include "foo.pb.h"

int main(int argc, char **argv)
{
    struct sockaddr_in addr;

    addr.sin_family = AF_INET;
    inet_aton("127.0.0.1", &addr.sin_addr);
    addr.sin_port = htons(5555);

    // initialise a foo and set some properties
    GOOGLE_PROTOBUF_VERIFY_VERSION;
    prototest::Foo foo;
    foo.set_id(4);
    foo.set_bar("narf");

    // serialise to string, this one is obvious ; )
    std::string buf;
    foo.SerializeToString(&buf);

    int sock = socket(PF_INET, SOCK_DGRAM, 0);
    sendto(sock, buf.data(), strlen(buf.c_str()), 0,
           (struct sockaddr *)&addr, sizeof(addr));

    return 0;
}
```

I compiled it via `clang++`:

```
clang++ -o send send.cc foo.pb.cc -lprotobuf
```

And finally, this is the `Python` code, which waits for UDP packets and
deserialise them into `foo`. Again: no error checking whatsoever, this is
only to demonstrate the functionality:


```Python
# Filename: receive.py

import socket
from foo_pb2 import Foo

sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
sock.bind(("127.0.0.1", 5555))

foo = Foo()
while True:
    data, addr = sock.recvfrom(1024)
    foo.ParseFromString(data)
    print("Got foo with id={0} and bar={1}".format(foo.id, foo.bar))
```

Now we’re done and this is the final directory structure:

```
├── foo.pb.cc
├── foo.pb.h
├── foo.proto
├── foo_pb2.py
├── receive.py
├── send
└── send.cc
```

To test the script, simply run `receive.py` to listen to UDP packets via

```
python receive.py
```

and keep your eyes on the output when you execute the C++ generated `send`
script:

```
./send
```
