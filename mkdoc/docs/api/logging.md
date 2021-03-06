### Listening

You can bind to the opendnp3 log stream by calling passing a _shared_ptr<ILogHandler>_ into the  _DNP3Manager_ constructor. This interface
just has a single method:

```c++
class ILogHandler
{
public:
	virtual void Log( const LogEntry& entry ) = 0;
};
```
If you need to send log messages to more than one location, create your own proxy _IlogHandler_. Keep in mind that this is a callback from the
thread-pool and will block that thread from executing. A good strategy for a file-logger in a big system would be to send a message
to a worker thread to write the message to disk.

### Individual loggers

Each channel and stack instance (master or outstation) has its own logger whose log level can be individually configured and adjusted at run-time. This allows you increase the log-level of a particular channel or stack, without
receiving full protocol analysis for everything and grinding the system to a halt. You specify the initial log level for each channel when it is created and this is inherited by stacks on that channel. You can adjust it
during execute by calling _IChannel::SetLogFilters_, _IMaster::SetLogFilters_, or _IOutstation::SetLogFilters_.

### Log levels

Opendnp3 defines a number of typical log levels like DEBUG, INFO, WARN, ERR. In addition it defines a number of log levels specific to the protocol dissection and analysis. Turning
on all the various DNP3 analysis levels allows you to create useful communication traces like the following.

```sh
ms(1440709781929) <-TL--  outstation - FIR: 1 FIN: 1 SEQ: 0 LEN: 11
ms(1440709781932) <-AL--  outstation - C0 15 3C 02 06 3C 03 06 3C 04 06
ms(1440709781934) <-AL--  outstation - FIR: 1 FIN: 1 CON: 0 UNS: 0 SEQ: 0 FUNC: DISABLE_UNSOLICITED
ms(1440709781939) <-AL--  outstation - 060,002 - Class Data - Class 1 - all objects
ms(1440709781944) <-AL--  outstation - 060,003 - Class Data - Class 2 - all objects
ms(1440709781949) <-AL--  outstation - 060,004 - Class Data - Class 3 - all objects
```
