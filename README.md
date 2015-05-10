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
