# Example of TLS-enabled Erlang/Elixir Distribution using Nerves

This repository demonstrates TLS-enabled Erlang/Elixir distribution using [Nerves](https://github.com/nerves-project/).

## Background

The default distribution method of Erlang/Elixir is the plain TCP.

> The default distribution module is inet_tcp_dist in the Kernel application. When starting an Erlang node distributed, net_kernel uses this module to set up listen ports and connections.
>
> [Erlang -- Using TLS for Erlang Distribution](http://erlang.org/doc/apps/ssl/ssl_distribution.html)

The document above describes how to enable TLS distribution. Here, I attempt to enable TLS distribution from/to Nerves device.

## Setup

### Device

Build a firmware using this repository and upload it to your device.

```sh
$ git clone git@github.com:kentaro/nerves_tls_distribution_example.git
$ cd 
$ mix firmware.burn
```

Insert the SD card into which the firmware is burned into your device.

Then, start a node on the device via IEx console.

```
$ ssh nerves.local

(snip)

iex(1)> System.cmd("epmd", ["-daemon"])
{"", 0}
iex(2)> Node.start(:"device@nerves.local")
{:ok, #PID<0.1215.0>}
iex(device@nerves.local)3>
```

### Host

We use [ConnorRigby/elixir-distribution-ssl-example](ConnorRigby/elixir-distribution-ssl-example) to reduce effort.

Build a release in other teminal according to the instruction described on the repository's README.

```sh
$ git clone git@github.com:ConnorRigby/elixir-distribution-ssl-example.git
$ cd elixir-distribution-ssl-example
$ mix release
```

## Usage

Start a node in the teminal in which [ConnorRigby/elixir-distribution-ssl-example](ConnorRigby/elixir-distribution-ssl-example) is setup.

```sh
$ env RELEASE_COOKIE="nerves_tls_distribution_example_cookie" RELEASE_NODE=host@localhost RELEASE_DISTRIBUTION=name _build/rpi3_dev/rel/example/bin/example start_iex
```

The path to the program may differ from above. Change it according to your environment.

Then, connect to the node.

```
iex(host@localhost)1> Node.connect(:"device@nerves.local")
true
```

Now you see TLS distribution between the host and the Nerves device is successfully enabled!

## References

* [Erlang -- Using TLS for Erlang Distribution](http://erlang.org/doc/apps/ssl/ssl_distribution.html)
* [ConnorRigby/elixir-distribution-ssl-example: Example of how to enable TLS distribution in an Elixir Release](https://github.com/ConnorRigby/elixir-distribution-ssl-example)
* [Erlang -- init](https://erlang.org/doc/man/init.html#get_argument-1)
* [Elixirのノード間通信をTLSで行う](https://zenn.dev/kentarok/scraps/9ff1559b6af1cb) (My blog post in Japanese)

## Author

Kentaro Kuribayashi <kentarok@gmail.com>
