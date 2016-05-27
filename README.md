
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
version of Maven (3.2.3 instead of 3.3.3).

## Hiding Repositories in a Profile

The `boot` project also uses Maven 3.3.3 and it works fine as long as
the environment is clean (no `local-settings.xml` no wrapper
properties etc.). Spring Boot 1.3.5.RELEASE has a profile called
"default" in `spring-boot-parent` which defines repositories, and it
isn't exposing them to the `app` project. But using a profile that is
active by default is a bad idea because it is de-activated by any
other explicitly activated profile. There's a trick you can play to
use a profile, but not have it accidentally deactivated: activate it
only if a property is not false:

```
<profile>
	<id>spring</id>
	<activation>
		<property>
			<name>!skip-spring</name>
		</property>
	</activation>
...
```

The user can opt out by calling `./mvnw install -Dskip-spring=false`,
and otherwise it is active in the build of this project, but not
projects that depend on this one.
