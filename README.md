Name
====

lua-resty-memcached-shdict - Powerful memcached client with a shdict caching layer ando many other features

Synopsis
========

```lua
local shdict_memc = require "resty.memcached.shdict"

local function dlog(ctx, ...)
    ngx.log(ngx.DEBUG, "my app: ", ...)
end

local function error_log(ctx, ...)
    ngx.log(ngx.ERR, "my app: ", ...)
end

local function warn(ctx, ...)
    ngx.log(ngx.WARN, "my app: ", ...)
end

local memc_fetch, memc_store =
    shdict_memc.gen_memc_methods{
        debug_logger = dlog,
        warn_logger = warn,
        error_logger = error_log,

        shdict_set = meta_shdict_set,  -- generated by lua-resty-shdict-simple or
                                       -- any other API compatible function factories

        shdict_get = meta_shdict_get,  -- ditto

        disable_shdict = false,  -- optional, default false

        memc_fetch_retries = 2,  -- optional, default 1
        memc_fetch_retry_delay = 100, -- in ms, optional, default to 100 (ms)

        memc_conn_max_idle_time = 10 * 1000,  -- in ms, for in-pool connections,
                                              -- optional, default to nil

        memc_store_retries = 2,  -- optional, default to 1
        memc_store_retry_delay = 100,  -- in ms, optional, default to 100 (ms)

        store_ttl = 1000,  -- in ms, optional, default to 0 (i.e., never expires)
    }

-- on hot code paths:

local ctx = ngx.ctx

local data, err = memc_fetch(ctx, key)

memc_store(ctx, key, value)  -- using the default ttl specified by "store_ttl"
memc_store(ctx, key, value, ttl)  -- overriding the default ttl
```

Description
===========

This library provides a powerful memcached client that deliver the following important features:

1. Automatically using shm-based caching layer with OpenResty's [lua_shared_dict](https://github.com/openresty/lua-nginx-module#lua_shared_dict).
1. Automatically using server-level (not worker-level) cache locks (based on
[lua-resty-lock](https://github.com/openresty/lua-resty-lock)) to avoid duplicate and concurrent
queries to the memcached server for the same key when it is a cache miss in the local shm cache.
1. Automatically handles and logs any errors while accessing the Memcached server or `lua_shared_dict`.
1. Automatically uses connection pools for all the Memcached queries.
1. Automatically returns stale (or expired) data item in the local shm cache when a cache miss happens
*and* some other light threads are already querying Memcached and updating the cache.
1. Automatically retry querying Memcached when failures happen (like intermittent network issues).

This library is mostly used for cases that use Memcached or Memcached-compatible servers as the
data storage (like Kyoto Tycoon).

Dependencies
============

This library depends on the following Lua libraries:

* [lua-resty-memcached](https://github.com/openresty/lua-resty-memcached)
* [lua-resty-lock](https://github.com/openresty/lua-resty-lock)
* [lua-resty-shdict-simple](https://github.com/openresty/lua-resty-shdict-simple)


Author
======

Yichun "agentzh" Zhang (章亦春) <agentzh@gmail.com>, CloudFlare Inc.

[Back to TOC](#table-of-contents)

Copyright and License
=====================

This module is licensed under the BSD license.

Copyright (C) 2016, by CloudFlare Inc.

All rights reserved.

Redistribution and use in source and binary forms, with or without modification, are permitted provided that the following conditions are met:

* Redistributions of source code must retain the above copyright notice, this list of conditions and the following disclaimer.

* Redistributions in binary form must reproduce the above copyright notice, this list of conditions and the following disclaimer in the documentation and/or other materials provided with the distribution.

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.

[Back to TOC](#table-of-contents)

See Also
========

* [lua-resty-shdict-simple](https://github.com/openresty/lua-resty-shdict-simple)
* [lua-resty-lrucache](https://github.com/openresty/lua-resty-lrucache)
* [lua-resty-memcached](https://github.com/openresty/lua-resty-memcached)
* [lua_shared_dict](https://github.com/openresty/lua-nginx-module#lua_shared_dict)

[Back to TOC](#table-of-contents)
