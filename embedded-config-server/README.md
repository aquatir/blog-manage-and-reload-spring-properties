The following shows an example configuration of an app with embedded config server, spring-cloud v 1.1.3 and spring boot 1.5.6.
This example uses file system for configuration (Default usage is with git. I prefer throwing in configurations with puppet/ansible, hence local file system usage)

<h2> Starting example guide </h2>
Create and populate config dir.

```
mkdir /tmp/props
echo foo.property=ONE > /tmp/props/myapp.properties
```

Launch an app with from IDE / maven plugin / however you like. Maven example is shown here

```
cd embedded-config-server
mvn spring-boot:run
```

Somewhere in log you should see line as following
```
Adding property source: file:/tmp/props/myapp.properties
```
Call "/" to output current value
```
curl http://localhost:8000/
ONE
```
Insert new value in config file
```
echo foo.property=TWO > /tmp/props/myapp.properties
```
Make sure that changes are not loaded yet
```
curl http://localhost:8000/
ONE
```
Call "/refresh" on your app to load new values (Remember: It's a POST method)
```
curl -X POST http://localhost:8000/refresh
["foo.property"]
```

Call "/" to see new value
```
curl http://localhost:8000/
TWO
```

Congrats!

<h2> Things to notice </h2>
Examples uses spring boot 1.5.6. It WILL NOT work with spring-boot 2.0.0+ out of the box. This is due to property  <i>management.security.enabled=false</i> being removed, most <i> actuator </i> endpoint being moved from "/" of your app to "/actuator" and /refresh endoint being disabled by default

Please, don't use <i>management.security.enabled=false</i> in you production environment UNLESS you know what you are doing (e.g. blocking illegal access on http-server layer)

