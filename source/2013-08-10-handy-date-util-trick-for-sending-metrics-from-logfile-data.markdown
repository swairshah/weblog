<!-- begin metadata
title: Handy `date` Util trick for sending metrics from logfile data
date: 2013-08-10 12:21
end metadata -->

There is useful data logged into your webserver/application logs. It may not
always be a good idea to send metrics to Graphite or Ganglia directly from your
code, if you want to seperate concerns of ops from those of the devs. You may want
to create some metrics from existing logfiles. You may want to grep for a certain IP 
, error message, response time etc and you need this data with the time stamp. 

The 'date' util provides some handy functionalities which we can use. Say our
logs are something like follows:

```
58.39.130.55 - - [9 Aug 07:21:00] "HEAD / HTTP/1.0" 200 0 "-" "-"
66.249.73.119 - - [9 Aug 07:22:10] "GET /robots.txt HTTP/1.1" 404
143 "-" "Mozilla/5.0 (compatible; Googlebot/2.1; +http://www.google.com/bot.html)"
66.249.73.119 - - [9 Aug 07:22:19] "HEAD / HTTP/1.1" 200 0 "-" \
```

If you want to send per minute hits or per min errors, you can have a bash for loop, which will grep for your required param (user agent, 200s, 404s etc) for the last 10 mins, and put it in a script to run as a 10 min cron.

```bash
for (( i = 10; i > 0; i-- ));
do
    timestamp=`date +"%d %b %H:%M" -d "-$i min"`
    epoch=`date +"%s" -d "$i min"`
    errors=`grep "$timestamp" $logfile | grep -i "xml:error" | wc -l`
done
```
The 'date' util can output the date in any format you want it to, pass the format using `+" "`.
You can pass `-x min` to get (current time-x min). You can get the epoch time (which you'll need to send data to Graphite server) with +"%s".

Set this script as a 10 min cron and you have metrics from logfiles!
