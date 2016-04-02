<div align="center"><h2>Slowloris HTTP DoS</h2><br>
<font style="font-size: 9px; line-height: 8pt"><pre>CCCCCCCCCCOOCCOOOOO888@8@8888OOOOCCOOO888888888@@@@@@@@@8@8@@@@888OOCooocccc::::
CCCCCCCCCCCCCCCOO888@888888OOOCCCOOOO888888888888@88888@@@@@@@888@8OOCCoococc:::
CCCCCCCCCCCCCCOO88@@888888OOOOOOOOOO8888888O88888888O8O8OOO8888@88@@8OOCOOOCoc::
CCCCooooooCCCO88@@8@88@888OOOOOOO88888888888OOOOOOOOOOCCCCCOOOO888@8888OOOCc::::
CooCoCoooCCCO8@88@8888888OOO888888888888888888OOOOCCCooooooooCCOOO8888888Cocooc:
ooooooCoCCC88@88888@888OO8888888888888888O8O8888OOCCCooooccccccCOOOO88@888OCoccc
ooooCCOO8O888888888@88O8OO88888OO888O8888OOOO88888OCocoococ::ccooCOO8O888888Cooo
oCCCCCCO8OOOCCCOO88@88OOOOOO8888O888OOOOOCOO88888O8OOOCooCocc:::coCOOO888888OOCC
oCCCCCOOO88OCooCO88@8OOOOOO88O888888OOCCCCoCOOO8888OOOOOOOCoc::::coCOOOO888O88OC
oCCCCOO88OOCCCCOO8@@8OOCOOOOO8888888OoocccccoCO8O8OO88OOOOOCc.:ccooCCOOOO88888OO
CCCOOOO88OOCCOOO8@888OOCCoooCOO8888Ooc::...::coOO88888O888OOo:cocooCCCCOOOOOO88O
CCCOO88888OOCOO8@@888OCcc:::cCOO888Oc..... ....cCOOOOOOOOOOOc.:cooooCCCOOOOOOOOO
OOOOOO88888OOOO8@8@8Ooc:.:...cOO8O88c.      .  .coOOO888OOOOCoooooccoCOOOOOCOOOO
OOOOO888@8@88888888Oo:. .  ...cO888Oc..          :oOOOOOOOOOCCoocooCoCoCOOOOOOOO
COOO888@88888888888Oo:.       .O8888C:  .oCOo.  ...cCCCOOOoooooocccooooooooCCCOO
CCCCOO888888O888888Oo. .o8Oo. .cO88Oo:       :. .:..ccoCCCooCooccooccccoooooCCCC
coooCCO8@88OO8O888Oo:::... ..  :cO8Oc. . .....  :.  .:ccCoooooccoooocccccooooCCC
:ccooooCO888OOOO8OOc..:...::. .co8@8Coc::..  ....  ..:cooCooooccccc::::ccooCCooC
.:::coocccoO8OOOOOOC:..::....coCO8@8OOCCOc:...  ....:ccoooocccc:::::::::cooooooC
....::::ccccoCCOOOOOCc......:oCO8@8@88OCCCoccccc::c::.:oCcc:::cccc:..::::coooooo
.......::::::::cCCCCCCoocc:cO888@8888OOOOCOOOCoocc::.:cocc::cc:::...:::coocccccc
...........:::..:coCCCCCCCO88OOOO8OOOCCooCCCooccc::::ccc::::::.......:ccocccc:co
.............::....:oCCoooooCOOCCOCCCoccococc:::::coc::::....... ...:::cccc:cooo
 ..... ............. .coocoooCCoco:::ccccccc:::ccc::..........  ....:::cc::::coC
   .  . ...    .... ..  .:cccoCooc:..  ::cccc:::c:.. ......... ......::::c:cccco
  .  .. ... ..    .. ..   ..:...:cooc::cccccc:.....  .........  .....:::::ccoocc
       .   .         .. ..::cccc:.::ccoocc:. ........... ..  . ..:::.:::::::ccco
</pre></font>
</div>

<p><b>Welcome to Slowloris - the low bandwidth, yet greedy and poisonous HTTP client!</b></p>

<p>Written by <a href="http://web.archive.org/web/20150426090206/http://ha.ckers.org/">RSnake</a> with help from John Kinsella, IPv6 version by <a href="http://web.archive.org/web/20150426090206/http://twitter.com/hugo_glez">Hugo Gonzalez</a> and a dash of inspiration from <a href="http://web.archive.org/web/20150426090206/http://www.outpost24.com/">Robert E Lee</a>.</p>

<p><b>UPDATE 3:</b> IPv6 version provided by Hugo Gonzalez.</p>

<p><b>UPDATE 2:</b> Video presentation of Slowloris at DefCon (the middle section of the presentation) can be seen here:  <a href="http://web.archive.org/web/20150426090206/http://vimeo.com/7618090">Hijacking Web 2.0 Sites with SSLstrip and SlowLoris -- Sam Bowne and RSnake at Defcon 17</a>.</p>

<p><b>UPDATE:</b> Amit Klein pointed me to a <a href="http://web.archive.org/web/20150426090206/http://www.securityfocus.com/archive/1/456339/30/0/threaded">post written by Adrian Ilarion Ciobanu written in early 2007</a>
 that perfectly describes this denial of service attack.  It was also 
described in 2005 in the "Programming Model Attacks" section of Apache 
Security.  So although there was no tool released at that time these two
 still technically deserves all the credit for this.  I apologize for 
having missed these.</p>

<p>In considering the ramifications of a slow denial of service attack 
against particular services, rather than flooding networks, a concept 
emerged that would allow a single machine to take down another machine's
 web server with minimal bandwidth and side effects on unrelated 
services and ports.  The ideal situation for many denial of service 
attacks is where all other services remain intact but the webserver 
itself is completely inaccessible.  Slowloris was born from this 
concept, and is therefore relatively very stealthy compared to most 
flooding tools.</p>

<p>Slowloris holds connections open by sending partial HTTP requests.  
It continues to send subsequent headers at regular intervals to keep the
 sockets from closing.  In this way webservers can be quickly tied up.  
In particular, servers that have threading will tend to be vulnerable, 
by virtue of the fact that they attempt to limit the amount of threading
 they'll allow.  Slowloris must wait for all the sockets to become 
available before it's successful at consuming them, so if it's a high 
traffic website, it may take a while for the site to free up it's 
sockets.  So while you may be unable to see the website from your 
vantage point, others may still be able to see it until all sockets are 
freed by them and consumed by Slowloris.  This is because other users of
 the system must finish their requests before the sockets become 
available for Slowloris to consume.  If others re-initiate their 
connections in that brief time-period they'll still be able to see the 
site.  So it's a bit of a race condition, but one that Slowloris will 
eventually always win - and sooner than later.</p>

<p>Slowloris also has a few stealth features built into it.  Firstly, it
 can be changed to send different host headers, if your target is a 
virtual host and logs are stored seperately per virtual host.  But most 
importantly, while the attack is underway, the log file won't be written
 until the request is completed.  So you can keep a server down for 
minutes at a time without a single log file entry showing up to warn 
someone who might watching in that instant.  Of course once your attack 
stops or once the session gets shut down there will be several hundred 
400 errors in the web server logs.  That's unavoidable as Slowloris sits
 today, although it may be possible to turn them into 200 OK messages 
instead by completing a valid request, but Slowloris doesn't yet do 
that.</p>

<p>HTTPReady quickly came up as a possible solution to a Slowloris 
attack, because it won't cause the HTTP server to launch until a full 
request is recieved.  This is true only for GET and HEAD requests.  As 
long as you give Slowloris the switch to modify it's method to POST, 
HTTPReady turns out to be a worthless defense against this type of 
attack.</p>

<p>This is <b><i>NOT</i></b> a TCP DoS, because it is actually making a full TCP connection, not a partial one, however it <i>is</i>
 making partial HTTP requests.  It's the equivalent of a SYN flood but 
over HTTP.  One example of the difference is that if there are two 
web-servers running on the same machine one server can be DoSed without 
affecting the other webserver instance.  Slowloris would also 
theoretically work over other protocols like UDP, if the program was 
modified slightly and the webserver supported it.  Slowloris is also <b><i>NOT</i></b>
 a GET request flooder. Slowloris requires only a few hundred requests 
at long term and regular intervals, as opposed to tens of thousands on 
an ongoing basis.</p>

<p>Interestingly enough, in testing this has been shown in at least one 
instance to lock up database connections and force other strange issues 
and errors to arise that can allow for fingerprinting and other odd 
things to become obvious once the DoS is complete and the server 
attempts to clean itself up.  I would guess that this issue arises when 
the webserver is allowed to open more connections than the database is, 
causing the database to fail first and for longer than the webserver.</p>

<p>Slowloris lets the webserver return to normal almost instantly 
(usually within 5 seconds or so).  That makes it ideal for certain 
attacks that may just require a brief down-time.  As described in <a href="http://web.archive.org/web/20150426090206/http://ha.ckers.org/blog/20090504/using-denial-of-service-for-hacking/">this blog post, DoS is actually very useful for certain types of attacks</a> where timing is key, or as a diversionary tactic, etc....</p>

<p>This affects a number of webservers that use threaded processes and 
ironically attempt to limit that to prevent memory exhaustion - fixing 
one problem created another.  This includes but is not necessarily 
limited to the following:</p>

<p>
  </p><ul>
    <li>Apache 1.x
    </li><li>Apache 2.x
    </li><li>dhttpd
    </li><li>GoAhead WebServer
    </li><li>WebSense "block pages" (unconfirmed)
    </li><li>Trapeze Wireless Web Portal (unconfirmed)
    </li><li>Verizon's MI424-WR FIOS Cable modem (unconfirmed)
    </li><li>Verizon's Motorola Set-Top Box (port 8082 and requires auth - unconfirmed)
    </li><li><a href="http://web.archive.org/web/20150426090206/http://www.bee-ware.net/en/">BeeWare</a> WAF (unconfirmed)
    </li><li><a href="http://web.archive.org/web/20150426090206/http://www.denyall.com/">Deny All</a> WAF (unconfirmed)
    </li><li>Flask (<a href="http://web.archive.org/web/20150426090206/https://github.com/mitsuhiko/flask/issues/1266">development server</a>)
  </li></ul>

<p></p>

<p>There are a number of webservers that this doesn't affect as well, in my testing:</p>

<p>
  </p><ul>
    <li>IIS6.0
    </li><li>IIS7.0
    </li><li>lighttpd
    </li><li>Squid 
    </li><li>nginx 
    </li><li>Cherokee (<a href="http://web.archive.org/web/20150426090206/http://lists.octality.com/pipermail/cherokee/2009-June/010530.html">verified by user community</a>)
    </li><li>Netscaler
    </li><li>Cisco CSS (<a href="http://web.archive.org/web/20150426090206/http://www.cupfighter.net/index.php/2009/06/slowloris-css/">verified by user community</a>)
  </li></ul>

<p></p>

<p>This is obviously not a complete list, and there may be a number of 
variations on these web-servers that are or are not vulnerable.  I 
didn't test every configuration or variant, so your mileage may vary.  
This also may not work if there is an upstream device that somehow 
limits/buffers/proxies HTTP requests.  Please note though that <b>Slowloris only represents one variant of this attack</b>
 and other variants may have different impacts on other webservers and 
upstream devices.  This command should work on most systems, but please 
be sure to check the options as well:</p>

<p><b>perl slowloris.pl -dns example.com</b></p>

<p>Requirements:  This is a Perl program requiring the Perl interpreter with the modules <a href="http://web.archive.org/web/20150426090206/http://perldoc.perl.org/IO/Socket/INET.html">IO::Socket::INET</a>, <a href="http://web.archive.org/web/20150426090206/http://search.cpan.org/%7Ebehroozi/IO-Socket-SSL-0.97/">IO::Socket::SSL</a>, and <a href="http://web.archive.org/web/20150426090206/http://search.cpan.org/%7Ejv/Getopt-Long-2.38/">GetOpt::Long</a>.
  Slowloris works MUCH better and faster if you have threading, so I 
highly encourage you to also install threads and threads::shared if you 
don't have those modules already.  You can install modules using CPAN:</p>

<p></p><blockquote>perl -MCPAN -e 'install IO::Socket::INET'<br>
perl -MCPAN -e 'install IO::Socket::SSL'</blockquote><p></p>

The IPv6 version needs:

<p></p><blockquote>perl -MCPAN -e 'install IO::Socket::INET6'<br>
perl -MCPAN -e 'install IO::Socket::SSL'</blockquote><p></p>


<p><img src="http://web.archive.org/web/20150426090206im_/http://ha.ckers.org/images/microsoft_icon.gif" align="left" height="50" width="50"><b>Windows users</b>: You probably will not be able to successfuly execute a Slowloris denial of service from Windows even if you use <a href="http://web.archive.org/web/20150426090206/http://www.cygwin.com/">Cygwin</a>.
 I have not had any luck getting Slowloris to successfuly deny service 
from within Windows, because Slowloris requires more than a few hundred 
sockets to work (sometimes a thousand or more), and Windows limits 
sockets to around 130, from what I've seen.  I highly suggest you use a 
*NIX operating system to execute Slowloris from for the best results, 
and not from within a virtual machine, as that could have unexpected 
results based on the parent operating system.</p>

<p>Version: Slowloris is currently at version 0.7 - 06/17/2009 and 0.7.1 (IPv6 version) - 04/02/2013</p>

<p>Download: <a href="http://web.archive.org/web/20150426090206/http://ha.ckers.org/slowloris/slowloris.pl">slowloris.pl</a> or <a href="http://web.archive.org/web/20150426090206/http://ha.ckers.org/slowloris/slowloris6.pl">slowloris6.pl (IPv6 version)</a></p>

<p>Getting started: <b>perldoc slowloris.pl</b> or <b>perldoc slowloris6.pl</b></p>

<p>Issues: For a complete list of issues look at the Perl documentation,
 which explains all of the things to think about when running this 
denial of service attack.</p>

<p>Thanks:  Thank you to John Kinsella for the help with threading and <a href="http://web.archive.org/web/20150426090206/http://ha.ckers.org/blog/about/">id</a> and greyhat for help with testing.  Big thanks to Hugo Gonzalez for IPv6 too!</p>

<p></p><div align="center"><img src="http://web.archive.org/web/20150426090206im_/http://ha.ckers.org/images/slowloris.png" height="293" width="297"></div><p></p>

<br><br>

<p>Back to <a href="http://web.archive.org/web/20150426090206/http://ha.ckers.org/">http://ha.ckers.org/</a></p>
</div>

http://ha.ckers.org/slowloris/

http://web.archive.org/web/20150426090206/http://ha.ckers.org/slowloris
