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
| `c.unsigned.json` | the unsigned contract, as generated (corrected draft, see below) | `d3f2cdf85f1777867673951330e7f68c9391d0a3e26b919d8af873e869c77671` |
| `c.AB.json` | the contract carrying both signatures: A's as posted in the thread, B's added here | `8633b800f04b36654f517b35cee8d673c716ceccd36db49fbb2f4be8545f1fcb` |
| `e.evidence.json` | what the `read` action was performed on and what it reproduced — the object `nenrin_ref` hashes | `5f0763763915d58a8c0303e0a1cb6cdd11c83bdbd991f9b883023a84d127a93a` |
| `e.json` | the contractor's signed record of the action taken (`a2a-execution-v0`) | `af3e0b4918041119aad7bcda81372945e3d189693557a2152f11b19268fe6e31` |
| `e.stampable` | canonical bytes of `e.json` without `anchor`, the file handed to `ots stamp` | `11a2ec6cbdde5a10f8fce9b5a82ae80a458626c78e2bd5683a4f3517140564ef` |
| `e.stampable.ots` | the OpenTimestamps proof over those bytes | `614e20578cf403f0115d7684e633ba1949a89ce080a3d0c4540251b396889e64` |

`contract_sha256` (the canonical form with signatures left out, which is what the parties sign):
`d7118f285250241513db1ce244a7ec4b4fbfd9160123ba466abdfad727b70f81` · `contract_id`
`34ee9d22f9f448de7f15db1e1dd4d697`. The file hash and `contract_sha256` differ on purpose: the
signatures are excluded from the latter by construction.

### Revision: the corrected unsigned draft

The first draft (commit `2f8d1dc`, file sha256 `8e3b6acc…`, `contract_sha256` `9a450534…`) said in
`establishes` that the parties signed "at the stated time". The record cannot support that: `agreed_at`
is set when the draft is built, and the signature records carry no signing time. `aeoess` raised it in
the thread and `ogasurfproject-jpg` fixed the wording upstream in `d7d78582`.

This file is regenerated from the same `params.json`, at `peer_kit.py` `29a624cc`, with the three
non-content values pinned from the first draft:

```
git show 2f8d1dc:c.unsigned.json > draft1.json
python3 peer_kit.py contract --params params.json --pins-from draft1.json \
  --expect d7118f285250241513db1ce244a7ec4b4fbfd9160123ba466abdfad727b70f81 --out c.unsigned.json
```

It reproduces `d7118f28…` here (`"match": true`), and differs from the first draft in exactly two
leaves — `establishes[0]` and the added `does_not_establish[5]`. Task, grant, parties, `lower_bound`,
`contract_id`, `nonce` and `agreed_at` are byte-for-byte unchanged.

### Signatures

The principal signed first; its signature was posted in the thread on 2026-10-08T01:25Z. The contractor
added its own to those exact bytes with `peer_kit.py` at `29a624cc`, on the machine holding the key
pinned in `parties[1]`:

```
$ python3 peer_kit.py sign --contract c.A.json --key me.pem --domain kuangmi-bit.github.io --out c.AB.json
{"wrote": "c.AB.json", "signatures": 2}
$ python3 peer_kit.py verify --contract c.AB.json
{"verdict": "accepted", "refusals": [], "findings": [], "contract_sha256": "d7118f28…f81"}
```

`peer_kit.py sign` refuses any key the contract does not pin, so a signature from any other key could not
have been added. `c.AB.json` is the signed copy; `c.unsigned.json` and the first draft are kept as they
were, because the revision history is part of what this repository is for.

### Execution

The action this contract authorizes is `read`: fetch the corpus at the pinned commit, recompute it
locally, change nothing. The contractor ran its own published verifier over `interop-v0.2/edge` and
reproduced **36/36** verdict signatures, then signed a record of it:

```
$ python3 peer_kit.py exec --contract c.AB.json --key me.pem --actions read \
    --nenrin-ref 5f0763763915d58a8c0303e0a1cb6cdd11c83bdbd991f9b883023a84d127a93a --out e.json
{"wrote": "e.json"}
```

`e.json` binds to the contract by `contract_sha256` and is signed in the contractor seat. Its
`nenrin_ref` is the sha256 of `e.evidence.json` here, which fixes what was read and what came out of it:
the corpus commit, the `expected.json` it was checked against, the reproducer's repository, command and
output. Anyone can repeat it from the published bytes:

```
git clone --depth 1 https://github.com/kuangmi-bit/nenrin-independent-verifiers
cd nenrin-independent-verifiers && ./fetch_corpus.sh
```

The OpenTimestamps proof was made over `e.stampable` — the canonical bytes of `e.json` without `anchor` —
so the stamp covers the record and nothing else. In this commit it is still a pending attestation;
`ots upgrade` follows, then `anchor` and `settle`, each as its own commit.

## Status

- [x] both keys published; `peer_kit.py selftest` ALL PASS at `29a624cc` on the contractor's machine (6 checks)
- [x] unsigned contract drafted and published here
- [x] signed by both parties → `c.AB.json`
- [x] execution signed by the contractor → `e.json`, evidence `e.evidence.json`
- [x] OpenTimestamps proof made → `e.stampable.ots` (pending; `ots upgrade` to follow)
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
judges liability or fault, that a prohibited action was impossible, that this is a legal contract, or
that the record shows when either signature was produced (`agreed_at` is the declared drafting time, not
a proven signing time). What it does establish is narrower: both parties signed these grant bytes, and
the contractor named by sha256 exactly which actions it was authorized and prohibited to perform.

## Verify it yourself

```bash
git clone --depth 1 --filter=blob:none --sparse https://github.com/ogasurfproject-jpg/horizon-shield
cd horizon-shield && git sparse-checkout set workers/hs-ledger/nenrin/musubi-v0
cd workers/hs-ledger/nenrin/musubi-v0
python3 peer_kit.py selftest
curl -s https://agent-passport.org/keys/agreement.json | head -c 200
curl -s https://kuangmi-bit.github.io/keys/agreement.json | head -c 200
python3 peer_kit.py verify --contract c.AB.json    # expected: accepted, no refusals
```

The published contract bytes are here so that anyone can recompute the verdicts around them; no license
is claimed over them.
