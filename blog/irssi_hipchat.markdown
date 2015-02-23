irssi + bitlbee + hipchat = fun
===============================

irssi
-----

I am not going to cover irssi.. There are many good tutorials out
there for irssi. If you are not already using irssi, do not look at
this page, it will confuse you. Nuff said.

bitlbee
-------

[Bitlbee](http://www.bitlbee.org/) is the driver behind this. It does
the XMPP/Jabber to IRC work for you. You just have to set it up.

hipchat
-------

I assume you are already stuck with hipchat. If so, doing this will
make you happy. I can use my own logrotate/grep fu to search my
history and use the same commands I've become accustomed to in
irssi. And the best part is that I do not need a big nasty UI to do
what I want. Its in the same terminal. Win.

configure bitlbee
----------------

[This](http://www.antonfagerberg.com/texts/my-perfect-irssi-setup/) is
what I used to configure bitlbee. I started with the apt-get and ended
when the Google Talk portion started. Creating an account and
registering is something straightforward to do. And when you connect
to &localhost, it will totally look like you are in a chat room with
@root. Dont worry, he wont leak your passwords. You **have** to create
an account because it is where bitlbee stores buddy lists and channel
lists.

**Note** I have since built bitlbee using *feat/hip-cat* (yes thats not
 misspelt) branch and its rendering the html spit out by hipchat
 better. Its still <a href...> html, but its not complaining about
 unknown users anymore, and the <br/> nastyness is gone. I also took
 the bitlbee init script and modified it to use the new bitlbee
 binary. WIN!

configure hipchat in bitlbee
-----------------

[Here](http://www.phase2technology.com/blog/using-hipchat-through-an-irc-client-with-bitlbee/)
is the page I used to get my account added to bitlbee. So, now that
you have run all those commnds, you will log in to your favorite
channel and notice no one is linked by their mention names... Yes
*that sucks* (tm). But luckily I wrote a script to add users to my
hub_cap.xml.

I actually stole a script from some IRC buddies and crafted it to my
purposes. It will look at an xml file that you can get from the
hipchat API and insert anyone who is not alread in your list of
existing buddies. So first shot will add a billion users, and then you
can run it (or cron it) after new people join up.

[Here](https://gist.github.com/hub-cap/7bdf65a20bf9a3a16d96) is the
script I use (on a cron) to generate my buddy list. It takes your
bitlbee config file and the hipchat api json for users. You can
generate the hipchat document like so...

```
http://api.hipchat.com/v2/user?auth_token=YOUR_HIPCHAT_TOKEN&max-results=500
```

I personally run a cron script that looks like this

```
curl "http://api.hipchat.com/v2/user?auth_token=YOUR_HIPCHAT_TOKEN&max-results=500" -o /tmp/hipchat-names.json
sudo python .hipchat_renames.py /var/lib/bitlbee/hub_cap.xml /tmp/hipchat-names.json
```

And it regen's my config file for me.

**THIS WILL WIPE YOUR EXISTING CONFIG FILE**

But I don't use irssi to control this file anyway, and do not run
bitlbee commands like adding buddies to my buddylist. So... **YMMV**

ok so now that is out of the way, you may notice that the first time
you try this, it will wipe your config file. At least it did mine. I
wasnt restarting things in the right order. The correct order seems to
be to

* disconnect (/disconnect localhost)
* Kill bitlbee daemon on server (/etc/init.d/bitlbee)
* Run the script
* Start bitlbee daemon
* reconnect (/connect localhost)
* identify yourself (identify PASSWD)

And viola. Now if, in the &bitlbee window you run 'blist' it should
show all your buddies with their proper hipchat mention.

Finally, you will want to use an irssi script that inserts the @ sign
before user's handles. Because hipchat is hipchat and does things
different. So, for instance, when I want to talk to my bud kurt, i can
type ku<tab> and it will autotab to @kurt. The script was not written
by me so I am waiting for consent on publishing it. Ping me personally
(on hipchat or freenode) for it until I update the post to include it.
