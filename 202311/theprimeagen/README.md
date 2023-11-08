
```sh
lsof -ti :42069 | xargs kill -9
kill $(lsof -t -i:port)
```

