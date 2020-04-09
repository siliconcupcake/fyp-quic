# FYP quic
This project uses picoquic [<a href="https://github.com/private-octopus/picoquic">link</a>] for creating application over quic.

### Building Picoquic (Linux)

To build Picoquic on Linux, you need to:
 * Install and build Openssl on your machine
 * Clone and compile Picotls, using cmake as explained in the Picotls documentation.
 * Clone and compile Picoquic:
~~~
    cmake .
    make
~~~

### Demo
For server :
~~~
    ./picoquicdemo
~~~
