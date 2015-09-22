<?php
/**
 * Created by Tural Aliyev <aliyev@tural.us>
 * User: turalaliyev
 * Date: 9/22/15
 * Time: 23:53
 */

namespace common\extensions;


class XmppPrebind
{
    protected $sid;
    protected $rid;
    protected $jid;
    protected $boshUrl;
    protected $user;
    protected $tld;
    protected $password;

    public function __construct($params)
    {
        foreach ($params as $key => $value)
            $this->{$key} = $value;
    }


    public function getRid()
    {
        return $this->rid;
    }

    public function getSid()
    {
        return $this->sid;
    }

    public function getJid()
    {
        return $this->jid;
    }

    public function connect()
    {
        $hash = base64_encode("$this->user@$this->tld\0$this->user\0$this->password") . "\n";
        $rid = rand();
        $jid = "$this->user@$this->tld";

        $body = "<body rid='$rid'
                    xmlns='http://jabber.org/protocol/httpbind'
                    to='$this->tld' xml:lang='en' wait='60' hold='1'
                    window='5' content='text/xml; charset=utf-8'
                    ver='1.6' xmpp:version='1.0' xmlns:xmpp='urn:xmpp:xbosh'/>";

        $return = $this->__sendBody($body);
        $xml = new \SimpleXMLElement($return);

        $sid = $xml['sid'];
        $rid++;
        $body = "<body rid='$rid' xmlns='http://jabber.org/protocol/httpbind' sid='$sid'>
                    <auth xmlns='urn:ietf:params:xml:ns:xmpp-sasl' mechanism='PLAIN'>$hash</auth>
                </body>";
        $this->__sendBody($body);

        $rid++;
        $body = "<body rid='$rid' xmlns='http://jabber.org/protocol/httpbind' sid='$sid' to='$this->tld' xml:lang='en' xmpp:restart='true' xmlns:xmpp='urn:xmpp:xbosh'/>";
        $this->__sendBody($body);

        $rid++;
        $body = "<body rid='$rid' xmlns='http://jabber.org/protocol/httpbind' sid='$sid'>
                        <iq type='set' id='_bind_auth_2' xmlns='jabber:client'>
                            <bind xmlns='urn:ietf:params:xml:ns:xmpp-bind'/>
                        </iq>
                </body>";

        $this->__sendBody($body);
        $rid++;
        $body = "<body rid='$rid' xmlns='http://jabber.org/protocol/httpbind' sid='$sid'>
            <iq type='set' id='_session_auth_2' xmlns='jabber:client'>
                <session xmlns='urn:ietf:params:xml:ns:xmpp-session'/>
            </iq>
        </body>";
        $this->__sendBody($body);
        $rid++;


        $body = "<body rid='$rid' sid='$sid' xmlns='http://jabber.org/protocol/httpbind'>
                  <iq id='bind_1' type='set' xmlns='jabber:client'>
                    <bind xmlns='urn:ietf:params:xml:ns:xmpp-bind'>
                      <resource>httpclient</resource>
                    </bind>
                  </iq>
                </body>";
        $this->__sendBody($body);
        $rid++;

        $sid = (array)$sid;
        return [
            "rid" => $rid,
            "sid" => $sid[0],
            "jid" => "$jid"
        ];
    }

    private function __sendBody($body)
    {
        $ch = curl_init($this->boshUrl);
        curl_setopt($ch, CURLOPT_HEADER, 0);
        curl_setopt($ch, CURLOPT_POST, 1);
        curl_setopt($ch, CURLOPT_POSTFIELDS, $body);
        curl_setopt($ch, CURLOPT_FOLLOWLOCATION, true);
        $header = array('Content-Type: text/xml; charset=utf-8');
        curl_setopt($ch, CURLOPT_HTTPHEADER, $header);
        curl_setopt($ch, CURLOPT_VERBOSE, 0);
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
        $output = curl_exec($ch);
        curl_close($ch);
        return ($output);
    }
}
