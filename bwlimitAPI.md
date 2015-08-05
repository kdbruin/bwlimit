# Introduction #

**bwlimit** is implemented as an API as well as a command line tool.  This page documents the API.

# SYNOPSIS #

```
#include "bwlimit.h"
```
```
void bwlimit_msleep(long ms); 
long long bwlimit_timer(void); 
void bwlimit_init(struct bwlstate *state);
void bwlimit_args(struct bwlstate *state, int argc, char **argv); 
void bwlimit_start(struct bwlstate *state); 
void bwlimit_limit(struct bwlstate *state, size_t size); 
void bwlimit_end(struct bwlstate *state); 
```

# DESCRIPTION #

The main sequence of calls required to bandwidth limit a data stream is _bwlimit\_init()_ -> _bwlimit\_start()_ -> _bwlimit\_limit()_ -> _bwlimit\_end()_.  _bwlimit\_args()_ can be called between init and start to set the bandwidth limits per hour based on **[bwlimit(1)](bwlimit.md)** compatible command line arguments.

**bwlimit\_init(struct bwlstate `*`state)**
> This must be called at the start of the process.  It initialises the passed struct bwlstate structure with the default settings and prepares it for use.

**bwlimit\_args(struct bwlstate `*`state, int argc, char `*``*`argv)**
> This is optionally called after init and before start to set the bandwidth limit schedule based on command line arguments compatible with **[bwlimit(1)](bwlimit.md)**.  Bandwidth limits can be set directly in the bwlimit state structure through the **schedule[.md](.md)** member, which is a 24 element integer array containing the bandwidth limits in KiB/s for each of the 24 hours.

**bwlimit\_start(struct bwlstate `*``*`state)**
> This starts the bandwidth limit process.  Essentially it resets the totals and sets the start time.

**bwlimit\_limit(struct bwlstate `*`state, size\_t size)**
> This is the guts of the bandwidth limiting logic.  It should be called somewhere in the main loop after a read and before the write.  It is passed the size of the data just read and will effect a delay based on the amount of data passed through so far to limit the throughput to the desired bandwidth for the current hour.

**bwlimit\_end(struct bwlstate `*`state)**
> This should be called once processing is complete to perform cleanup and to output totals in verbose mode.

**bwlimit\_timer()**
> This is a timer routine used internally to return the number of nanoseconds since an unknown point in time.  The granularity of the timer may not be as fine as a nanosecond, and will depend upon the platform on which it is running.

**bwlimit\_msleep(long ms)**
> This is a sleep routine used internally to sleep for a specified number of miliseconds.

**struct bwlstate**

```
struct bwlstate {
    int schedule[24];   /* bandwidth schedule (KiB/s/hour) */
    int grain;          /* grain size */
    int kbs;            /* currently selected bandwidth limit */
    long long btot;     /* total bytes transfered */
    long long start;    /* start */
    long long lt;       /* time stamp of last limit */
    long long tot;      /* total bytes between limiting */
    int verbose;        /* verbose mode */
};
```

Most of the members of this structure should not be modified, however three members may be modified before _bwlimit\_start()_ is called.

**schedule**
> This is an array of 24 integers representing the bandwidth limits for each hour of the day.  This can be initialised by the application any way it sees fit.  A bandwidth limit of 0 means unlimited which is what this array is initialised to by default.

**grain**
> This is the grain size, or the number of times per second, that bandwidth is checked.  This is set to 1000 (ie once every 1ms) by default.

**verbose**
> If set to a non-zero value, the _bwlimit`_``*`() API_ will output to stderr, diagnostic information showing bandwidth and limiting progress.

# AUTHOR #
**bwlimit** was originally written by Austin France.

The **bwlimit** project is open source and can be found at http://bwlimit.googlecode.com/. It is release under the [New BSD license](http://www.opensource.org/licenses/bsd-license.php).

# SEE ALSO #
**[bwlimit(1)](bwlimit.md)**