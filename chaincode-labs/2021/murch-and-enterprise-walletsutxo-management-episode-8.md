---
title: "Murch and Enterprise Wallets/UTXO Management - Episode 8"
transcript_by: Extheoisah via review.btctranscripts.com
media: https://www.youtube.com/watch?v=yy6Mt5-fl0g
speakers: ["Adam Back","Adam Jonas"]
categories: ["podcast"]
date: 2020-11-09
---

## <Untitled Chapter 1>

Speaker 0: 00:00:00

Just to throw out a few numbers there, non-SegWit inputs cost almost 300 bytes, and native SegWit inputs cost slightly more than 100 bytes. There's almost a reduction by two-thirds in fees if you switch from non-SegWit to native SegWit. To native segwit. Hi, everyone.

Speaker 1: 00:00:29

Welcome to the Chain Code podcast. My name is Carolee.

Speaker 2: 00:00:32

And it's Jonas.

Speaker 1: 00:00:33

And we're back!

Speaker 2: 00:00:34

We're back.

Speaker 1: 00:00:35

We're not all back. So, while Jonas and I are coming to you very socially distanced from the New York office...

Speaker 2: 00:00:43

John is the most socially distanced.

Speaker 1: 00:00:46

So, John is now in London.

Speaker 2: 00:00:48

Sunny old London.

Speaker 1: 00:00:51

So while we are sad to not have him with us, we are very happy to be back in the studio. We

Speaker 2: 00:00:57

have some new people that work here though. That's exciting.

Speaker 1: 00:00:59

That is very exciting. You

Speaker 2: 00:01:00

want to talk to Merch for the first one back? I

Speaker 1: 00:01:02

think it would be a great idea for you to talk to some call him Mark. Mark. And what do you think you guys are going to talk about? He worked at BitGo before he came

Speaker 2: 00:01:11

to Chaincode, so he knows a ton about UTXO management and enterprise wallets and stuff like that. So let's start there. So welcome to the Chaincode podcast, Mert. Hey, thanks. Welcome to the Chaincode podcast, but also welcome to Chaincode. Oh

Speaker 0: 00:01:30

yeah, thank you. Yeah,

Speaker 2: 00:01:31

what do you think about the office and moving to New York and all that?

Speaker 0: 00:01:35

Well, that sounds like you're fishing for compliments. This is the most beautiful office I've ever worked in, obviously.

Speaker 2: 00:01:41

Can you say the same about New York?

Speaker 0: 00:01:44

I am very happy to be back in a big city. It suits my lifestyle. I'm so glad not to own a car anymore. Yeah. I mean, the pandemic makes it all a little less lively and less social. So, looking forward to all of that being behind us eventually. Yeah. So

Speaker 2: 00:02:06

everybody calls you Merch. You prefer to be called Merch. Merch is fine. Where does Merch come from?

Speaker 0: 00:02:12

I don't know. I needed a nickname for computer games like 20 years ago. And an urchin has spiked hair. I had spiked hair at that time. Some people would call me basically sea urchin in German. So it's sort of like that meeting a character from a book, a

Speaker 2: 00:02:36

fantasy novel that I had read, and it stuck. And you were Bicco for five years, four years?

Speaker 0: 00:02:45

No, for three and a third.

Speaker 2: 00:02:49

Great. Well, I'm glad you're going to be building and answering questions and reading and writing here at Chincode. So welcome. Thank you. Today, we're going to talk a little bit about UTXO management, and it's something that you've been diving into for quite a while. Maybe give us an overview of what you see as UTXO management and the world of enterprise wallets.

Speaker 0: 00:03:09

Yeah, sure. Well, as you already stated in your last episode, or second last episode, with the UTXO set, Those are the pieces of Bitcoin that float around in the network. They are essentially what the entries of the ledger is. Hey, I have money here, this amount, this address it was received to, and this is the locking conditions that I have to fulfill in order to spend it. So it

## Enterprise UTXO management

turns out that when you have an enterprise wallet, you have tons of these because you are scaling to make payments all day long with hundreds to thousands or tens of thousands customers that want to trade on your platform. So they deposit into your wallet, they withdraw from your wallet. And it's a little different than having a mobile phone wallet and you make a payment once a month. So there's a huge efficiency of scale to be had there. Maybe

Speaker 2: 00:04:08

to give us a little bit more context, like in 2017, fees and the UTXO set bloated. So tell us a little bit about that time and then maybe how enterprise

Speaker 0: 00:04:19

wallets have changed since. Yeah, in 2017 there was a big hype cycle peak for Bitcoin. People got really excited

## The impact of the 2017 hype cycle

about Bitcoin and traditionally the times when the price is making big swings is also a time when people participate more in trading, which means they move money back to exchanges and from exchanges. So the transaction volume goes up as price volatility goes up. And especially towards the end of the year, there was just an immense amount of speculation in the market. And between October and January, basically the mempool, the queue of transactions to be confirmed in blocks was just out of bounds, completely crazy.

Speaker 2: 00:05:10

And so tell us again, just for review, why is creating this huge set of UTXOs bad for individuals and sort of the ecosystem as a whole?

Speaker 0: 00:05:22

Right. So everybody has to keep track of all pieces of Bitcoin that float around. And

## The importance of UTXO set minimization

this main set, this UTXO set, is the means of doing so. There's a huge performance gain if you can keep it completely in memory, but if it gets too large that's not feasible anymore. And then computers that don't have a lot of memory can't efficiently process the blockchain anymore. It makes it more costly to run a full node, especially in data centers where it's easily accessible and you pay for the resources you use. It also increases the time for people to catch up with the network if there's more traffic on the blockchain. So the initial sync takes a longer time if there's more transactions. Generally you want to keep the UTXO set big enough that everybody can have their balance represented, but small

Speaker 2: 00:06:17

enough that it's not a resource bottleneck for people to do full validation. Right, that makes sense. So maybe fast forward a little bit and tell us about the fee market now. What's changed in the last four years and where are we today?

Speaker 0: 00:06:37

So I think generally there are more tools and a lot of wallets have implemented more

## The fee market today

tools to respond to rising fees or to backlogs of transactions. Adoption is not quite as high as I would have liked to see. So we had a halving in May. And historically, there used to be a lot of news. It's like, oh, Bitcoin's not dead yet. And it sort of gets picked up by a lot of people that are aware of

Speaker 2: 00:07:09

it, but not following. And then suddenly the price goes through the roof and so does the mempool. Got it. Given that we haven't explicitly said it, so what's the interaction

Speaker 0: 00:07:18

between the mempool and the fees? Right, so the block production is somewhat fixed. It's flexible in that when the miners gain more hash rate, it gets a little quicker. Or in the past couple of weeks, there were some rumors that the rain season in China ended, and then some miners started moving their hardware to other locations where cheaper electricity was to be had. And the hash rate in the global network went down by a sixth in the past two weeks. So we've just been having sometimes even a third lower block production. And now usually we have 144 targeted blocks per day. Most of the time it's more like 150. And right now we're seeing about 96 to 100 blocks per day. So there's just less traffic or less transaction confirmations available for the whole network to go through. So the network can process about maybe 2, 000 to 4, 000 transactions per block. And if there's just two-thirds in the number of blocks, it'll do that much less per day, and if there's the same demand for getting transactions confirmed, they stack up.

Speaker 2: 00:08:37

Okay, so things are starting to stack up now, and we're starting to see the mempool get a little bit more congested. Right. So what is that, given what we've learned since 2017, what tools are available for wallets and at their disposal to deal with this?

Speaker 0: 00:08:55

Right, yeah, I think we haven't said it explicitly. The mempool is the data structure that holds unconfirmed transactions. So what tools there are, for one, you can batch transactions together, which means that you make multiple payments in a single transaction. That's more efficient

## Batching Transactions

because it shares the inputs. The inputs are the big part of the transaction. The outputs are fairly small. And if you make multiple payments in a single transaction, you can have fewer inputs per payment. So the overall size of the transaction is smaller per payment. And that is the efficiency gain. Because the limit of what can be put in the blockchain is the block space. And if you use less space to make the same amount of payments, you can fit more payments.

Speaker 2: 00:09:47

Maybe as you're sort of describing these different features available, you could tell us a little bit about how common they are, who's using them, who's taking advantage of them, or... Right.

Speaker 0: 00:09:57

So batching, of course, requires you to make a lot of payments for you to be doable in the first place. If you're making one payment per hour you'd have to make the first recipient wait for over an hour until the second recipient comes along so you can make two payments in one transaction. Obviously that's not gonna work and you're just gonna send out the first payment and then the second payment an hour later. Now, if you're a big Bitcoin exchange and have hundreds of thousands of customers, Some of them want to always, there's going to be some that want to withdraw, and you can just make one payment per minute and send to 20 people at the same time. So you'll continuously just have this rolling set of payments you're batching into a transaction, and just on a clock, tick out a transaction once per minute, and you gain maybe a 70% efficiency gain in block space per payment. Wow, that's

Speaker 2: 00:10:59

quite a bit.

Speaker 0: 00:11:00

Mm-hmm. What about consolidations? What's that? Again, this is something that affects you if you

## Consolidations

have a lot of traffic. Every time you receive a payment, you'll have a new UTXO. And some of them might be fairly small, right? In some countries, say, Bitcoin exchange in South America, the average deposit size will be a lot smaller than the average batch of payments that they're going to make. And let's say people deposit $20 each time. But when you do a withdrawal, altogether you're trying to send out $400 worth of Bitcoin. So you'd have to use 20 pieces of Bitcoin of those $20 pieces to make up those $400, And you'd pay that at a high fee rate because you want to make the withdrawal go through quickly. So you'd have this huge transaction that you pay premium fee rate for to get it through quickly. And what you'd rather do is, well, let's just consolidate all those 20 tiny pieces overnight, and tomorrow I'll have one piece of $400 worth of Bitcoin, and I'll use that to make a payment. But of course, they need the withdrawal now. So you have to be a little foresightful. And you keep continuously consolidating all those tiny pieces when there's little demand for transactions. So overnight, over weekends, when there's a flurry of quick blocks. With the bigger pieces you have in your wallet, you make batch

Speaker 2: 00:12:40

payments with few inputs. Got it. Is there a footprint on-chain when that's happening?

Speaker 0: 00:12:45

Yeah, you can tell fairly easily on-chain because you would be looking at transactions that have a lot of inputs, very few outputs, usually only a single one, because it's a wallet sending to itself, so there's no recipient next to itself. And these might set for a while. So many inputs, few outputs, low fee rate. It's a fingerprint that usually indicates a consolidation transaction.

Speaker 2: 00:13:14

And this may sort of bleed into some other parts of your expertise in coin selection. But when you're creating these kinds of outputs, do you want a diversity of different kinds of outputs? Like how do you sort of think about what it's being consolidated into?

Speaker 0: 00:13:28

Right. Different values do help because you get more options on composing an input set. That can be helpful to avoid creating a change output, which is when the spender sends funds back to themselves because they overshoot on what they wanted to send to the receiver. I don't think that it is necessarily a valid strategy to seed the wallet with specific values. Either it happens naturally for you or you'll have a better efficiency gain by just consolidating and then using a single input instead of combining any.

Speaker 2: 00:14:03

Got it. Okay. Change splitting.

Speaker 0: 00:14:06

Right. Change splitting, yeah, that ties

## Change splitting

into a similar problem. You would want to do change splitting if you have a lot of traffic on a wallet, but you have very few pieces of Bitcoin. So let's say you start a new exchange and you've already lined up a bunch of users and they're chomping at the bit to get started. So you know you'll make a lot of transactions right out of the door. If you start your wallet with one piece of 100 Bitcoin, you'll have a bad time because now in your first transaction you use that piece of Bitcoin and then all of your money is in flight. All of it was used in that transaction. Sure, you'll get the remainder back in a change output, but everybody is going to wait until that transaction goes through or you'll build a chain of unconfirmed transactions, which can lead to other problems. And so what you do is you split the change. Instead of sending, say, 0.1 bitcoin and getting back 99.9 bitcoin in a single piece, you'd split it into, say, eight pieces of various values. And now after the first transaction is confirmed, you'll have eight pieces to work with, and you can do eight transactions in parallel that are not in the same transaction graph. And you'll only send an eighth of your total balance on flight if you make a transaction. That's the basic idea. You want to fan out your value to a good number of pieces, especially if you have a very high traffic wallet. Cool. What other things do we have at our disposal to sort of battle this oncoming fee onslaught? Since 2015 we've had replace by fee. I think it's a little complicated to implement. There's some UX considerations

## Replace By Fee

but it is an extremely powerful tool to save money, especially as an exchange that can use it fairly versatile. Can you tell me what ReplaceByFee is? So BIP 125 is a way of explicitly stating that a transaction will be replaceable. And that's important because there is some interest from people to accept transactions without a confirmation. We call this practice zero conf. They are interested in keeping the traditional behavior of nodes the same, where a node will only accept the first transaction they see that spends specific funds. We call this the first seen safe behavior. So these transactions that mark themselves as replaceable, they explicitly say, please don't trust me. I'm only going to be reliable after I'm included in a block and confirmed. Before that, the spender may replace me at will. So it gives a second way of sending transactions that does not impact the traditional way of sending, the first seen safe way of sending. But people that use replace by fee, they can, for example, put a transaction into the mempool with a low fee rate, barely making it into the next block, maybe at the 90th percentile of the block space. And then as more transactions arrive and get queued to be included in the next block, it might get displaced out of the block, and then they update it with replace by fee, send a higher fee, reduce the amount of change they get back, and bump it up again to, say, the 85th percentile of the block. And then they wait, and once it goes below 95, they bump it up again. So they can continuously keep a transaction at the bottom part of the block. And this is completely different than what most other people do when they send their transactions today. They have to look at what was included in the last few blocks and what is currently in the queue, and then they get a single shot where they pick a fee rate and say, like, all right, this might be good to get it into the next block. I want to be in the next block, or I want to be in the next five blocks. But they have this single shot of guesstimating the right fee rate, and then they just sit there and wait whether it works or not. And sometimes a block takes an hour, and if you wait for an hour and people keep overbidding of what's in the next block, so they put it more on the top of the top one megabyte waiting, it'll get displaced. And then maybe if it's the start of a big backlog, a transaction might sit for five days or 10 days if you look at the current situation in the mempool. And meanwhile, if you're doing replace by fee, you can aim to be in the last quarter of the block. And every time it falls into the last tenth, you keep bumping it up to the last quarter. And you you don't have to participate in this overshooting extremely, which is the primary reason why the top fee rate in the mempool keeps

Speaker 2: 00:19:24

going up and keeps stabilizing itself for days after a fee event. And So wouldn't miners actually be opposed to something like RBF? I mean, wouldn't they want this overbidding to occur? Isn't that in their best interest? That's an interesting question. So I

Speaker 0: 00:19:43

think maybe from a short-term perspective, yes, They would definitely be interested in making more fees. But from a long-term perspective, the overall value that they gain from Bitcoin, I mean, they're being paid in Bitcoin, So if Bitcoin becomes more useful, more valuable in general, their payments and the stashes that they've built up over the years get more valuable. So Maybe in the short term they will try to make as much fees as possible, but in the long term they should be interested in the overall success of the project. And RBF makes it much easier to get the outcome, the transaction confirmation that you want. It also allows people to bid each other up. So I don't know if it overall would lead to lower fees. It would give people more control to stop bidding at a certain point, to be fine with being delayed, or to...

Speaker 2: 00:20:49

It just would make the whole bidding process more flexible and fluid got it So there's a lot of tools again that we've discussed and When we haven't discussed a segwit, So SegWit should be helping us with fee management. And you can talk about that, but I would also like to hear about why

## SegWit

isn't SegWit more prevalent? Why haven't we seen it more?

Speaker 0: 00:21:14

Yeah, So the benefit that SegWit offers is that it increases the available block space in the sense that we can make more payments with a block if we use SegWit. SegWit is a new type of output. Well, I shouldn't say new. It's been around for proposed five years ago, active for three years, SegWit outputs are more block space efficient. If you use SegWit outputs, you directly save money because your transactions will cost less fees, but also the whole ecosystem benefits because your transactions take less of the overall resource, so there's more to go around for everyone. So it's a win-win. Everybody should be using SegWit yesterday. And especially a lot of big enterprises use multi-SIG. Just to throw out a few numbers there, non-SegWit inputs cost almost 300 bytes and native SegWit inputs cost slightly more than 100 bytes. There's almost a reduction by two-thirds in fees if you switch from non-SegWit to native SegWit. Now, we already said, these people, they run enterprises that send transactions once per minute to 20 people. I've worked with customers that pay on the order of $100, 000 per month in transaction fees. And even if all their system was built with non-segwit transactions in mind, and there's a few changes to be made, I think that overall it shouldn't take more than a couple developer months to roll out a feature like that. And then they'd start saving two-thirds of all their fees.

Speaker 2: 00:23:09

Seems like a good investment.

Speaker 0: 00:23:10

Yeah, it just blows my mind that it was a big focus of what I was working on, was working with customers and telling them about how to, when I was at BitGo, to improve their UTXO management. And that seemed like such a low-hanging fruit for everyone. You invest two developer months, you start saving two-thirds of all of your transaction fees, in the case that I said, like $60, 000 a month. I mean, in most places on earth, you can pay a lot of people from that. So, but they're already making money and a lot of them just make the customer pay the fees so it's sort of a tragedy of the commons. The customer pays for it And so it's a lower priority item. They might prioritize a different feature that might attract more customers or something like that. I guess in the end, it comes down to the users in general demanding from their services, look, it's not my fault that you're not implementing SegWit. I'm not paying those fees. I'm gonna go somewhere else that

Speaker 2: 00:24:25

uses SegWit where they make me pay less fees. And then eventually it'll change. So you were talking a little bit earlier about sort of overshooting and certainly there's an aspect of user experience there where people who maybe don't understand an unconfirmed transaction sitting in the mempool for weeks or whatever the case may be, means that they will overpay for fees to get that sort of instantaneous confirmation. Can you tell me a little bit about

## How enterprises estimate fees

how enterprises now are estimating their fees? Are they using it, are they sort of using something like Bitcoin Core in terms of looking at their own mempool or like, how do they actually do

Speaker 0: 00:25:12

it? So there's a bunch of different services that offer estimates on what fee rate will get you confirmed within whatever your target number of blocks is that you prefer. And some just use these third-party resources to guess. I think that scientifically research is definitely not done with making better guesses and some other approaches explicitly look at the mempool and some even combine the two. That does help. It also is less gameable. For example, a miner could just fill the mem pool with a lot of transactions they don't intend to include in blocks. Say you had a cabal of miners that all don't touch these transactions. They could fairly cheaply introduce a fee floor that you need to go above, or it looks like you need to go above in order to get confirmed. But we can also just sidestep this whole one-shot problem by continuously monitoring where a transaction is in the queue and then putting it at the right point of the queue. So if you would say, for example, hey, I have this payment I have to make. I have 24 hours to get it confirmed. So I'm aiming to be in the next 140 blocks, or let's play it safe, next 120 blocks. And then you can just put it at the bottom of the mempool and you just, as this block that you're aiming to be included in approaches, you just bump it up so it stays at first the top 120 megabytes, then when it's only 100 blocks away in the top 100. So you bump it up, and eventually maybe bump it to half of what you're aiming at, bump it to the top 50. Now if nobody adds any transactions, you'll be in the next 50 blocks, which is way ahead of what your requirement was, but you probably paid a fraction of the top few, right? So if you think more about when does this actually need to be confirmed and you, you give yourself that time, you can often, like right now you can save up to 90% or 95% of your fees if you can wait for the next weekend.

Speaker 2: 00:27:51

Got

Speaker 0: 00:27:51

it.

Speaker 2: 00:27:54

Cool, so we've talked about a few different, maybe better known things. What's an Omnibus wallet?

Speaker 0: 00:28:00

Oh, This is a concept that comes up in enterprise wallet

## Omnibus wallets

stuff. So as a solo user, you'll have all your funds, of course, in a separate wallet, and you probably all know your five UTXOs by first name, right? No, your five-year takes those by first name. But in an enterprise wallet where you literally get hundreds of deposits

Speaker 2: 00:28:26

per hour,

Speaker 0: 00:28:29

the first approach many people do when they start building a service like that is, oh, I need to have separate address spaces for each user that I have. And then they basically do their account management on chain. And it is terrible. It does not scale at all. I think one very well-known company here in the US would, when you withdraw money, send it through an address that belonged to that user first so that the return address for the funds would actually tie to that user. So every time somebody would withdraw, they actually made two payments on chain, right, for every single withdrawal. And that just basically took multiple percent points extra on-chain traffic just to process

Speaker 2: 00:29:26

it. It just sounds like a relic of a time when traffic wasn't being considered and fees weren't being considered.

Speaker 0: 00:29:31

Right, right. And then, of course, that time is over, and you're with those structures. So as an enterprise, though, you're taking custody of the funds in the first place already, right? So what you really need is you need to know who to credit your IOUs in your database when they deposit. And then when you send out, you send to their address and you don't have to send from specific funds, right? This is your stash, your custodial bit of Bitcoin that people sent to you. You have custody of the Bitcoin now. You owe some. But why does that have to be on chain, this accounting? So The idea of having an omnibus wallet is you have a single wallet for all of your users and you distinguish user deposits by giving out different invoice addresses. So here's Jonah's address. When somebody sends Bitcoin to this address, we will credit Jonah's user account and we'll offer him a new one every time the address has been used. But also there's a lot of address reuse unfortunately there. But it distinguishes who to credit on the deposit and then you have all the funds in one wallet. And now you can do stuff like consolidating, batching, maybe even take 70 or 90 or more percent of your value and put it into a non-hot wallet, right? You don't want all your funds sitting there on a server in AWS and if somebody breaks in they can take all your money, right? So you just keep an amount that you need to do your business with, and the rest of it goes into a stash somewhere else, more secure. And all of this is the concept of having an Omnibus wallet where everything goes into one part.

Speaker 2: 00:31:33

Got it. What else? What else did we miss?

Speaker 0: 00:31:36

Off-chain sending might actually work well with Omnibus wallets. Cool. Because once you have the accounting already in your database, right?

Speaker 2: 00:31:43

Okay. So tell me about off-chain

Speaker 0: 00:31:45

sending. Okay. Well, you have the omnibus wallet, right? You get the

## Off-chain sending

deposits into the wallet and have virtual accounting. Now, you already have all your users basically in your database. And If they're trying to send to each other, why the hell would you put it on the chain? If you know your user Alice is sending to your user Bob, you just update your database. And that's the idea of off-chain sending, right? Why would I put that on the chain if I know it's all in my data anyway.

Speaker 2: 00:32:19

That doesn't sound very Bitcoin-y, updating a database there.

Speaker 0: 00:32:24

So there's actually quite a few critics of custody in Bitcoin altogether, but nothing in Bitcoin precludes people from offering banking services to Bitcoin users. And if they're going to send a ton of money that way and make it a smoother experience, such a payment inside a database is instant, right? No waiting, no fees. And of course, that will be attractive to some users and make it more efficient. And in my opinion, it is a form of off-chain scaling, which can cause more Bitcoin payments to be made without having a huge footprint on the blockchain? Is it the right solution for everything? No. Should all money be in custody? No. Then a lot of what's important about Bitcoin to me wouldn't work anymore. Is it maybe the right thing for my grandma that wants to have exposure to Bitcoin but doesn't want to have her own treasure next to her knitting set. Yeah, maybe. Maybe I'd be more comfortable with her holding her money in custody and looking at it through the lens

Speaker 2: 00:33:41

of a web interface. Cool. Is there Anything else that you're excited about in the near future, maybe that's just going to help with this or?

Speaker 0: 00:33:51

Maybe taproot, Schnorr. So we talked a little bit about more efficient output

## Taproot

formats previously. So SegWit made transactions take less block space. Taproot will do that again. So for a native SegWit, 203 multisig, it'll go from 105 bytes to 58. That's a reduction of over 40% again. And additionally, it'll increase privacy because single SIG payments can look the same like multi-SIG payments. So we just got the code merged to Bitcoin Core. People are still debating activation parameters and so forth. I think we might see it mid-next year active on the network if everything goes smoothly. If there's another... It might take longer. And once we get Taproot out, we will see another huge efficiency gain in block space.

Speaker 2: 00:35:03

Something else to look forward to. Yes. Cool. Great. And maybe we'll do this again soon. Yeah, let's do this again soon. Cool. Bye for

Speaker 0: 00:35:12

now. Bye.

Speaker 1: 00:35:17

So Jonas, how was it to talk to another human being besides your family?

Speaker 2: 00:35:22

Pretty good. Getting right back in that saddle and, you know, a little rocky. Had a couple moments that, you know, I just remember what it's like to be on a podcast.

Speaker 1: 00:35:31

Kind of warm up.

Speaker 2: 00:35:31

Yeah. Well, I think we did all right. I

Speaker 1: 00:35:34

think you did great. Well, thank you everyone for listening. We're very happy to be back. You can visit us at www.chaincode.com.

Speaker 2: 00:35:44

Or chaincode Labs on Twitter.

Speaker 1: 00:35:46

All right, bye everyone. Bye everyone. Bye. Bye.

Speaker 0: 00:35:49

Bye. Bye. Bye. Bye. You
