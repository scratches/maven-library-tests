```
$ rm -rf ~/.m2/repository/com/example/
$ mvn install
$ rm -rf /tmp/repository
$ mkdir -p /tmp/repository/com/example
$ cp -rf ~/.m2/repository/com/example/module/ /tmp/repository/com/example/
$ cd app/; mvn -s ../settings.xml clean install -U -X
```

Grep the output and see that repo.spring.io is pulled in for completely random
dependencies (e.g. arqillian).
