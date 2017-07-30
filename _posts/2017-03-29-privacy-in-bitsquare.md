---
layout: post
title: Privacy in Bisq
author: Manfred Karrer
banner: null
---
<p>To write a blog post about privacy in Bisq has been already a long time on my TODO list.</p>
<p>One reason why I postponed it for so long was that I tried to fix the weaknesses rather than to explain such complicated stuff to a broader (potentially non-technical) audience.</p>
<p>But as we still don’t have the resources to fix the main issue (see section below about the BitcoinJ bloom filter) I want to explain the background of the issue and propose the solution how you can protect your privacy even in the presence of that existing weakness.</p>
<p>Beside that I want to give an overview about different areas where privacy is relevant in Bisq.<br/>
  I think we have already achieved a very high level of protection of user’s privacy. We can do still better and are working on further improvements.
</p>
<p>But the main goal of that article is to give those who take privacy serious enough background and guide them how they can get out the most from Bisq.</p>
<p>Be warned, that blog post will be a bit technical. Privacy is a complex topic and requires some basic understanding about how Bitcoin transactions work.</p>
<p>So let’s get started.</p>
<p>One area where privacy plays an important role is how the data between the peers are transferred.</p>
<h3>Privacy in the P2P network</h3>
<p>We use Tor (<a href="https://www.youtube.com/watch?v=wHmxCeLpveA">hidden services</a>) for the P2P network.</p>
<p>With Tor we get a very high level of privacy on the transport layer. So there are no IP addresses visible which could be used to identify a trader. As we use hidden services we even don’t have the issue that the exit nodes are a critical bottleneck in Tor.</p>
<p>In Bisq there are mainly 2 different types of messages:</p>
<p>– Broadcast messages (e.g. offers are broadcasted to all peers)<br/>
  – Direct messages (e.g. 2 peers are communicating directly in the trade process)</p>
<p>In the first case the messages are not encrypted as all users need to be able to read the offers. The only identifying data included in an offer is the onion address of the offer-maker. That is required to contact him when one wants to take the offer.</p>
<p>The direct messages are sent directly to the other peer and are encrypted and signed in Bisq. Beside that it is encrypted as well on the Tor layer.</p>
<p>Though there are some things which needs to be considered &#8211; even with Tor.</p>
<p><b>The repeated usage of the onion address carries a potential privacy concern:</b><br/>
  Onion addresses of offers and trades could be used to map together an identity.</p>
<p>But that privacy leak to the trading peer can be also seen as a feature &#8211; and is actually used as such:<br/>
  The small icon on the right of an offer entry or trade shows the onion address, whether you have traded already as well as the nr. of trades. This kind of “P2P reputation” (only you get the info about the data which you have anyway in the app from past trades – there is no centralized data collection) can be useful to easily identify traders with which one has traded already.
</p>
<p>You can even tag a peer with arbitrary text like “Fast trader” (this data is only used in the scope of your local application).</p>
<div id="attachment_1760" style="width: 510px" class="wp-caption alignnone">
  <a href="/images/Screen-Shot-2017-03-28-at-23.14.54.png"><img class="wp-image-1760" src="/images/Screen-Shot-2017-03-28-at-23.14.54-300x159.png" alt="" width="500" height="264" srcset="/images/Screen-Shot-2017-03-28-at-23.14.54-300x159.png 300w, /images/Screen-Shot-2017-03-28-at-23.14.54-768x406.png 768w, /images/Screen-Shot-2017-03-28-at-23.14.54-1024x541.png 1024w, /images/Screen-Shot-2017-03-28-at-23.14.54-492x260.png 492w" sizes="(max-width: 500px) 100vw, 500px"/></a>
  <p class="wp-caption-text">Edit peer info</p></div>
<p>&nbsp;</p>
<div id="attachment_1761" style="width: 510px" class="wp-caption alignnone">
  <a href="/images/Screen-Shot-2017-03-28-at-23.15.42.png"><img class="wp-image-1761" src="/images/Screen-Shot-2017-03-28-at-23.15.42-300x66.png" alt="" width="500" height="109" srcset="/images/Screen-Shot-2017-03-28-at-23.15.42-300x66.png 300w, /images/Screen-Shot-2017-03-28-at-23.15.42-768x168.png 768w, /images/Screen-Shot-2017-03-28-at-23.15.42-1024x224.png 1024w, /images/Screen-Shot-2017-03-28-at-23.15.42.png 1044w" sizes="(max-width: 500px) 100vw, 500px"/></a>
  <p class="wp-caption-text">Tooltip</p></div>
<p>&nbsp;</p>
<p>There might be different opinions if the re-use of the onion address for all offers and trades is positive (can be used for reputation) or if it is considered negative in regards to privacy.</p>
<p><b>Future improvements:</b><br/>
  In future versions we want to make it possible to reset your onion address (in the settings).<br/>
  There might be another option that we use separate onion addresses for each offer. But that might be resource heavy as multiple Tor circuits need to be maintained, multiple hidden service published (takes about 30 sec. – that is the delay at the application startup) and will complicate code as well. So this is not considered for the near future.
</p>
<p>Beside that we will support an optional GPG key which can be used for reputation.<br/>
  In the next version we will add this already on the data layer, though it is not implemented yet in the UI. With that key we could build a reputation system where a user can proof with his signature that he is the originator of certain offers or trades if he wants to and to whom he wants to.
</p>
<p>That way we decouple the network ID with reputation and users can choose if they want to build up a long term reputation at the cost of loss of some privacy to the trading peer or if they prefer to not use reputation and in case of network ID renewals the offers and trades cannot be associated to one identity.</p>
<p><strong>What can you do now?</strong><br/>
  For users who don’t want to connect potentially all their offers to one identity it is recommended to<a href="https://github.com/bitsquare/bitsquare/wiki/Switching-to-a-new-data-directory"> create a new data directory</a> from time to time (you must not have open offers, trades or disputes) or to use a program argument (e.g. –appName=Bisq-2) so a new data directory with that given name will be created and you can run multiple instances of Bisq in parallel which are completely unrelated (that setup is used by developers as well).
</p>
<p><b>Summary:</b><br/>
  The usage of the network ID (onion address) might be seen as privacy weakness but to have a long term ID is a requirement for reputation.<br/>
  In future we will decouple that by using an optional GPG key for reputation and enable renewal of the onion address.
</p>
<h3>Privacy in Fiat trades</h3>
<p>When the users are trading Bitcoin with a national currency the transfer of the Fiat currency requires usually some personally identifying data.</p>
<p>With a bank transfer it is typically the name and the bank account number. With other payment methods it might be an email address or phone number (e.g. ClearXchange, Interac, Swish,…). Only with OKPay and PerfectMoney an account number alone is sufficient. But even there the account is usually verified in the registration process at the payment provider so the company knows the real life identity behind that account number.</p>
<p>It is important to understand that
  <strong>only the trading peer will see that account data</strong> (the name in the case of a bank transfer)
  <strong>as well as the arbitrator in case of a dispute</strong>.<br/>
  In all trades which are not disputed, the arbitrator has no access to this data. The account data is only stored locally on your computer.
</p>
<p>The Fiat receiver needs to expose his bank data to the other peer, otherwise the Fiat sender could not transfer the money.<br/>
  The Fiat sender usually also gets exposed by the receiving bank as most banks show the name and bank account nr. of the sender in the transaction history.
</p>
<p>There is another important reason why we exchange the account data in both directions:<br/>
  There are some<a href="https://github.com/bitsquare/bitsquare/issues/373"> known social engineering scams</a> where a BTC seller receives the Fiat money from a victim who got tricked into a purchase at some Ebay-like platforms but the victim never receives the purchased good. The scammer gives the victim the bank account nr. of the BTC seller, the BTC seller receives the Fiat and then releases the BTC to the scammer. After a few days the victim discovers that he got scammed, goes to the police and probably requests a bank chargeback. The seller gets in trouble to explain that he was not the scammer and probably accepts the chargeback to avoid more hassles.
</p>
<p>Luckily that never happened at Bisq but we need to be careful not to attract such scammers.<br/>
  To protect against such fraud we require the Fiat receiver to only release the BTC if the bank data in the Bisq application is matching with the data on his bank statement, otherwise he needs to open a dispute.
</p>
<p><strong>Planned improvements:</strong><br/>
  The payment account data (e.g. bank account number and name in case of a bank transfer) could be encrypted by default when the data is exchanged with the arbitrator in case of a dispute. In most cases the arbitrator does not require the data so the traders privacy is better protected. Only in those rare cases when the arbitrator needs the data for the dispute resolution process he can request the decrypted data from the trader.
</p>
<p><b>Summary:</b><br/>
  The exposure of the bank account data to the other peer (and arbitrator in case of a dispute) is a necessary requirement when a Fiat transfer is involved. In future releases we might add an improvement that the arbitrator does not see the account data by default and has to request it from the trader if needed.
</p>
<h3>Avoiding coin merge</h3>
<p>The returned change amount when funding an offer and/or trade as well as the funds you get out from a completed trade are re-used for other trades if you choose to use the
  <b>Bisq internal wallet</b>.<br/>
  This connects the trades at the Bitcoin
  <a href="https://www.youtube.com/watch?v=HScK4pkDNds">transaction graph</a> level.</p>
<p>To avoid that, you need to fund each offer independently from an external wallet and withdraw the funds at the end of the trade to an external wallet.<br/>
  Of course you need to take care that you don’t leak your privacy with
  <b>coin merge</b> again in the external wallet (you can use multiple external wallets as well to make that easier).
</p>
<p>UI-wise that strategy is
  <a href="https://github.com/bitsquare/bitsquare/wiki/Dedicated-trade-wallets">fully supported</a> in Bisq (in fact it was the only option initially) but we are aware that most people prefer the more convenient usage of the internal wallet to re-use bitcoins from one trade for funding the next trade.<br/>
  Unfortunately there is no good solution to combine both <b>convenience with privacy</b> here.</p>
<p>To offer a tool (similar to coin control in Bitcoin Core) to let the user decide which unspent transaction outputs (UTXO) should be used for funding an offer or trade might help to mitigate the problem. But there is some complexity and difficulty involved to decide which UTXO to use as well the problem that often you don&#8217;t have enough options to choose from. So that approach does not look like a feasible strategy to solve that issue. It is a conceptual problem from the way how transactions are connected in a graph in Bitcoin.</p>
<p><b>Coinjoin</b> is one of the very few strategies to combat that issue.<br/>
  We have rough plans to either add Coinjoin to Bisq in future, integrate an external Coinjoin implementation in a user friendly manner or find another solution with an automated trade to an Altcoin which has strong privacy protection built in at the protocol level like<a href="https://getmonero.org/"> Monero</a> or<a href="https://z.cash/"> Zcash</a>.
</p>
<p>Hopefully we will see more privacy improvements like<a href="https://bitcoinmagazine.com/articles/confidential-transactions-how-hiding-transaction-amounts-increases-bitcoin-privacy-1464892525/"> Confidential transactions</a> integrated to Bitcoin as well.
</p>
<p><b>Summary:</b><br/>
  If you want strong privacy you need to fund each trade independently and withdraw the funds from a completed trade to an external wallet where you have to take care to not merge the coins again (which is not easy).
</p>
<h3>Privacy in the Bitcoin network</h3>
<p>The connection to the Bitcoin network (we use<a href="http://bitcoinj.github.io/"> BitcoinJ</a> which uses the SPV model) is by default over Tor (we use a mix of connections to clear-net full nodes which are passing the Tor exit nodes and full nodes running as hidden services, therefore never exiting the Tor network).<br/>
  The user can pass a program argument to use a custom Bitcoin full node as well. Alternatively a locally running Bitcoin node (localhost) can be used. The Bisq application discovers that local node automatically and uses it as the only node for the Bitcoin network connection. So no configuration is required.
</p>
<p>The users who don’t run their own full Bitcoin node are exposed to a severe privacy leak inherited from the broken
  <b>BitcoinJ bloom filters</b> [<a href="https://jonasnick.github.io/blog/2015/02/12/privacy-in-bitcoinj">1</a>], [<a href="https://eprint.iacr.org/2014/763.pdf">2</a>].<br/>
  I tried to fix the most critical flaws [<a href="https://github.com/bitsquare/bitsquare/issues/414">3</a>] but unfortunately it turned out that it requires more effort to fix that [<a href="https://github.com/bitsquare/bitsquare/issues/487">4</a>]. So the bloom filters are still leaking considerable information to full nodes (in case those are operated by chain analysis companies spying on the network).
</p>
<p><b>What is leaked:</b><br/>
  A spying full node will find out quite easily that all the addresses created by the HD wallet (about 1300) are from one wallet (belongs to the same owner).
</p>
<p>They don’t see the IP address as we use Tor (with other BitcoinJ wallets even the IP address is leaked).<br/>
  If one of those addresses is connected to the real life identity of the user all the other addresses are de-anonymized as well (derived from the fact that all come from one wallet – same owner).
</p>
<p>Revealing a real life identity can happen easily if you use one of your wallet addresses for any service where you have to register with your ID (centralized exchanges, merchants,…).<br/>
  Even if you don’t leak any Bisq address, with more sophisticated graph analysis using typical usage patterns (e.g.<a href="https://diyhpl.us/wiki/transcripts/bitcoin-adam3us-fungibility-privacy/"> coin merge</a>) it can happen easier than expected that you lose your privacy.
</p>
<p>So don’t expect privacy on the Bitcoin level unless you run your own full node or you really know what you are doing and are aware of all the pitfalls.</p>
<p>We have some
  <a href="https://github.com/bitsquare/bitsquare/issues/487"> bounties</a> open in that area and we consider this a high priority issue which hopefully gets solved soon.<br/>
  Any developer experienced with BitcoinJ is very welcome to get in touch with us!</p>
<p>Unfortunately the bloom filters are broken also on the design level, but to fix the implementation flaws would give us at least some level of improvement (and render the effort for the spies higher as well as reduce the quality of their data due to a higher degree of uncertainty – though there might be controversial opinions about that).<br/>
  That said, you should not be tempted to assume that the privacy problems of bloom filters are fixed after the BitcoinJ bloom filter implementation flaw is fixed.<br/>
  To get a new bloom filter design implemented and deployed to Bitcoin Core is unfortunately something we cannot expect to happen soon. There are a few interesting efforts in that direction but I am not aware that anyone is working on that [<a href="https://github.com/Giszmo/TransactionFinder">5</a>], [<a href="https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2016-May/012636.html">6</a>], [<a href="https://www.reddit.com/r/Bitcoin/comments/4v28jl/how_have_fungiblity_problems_affected_you_in/d5ux6aq">7</a>].
</p>
<p>But there is another very <strong>promising solution on the horizon</strong>:<br/>
  To use a <strong>Bitcoin Core node in SPV mode</strong> [<a href="https://github.com/bitcoin/bitcoin/pull/9483">8</a>].<br/>
  <a href="https://twitter.com/_jonasschnelli_">Jonas Schnelli</a> is working on that and we consider to replace BitcoinJ by that as soon it is production ready and we have enough dev resources to implement it.
</p>
<p><b>So what can a user do in the current situation to get a protection against those spying full nodes?</b><br/>
  As said initially the only protection is to
  <b>run your own full node</b>, either locally (then Bisq connects by default to it) or you pass the IP address to your node (or a trusted node you know) via a program argument (—btcNodes=[comma separated IP addresses]).
</p>
<p>But it is important to do that already at the <b>first startup</b> and
  <b>always</b>. One connection to the public Bitcoin network can be enough that you get connected to a spying node and your privacy is leaked (only a new data directory which creates a new wallet will help then – in our<a href="https://github.com/bitsquare/bitsquare/wiki"> Github wiki</a> there are instructions [<a href="https://github.com/bitsquare/bitsquare/wiki/Switching-to-a-new-data-directory">9</a>] how to do that).
</p>
<p>To communicate that complicated issue by displaying a popup at the first Bisq startup would be too confusing and overstraining for most users.</p>
<p>One possible compromise might be to use by default a white-list of trusted full nodes. The user can change to run his own local or remote full node, use his own list of trusted nodes or use the public Bitcoin network. As explained above it must already be taken care of at the first startup, so to use the public network as default would require a popup explaining the complex topic.<br/>
  To use a white-list of trusted nodes compiled by the Bisq developers introduces centralization and trust issues.<br/>
  Though I think that is less critical as the user can change afterwards to the public network without any damage, which is not true for the other direction (first public then trusted nodes).
</p>
<p>This is clearly controversial and not an optimal solution at all, but might be preferable to the current state where everyone is leaking potentially (and I assume to a high probability) their privacy.<br/>
  We have not decided to go that road yet, but it is in discussion.</p>
<p>Though as said above a SPV Bitcoin node would be probably the solution we will go mid-term and that would solve that issue anyway.</p>
<p><b>Summary:</b><br/>
  For achieving privacy protection on the Bitcoin network level you need to run your own full node.<br/>
  We are trying to fix the implementation flaws in BitcoinJ but unfortunately bloom filters are already broken on the design level so we will never get proper privacy with the current bloom filters (Bitcoin side).<br/>
  Alternatively we could provide a white-list of trusted full nodes and use that as default instead of the connection to the public network. This is a problematic approach as well and still in discussion.
</p>
<p>The mid-term goal is to use a SPV Bitcoin node integrated in Bisq (similar like we use the Tor binary). For the user that would be transparent and the usability comparable with the current BitcoinJ SPV model. That would not only solve the bloom filter issue but also other weaknesses of BitcoinJ’s SPV model (e.g. no validation of consensus rules; in case of a hardfork with Bitcoin Unlimited it would
  <a href="https://groups.google.com/forum/#!topic/bitcoinj/PmMN18xxCI0">follow automatically the longest PoW chain</a>).
</p>
<h3>Conclusion</h3>
<p>Those who care about privacy, take the time to understand the complex context [<a href="https://hackernoon.com/bitcoin-privacy-landscape-in-2017-zero-to-hero-guidelines-and-research-a10d30f1e034#.6ptypp28o">10</a>],[<a href="https://www.youtube.com/watch?v=HScK4pkDNds&amp;t=11m30s">11</a>],[<a href="https://www.reddit.com/r/bitcoin_devlist/comments/3bsugu/bloom_filtering_privacy_adam_back_feb_20_2015/">12</a>] and are willing to take the burden to run a full node as well keeping the funding of trades independent to avoid coin merge, can use Bisq with a very high level of privacy protection.<br/>
  The others are probably leaking their privacy already in many other areas as well so Bisq does not really make their exposure worse.
</p>
<p>This is not a satisfying situation though as we want to provide
  <b>privacy by default</b> in a user friendly manner. Convenience and privacy are unfortunately often hard to combine.<br/>
  But we will continue to work to find the best solutions to solve those current weaknesses.</p>
<p>With all that said we have to emphasize that
  <strong>Bisq has already archived a very high level of privacy protection</strong> and clearly
  <strong>outperforms any other Bitcoin exchange</strong> in that matter.</p>
<ul>
  <li>No registration required. No centralized data collection.</li>
  <li>We use Tor by default for all network traffic. So your IP address never get leaked!</li>
  <li>Our UI supports coin merge avoidance.</li>
  <li>Bisq uses a HD wallet. No address re-use</li>
  <li>There will be future improvements to decouple the network ID with an optional reputation key.</li>
  <li>Once we can use a SPV Bitcoin Core node instead of BitcoinJ we get rid of the bloom filter problem.</li>
</ul>
<p>Protection of privacy is not only a<a href="/blog/explorering-the-new-territory/"> core value of Bisq</a> but we see it also as a fundamental property of money to achieve
  <a href="https://decentralize.today/bitcoin-fungibility-the-most-important-feature-of-bitcoin-4b87a381f21a#.nj06fezdn">fungibility</a>.<br/>
  Bitcoin and the surrounding infrastructure (like exchanges) need to improve in that area so Bitcoin can develop it’s full potential as sound money. Sound money for a sound society. Protection of privacy has to be the default state for all, not just a privilege for techies and geeks.
</p>
<p>
  <a href="https://en.wikipedia.org/wiki/First_Amendment_to_the_United_States_Constitution">America’s founding fathers</a> have been more aware of this than today’s
  <a href="http://www.reuters.com/article/us-britain-security-rudd-idUSKBN16X0BE">retarded politicians.</a><br/>
  As we cannot expect much support from that side, let&#8217;s build our new model as we think it should look like and make the retarded model obsolete.
</p>
<p><b>References:</b><br/>
  [1] Privacy in BitcoinJ:<a href="https://jonasnick.github.io/blog/2015/02/12/privacy-in-bitcoinj"> https://jonasnick.github.io/blog/2015/02/12/privacy-in-bitcoinj</a><br/>
  [2] On the Privacy Provisions of Bloom Filters in Lightweight Bitcoin Clients:<a href="https://eprint.iacr.org/2014/763.pdf"> https://eprint.iacr.org/2014/763.pdf</a><br/>
  [3] Review bloom filter fixes in Bisq’s BitcoinJ fork:<a href="https://github.com/bitsquare/bitsquare/issues/414"> https://github.com/bitsquare/bitsquare/issues/414</a><br/>
  [4] Bounty: Review Bisq’s BitcoinJ Bloomfilter fixes:<a href="https://github.com/bitsquare/bitsquare/issues/487"> https://github.com/bitsquare/bitsquare/issues/487</a><br/>
  [5] Leo Wandersleb efforts:<a href="https://github.com/Giszmo/TransactionFinder"> https://github.com/Giszmo/TransactionFinder</a><br/>
  [6] Committed bloom filters for improved wallet performance and SPV security:<a href="https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2016-May/012636.html"> https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2016-May/012636.html</a><br/>
  [7] How have fungiblity problems affected you in Bitcoin?:<a href="https://www.reddit.com/r/Bitcoin/comments/4v28jl/how_have_fungiblity_problems_affected_you_in/d5ux6aq"> https://www.reddit.com/r/Bitcoin/comments/4v28jl/how_have_fungiblity_problems_affected_you_in/d5ux6aq</a><br/>
  [8]Complete hybrid full block SPV mode:
  <a href="https://github.com/bitcoin/bitcoin/pull/9483">https://github.com/bitcoin/bitcoin/pull/9483</a><br/>
  [9] Switching to a new data directory:<a href="https://github.com/bitsquare/bitsquare/wiki/Switching-to-a-new-data-directory"> https://github.com/bitsquare/bitsquare/wiki/Switching-to-a-new-data-directory</a><br/>
  [10] Bitcoin Privacy Landscape In 2017 — Beyond Coin Mixing, General Guidelines And Research:
  <a href="https://hackernoon.com/bitcoin-privacy-landscape-in-2017-zero-to-hero-guidelines-and-research-a10d30f1e034#.6ptypp28o">https://hackernoon.com/bitcoin-privacy-landscape-in-2017-zero-to-hero-guidelines-and-research-a10d30f1e034#.6ptypp28o</a><br/>
  [11] Bitcoin Privacy: Theory and Practice &#8211; Jonas Nick:
  <a href="https://www.youtube.com/watch?v=HScK4pkDNds&amp;t=11m30s">https://www.youtube.com/watch?v=HScK4pkDNds&amp;t=11m30s</a><br/>
  [12] Bloom filtering, privacy &#8211; Adam Back:
  <a href="https://www.reddit.com/r/bitcoin_devlist/comments/3bsugu/bloom_filtering_privacy_adam_back_feb_20_2015/">https://www.reddit.com/r/bitcoin_devlist/comments/3bsugu/bloom_filtering_privacy_adam_back_feb_20_2015/</a>
</p>
