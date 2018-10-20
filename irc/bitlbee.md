# BitlBee
This guide will desribe how I have setup my bitlbee.

## Docker 
I have used my Docker image [here](https://github.com/eyJhb/docker-images/tree/master/bitlbee), which should get you up and running with 

- Facebook
- Slack
- Discord

## Initial commands

Good readme regarding contact lists [here](https://wiki.bitlbee.org/ManagingContactList).
Facebook setup guide [here](https://wiki.bitlbee.org/HowtoFacebookMQTT).
Slack setup guide [here](https://blog.nobugware.com/post/2018/bitlbee_slack_hangouts_facebook_irc_gateway/), slack legacy token [here](https://api.slack.com/custom-integrations/legacy-tokens).

```
register <password>
set nick_lowercase true

# facebook stuff
account add facebook <email> <password>
account facebook on
# facebook settings
account facebook set group_chat_open all
account facebook set show_unread true
account facebook set mark_read_reply
account facebook set mark_read_reply true
account facebook set mark_read 
# custom channel for facebook users
channel add &fb
channel set &fb set auto_join true
channel set &fb set account facebook 
channel set &fb set fill_by account
channel set &fb set show_users online+,special%,away,offline

# slack
account add slack <email> <email>@<network>.slack.com
account slack set api_token <legacy-token>
account slack on
chat add slack backend #uni-slack-backend
```


