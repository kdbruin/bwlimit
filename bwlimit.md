# NAME #
**bwlimit** - bandwidth limiter for pipelines

# SYNOPSIS #

```
bwlimit [-v] [-g<granularity>] [[schedule,]limit [...]]
```

# DESCRIPTION #

**bwlimit** is a command line utility intended to provide a simple means of bandwidth limiting a stream of data in a pipeline.  It is intended to be used in conjunction with utilities like **ssh(1)** to limit the amount of data transmitted across the network, though it can be used anywhere a pipeline might require bandwidth limiting, for instance writing to disk.

bwlimit supports limiting bandwidth on a schedule, that is different limits can be set at different times of the day, such that long transfers will slow down or speed up while still running, depending on what time of day it is.

# USAGE #

The schedule is internally held a 24 integers which represent the respective bandwidth specified in KiB/s units, for each of the 24 hours in a day.

Each schedule argument is parsed and used to add to the current 24 hour schedule.  Each successive schedule argument overwrites the previous for the hours it specified.  The _schedule:_ part of the argument can be omitted, in which case the bandwidth argument applies to the entire 24 hour schedule (and overwrites all previous schedules).  If schedule is specified it is either a single hour, or a range of hours separated with a hyphen, for example _7-21:_ means hours 7 thru 21.

The default schedule is unlimited 24 hrs a day, so **bwlimit** with no arguments is effectively the same as **cat(1)**.

## Examples ##

**bwlimit**
> This simply copies standard input to standard output without applying any limits.

**bwlimit 100**
> This sets the bandwidth limit to 100 KiB/s for the whole 24 hours.

**bwlimit 100 7-21,50**
> This sets the default limit to 100 KiB/s and then sets a limit of 50KiB/s for the hours 7 thru 21.

**bwlimit 0 7,100 8,100 9-17,50 18-21,100**
> This sets the default limit as unlimited but then sets a limit of 100 KiB/s between 7-8:59:59am and 18-20:59:59pm, and a limit of 50 KiB/s for the hours 9am thru 5pm.

**bwlimit 7-21,100 9-17,50**
> This example is exactly the same as the previous example, just shorter.  It uses the fact that the default limit is unlimited to drop the first argument.  It also uses the fact that subsequent schedules overwrite previous ones and sets the 100KiB/s limit for hours 7 thru 21 and then overwrites the hours 9 thru 17 with a limit of 50KiB/s

## Options ##

**`-v`** Verbose mode.  Causes **bwlimit** to print details of bandwidth usage and limits to standard error.

**`-g<granularity>`**
> Set the number of times a second **bwlimit** will check and limit bandwidth.  The finer the granularity the smoother the bandwidth usage.  The default is to check bandwidth ever 1ms interval which in most cases will be fine enough granularity.  Setting granularity to 0 means that bandwidth is checked after every read, which given the internal buffer size for the read is 16k means every 16k transfered.

# AUTHOR #
**bwlimit** was originally written by Austin France.

The **bwlimit** project is open source and can be found at http://bwlimit.googlecode.com/. It is release under the [New BSD license](http://www.opensource.org/licenses/bsd-license.php).

# SEE ALSO #

**[bwlimit\_\*() API](bwlimitAPI.md)**