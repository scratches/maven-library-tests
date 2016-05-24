
## TL;DR

You know that thing about "do not put libraries in Maven Central with
repository declarations"? Well it's still good advice (here's the
original
[Sonatype Blog](http://blog.sonatype.com/2009/02/why-putting-repositories-in-your-poms-is-a-bad-idea/)). But
you can hide repository declarations inside a profile, even if it is
active by default. As long as you don't use older versions of Maven!

## Try It Out

For each of the sub directories (`boot`, `broken`, `fixed`) do this:

```
$ rm -rf ~/.m2/repository/com/example/
$ (cd module; ../mvnw install)
$ rm -rf /tmp/repository
$ mkdir -p /tmp/repository/com/example
$ cp -rf ~/.m2/repository/com/example/module/ /tmp/repository/com/example/
$ (cd app/; ../mvnw -s ../settings.xml clean install -U -X)
```

Grep the output and see if `repo.spring.io` is pulled in, usually for
completely random dependencies (e.g. arquillian).

The `boot` and `broken` ones bind to `repo.spring.io`, and the `fixed`
one does not. The difference between `broken` and `fixed` is only the
version of Maven (3.2.3 instead of 3.3.3). The `boot` project also
uses Maven 3.3.3, so it's a bit of a mystery still why that is broken
(but it's something in Spring Boot). Spring Boot 1.3.5.RELEASE has a
profile called "default" in `spring-boot-parent` which defines
repositories, so maybe this is the culprit.
