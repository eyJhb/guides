# Flexget
## Basic setup
Basic setup of flexget, to get everything up and running.
Remember if you use Docker, you can use the docker name specified in the compose file, instead of a IP.

```
tasks:
  taska:
    rss: http://showrss.karmorra.info/rss.php?user_id=USERID&hd=2&proper=0
    all_series: yes
    transmission:
      host: localhost
      port: 9091
      username: USERNAME
      password: PASSWORD
      path: /home/xbian/TV/{{series_name}}/Season {{series_season}}
      addpaused: no
```

Source [here](https://flexget.com/Cookbook/Series/SeriesTransmissionshowRSS).
