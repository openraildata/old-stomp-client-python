# stomp-client-python
## An example Stomp client for Python

Replace < Your GUID queue >  below using your API queue GUID, you should be able to subscribe to the Darwin real-time feed. 
The key here is to treat the gzip compressed message as a memory based gzip file, so when you read the file, it automatically ungzips for you.

Raise issues if you encounter any problems.

```python

import stomp, gzip, StringIO, xml

class MyListener(object):
        #
        # def __init__ (self, conn):
        #       self._conn = conn

        def on_error(self, headers, message):
                print('received an error %s' % message)

        def on_message(self, headers, message):
                fp = gzip.GzipFile(fileobj = StringIO.StringIO(message))
                text = fp.readlines()
                fp.close()
                print('%s\n' % text)

        #       self._conn.ack(id=headers['message-id'], subscription=headers['subscription'])

conn = stomp.Connection([('datafeeds.nationalrail.co.uk', 61613)])

conn.set_listener('', MyListener())
conn.start()
conn.connect(username = 'd3user', passcode = 'd3password', wait=False)

conn.subscribe(destination='/queue/<your GUID queue>', id=1, ack='auto')

#conn.send(body=' '.join(sys.argv[1:]), destination='')

mydata = raw_input('Prompt :')

conn.disconnect()



```


sample XML results:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Pport xmlns:fc="http://www.thalesgroup.com/rtti/PushPort/Forecasts/v2"
        xmlns:ct="http://www.thalesgroup.com/rtti/PushPort/CommonTypes/v1" 
        ts="2015-05-10T18:11:02.7643958+01:00" 
        version="12.0" 
        xmlns="http://www.thalesgroup.com/rtti/PushPort/v12">
        
        <uR updateOrigin="TD">
                <TS rid="201505100725954" uid="Y62668" ssd="2015-05-10">
                        <fc:Location tpl="MEADWHL" wta="18:15:30" wtd="18:16:30" pta="18:16" ptd="18:16">
                                <fc:arr et="18:16" wet="18:14" src="TD" /><fc:dep et="18:16" src="Darwin" />
                                <fc:plat platsrc="M">2</fc:plat>
                        </fc:Location>
                </TS>
        </uR>
</Pport>

```

```xml
<?xml version="1.0" encoding="utf-8"?><Pport xmlns:fc="http://www.thalesgroup.com/rtti/PushPort/Forecasts/v2" xmlns:ct="http://www.thalesgroup.com/rtti/PushPort/CommonTypes/v1" ts="2015-05-10T18:11:02.7643958+01:00" version="12.0" xmlns="http://www.thalesgroup.com/rtti/PushPort/v12"><uR updateOrigin="CIS" requestSource="at05" requestID="0000000000016151"><TS rid="201505100742207" uid="G98575" ssd="2015-05-10"><fc:Location tpl="BLGRVE" wta="18:11" wtd="18:11:30" pta="18:11" ptd="18:11"><fc:arr at="18:12" src="TD" /><fc:dep et="18:12" src="Darwin" /><fc:plat platsrc="A" conf="true">2</fc:plat></fc:Location></TS></uR></Pport>
```
