# CCSO (aka CSO or PH) Nameserver Module

The CCSO Nameserver Module is designed for communicating with CCSO
nameservers (sometimes known as CSO or PH servers) from Python
scripts. CCSO servers are mostly used as electronic phone books and as
ways to maintain site-wide e-mail addresses.

## Getting a Copy

The CCSO Nameserver Module is currently available as a single text file.

## Installation

Copy the ccso.py file to somewhere that the Python interpreter can find it. A
likely choice might be `/usr/local/lib/site-python` or
`/usr/local/lib/python1.4`.

## Usage

The CCSO library provides two classes, `Local` and `Network`.

### Local

When creating an instance of `Local` you can supply one optional
parameter. That parameter is the pathname of `qi`, the CCSO
Query Interpreter. The default is `/usr/local/sbin/qi` (because
that's what it is on my system). `qi` will always be invoked
with the `-q` flag so that prompts don't have to be parsed.

### Network

The Network class has two optional parameters when creating an
instance. The first parameter is the name of the host that the server
is running on. The default host name is 'localhost'. The second
optional parameter is the port that the server is listening to. The
default port is 105 or whatever port csnet-ns is defined as on your
system.

### Methods Common to Both Network and Local Classes

The Local and Network classes have a common interface. In fact 90% of
the interface is implemented in a common superclass. All that the
Local and Network classes do are provide definitions for some
low-level I/O functions.

* `query(query_string)` - sends a query to the database server.
   Returns a list of dictionaries that contain the reponses to your query.
* `login(alias, password)` - logs into the database server. Logging
   in allows changes to be made to the database.
* `logout()` - logs out of the server but leaves the connection in
   place.
* `othercmd(cmd)` - send a raw command to the database server and get
   back a (mostly) raw response.
* `close()` - close the connection to the database server.

## Examples

### Simple Query

    import site #if ccso.py is in your site-python directory
    import ccso
    
    s = ccso.Network()
    print s.query('Tom Jones')

### Creating an Entry

    import site #if ccso.py is in your site-python directory
    import ccso
    
    s = ccso.Local()
    r = ccso.othercmd('make alias=testuser name="Test User" '
                      'email="testuser@somedomain.com"')
    if r[-1] == 200:
        print 'OK'
    else:
        print 'NOT OK'

## References

* [PH/CSONameserver FAQ](http://www.camelcity.com/~noel/usenet/ph-FAQ.htm)

  Probably the best place to start if you are looking for more
  information.

* [The CCSO Nameserver Home Page](http://www.qualcomm.com/~ppomes/ph.html)
  
  Where you can get the latest sources.

## History

### Version 0.4, February 11, 1997

*Woohoo! Password encryption is now supported!* Thanks to Andy
Steingruebl who pointed me to Perl's `Net::PH` module, I was able to
figure out how to write the encryption code in Python. The C code in
the CCSO distribution relied on the fact that integer multiplication
in C does not generate an exception when the result of a
multiplication overflows. I hadn't applied enough brain power to
figure it out, so the `Net::PH` code helped tremendously.

### Version 0.3, January 27, 1997

Wow, but it's been a long time since I've worken on this. But, I
started a new job and I needed to access our CSO server from
Python. My old code wasn't quite up to snuff, so I've made some
changes.

The biggest change is in how you specify the connection to the
server. I needed to be able to talk to qi running as a subprocess of
the Python script. The CCSO class is now a superclass that implements
the generic behavior, while the subclasses Local and Network implement
the specific details of connecting to a local server or a remote
server.

### Version 0.2, November 10, 1994

Bugs fixed:

* In method `query()`, `resp[0]` and `resp[1]` needed to be changed to
  `response[0]` and `response[1]`.  Thanks to Ray Price
  <rlprice@ka.reg.uci.edu>.

* In `get_response()`, the return of `digits.match()` was not checked properly.

Features added:

* `login(alias)`/`logout()` methods allow you to log into the database.
  Passwords are currently sent in the clear, but I'm working on adding the
  encrypting of the login challenge.  Unfortunately, this will require some
  C code.

* `othercmd(str)` method for sending your own commands to the server.  Could
  be useful for sending "make" commands when you are logged in.  Returns
  the raw responses (but parsed out into fields).

* `get_email(str)` method for looking up e-mail addresses.  Returns a list
  of dictionary objects.  Each dictionary object has two keys: "name" and
  "email".

* The `siteinfo` attribute is a dictionary object which contains the
  responses parsed out of the "siteinfo" command.

### Version 0.1

Original version.
