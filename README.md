# igropyr-platform

`(igropyr platform)` — supported-host detection and shared-library
loading for [Chez Scheme][chez], part of [Igropyr][igropyr]. It reads
the Chez machine type to name the OS and architecture, refuses
unsupported hosts, loads the first shared library that exists from a
per-OS candidate list, and carries the handful of struct-offset
constants the FFI layers need.

## API

```scheme
(import (igropyr platform))

platform-os                     ; 'macos | 'linux | 'freebsd | 'unsupported
platform-arch                   ; 'x86_64 | 'arm64 | 'unsupported
(ensure-supported-platform!)    ; raise unless the host is supported
(load-first-shared-object! who candidates)  ; load the first name that works
addrinfo-address-offset  addrinfo-next-offset   ; struct addrinfo (getaddrinfo)
uv-stat-mode-offset      uv-stat-size-offset     ; libuv uv_stat_t
```

`load-first-shared-object!` tries each candidate name in order and
returns the one that loaded, or raises through `who` naming every
candidate when none can be found. The offset constants encode the LP64
`struct addrinfo` layout differences between BSD (macOS/FreeBSD) and
Linux, and libuv's platform-independent `uv_stat_t`.

## Dependencies

Imports one sibling library, [`(igropyr util)`][util], for its string
helpers. Put both on the library path together.

## Layout and use

The source is `platform.sc`; the library it defines is `(igropyr
platform)`. Chez resolves that name to `igropyr/platform.sc` on the
library path, so put `platform.sc` (and `util.sc`) in an `igropyr/`
directory that is on your `CHEZSCHEMELIBDIRS`, and include `.sc` in the
library extensions. Inside Igropyr, drop it alongside the other
`igropyr/*.sc` sources — it is already listed in the build.

```sh
CHEZSCHEMELIBDIRS=. CHEZSCHEMELIBEXTS=.sc scheme --script your-program.ss
```

## License

MIT. See [LICENSE](LICENSE).

[chez]: https://www.scheme.com
[igropyr]: https://github.com/guenchi/Igropyr
[util]: https://github.com/guenchi/igropyr-util
