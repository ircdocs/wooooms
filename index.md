---
title: Wall of Outdated or Otherwise Misleading Specs
layout: default
---

<div id="printable-toc" style="display: none"></div>


So, uh. IRC specifications. There are a _damn lot_ of them. And a lot of those are outdated or simply misleading. Sure, there's attempts at [writing](http://ircv3.net) [new](https://modern.ircdocs.horse) [ones](https://tools.ietf.org/html/draft-oakley-irc-ctcp-01), but the simple fact is that most out there today that are still referenced are misleading in some way(s).

This page lists the common IRC specs out there, and roughly catalogues the outdated or misleading sections of them.


---


<h2><a href="https://tools.ietf.org/html/rfc1459">RFC 1459</a></h2>

<h3><a href="https://tools.ietf.org/html/rfc1459#section-1.2">1.2 - Clients</a></h3>

The spec reads:

      server.  Each client is distinguished from other clients by a unique
      nickname having a maximum length of nine (9) characters.  See the

Nicks are no longer limited to nine characters. See the [`NICKLEN`](https://modern.ircdocs.horse/#nicklen-parameter) RPL_ISUPPORT token for the actual maximum nick length on the network.

<h3><a href="https://tools.ietf.org/html/rfc1459#section-1.3">1.3 - Channels</a></h3>

The spec reads:

      Channels names are strings (beginning with a '&' or '#' character) of
      length up to 200 characters.  Apart from the the requirement that the
      first character being either '&' or '#'; the only restriction on a

Channels can start with any character defined by the [`CHANTYPES`](https://modern.ircdocs.horse/#chantypes-parameter) RPL_ISUPPORT token, and the maximum channel name length is defined by the [`CHANNELLEN`](https://modern.ircdocs.horse/#channellen-parameter) token.

The spec reads:

      think is in each channel and the mode of that channel.  If the
      channel exists on both sides, the JOINs and MODEs are interpreted in
      an inclusive manner so that both sides of the new connection will
      agree about which clients are in the channel and what modes the
      channel has.

Modes aren't strictly interpreted in an inclusive manner. Commonly, server-to-server (S2S) protocols use some sort of timestamping function to ensure clients can't exploit netsplits to incorrectly give themselves permissions. Servers should agree about exactly what modes the channel has, in any case.

<h3><a href="https://tools.ietf.org/html/rfc1459#section-1.3.1">1.3.1 - Channel Operators</a></h3>

These days there are also different levels of channel moderator, including 'halfops' (half operators), 'protected ops', and the 'founder' rank. These moderation levels have varying privileges and can execute, and not execute, various channel management commands based on what the server defines.

These channel moderators also have different prefixes, defined by the [`PREFIX`](https://modern.ircdocs.horse/#prefix-parameter) RPL_ISUPPORT token.

<h3><a href="https://tools.ietf.org/html/rfc1459#section-2.1">2.1 - The IRC Specification Overview</a></h3>

The spec reads:

      The protocol as described herein is for use both with server to
      server and client to server connections.  There are, however, more

This protocol is only used for client connections. Server protocols these days are varied and while some (such as [TS6](https://github.com/grawity/irc-docs/blob/master/server/ts6.txt) and [P10](https://github.com/grawity/irc-docs/blob/master/server/p10-nefarious2.txt)) are based on this original S2S protocol, others may not be.

For the best guide on a server's S2S protocol, look at the documentation+code for the specific IRC server you're using.

<h3><a href="https://tools.ietf.org/html/rfc1459#section-2.2">2.2 - Character codes</a></h3>

The spec reads:

      No specific character set is specified.

While this is accurate, UTF-8 has become the generally-accepted standard encoding for sending messages on IRC. When decoding, using a combination of UTF-8 and [Latin-1/ISO-8859-1(5)/CP1252](https://en.wikipedia.org/wiki/Windows-1252) is best. See [this page](https://modern.ircdocs.horse/#character-encodings) for further discussion on character sets with the client protocol.

The spec reads:

      Because of IRC's scandanavian origin, the characters {}| are
      considered to be the lower case equivalents of the characters []\,
      respectively. This is a critical issue when determining the
      equivalence of two nicknames.

This is true for servers following `rfc1459` casefolding. However, servers may not do this. To discover the specific casefolding rules the server has in place, see the [`CASEMAPPING`](https://modern.ircdocs.horse/#casemapping-parameter) RPL_ISUPPORT token.

<h3><a href="https://tools.ietf.org/html/rfc1459#section-2.3.1">2.3.1 - Message format in 'pseudo' BNF</a></h3>

See the IRCv3 [Message Tags](http://ircv3.net/specs/core/message-tags-3.2.html) specification for info regarding updates to this.

<h3><a href="https://tools.ietf.org/html/rfc1459#section-4.1">4.1 - Connection Registration</a></h3>

The spec reads:

      A "PASS" command is not required for either client or server
      connection to be registered, but it must precede the server message
      or the latter of the NICK/USER combination.  It is strongly
      recommended that all server connections have a password in order to
      give some level of security to the actual connections.  The
      recommended order for a client to register is as follows:

            1. Pass message
            2. Nick message
            3. User message

There are a few more commands that can be used during connection registration, and some specific numerics that are sent once registration is completed. See [this page](https://modern.ircdocs.horse/#connection-registration) for a more accurate overview of connection registration.

<h3><a href="https://tools.ietf.org/html/rfc1459#section-4.1.3">4.1.3 - User message</a></h3>

The spec reads:

         Command: USER
      Parameters: <username> <hostname> <servername> <realname>

      ...

      Note that hostname and servername are normally ignored by the IRC
      server when the USER command comes from a directly connected client
      (for security reasons), but they are used in server to server
      communication.  This means that a NICK must always be sent to a

This is correct, and I point it out because client and server authors often mistakenly believe the `<hostname>` and `<servername>` parameters for parameters that they should actually send real data for or parse. Particularly because of differences between RFC 1459 and RFC 2812, clients should just send `"0 *"` for the `<hostname>` and `<servername>` parameters.

Many servers also prefix usernames given to them by the client with a `'~'` character, to note that it is user-supplied rather than having been retrieved from an identity server. See [this page](https://modern.ircdocs.horse/#user-message) for more discussion on this.

<h3><a href="https://tools.ietf.org/html/rfc1459#section-4.1.5">4.1.5 - Oper message</a></h3>

The specific failure cases for this command, and what to do when they're encountered, are expanded on [here](https://modern.ircdocs.horse/#oper-message).

<h3><a href="https://tools.ietf.org/html/rfc1459#section-4.1.6">4.1.6 - Quit message</a></h3>

These days, servers prepend `"Quit: "` to user-provided quit messages to separate them from other protocol or server issues. In addition, quits created due to netsplits often don't contain the actual names of servers and use the fake server names `"*.net *.split"`. This is expanded on [here](https://modern.ircdocs.horse/#quit-message).

<h3><a href="https://tools.ietf.org/html/rfc1459#section-4.2">4.2 - Channel operations</a></h3>

The spec reads:

      ultimately clash.  It is also required that servers keep a nickname
      history to ensure that wherever a <nick> parameter is given, the
      server check its history in case it has recently been changed.

These days, S2S protocols generally use a timestamp-based approach to prevent these issues from happening, rather than nick-delay solutions. Because of this, the recently-changed-nicks history isn't required.

<h3><a href="https://tools.ietf.org/html/rfc1459#section-4.2.1">4.2.1 - Join message</a></h3>

In addition to the elements affecting this command listed here, there are now [ban](https://modern.ircdocs.horse/#ban-exemption-channel-mode) and [invite exceptions](https://modern.ircdocs.horse/#invite-exemption-channel-mode), the channel limit noted by [`CHANLIMIT`](https://modern.ircdocs.horse/#chanlimit-parameter), and the special argument of `'0'` which some servers accept. See a more accurate description of this command [here](https://modern.ircdocs.horse/#join-message).

<h3><a href="https://tools.ietf.org/html/rfc1459#section-4.2.2">4.2.2 - Part message</a></h3>

See [here](https://modern.ircdocs.horse/#part-message) for a more in-depth explanation of this command, as well as exactly how it's relayed to other clients.

<h3><a href="https://tools.ietf.org/html/rfc1459#section-4.2.3">4.2.3 - Mode message</a></h3>

The spec reads:

      The MODE command is a dual-purpose command in IRC.  It allows both
      usernames and channels to have their mode changed.  The rationale for
      this choice is that one day nicknames will be obsolete and the
      equivalent property will be the channel.

I haven't been able to find any reasonable meaning in the phrase _"one day nicknames will be obsolete and the equivalent property will be the channel"_. It means nothing, and nicknames are just as relevant as they've always been.

For a wildly more accurate definition of this command, see [here](https://modern.ircdocs.horse/#mode-message).

<h4><a href="https://tools.ietf.org/html/rfc1459#section-4.2.3.1">4.2.3.1 - Channel modes</a></h4>

The specific list of channel modes here is outdated – 'private' is no longer standard, and there are a few more standard modes out there. See [here](https://modern.ircdocs.horse/#channel-modes) for an updated list.

There are now several 'types' of channel modes which are defined with the [`CHANMODES`](https://modern.ircdocs.horse/#chanmodes-parameter) RPL_ISUPPORT token. See [here](https://modern.ircdocs.horse/#mode-message) for further discussion on this and how it's implemented today.

The spec also reads:

      When using the 'o' and 'b' options, a restriction on a total of three
      per mode command has been imposed.  That is, any combination of 'o'
      and

In addition to the mistaken cut-off text here, these restrictions are now defined by the [`MODES`](https://modern.ircdocs.horse/#modes-parameter) RPL_ISUPPORT parameter.

<h4><a href="https://tools.ietf.org/html/rfc1459#section-4.2.3.2">4.2.3.2 - User modes</a></h4>

The list of user modes here is outdated, and there are some other standard ones. See [here](https://modern.ircdocs.horse/#user-modes) for a slightly-updated list.


<!-- TODO(dan): Finish RFC 1459 section -->


---


<h2><a href="https://tools.ietf.org/html/rfc2810">RFC 2810</a></h2>

<h3><a href="https://tools.ietf.org/html/rfc2810#section-2.2">2.2 - Clients</a></h3>

The spec reads:

      User clients are generally programs providing a text based
      interface that is used to communicate interactively via IRC.  This
      particular type of clients is often referred as "users".

      ...

      Unlike users, service clients are not intended to be used manually
      nor for talking.  They have a more limited access to the chat
      functions of the protocol, while optionally having access to more
      private data from the servers.

While in practice, both 'bots' and 'pseudo-servers' that create fake clients are used with IRC, the user-clients vs service-clients split described in this section is not accurate. It would be more accurate to frame the different clients as such:

- True Clients: These are clients that connect to servers using the IRC client protocol. They can be controlled by a human user, a bot program, or some combination of the two.
- Fake Clients: These are clients that are introduced through the S2S protocol, but are not actual externally-connected clients. Some examples of this are the NickServ and ChanServ clients common on networks.

<h3><a href="https://tools.ietf.org/html/rfc2810#section-3">3 - Architecture</a></h3>

The spec reads:

      The IRC protocol provides no mean for two clients to directly
      communicate.  All communication between clients is relayed by the
      server(s).

While this is accurate (the IRC protocol _itself_ doesn't provide this), the [DCC protocol](https://modern.ircdocs.horse/dcc.html) does provide this.

<h3><a href="https://tools.ietf.org/html/rfc2810#section-6.1">6.1 - Scalability</a></h3>

The spec reads:

      It is widely recognized that this protocol does not scale
      sufficiently well when used in a large arena.  The main problem comes
      from the requirement that all servers know about all other servers,
      clients and channels and that information regarding them be updated
      as soon as it changes.

While mostly accurate, some S2S protocols only share a limited amount of information with other servers – for instance, not sharing detailed channel information with a server unless there's a client connected to that channel on the server. These sort of workarounds try to help address this issue.


---


<h2><a href="https://tools.ietf.org/html/rfc2811">RFC 2811</a></h2>

<h3><a href="https://tools.ietf.org/html/rfc2811#section-2.4.2">2.4.2 - Channel Creator</a></h3>

The spec reads:

      A user who creates a channel with the character '!' as prefix is
      identified as the "channel creator".  Upon creation of the channel,
      this user is also given channel operator status.

These days, this 'channel creator' role has generally been replaced with a 'channel founder' role using the channel member prefix `'~'` (which is only used when the server supports user account registration and channel ownership via ChanServ or similar).

<h3><a href="https://tools.ietf.org/html/rfc2811#section-3">3. - Channel lifetime</a></h3>

The spec reads:

      In regard to the lifetime of a channel, there are typically two
      groups of channels: standard channels which prefix is either '&', '#'
      or '+', and "safe channels" which prefix is '!'.

'Safe channels' aren't implemented these days. You can still find a couple networks out there that support them, but they're very few and far between. It's more appropriate to say that there are two groups of channels in regards to lifetimes:

- Unregistered channels act as described in the **Standard Channels** (3.1) section, with the caveats listed below.
- Registered channels have long lifetimes, lasting longer than the life of the clients connected to it and usually longer than the life of the ircd itself (around 90-120 days is relatively common).

See how ChanServ works for a better representation of channel lifetimes than I'm giving here.

<h3><a href="https://tools.ietf.org/html/rfc2811#section-3.1">3.1 - Standard channels</a></h3>

The spec reads:

      The user creating a channel automatically becomes channel operator
      with the notable exception of channels which name is prefixed by the
      character '+', see section 4 (Channel modes).  See section 2.4.1
      (Channel Operators) for more details on this title.

As with 'safe channels' above, these sort of channels aren't widely available or implemented these days.

The spec also reads:

      In order to avoid the creation of duplicate channels (typically when
      the IRC network becomes disjoint because of a split between two
      servers), channel names SHOULD NOT be allowed to be reused by a user
      if a channel operator (See Section 2.4.1 (Channel Operators)) has
      recently left the channel because of a network split.  If this
      ...
      the character '+'.  This mechanism is commonly known as "Channel
      Delay".

Channel delay has largely been replaced with systems that rely on timestamps to appropriately merge channels when the two sides of the network rejoin. However, this issue mostly relies on how the server-to-server (S2S) protocol of the specific ircd you're using has been implemented. Check your server's S2S code+documentation for more accurate information for how they've chosen to implement this.

<h3><a href="https://tools.ietf.org/html/rfc2811#section-4">4 - Channel Modes</a></h3>

This section has a list of channel modes. For an updated list of modes that are commonly-implemented across servers, I'd recommend seeing [this page instead](https://modern.ircdocs.horse/#channel-modes).

<h3><a href="https://tools.ietf.org/html/rfc2811#section-4.1">4.1 - Member Status</a></h3>

This section lists a series of membership prefixes and levels. The 'channel creator' status has largely been replaced with the ones listed on [this page](https://modern.ircdocs.horse/#channel-membership-prefixes), which I'd recommend consulting instead.

<h3><a href="https://tools.ietf.org/html/rfc2811#section-4.2">4.2 - Channel Flags</a></h3>

As noted above, some of the flags listed here aren't actually widely-implemented or standard across different servers. See [this page](https://modern.ircdocs.horse/#channel-modes) for a more accurate list of what's out there today.

<h3><a href="https://tools.ietf.org/html/rfc2811#section-5.1">5.1 - Tracking Recently Used Channels</a></h3>

As noted above, channel delay has largely been replaced with systems that use timestamps to ensure channels are merged appropriately. However, check your server's S2S protocol documentation for more information on how they tackle this issue.

<h3><a href="https://tools.ietf.org/html/rfc2811#section-6.3">6.3 - Collisions And Channel Modes</a></h3>

The spec reads:

      each other.  Channel collisions (either legitimate or not) are
      treated as inclusive events, meaning that the resulting channel has
      for members all the users who are members of the channel on either
      server prior to the connection.

While all members of the channels are joined, typically only one side's channel membership prefixes/statuses (the side with the older channel) end up surviving the rejoin. This prevents malicious people from causing a server split, parting and rejoining the channel to become a chanop, then wreaking havoc when the channels merge back together.

However, how the server merges channels is related to the server's S2S protocol. Check the S2S protocol documentation and code for the server you're looking at to see how they've done it.

<h3><a href="https://tools.ietf.org/html/rfc2811#section-6.4">6.4 - Resource Exhaustion</a></h3>

Typically there are two resource exhaustion issues servers look to mitigate these days:

- Clients setting too many masks on a channel.
- Clients joining too many channels at once.

The number of masks clients can set on a channel is almost always restricted, and noted by the [`MAXLIST`](https://modern.ircdocs.horse/#maxlist-parameter) RPL_ISUPPORT token sent to them when they join.

Clients usually have a maximum number of channels that they can join, noted by the [`CHANLIMIT`](https://modern.ircdocs.horse/#chanlimit-parameter) RPL_ISUPPORT token sent to them when they join.

<h3><a href="https://tools.ietf.org/html/rfc2811#section-7.1">7.1 - Access Control</a></h3>

The spec reads:

      This mechanism can only be efficient and safe if the IRC servers have
      an accurate way of authenticating user connections, and if users
      cannot easily get around it.  While it is in theory possible to
      implement such a strict authentication mechanism, most IRC networks
      (especially public networks) do not have anything like this in place
      and provide little guaranty about the accuracy of the username and
      hostname for a particular client connection.

While the username and hostname of a client often can't be trusted, many (I'd even say _most_) networks these days do have user account registration through NickServ or some similar system. In addition, many servers let you explicitly control access to your channel by way of allowing specific accounts to join the channel (with the use of [extbans](https://modern.ircdocs.horse/#extban-parameter)).

<h3><a href="https://tools.ietf.org/html/rfc2811#section-7.2">7.2 - Channel Privacy</a></h3>

The spec reads:

      its access control settings.  This method has long been used by
      individuals to "take over" channels by "illegitimately" gaining
      channel operator status on the channel.  The same method can be used
      to find out the exact list of members of a channel, as well as to
      eventually receive some of the messages sent to the channel.

As noted above, most S2S protocols work to prevent the malicious user from gaining channel operator credentials or similar. Channel ownership in the form of ChanServ also helps reduce the viability of ongoing disruption if a malicious actor does gain channel operator permissions.

<h3><a href="https://tools.ietf.org/html/rfc2811#section-7.3">7.3 - Anonymity</a></h3>

For better or for worse, this channel mode typically isn't implemented in today's servers.


<!-- ---


<h2><a href="https://tools.ietf.org/html/rfc2812">RFC 2812</a></h2> -->


---


<h2><a href="https://tools.ietf.org/html/rfc2813">RFC 2813</a></h2>

The protocol described in this specification isn't used today. Well, to be specific, it is used as the basis of newer server-to-server (S2S) protocols like [TS6](https://github.com/grawity/irc-docs/blob/master/server/ts6.txt) and [P10](https://github.com/grawity/irc-docs/blob/master/server/p10-nefarious2.txt), but isn't useful on its' own.

These days, most servers have either customised up or written their own S2S protocol. Look at your IRC server's documentation for info on the S2S protocol it uses.


---


<h2><a href="https://tools.ietf.org/html/rfc7194">RFC 7194</a></h2>

<h3><a href="https://tools.ietf.org/html/rfc7194#section-2.2.2">2.2.2 - Client Certificate</a></h3>

This section deals with clients connecting to servers using TLS, and supplying a client certificate while connecting.

The section reads:

      The certificate's Common Name should match the main IRC nickname.

      If the network offers nick registration, this nick should be used.

      If the network offers grouped nicks, the main nick or account name
      should be used.

The client certificate's common name isn't really looked at in any way.

Servers only really look at a hashed signature (fingerprint) of the certificate, using it as an extra layer of authentication for [operators](https://modern.ircdocs.horse/#operators) or to authenticate to user accounts using [SASL](http://ircv3.net/specs/extensions/sasl-3.1.html) + CertFP (which just uses this certificate fingerprint).


---


<h2><a href="https://tools.ietf.org/html/draft-hardy-irc-isupport-00">Hardy's IRC RPL_ISUPPORT Numeric Definition Internet-Draft</a></h2>

On the whole, this specification is pretty much accurate and works well. There are a few tokens specified here which aren't well-used these days, and some that are missing but should be here. The Modern docs has a slightly-updated description of the RPL_ISUPPORT numeric in these sections: [1](https://modern.ircdocs.horse/#connection-registration), [2](https://modern.ircdocs.horse/#feature-advertisement), [3](https://modern.ircdocs.horse/#rplisupport-005), and [4](https://modern.ircdocs.horse/#rplisupport-005).

<h3><a href="https://tools.ietf.org/html/draft-hardy-irc-isupport-00#section-3">3 - The RPL_ISUPPORT numeric</a></h3>

The spec reads:

      counter this, a server MAY issue multiple RPL_ISUPPORT numerics.  A
      server MUST issue the RPL_ISUPPORT numeric after client registration
      has completed.  It MUST be issued after the RPL_WELCOME (XXX -
      reference?) welcome and MUST be issued before any further commands
      from the client are processed.

This is accurate, but a little imprecise. To be specific, the `001-004` numerics are the first numerics sent after connection registration has completed, followed by one or more `RPL_ISUPPORT (005)` numerics. The `RPL_ISUPPORT` numerics MUST come before either the `RPL_ENDOFMOTD` / `ERR_NOMOTD` numerics.


---


<h2><a href="http://www.irchelp.org/protocol/ctcpspec.html">The Client-To-Client Protocol (CTCP)</a></h2>

My advice on this specification can roughly be summed up as _"go read [this Internet-Draft](https://tools.ietf.org/html/draft-oakley-irc-ctcp-01) that grawity and I wrote up instead"_. Still, here are the sections of the original spec that have issues today.

### LOW LEVEL QUOTING

The spec reads:

      Even though messages to and from IRC servers cannot contain NUL, NL,
      or CR, it still might be desirable to send ANY character (in so called
      "middle level messages") between clients. In order for this to be
      possible, those three characters have to be quoted. Therefore a quote
      character is needed. Of course, the quote character itself has to be
      quoted too, since it is in-band.

Quite simply, nothing does this. No clients actually implement this in reality. Ignore this entirely

### TAGGED DATA

The spec reads:

      The text part of a PRIVMSG or NOTICE might contain zero or more
      extended messages, intermixed with zero or more chunks of non-extended
      data.

In reality, there can be zero (`0`) or one (`1`). Clients just see whether the first character of incoming messages is a `\001` (`X-DELIM`), and if so, treat it as a CTCP message. Otherwise, assume it's a normal message and display it to the user as such.

If you do insert more than one "extended message", most clients won't know what on earth you're talking about. Avoid them.

### CTCP LEVEL QUOTING

The spec reads:

      In order to be able to send the delimiter X-DELIM inside an extended
      data message, it has to be quoted. This introduces another quote
      character (which differs from the low level quote character so it
      won't have to be quoted yet again).

No. Just, no. Nothing does this. For the love of whatever spirit you subscribe to (or lack thereof), don't implement this. It is unnecessary, and clients have decided, on the whole, to not actually follow the advice given here.

### SED

The spec reads:

      SED probably stands for something like "Simple Encryption D???". It is
      used by clients to exchange encrypted messages between clients. A
      message encoded by SED probably looks something like:

As you can probably guess from the use of the word 'probably' in this section, this isn't documented or used today.

### VERSION

The spec reads:

            \001VERSION #:#:#\001

      where the first # denotes the name of the client, the second # denotes
      the version of the client, the third # the enviroment the client is
      running in.

Some clients format their `VERSION` values like this. A much larger number don't, however. Treat the returned value as an opaque blob.

### SOURCE

The spec reads:

            \001SOURCE #:#:#\001

      where the first # is the name of an Internet host where the client can
      be gotten from with anonymous FTP the second # a directory names, and
      the third # a space separated list of files to be gotten from that
      directory.

These days, clients that implement this just link to their Github repo or similar.

### ERRMSG

Honestly, a lot of clients don't implement this or any sort of `ERROR` CTCP messages. Avoid sending them in new clients.

### DCC

These days:

- DCC implementations commonly also have the `RESUME` and `ACCEPT` commands.
- IPv4 addresses can **either** be standard IP address strings (e.g. `127.0.0.1`) or the positive integer that is the IP address in network byte order (e.g. `2130706433`), whereas IPv6 addresses are sent and accepted as address strings such as `::1`.
- Port `0` may be sent by a client when it wants the client receiving the request to open the DCC connection, to work around NAT restrictions.

However, DCC still doesn't have any automatic or opportunistic forms of encryption, so that's still accurate!


---


## Acknowledgements

Thanks to everyone who's written sane IRC specifications (including most of the above). IRC wouldn't be here today without them, even if they have gotten a little old :)

Thanks to [thommey](https://github.com/thommey) for inspiring this page.
