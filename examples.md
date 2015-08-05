# Pull Examples #

In these examples, note that bandwidth is only limited in the return direction (output from the command run on the server), this is because we want to limit the bandwidth of the data we are pulling from that server.

## rdiff-backup ##

```
rdiff-backup --remote-schema="ssh -C %s 'rdiff-backup --server | bwlimit 100 9-17,20'" <remote-path> <local-path>
```

## rsync ##

rsync is a little more difficult, bwlimit cannot be used with rsync directly, we need to use a little **bash(1)** script to run **bwlimit**

```
#/bin/bash
# bwssh - run rsync server with bandwidth limit on returned data
HOST=$1 ; shift
exec ssh $HOST "$@ | bwlimit 100 9-17,20"
```

```
$ rsync -ebwssh <remote-path> <local-path>
```

# Push Examples #

If we were pushing data to a server then the limit needs to be placed before the remote command.

## rdiff-backup ##

```
rdiff-backup --remote-schema="bwlimit 100 9-17,20 | ssh -C %s 'rdiff-backup --server'" <local-path> <remote-path>
```

## rsync ##

rsync is a little more difficult, bwlimit cannot be used with rsync directly, we need to use a little **bash(1)** script to run **bwlimit**

```
#/bin/bash
# bwssh - run rsync server with bandwidth limit on data sent to server
HOST=$1 ; shift
exec bwlimit 100 9-17,20 | ssh $HOST "$@"
```

```
$ rsync -ebwssh <remote-path> <local-path>
```