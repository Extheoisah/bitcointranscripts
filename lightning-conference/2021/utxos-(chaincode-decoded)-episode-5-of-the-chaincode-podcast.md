---
title: "UTXOs (Chaincode Decoded) - Episode 5 of the Chaincode Podcast"
transcript_by: Extheoisah via review.btctranscripts.com
media: https://www.youtube.com/watch?v=sNLbg9BUV4Y
tags: ["UTXO","Lightning"]
speakers: ["Adam Jonas","John Newbery","Adam Back"]
categories: ["Conference"]
date: 2020-12-02
---

## Introduction

Speaker 0: 00:00:00

If we go back in time to version 0.1, all that was stored was the blockchain and I think a marker saying whether that coin was spent or not. I mean that's okay for your first version but it doesn't really scale and it's bad performance because every time you want to access a coin you need to read it from disk. We need it from disk.

Speaker 1: 00:00:28

Welcome back to the Chaincode Podcast. This episode is going to be a little bit different. We're going to do something, there's no guest, it's just John and I talking. Hi, Jonas. Hey, John. This was suggested to us by one of our listeners, and credit goes to Amiti on this. She suggested that we rewind a little bit and talk about some of the technical concepts that we're discussing with our guests. We're going to call this Chain Code Decoded. What do you think about that, John? Johnathan Williams I think it's

Speaker 0: 00:00:56

a great name. I love it. Peter Bellis We've

Speaker 1: 00:00:58

been desperately looking for an opportunity to use it.

Speaker 0: 00:01:03

Welcome to the Chaincode Decoded podcast.

Speaker 1: 00:01:11

So what are we going to talk about today, John?

Speaker 0: 00:01:13

Well, we thought it'd be interesting to go back and discuss something that we talked to two of our guests

## Ultraprune ( - [episode 1]

about. In episode one we had Sipa talking about Ultra Prune, which was a PR that he implemented in 2012.

## AssumeUTXO ( - episode 4

And in episode four we talked to James O'Byrne about Assume UTXO, which is an ongoing project that he's working on right now. And in both of those episodes, we discussed something that we call the UTXO set. So today we're going to go back and talk about that some more and give a bit more context on what that is.

Speaker 1: 00:01:45

Very good. So maybe start off what is the UTXO set? The UTXO

Speaker 0: 00:01:51

set is Bitcoin. It is what Bitcoin is and it is what the consensus algorithm behind Bitcoin is designed to allow us to all reach a shared state on what the UTXO set is. So, if I have a wallet in Bitcoin, I don't have an account, there's no such thing as accounts on the network level, on the protocol level, I just have a bunch of transaction outputs. And we call those my coins. And when I want to create a transaction I spend some of my coins and I create new coins. So Bitcoin is this set of coins, a set of transaction outputs, and every transaction can be seen as a patch on that set. It removes some items and adds new items. It removes the outputs that are spent and adds the new outputs from those new transactions. And if we think about a block, a block is an aggregate of transactions and it can be thought of as the union of all of those patches. So a block removes some UTXOs from the set and adds new UTXOs that get created in that block. And if we think about the blockchain, that is simply a sequence of these blocks, each of which removes some UTXOs and adds new UTXOs. So the blockchain is this append-only log with proofs attached. And if I give you that log and you replay it, each of those blocks in turn will amend that set. And At the end, you should arrive at the same result that I arrive at. That is what we call consensus. We've reached the same result. Cool. Tell me a little bit about the

Speaker 1: 00:03:27

difference between the abstract concept of the UTXO set and maybe the more concrete implementation in Bitcoin Core or in Bitcoin. Yeah,

Speaker 0: 00:03:37

so what I've been talking about so far as a UTXO set is a very abstract idea. It exists as part of the protocol, it's something we can talk about and think about. But let's kind of move from that to how it's implemented in software and what the data structures are and what's actually going on in your Bitcoin Core node to turn that kind of abstract idea into something that's more real and concrete. In Bitcoin Core, as it stands today in 0.19 and 0.20, we have what's called a coins database. So all these coins are stored either in memory or on disk in this large database that is indexed by what we call the out point and that out point consists of the transaction ID and the index of the transaction output that the coin is. If we go back in time to earlier versions of Bitcoin, let's go right back to the beginning to version 0.1, there was no concrete implementation of the UTXO set. It was an abstract idea that people could talk about later. But actually, in software, all that was stored was the blockchain and an index from the transaction ID into that blockchain together with I think a marker saying whether that coin was spent or not that's I mean That's okay for your first version, but it doesn't really scale and it's bad performance because every time you want to access a coin you need to read it from disk. So in 0.8 in 2012, Peter Woller implemented Ultraprune, and go back to episode 1 if you want to hear about that. And then in 0.15, which would have been around 2016, the format of that database was changed. It was previously keyed on just the transaction ID and then each of the outputs was a sub-entry below that to being keyed by the output itself. So, how big is this data structure at this point? And I guess, in what cases do we need to access it? Data structure at this point, I believe, is on the order of four gigabytes, plus or minus a gigabyte I think. And so for a large machine you can easily store all of that in memory, but for a less powered machine you're not going to be able to get all

Speaker 1: 00:05:57

of that in RAM. And maybe before you go too much further, what are the advantages for people who are on the cusp of understanding what you're talking about? What are

Speaker 0: 00:06:05

the advantages of keeping in memory or putting it on disk? Performance, predominantly. So accessing data from disk, either to write it to disk or to read it from disk, is many, many times slower than accessing memory RAM. So it might be... It's the order of like 100, 000 or something like that, yeah. So if you scale up the times and it's like a second for memory, then it would be months or years. So it's just much, much quicker to keep stuff in memory when you can, but obviously memory is limited, and so if this data set becomes too big for your machine's memory, you need some way of what we call flushing that to disk. And so we have most of it stored on disk. And then above that we have what's called a cache for items that we think are going to be accessed more frequently. And that way we can get good performance and yet we can still store the entire data set without running out of memory. Peter. So in what cases do we need to access this? We access this whenever we try and spend a transaction. So if a transaction comes in, that transaction contains inputs, which are the coins that are being spent, and it produces new outputs. And so to verify that transaction as a full node, I need to first of all make sure that those coins exist, that it's trying to spend, so it's not just trying to make money out of thin air, and also that it fulfills the spending conditions of those coins. And those spending conditions are usually producer signature for the public key, which is associated with this output. So that's what the UTXO set does. It allows a full node to verify first of all the existence, and then verify the spending

Speaker 1: 00:07:51

conditions are met when that coin tries to be spent. Peter Bellis So there's got to be, we're talking about memory, we're talking about flushing the disk, there has to be some sort of cache layer as well. Can you tell me,

## The cache layer

does that exist? And if so, are there different layers to it? Yeah,

Speaker 0: 00:08:06

exactly. There are many layers to it. So at the bottom, you have your disk. And when you shut down and start up, that's where everything lives, because the memory does not persist between running Bitcoin Core and shutting it down and starting up. So at the end, when you shut down your Bitcoin D-node, it saves everything to disk. At the start, you load it up and you have all of the coins on disk. And Then you want to access those coins and keep them in memory where you can. And as you run Bitcoin and progress through the blockchain, you'll access coins to spend them but you'll create new coins as they're being created in transactions and those will be stored in a cash. And that cash actually is more of a, it's generally more of a write buffer than a read cash because generally We only read coins once because coins only get spent once. So you don't get much benefit from a read cache where you're reading the same thing many times, but you do get benefit if you say, create a new coin, keep it in your cache, and then spend it before you need to flush it to disk. That's a big win, especially in initial sync. So we have the disk, that's the base layer. Above that we have our coins cache. And then in various places we might have a cache above that, the second layer. So for example the mem pool is a cache above that, or when we're connecting a block we'll have a cache above that and if that block is successfully connected we'll flush that higher level cache down to the lower level cache. And if it doesn't succeed in being connected, we just throw away that higher level cache so those changes don't propagate down.

## dbcache parameter

Speaker 1: 00:09:51

So listeners might have heard of the dbcache parameter. What's that and why does that affect what's going on during initial sync, also known as initial lockdown load?

Speaker 0: 00:10:02

So dbcache is a parameter that controls the size of that first lower level cache that I talked about earlier. And obviously the bigger that is, the less frequently you need to flush out that cache. The default setting for dbcache is 300 megabytes. So that means when you turn Bitcoin on for the first time, and you start downloading blocks, you'll be creating these new coins. The blocks will be adding new coins to your UTXO set stored in your coins cache. And when that reaches 300 megabytes, the cache is full up and you need to flush the disk. And that is pretty slow. It's a little bit disruptive to initial sync because everything pauses whilst you write everything to disk and then subsequently everything is cleared from that cache. So when you come to spend those coins in a later block, it needs to be read from disk again. So you lose both on having to write and having to read. The larger that DB cache is, the less frequently you need to do that flush to disk. So if you have a slow block download and you have memory to spare, try bumping up that DB cache to a higher number than 300. Incidentally, I prefer the term initial sync to initial block download, because there's a lot more going on during that process and downloading blocks. The vast majority of work is taking signatures and doing these flushes to disk and reads from disk.

## Different ideas on how to keep track of the UTXO set

So I think initial sync kind of captures that whole process a bit better. Got it.

Speaker 1: 00:11:34

So this set seems to change a lot and we're talking about, obviously it's very important and everybody has to keep track of it. So this sounds like a problem for accumulators or something like that. Are there sort of different ways to keep track of the set or is anyone working on something like that?

Speaker 0: 00:11:53

Yeah absolutely. There have been a lot of proposals, a lot of theoretical ideas about what you can do instead of just keeping this very large set of data. If we go back to what I said the UTXO set was used for, first was existence of a coin in that set and second was to know what the conditions of spend of that coin are. So we can use, potentially use, interesting cryptographic constructions where we don't actually need to store that data ourselves. We simply store some artifact. Let's call it an accumulator where If someone wants to spend a coin in a transaction, they provide some succinct proof that that coin is a member of the set. And if they give you that proof and you have this artifact, this accumulator thing, you know for sure that that coin existed. So it's an interesting area of research and there are lots of variations on how you might do this. The main downsides, well there are many downsides, But areas for research are the performance of these accumulators or accumulator-like structures, and whether doing this changes the trust model. This would obviously be very disruptive for the peer-to-peer layer because you're changing the traffic and what you need to receive when validating a block or a transaction. So right now it's more kind of an area of scientific research, but in the future we might see potentially Bitcoin moving to a model where instead of having to store this very large data set, you simply store a much smaller set. And that set could be some kind of accumulator, like a traditional RSA accumulator. It could be some kind of Merkle construct, like a Merkle tree or mountain range. And

## utreexo

an interesting proposal here comes from Taj Dreyer in the form of UTXO.

## What’s the future of the size of the UTXO set?

So look up that paper if you're interested in this.

Speaker 1: 00:13:57

So the way you're talking about this, the UTXO just continues to grow. Is that the case? Are there ways to consolidate it? Do we want it big? Do we want it small? What's the future of the UTXO set? Well,

Speaker 0: 00:14:11

smaller the better, because this is a data set, a structure that every four node in the network needs to store. Even if you run a pruned node, there's no way to prune the UTXO set. You need it if you want to validate all blocks and transactions coming in. So everything else being equal, we'd like it to be small. And Unlike the blockchain, which always grows, the UTXO set doesn't necessarily always grow. We've seen points in the past where the UTXO set has shrunk.

Speaker 1: 00:14:40

And why? Historically, why would that happen?

Speaker 0: 00:14:43

Well, during 2017, it grew. There were lots of transactions, lots of traffic on the network and fees were not very high. And following that in 2018, fees reduced again. And A lot of actors

## UTXO consolidation

on the network saw that as an opportunity to do what's called UTXO consolidation. Where they had a purse full of very small coins and they took those and combined them into one larger coin. And across the network, hundreds of thousands or millions of UTXOs were consolidated in

Speaker 1: 00:15:20

this way. And so for a company, why would they want to do that?

Speaker 0: 00:15:24

Well, for a company it's in their financial interests because if fees go up again in future, The fee that you pay is proportional more or less to the number of UTXOs that you use as inputs to that transaction. So if you can use them all up to consolidate when fees are low, when fees go higher, you will save money because you'll have fewer UTXOs and therefore fees will be lower.

Speaker 1: 00:15:48

But are you trying to clean up things that are close to dust limit? And if you're thinking of pennies and turn them in for a $10 bill, or are you trying to take all your pennies and turn them into a $100 bill or even larger?

Speaker 0: 00:16:03

Well, It depends on your usage pattern. There are trade-offs. In general, fewer UTXOs are better in terms of cost, but there might be a trade-off in terms of privacy. Especially for an exchange, they might want to have many UTXOs in their hot wallets so they have available UTXOs to make withdrawals. So they need some kind of management of their UTXO set, which does not just fully optimize for having one big UTXO. So there are trade-offs. But in general, wallet behavior can have a big impact on what happens to the UTXO set over time, and especially default wallet behavior in popular wallets. So Bitcoin Core in, I think, around 0.18, I'm guessing, changed the way

## UTXO selection

that it selects UTXOs for inclusion in a transaction from quite a naive model to a more sophisticated model called branch and bound, which would optimize for not creating change outputs. And so over time you'd expect the number of UTXOs in your wallet to decrease because you're not creating change outputs. And if everyone does that across the network then the global UTXO set shrinks.

Speaker 1: 00:17:16

Well that was a great conversation. I guess we want to hear from the listeners as to whether they thought this was useful and whether we should do more of these in the future. Do you want to hear from guests? Do you want to hear from John and I just talking about technical stuff? It's up to you. So we'd love to hear from you. Thanks.

Speaker 0: 00:17:35

Bye
