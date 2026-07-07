<p align="center"><img src="https://raw.githubusercontent.com/go-net-dhcp/brand/main/social/go-net-dhcp.png" alt="go-net-dhcp" width="640"></p>

<h1 align="center">go-net-dhcp</h1>
<p align="center"><strong>A small, dependency-free DHCPv4 server library in pure Go — no cgo.</strong></p>

<p align="center">
  🌐 <a href="https://go-net-dhcp.github.io">Website</a> ·
  📚 <a href="https://go-net-dhcp.github.io/docs/">Documentation</a>
</p>

<p align="center">
  <a href="https://go-net-dhcp.github.io/docs/"><img alt="Docs" src="https://img.shields.io/badge/docs-mkdocs--material-2563EB?style=flat-square"></a>
  <a href="https://github.com/go-net-dhcp/dhcp/blob/main/LICENSE"><img alt="License: BSD-3-Clause" src="https://img.shields.io/badge/license-BSD--3--Clause-blue?style=flat-square"></a>
  <img alt="Go 1.26.4+" src="https://img.shields.io/badge/go-1.26.4%2B-00ADD8?style=flat-square&logo=go&logoColor=white">
  <img alt="Coverage 100%" src="https://img.shields.io/badge/coverage-100%25-1a7f37?style=flat-square">
</p>

---

**go-net-dhcp** is a pure-Go (no cgo) **DHCPv4 server library**. It implements the
RFC 2131 / 2132 wire format (parse a DISCOVER/REQUEST, build an OFFER/ACK/NAK), a
stateless platform-agnostic decision core, and a real Linux UDP/67 server bound
to a single kernel interface via `SO_BINDTODEVICE`.

It targets the *"one subnet, hand out a lease per known MAC"* case — a host that
owns a bridge / VLAN and needs to answer DHCP for the guests it spawns on it,
**without running an external dnsmasq**. You supply a `Source` that resolves a
client MAC into a `Lease`; the library never persists anything itself.

The DHCPv4 wire codec is hand-rolled, so the core module has **zero third-party
dependencies**. Metrics are optional, and the Prometheus adapter lives in a
separate sub-package so the core stays lean.

## Repositories

| Repo | What it is |
|------|------------|
| [**dhcp**](https://github.com/go-net-dhcp/dhcp) | the library: RFC 2131/2132 wire codec, the `Decide` state machine, the Linux UDP/67 server, and the optional `prom` metrics adapter |
| [**docs**](https://github.com/go-net-dhcp/docs) | MkDocs Material documentation, versioned with [mike], served at [/docs/](https://go-net-dhcp.github.io/docs/) |
| [**go-net-dhcp.github.io**](https://github.com/go-net-dhcp/go-net-dhcp.github.io) | the Hugo landing page |
| [**brand**](https://github.com/go-net-dhcp/brand) | logos and brand assets |

## Principles

- **Pure Go, zero cgo.** Cross-compiles and embeds anywhere; a static binary by
  default.
- **Dependency-free core.** The DHCPv4 wire codec is hand-rolled; the optional
  Prometheus adapter is quarantined in its own sub-package.
- **Stateless & caller-driven.** You own the address plan via a `Source`; the
  library resolves a MAC to a `Lease` and never persists state.
- **Testable to the socket.** The Linux socket path is covered without root via
  injectable syscall seams, so every error branch is exercised in CI.
- **100% test coverage** is the target, enforced as a CI gate.

## Status

**Server library complete.** RFC 2131 / 2132 parse + build (OFFER / ACK / NAK),
the `Decide` state machine (DISCOVER→OFFER, REQUEST→ACK, mismatched requested-IP
→NAK, unknown MAC / other message types→drop), a real Linux `SO_BINDTODEVICE`
UDP/67 server, a cross-platform `StubServer`, and an optional Prometheus metrics
adapter. 100% coverage (socket error branches included, via syscall seams — no
root needed), `gofmt` + `go vet` clean, CI green across the six 64-bit Go targets
(amd64, arm64, riscv64, loong64, ppc64le, s390x).

BSD-3-Clause.

[mike]: https://github.com/jimporter/mike
