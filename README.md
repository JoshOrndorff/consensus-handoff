# Substrate Consensus Handoff

Ethereum will soon migrate from Proof of Work to Proof of Stake. Preparing this miagration has taken years of planning to get just right. This repository explores whether such a handoff is Possible for a Substrate chain, and how hard it might be to develop.

# Roadmap

This is exploratory, so I'll probably learn a lot along the way, but this is my rough plan for now.

## PoW Only
Migrate the node template to PoW authoring. This will help us see all of the relevant parts of Aura and PoW that will need to work together

## Pause after ten blocks
Make importing nodes only accept PoW blocks up to height N, then, instead of verifying PoW, look for an aure digest, and panic if it isn't found. In this scenario, upgraded nodes will refuse to import valid PoW blocks.

## Actual Handoff
This is the MVP step. If we can accomplish this, the rest is butter. Although churning butter is still a lot of work.

Here Upgraded nodes will actually author Aura blocks after height N

## Democracy over handoff
In Substrate world, on-chain governance is common. Allow the community to vote whether the handoff should happen, and if so, at what block height. Provide this information to the consensus layer through a runtime API:

```Rust
/// Trait for passing consensus handoff information from the runtime, where the vote occurs
/// to the consensus layer where it will be executed.
pub trait ConsensusHandoff {
  /// Check whether the handoff is complete. If it is, the new consensus logic should be used.
  fn handoff_block() -> bool;
}
```

## Forking Miners
Some miners may wish to fork the network to stay on the PoW chain. Until now the way to fork is to not upgrade the node. However, the PoW fork will likely want the non-pos-related improvements in future releases. So we add support for ignoring the handoff and staying on the PoW fork. Now the forking miners can run updated software and still fork.

Add support for:
* Never fork
* Fork at specific block
* Fork according to runtime API