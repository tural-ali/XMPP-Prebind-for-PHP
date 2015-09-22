# XMPP Prebind for PHP backend

This class is designed for PHP backend to prebind a XMPP Session with XMPP servers like Openfire (tested), eJabberd etc. 

In other words if you want to use frontend XMPP client like [ConverseJS](https://conversejs.org/) and take advantage of [persistent login](https://conversejs.org/docs/html/setup.html#prebind) (aka [Single Session Support](https://conversejs.org/docs/html/setup.html#session-support)) with PHP then you're in right place.

How to use?
=====
Clone this repository and require (or use namespace as I did) where you want to do the prebinding. Let's say our Jabber ID (JID) is john@domain.com. Use like below:

```
<?php
        use common\extensions\XmppPrebind; //or require class here. Totally up to you.

        $params = [
            "user" => "john",
            "password" => "johnspass",
            "tld" => "domain.com",
            "boshUrl" => "<bosh-url-of-your-xmpp-server>" 
            //For openfire it's something like http://<your-xmpp-fqdn>:7070/http-bind/
        ];
        $xmpp = new XmppPrebind($params);
        echo json_encode($xmpp->connect()); //will return JID, SID, RID as JSON
```
That's it!
