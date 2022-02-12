# StickLib Game Library #
* StickMUD is an LPMud game library that runs on the LDMud game driver.
* StickLib is a legacy copy of StickMUD from around the turn of the century with some recent updates added.
* StickMUD was originally built up from the LPMud 2.4.5 game library that comes with the LDMud game driver.
* StickMUD is located at telnet://stickmud.com:7680 and port 8680 encrypts data-in-transit when configured.

StickMUD features [game telnet protocols](https://github.com/stickmud/sticklib/tree/master/basic/player), such as:

* [Generic Mud Communication Protocol](http://www.gammon.com.au/gmcp) (GMCP)
* [Mud Server Status Protocol](https://tintin.sourceforge.io/protocols/mssp/) (MSSP)
* [Mud eXtension Protocol](http://www.gammon.com.au/mushclient/addingservermxp.htm) (MXP)
* [Mud Sound Protocol](https://www.zuggsoft.com/zmud/msp.htm) (MSP)
* [Mud Client Media Protocol](https://wiki.mudlet.org/w/Standards:MUD_Client_Media_Protocol) (MCMP)

The topdirectory contains these files and directories:

* `areas/` stock areas for the game
* `basic/` inherited or included building blocks supporting mud library files
* `bin/` singleton objects like commands and daemons that are used by many other objects
* `data/` configuration and savefiles for the game
* `doc/` mudlib-specific documentation, to be complemented with the doc/ files from the driver distribution.
* `guilds/` playable classes for the game
* `include/` header files
* `lib/` mud library files used by blueprint objects
* `log/` logfiles generated by the mudlib
* `open/` shared area with open read access for all coders
* `players/` coder directories
* `room/` standard game rooms
* `secure/` the login object, master object, simul_efuns and other objects needing extra protection
* `std/` common implementations of blueprint objects suggested for use
* `sys/` include files, including those from the driver distribution

Recommended port security for an internet game:

| Type             | Protocol | Port Range | Source   | Description        |
|:----------------:|:--------:|:---------- |:-------- |:------------------ |
| SSH              | TCP      | 22         | Your IP  | Developer Access   |
| Custom TCP Rule  | TCP      | 5680       | Anywhere | TELNET to Game     |
| Custom UDP Rule  | UDP      | 5681       | Anywhere | API to Game        |
| Custom TCP Rule  | TCP      | ?          | Anywhere | TLS Tunnel to Game |
| HTTP             | TCP      | 80         | Anywhere | Webserver          |
| HTTPS            | TCP      | 443        | Anywhere | Webserver          |

## Installation Instructions for Debian 11
### Install pkg-config
```
sudo apt install pkg-config
```
### Install Autoconf
```
sudo apt install autoconf
```
### Install bison
```
sudo apt install bison
```
### Install gcrypt
```
sudo apt install libgcrypt-dev
```
### Install libxml2
```
sudo apt install libxml2-dev
```
### Install Python 3.9
Reference: [https://github.com/ldmud/ldmud/wiki/Python](https://github.com/ldmud/ldmud/wiki/Python)
```
sudo apt install libpython3-dev
```
### Install git
```
sudo apt install git
```
### Install cmake
```
sudo apt install cmake
```
### Install json-c
Reference: [https://github.com/json-c/json-c](https://github.com/json-c/json-c)
```
git clone https://github.com/json-c/json-c.git
mkdir json-c-build
cd json-c-build
cmake ../json-c
make
make test
sudo make install
```
### Install development tools
```
sudo apt install build-essential
```
### Install PCRE
Download PCRE version 1 from [https://www.pcre.org/](https://www.pcre.org/).
```
tar -xf pcre-8.45.tar.gz
cd pcre-8.45
./configure --enable-utf8 --enable-unicode-properties
make
sudo make install
```
### Clone from the LDMud GitHub repository
```
cd ~/
git clone https://github.com/ldmud/ldmud.git
cd ldmud
```
#### Checkout the version that matches production StickMUD
```
git checkout 3.6.4
rm -rf ldmud/.git
```
#### Install Python Efun Package for LDMud
Reference: [https://github.com/ldmud/python-efuns](https://github.com/ldmud/python-efuns)
```
git clone https://github.com/ldmud/python-efuns.git
cd python-efuns
python3 setup.py install --user
```
#### Edit comm.c so that IP name lookups work for StickMUD
```
cd ~/ldmud
nano src/comm.c
```
So that IP name lookups work, patch the function f_interactive_info (svalue_t *sp) to update the hname:
```
    /* Connection information */
    case II_IP_NAME:
#ifdef ERQ_DEMON
        {
            string_t * hname;

#if 0
            hname = lookup_ip_entry(ip->addr.sin_addr, MY_FALSE);
#else       /* StickMUD is going to force this to always use the ERQ */
            hname = lookup_ip_entry(ip->addr.sin_addr, MY_TRUE);
#endif
            if (hname)
            {
                put_ref_string(&result, hname);
                break;
            }
        }
#endif
```
#### Tune the configuration so it is right for this server
```
cd src/settings
nano sticklib
```
Which should resemble the following:
```
#!/bin/sh
#
# Settings for the StickMUD
#
# configure will strip this part from the script.

#export LDFLAGS="-L/usr/local/opt/libiconv/lib -L/usr/local/opt/pcre/lib -L/usr/lib ${LDFLAGS}"
#export CPPFLAGS="-I/usr/local/opt/libiconv/include -I/usr/local/opt/pcre/include -I/usr/include ${CPPFLAGS}"
#export CFLAGS="-I/usr/include ${CFLAGS}"
#export EXTRA_CFLAGS=$CFLAGS

exec ./configure --prefix=/home/sticklib/ldmud --libdir=/home/sticklib/ldmud/lib --bindir=/home/sticklib/ldmud/bin --with-setting=sticklib $*
exit 1

# --- The actual settings ---

with_portno=5680
with_udp_port=5690
with_max_players=200
with_time_to_swap=0
with_time_to_swap_variables=0
with_time_clean_up=3600
with_time_to_reset=1800
with_read_file_max_size=100000
with_max_byte_transfer=100000
with_otable_size=131072
with_htable_size=131072
with_max_array_size=20000
with_max_mapping_keys=20000
with_max_mapping_size=60000
with_max_malloced=0
with_max_cost=5000000
with_master_name=secure/master_amy
with_max_trace=100
with_max_user_trace=60
with_evaluator_stack_size=5000
with_erq_debug=4
with_python_script=../python-efuns/startup.py

enable_erq=xerq
enable_access_control=no
enable_dynamic_costs=yes
enable_compat_mode=yes
enable_strict_euids=no
enable_supply_parse_command=no
enable_initialization_by___init=no
enable_malloc_trace=no
enable_malloc_lpc_trace=no
enable_comm_stat=yes
enable_apply_cache_stat=yes
enable_use_parse_command=no
enable_use_pcre=yes
enable_use_json=yes
enable_use_xml=yes
enable_use_python=yes
enable_use_mysql=no
enable_use_ipv6=no
```
#### Complete the driver installation (update paths as needed)
```
cd ..
./autogen.sh
./configure --prefix=/home/sticklib/ldmud --libdir=/home/sticklib/ldmud/lib --bindir=/home/sticklib/ldmud/bin --with-setting=sticklib
make
make install
make install-all
```
### Clone from the StickMUD GitHub repository
```
cd ~/ldmud
git clone --recursive https://github.com/StickMUD/StickLib.git lib
cd lib
rm -rf .git
```
### Start the game (adjust port from 5680 if necessary)
```
cd ../bin
./ldmud --debug-file /home/sticklib/ldmud/lib/log/driver_runtime.log --hard-malloc-limit 0 5680 >& /home/sticklib/ldmud/lib/log/driver_compiletime.log &
```
### Troubleshooting
Can't find library (i.e. PCRE) after a manual install?
```
sudo ldconfig
```
Can't find Python3?
```
export PYTHON_EMBED_CFLAGS="-I/usr/local/include/python3.9"
export PYTHON_EMBED_LIBS="-L/usr/local/lib -lpython3.9"
export PYTHON_CFLAGS="-I/usr/include/python3.9"
```
