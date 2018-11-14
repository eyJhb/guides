# SSH tricks
## GIT on shared webhosting SSH (azehosting)
Check if there are any listed keys
```
ssh-add -l
```

If no such daemon is found, start it with 
```
eval 'ssh-agent -s'
```

reason for eval is [here](https://stackoverflow.com/questions/17846529/could-not-open-a-connection-to-your-authentication-agent/4086756#4086756). 

## References
- https://stackoverflow.com/questions/17846529/could-not-open-a-connection-to-your-authentication-agent
- https://gist.github.com/jexchan/2351996
- https://stackoverflow.com/questions/17846529/could-not-open-a-connection-to-your-authentication-agent/4086756#4086756
