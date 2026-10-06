# MUSUBI peer contract: `agent-passport.org` ↔ `kuangmi-bit.github.io`

A two-party contract made under [issue #31](https://github.com/ogasurfproject-jpg/horizon-shield/issues/31)
of `ogasurfproject-jpg/horizon-shield`, by two operators, with nobody from that project as a party, a
witness, an approver or the ledger. This repository is the contractor's copy and the place the issue asks
the pair to publish in.

Made with `peer_kit.py` from that repository and nothing else. Nothing here needed an account, a key or a
server of theirs.

## Parties

| seat | domain | public key |
|---|---|---|
| principal | `agent-passport.org` | https://agent-passport.org/keys/agreement.json |
| contractor | `kuangmi-bit.github.io` | https://kuangmi-bit.github.io/keys/agreement.json |

## Task, pinned by `payload_digest`

> independent reproduction of the NENRIN interop-v0.2/edge corpus at horizon-shield commit
> `7546b106ec37f9298725e2cfcc5c4647f2ebe08e` by `kuangmi-bit.github.io`, publishing the verdict
> signatures under the contractor's repository `kuangmi-bit/nenrin-independent-verifiers`

`payload_digest`: `5d6a3dd5c30c0de742debec2804d46d167d9cc666e5ca4e4cb222e0349d07c43`
(sha256 of exactly that sentence, as the existing contracts do it)

Authorized: `read`. Prohibited: `delete`, `payment`. Earliest use: Bitcoin block `970190`.

## Files

| file | what it is | sha256 |
|---|---|---|
| `params.json` | the parameters both sides can rebuild the contract from | `f890e5689f5c75f965e4bf45441f5daba2e9b9bc0159327a64b2d32295d49111` |
| `c.unsigned.json` | the unsigned contract, as generated | `8e3b6acc16746bced8bd6fa3cce867e46e604770866303dfce0aea1a09bfe97a` |

`contract_sha256` (the canonical form with signatures left out, which is what the parties sign):
`9a450534e94473fcb4d8e241a550c3049eb77ca94ccc7e76c9a621f2bcf6ff09` · `contract_id`
`34ee9d22f9f448de7f15db1e1dd4d697`. The file hash and `contract_sha256` differ on purpose: the
signatures are excluded from the latter by construction.

## Status

- [x] both keys published; `peer_kit.py selftest` ALL PASS on the contractor's machine (5 checks)
- [x] unsigned contract drafted and published here
- [ ] signed by both parties → `c.AB.json`
- [ ] execution signed by the contractor → `e.json`
- [ ] OpenTimestamps proof → `e.stamp.ots`, then `ots upgrade`
- [ ] anchor and settle

Each later step is committed with its own hashes rather than overwriting anything.

## Read this before counting it as adoption

The contractor is already credited twice in that project (the two independent implementations, and PR
#30) and runs [a witness repository](https://github.com/kuangmi-bit/conduct-witness) for it. So this is
two operators who met in that thread, one of whom was already working there. It is not a first contact
and should not be read as one. The pairing itself happened in public, in the thread, where anyone can
check it.

## What this does and does not establish

The contract's own `does_not_establish` list is the honest one: that anyone enforced anything at runtime
(the grant is proved against the records afterwards), that the contractor obeyed the grant, that anyone
judges liability or fault, that a prohibited action was impossible, or that this is a legal contract.
What it does establish is narrower: both parties signed these bytes at a stated time, and the contractor
named by sha256 exactly which actions it was authorized and prohibited to perform.

## Verify it yourself

```bash
git clone --depth 1 --filter=blob:none --sparse https://github.com/ogasurfproject-jpg/horizon-shield
cd horizon-shield && git sparse-checkout set workers/hs-ledger/nenrin/musubi-v0
cd workers/hs-ledger/nenrin/musubi-v0
python3 peer_kit.py selftest
curl -s https://agent-passport.org/keys/agreement.json | head -c 200
curl -s https://kuangmi-bit.github.io/keys/agreement.json | head -c 200
python3 peer_kit.py verify --contract c.AB.json    # once both signatures are in this repository
```

The published contract bytes are here so that anyone can recompute the verdicts around them; no license
is claimed over them.
