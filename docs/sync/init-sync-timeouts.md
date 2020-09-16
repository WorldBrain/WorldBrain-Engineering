# Initial sync timeouts

There are 2 distinct types of init sync timeouts:
1. package timeout: 10s - event name: `packageStalled`
2. channel timeout: 60s - event name: `channelTimeout`

## On Memex ext:

1 is ignored.
2 stops the sync altogether.

## On Memex Go:

The sync timeouts are completely ignored. Instead it implements it’s own timeout which triggers if no sync progress (another event that emits from sync) occurs in 7 mins.
