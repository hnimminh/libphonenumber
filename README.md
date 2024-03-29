# libphonenumber
Lua bindings C/C++ for google libphonenumber, It's an origin-fork of `singlecomm/luaphonenumber` with support Lua5.2+

## Prerequisites
Ensure build dependencies are readily available on your system:
* [Google libphonenumber](https://github.com/googlei18n/libphonenumber)
* [ICU library](http://site.icu-project.org/)

```bash
## https://build.alpinelinux.org/buildlogs/build-edge-armhf/community/lua-luaphonenumber/lua-luaphonenumber-1.0.1-r2.log
apt-get install libicu-dev libphonenumber8 libphonenumber-dev
```

## Installation
#### By luarocks

```bash
luarocks install libphonenumber
```
Typical install outout sample
```shell
#luarocks install libphonenumber
Installing https://luarocks.org/libphonenumber-1.0-2.rockspec
Cloning into 'libphonenumber'...
remote: Enumerating objects: 51, done.
remote: Counting objects: 100% (24/24), done.
remote: Compressing objects: 100% (16/16), done.
remote: Total 51 (delta 10), reused 20 (delta 8), pack-reused 27
Receiving objects: 100% (51/51), 14.89 KiB | 1.06 MiB/s, done.
Resolving deltas: 100% (21/21), done.
Warning: variable CFLAGS was not passed in build_variables
g++  -std=c++11 -Wall -fPIC -I/usr/include/lua5.2 -c luaphonenumber.cpp
g++   -I/usr/include/lua5.2 -shared -Wl,-soname,luaphonenumber.so.1 -o luaphonenumber.so.1.0 luaphonenumber.o -lphonenumber -lgeocoding
ln -sf luaphonenumber.so.1.0 luaphonenumber.so
ln -sf luaphonenumber.so.1.0 luaphonenumber.so.1
cp luaphonenumber.so* /usr/local/lib/lua/5.2
libphonenumber 1.0-2 is now installed in /usr/local (license: MIT)

```


#### From source

```bash
git clone https://github.com/hnimminh/libphonenumber.git
cd libphonenumber && make && make install
```

```shell
#git clone https://github.com/hnimminh/libphonenumber.git
Cloning into 'libphonenumber'...
remote: Enumerating objects: 51, done.
remote: Counting objects: 100% (24/24), done.
remote: Compressing objects: 100% (16/16), done.
remote: Total 51 (delta 10), reused 20 (delta 8), pack-reused 27
Receiving objects: 100% (51/51), 14.89 KiB | 3.72 MiB/s, done.
Resolving deltas: 100% (21/21), done.

#cd libphonenumber && make && make install
g++  -std=c++11 -Wall -fPIC -I/usr/include/lua5.2 -c luaphonenumber.cpp
g++   -I/usr/include/lua5.2 -shared -Wl,-soname,luaphonenumber.so.1 -o luaphonenumber.so.1.0 luaphonenumber.o -lphonenumber -lgeocoding
ln -sf luaphonenumber.so.1.0 luaphonenumber.so
ln -sf luaphonenumber.so.1.0 luaphonenumber.so.1
cp luaphonenumber.so* /usr/local/lib/lua/5.2
## https://github.com/TheLinx/lao/issues/2#issuecomment-56251801
```


### Methods

##### parse( input, country, language, localization_country )

Parses the `input` against the phone numbering schema of the `country` jurisdiction. The results are subsequently localized for the `language` used in `localization_country` area.

```lua
phonenumber = require("luaphonenumber")

local pn = phonenumber.parse( "+18045551234", "us", "en", "US" )

print( "e164 format: " .. pn.E164 )                     -- e164 format: +18045551234
print( "rfc3966 format: " .. pn.RFC3966 )               -- rfc3966 format: tel:+1-804-555-1234
print( "international format: " .. pn.INTERNATIONAL )   -- international format: +1 804-555-1234
print( "national format: " .. pn.NATIONAL )             -- national format: (804) 555-1234
print( "country: " .. pn.country )                      -- country: US
print( "location: " .. pn.location )                    -- location: Virginia
print( "line type: " .. pn.type )                       -- line type: FIXED_LINE_OR_MOBILE
```

##### format( input, country, pattern )

Formats the `input` against the phone numbering schema of the `country` jurisdiction, using one of the following patterns:
* `E164`
* `INTERNATIONAL`
* `NATIONAL`
* `RFC3966`

```lua
phonenumber = require("luaphonenumber")

print( "e164 format: " .. phonenumber.format( "+18045551234", "us", "E164" ) )                      -- e164 format: +18045551234
print( "rfc3966 format: " .. phonenumber.format( "+18045551234", "us", "RFC3966" ) )                -- rfc3966 format: tel:+1-804-555-1234
print( "international format: " .. phonenumber.format( "+18045551234", "us", "INTERNATIONAL" ) )    -- international format: +1 804-555-1234
print( "national format: " .. phonenumber.format( "+18045551234", "us", "NATIONAL" ) )              -- national format: (804) 555-1234
```


##### get_country( input, bias_country )

Returns the country of the `input` with consideration to the `bias_country`.

When providing the `input` in e.164 format, the `bias_country` is effectively ignored. Also, it returns a country code of `ZZ` when it cannot make a proper assessment.

```lua
phonenumber = require 'luaphonenumber'

local input1 = "+18045551234"
local input2 = "8045551234"
local input3 = "+447400555123"
local input4 = "07400555123"

print( "Country of " .. input1 .. ": " .. phonenumber.get_country( input1, "us" ) )     -- Country of +18045551234: US
print( "Country of " .. input2 .. ": " .. phonenumber.get_country( input2, "us" ) )     -- Country of 8045551234: US
print( "Country of " .. input3 .. ": " .. phonenumber.get_country( input3, "us" ) )     -- Country of +447400555123: GB
print( "Country of " .. input4 .. ": " .. phonenumber.get_country( input4, "us" ) )     -- Country of 07400555123: ZZ
```


##### get_location( input, country, language, localization_country )

Returns the region (country subdivision) of the `input` against the phone numbering schema of the `country` jurisdiction. The results are subsequently localized for the `language` used in `localization_country` area.

```lua
phonenumber = require 'luaphonenumber'

local input1 = "+18045551234"
local country1 = "us"
local input2 = "+442085551234"
local country2 = "gb"

print( "Region of " .. input1 .. ": " .. phonenumber.get_location( input1, country1, "en", "US" ) )     -- Region of +18045551234: Virginia
print( "Region of " .. input2 .. ": " .. phonenumber.get_location( input2, country2, "en", "US" ) )     -- Region of +442085551234: London
```


##### get_type( input, country )

Returns the line type of `input` with consideration to the `country` numbering schema. The returned type can be one of the following:
* `FIXED_LINE `
* `FIXED_LINE_OR_MOBILE `
* `MOBILE `
* `PAGER `
* `PERSONAL_NUMBER `
* `PREMIUM_RATE `
* `SHARED_COST `
* `TOLL_FREE `
* `UAN `
* `UNKNOWN `
* `VOICEMAIL `
* `VOIP `

```lua
phonenumber = require 'luaphonenumber'

local input1 = "+18045551234"
local country1 = "us"
local input2 = "+442085551234"
local country2 = "gb"
local input3 = "+40740555123"
local country3 = "ro"

print( "Type of " .. input1 .. ": " .. phonenumber.get_type( input1, country1 ) )       -- Type of +18045551234: FIXED_LINE_OR_MOBILE
print( "Type of " .. input2 .. ": " .. phonenumber.get_type( input2, country2 ) )       -- Type of +442085551234: FIXED_LINE
print( "Type of " .. input3 .. ": " .. phonenumber.get_type( input3, country3 ) )       -- Type of +40740555123: MOBILE
```
