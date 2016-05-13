
> WARNING: the Maven profile name "default" is special.

## TL;DR

You know that thing about "do not put libraries in Maven Central with
repository declarations"? Well it's still good advice (here's the
original
[Sonatype Blog](http://blog.sonatype.com/2009/02/why-putting-repositories-in-your-poms-is-a-bad-idea/)). But
you can hide repository declarations inside a profile, even if it is
active by default. As long as you don't call it "default"!

## Proof

For each of the sub directories (`boot`, `broken`, `fixed`) do this:

```
$ rm -rf ~/.m2/repository/com/example/
$ mvn install
$ rm -rf /tmp/repository
$ mkdir -p /tmp/repository/com/example
$ cp -rf ~/.m2/repository/com/example/module/ /tmp/repository/com/example/
$ cd app/; mvn -s ../settings.xml clean install -U -X
```

Grep the output and see if `repo.spring.io` is pulled in, usually for
completely random dependencies (e.g. arquillian).

The `boot` and `broken` ones bind to `repo.spring.io`, and the `fixed`
one does not. The difference between `broken` and `fixed` is only the
name of the profile ("spring" instead of "default"). Spring Boot has a
profile called "default" in `spring-boot-parent`, so the obvious
conclusion is that this is the culprit, and renaming it will help.
