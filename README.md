stunnel plaintext https client example
============================

Use stunnel to create a cleartext http stream locally from an https (TLS / SSL) stream.

Installation
============

You need about stunnel v5.19+

### Ubuntu

```bash
sudo apt-get install stunnel
stunnel -help
```

### OS X

```bash
brew install stunnel
```

### Other

```bash
wget https://www.stunnel.org/downloads/stunnel-5.22.tar.gz
pushd ./stunnel-5.22

./configure
make -j 4

sudo make install
```

Demo
====

```bash
wget -q https://gist.githubusercontent.com/coolaj86/327cee3eee6fc119b389/raw/root.pem -O /tmp/root.pem
```

`stunnel-plaintext-demo.conf`:
```
pid = /tmp/stunnel-plaintext-demo.pid

client = yes
verify = 1
foreground = yes

[demo]
accept = localhost:3000

; note that you still need to set the HTTP header `Host: coolaj86.com`
sni = coolaj86.com
connect = coolaj86.com:443
CAfile = /tmp/root.pem
; TODO use CApath with Mozilla certs
```

And run it

```bash
stunnel ./stunnel-plaintext-demo.conf
```

And test it

```bash
curl http://localhost:3000 -H 'Host: coolaj86.com'
```

Notes
=====

If you test in a browser you will not get the correct host because the `Host` header will be set to `localhost`
instead of `coolaj86.com`.

If this demo quits working it'll probably be because 2 or 3 years from now the root certificate will be different
and I won't have gone back to update this.

TODO
====

Use the `CApath` option with a download of Mozilla's most recent root certs instead of including a specific root cert.

Before I was trying to use `socat`, but it doesn't support Server Name Indication (SNI) and it wasn't correctly
verifying the x.509 (TLS/SSL) certs, even with the cafile option.
