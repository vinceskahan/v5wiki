You can use a weeWX service to add fields to each LOOP packet or archive record.  However, if that service takes a long time to collect the data from the hardware, this will cause problems with the weeWX event processing.  One solution is to put the data collection onto a separate thread.

These are instructions for adding data by querying a network socket via a separate thread.

### How to do it

This is a service that collects data from a remote source.  It starts a RemoteDataThread that opens a socket and receives data as soon as the socket server sends it.  If there are any network failures, the RemoteDataThread simply retries.  Whenever it receives data, it places the data into the `values` dict.  The service is bound to `NEW_ARCHIVE_RECORD` events.  Whenever there is a new archive record, the service adds any observations from the values dict to the new archive record.

It expects data from the socket server in a single line of space-delimited name=value pairs.

Put the service code into a file in the `user` directory, say `/home/weewx/bin/user/remotedata.py`

```Python
import socket
import syslog
import time
import weewx
from weewx.engine import StdService

class RemoteDataService(StdService):
    def __init__(self, engine, config_dict):
        super(PressureService, self).__init__(engine, config_dict)
        d = config_dict.get('PressureService', {})
        self.server_name = d.get('server', 'localhost')
        self.port = int(d.get('port', 8083))
        # the thread will set this each time it reads new values.
        # the service will read this each archive interval.
        self.values = dict()
        # start the thread that captures the pressure value
        self._thread = RemoteDataThread(self, self.server_name)
        self._thread.start()
        # bind this service to happen with each new archive record
        self.bind(weewx.NEW_ARCHIVE_RECORD, self.new_archive_record)

    def shutDown(self):
        # when the service shuts down, tell the thread to die and wait for it
        if self._thread:
            self._thread.running = False
            self._thread.join()
            self._thread = None

    def new_archive_record(self, event):
        # add the values to the record.  this assumes that the
        # values are in units appropriate to the record's unit system.
        event.record.update(self.values)

class RemoteDataThread(threading.Thread):
    def __init__(self, service, server_name, server_port):
        threading.Thread.__init__(self)
        self.service = service
        self.server_name = server_name
        self.server_port = server_port
        self.running = False

    def run(self):
        self.running = True
        while self.running:
            try:
                sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
                server = (self.server_name, self.port)
                sock.connect(server)
                while self.running:
                    data = sock.recv(1024)
                    if data:
                        pairs = data.split(' ')
                        for p in pairs:
                            n, v = p.split('=')
                            self.service.values[n] = float(v)
            except socket.error, e:
                syslog.syslog(syslog.LOG_INFO, "fail: %s" % e)
            time.sleep(1)
```

To enable the service, add it to the service list in `weewx.conf`:

```
[Engine]
    [[Services]]
        data_services = ..., user.remotedata.RemoteDataService
```

Then restart weeWX.
