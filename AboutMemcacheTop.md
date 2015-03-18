# Introduction #

I wanted a simple command-line tool to be able to grab real-time stats from memcache (memcached, I know, I know), and output it in a view something like top.  I couldn't find anything like it, so I wrote one myself in perl.

When writing it, I tried to keep it simple, portable, and lightweight.  (No memcached perl modules required!  I tried to keep it to modules I thought would be preinstalled on almost any modern system.  It's also fairly polite - non-critical modules get checked, and if they aren't installed, the functionality is disabled without spewing errors or dying.)  I realize it's not written well.  But, hey, at least it exists, right?  Until the day I released it, there wasn't any comparable tool like it for memcached.

It gives you the basic stats, and not too much else.  (You can specify thresholds, for instance, and it'll change color to red if you exceed the thresholds.  You can also choose the refresh/ sleep time, and whether to show immediate (per second) stats, or lifetime stats.  But it pretty much all revolves around those stats.)


# Details #

The best way to learn more about it is to see what it looks like!

Here's some sample output:

```
memcache-top v0.5	(default port: 11211, color: on, refresh: 3 seconds)

INSTANCE		USAGE	HIT %	CONN	TIME	EVICT/s READ/s	WRITE/s
10.50.11.5:11211	88.8%	69.6%	1123	1.9ms	0.3	13.1K	36.2K	
10.50.11.5:11212	88.7%	69.6%	1175	0.6ms	0.3	12.4K	28.1K	
10.50.11.5:11213	88.8%	69.4%	1148	0.7ms	0.0	16.6K	32.1K	
10.50.12.5:11211	89.3%	81.5%	1460	0.7ms	0.3	17.7K	204.0K	
10.50.12.5:11212	89.4%	69.3%	1174	0.6ms	1.0	28.9K	63.5K	
10.50.12.5:11213	89.3%	69.4%	1158	0.7ms	0.7	166.3K	194.4K	
10.50.15.5:11211	89.3%	71.8%	1472	0.8ms	0.0	37.3K	59.2K	
10.50.15.5:11212	89.4%	69.3%	1143	0.7ms	0.7	44.9K	35.4K	
10.50.15.5:11213	89.3%	84.5%	1371	0.7ms	0.7	49.0K	187.2K	
10.50.9.90:11211	30.2%	76.3%	259	0.7ms	0.0	243	999	
10.50.9.90:11212	19.2%	60.3%	261	0.7ms	0.0	40	801	
10.50.9.90:11213	17.5%	16.9%	235	0.6ms	0.0	70	600	

AVERAGE:		72.4%	67.3%	998	0.8ms	0.3	32.2K	70.2K	

TOTAL:			23.4GB		11.7K	9.2ms	4.0	386.4K	842.3K	
(ctrl-c to quit.)
```

Here's v0.6 with commands enabled (--commands):

```
memcache-top v0.6	(default port: 11211, color: on, refresh: 3 seconds)

INSTANCE		USAGE	HIT %	CONN	TIME	EVICT/s GETS/s	SETS/s	READ/s	WRITE/s	
10.50.11.5:11211	88.9%	69.7%	1661	0.9ms	0.3	47	9	13.9K	9.8K	
10.50.11.5:11212	88.8%	69.9%	2121	0.7ms	1.3	168	10	17.6K	68.9K	
10.50.11.5:11213	88.9%	69.4%	1527	0.7ms	1.7	48	16	14.4K	13.6K	
10.50.12.5:11211	89.4%	81.9%	1406	1.6ms	1.0	26	11	7800	4059	
10.50.12.5:11212	89.5%	69.5%	2066	1.8ms	0.7	149	8	8892	153.8K	
10.50.12.5:11213	89.4%	69.4%	1430	1.4ms	2.0	25	12	6564	6386	
10.50.15.5:11211	89.5%	71.9%	2359	0.8ms	1.3	46	11	13.4K	18.5K	
10.50.15.5:11212	89.5%	69.3%	1298	0.8ms	1.0	24	5	6976	9140	
10.50.15.5:11213	89.4%	85.0%	1412	0.9ms	2.3	30	15	13.6K	26.4K	
10.50.9.90:11211	88.1%	68.3%	1471	0.7ms	3.7	39	14	22.5K	16.0K	
10.50.9.90:11212	64.4%	91.2%	2321	0.7ms	0.0	191	11	28.4K	16.5K	
10.50.9.90:11213	61.0%	58.7%	1380	0.7ms	0.0	32	12	9707	21.1K	

AVERAGE:		84.7%	72.9%	1704	1.0ms	1.3	69	11	13.5K	30.3K	

TOTAL:		19.9GB/	23.4GB		20.0K	11.7ms	15.3	826	132	162.6K	363.6K	
(ctrl-c to quit.)
```


From the comments, here are the command-line flags you can use:

```
#   If Getopt::Long is installed:
#     - Specify instances w/ --instances (multiple times or comma separated)
#     - Specify default port w/ --port (defaults to 11211)
#     - Specify sleep time w/ --sleep (default 3)
#     - Specify color output w/ --color (default) or --nocolor
#     - Specify lifetime stats w/ --lifetime or --nolifetime (default)
#       NOTE: lifetime stats break thresholds for evictions, bytes.
#     - Specify read and write bytes w/ --bytes (default) or --nobytes
#     - Specify get and set commands w/ --commands or --nocommands (default)
#     - Specify cumulative numbers w/ --cumulative (don't use with lifetime)
```


Here's an example of lifetime stats (--lifetime):

```
memcache-top v0.5	(default port: 11211, color: on, refresh: 3 seconds)

INSTANCE		USAGE	HIT %	CONN	TIME	EVICT	READ	WRITE
10.50.11.5:11211	88.8%	69.6%	1127	0.9ms	4.5M	1.2T	1.4T	
10.50.11.5:11212	88.7%	69.6%	1178	1.3ms	4.4M	515.9G	634.1G	
10.50.11.5:11213	88.8%	69.4%	1157	0.8ms	4.4M	461.5G	578.6G	
10.50.12.5:11211	89.3%	81.5%	1448	0.7ms	6.2M	398.3G	909.5G	
10.50.12.5:11212	89.4%	69.3%	1183	0.8ms	6.2M	251.7G	407.1G	
10.50.12.5:11213	89.3%	69.4%	1161	0.6ms	6.2M	739.1G	897.9G	
10.50.15.5:11211	89.3%	71.8%	1460	1.1ms	6.2M	825.3G	1.0T	
10.50.15.5:11212	89.4%	69.3%	1148	0.8ms	6.2M	452.6G	594.6G	
10.50.15.5:11213	89.3%	84.5%	1358	0.7ms	6.2M	1.0T	1.3T	
10.50.9.90:11211	30.2%	76.3%	259	0.8ms	0.0	0.7G	0.7G	
10.50.9.90:11212	19.2%	60.3%	262	0.7ms	0.0	0.4G	0.2G	
10.50.9.90:11213	17.5%	16.9%	235	0.8ms	0.0	0.4G	0.2G	

AVERAGE:		72.4%	67.3%	998	0.8ms	4.2M	484.3G	644.3G	

TOTAL:			23.4GB		11.7K	10.0ms	50.5M	5.7T	7.6T	
(ctrl-c to quit.)
```

Lifetime stats are pretty boring - unless the instances are relatively new, or doing a lot of traffic, they aren't going to update all that much with each run.  But hey, if you were curious, there you go.


### DISCLAIMER ###
This is not the best code in the world.  I make no warranties, promises, or guarantees that it will do anything, correct or incorrect.  It may very well kill you and your close relatives.  You've been warned.