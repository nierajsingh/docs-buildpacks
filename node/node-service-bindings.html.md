---
title: Configure Service Connections for Node.js
---
_This page assumes that you are using cf v6._

This guide is for developers who wish to bind a data source to a Node.js
application deployed and running on Cloud Foundry.

## <a id='creds'></a>Parse VCAP_SERVICES for Credentials  ##

You must parse the VCAP_SERVICES environment variable in your code to get the
required connection details such as host address, port, user name, and
password.

For example, if you are using PostgreSQL, your VCAP_SERVICES environment
variable might look something like this:

~~~json
{
	"mypostgres": [{
		"name": "myinstance",
		"credentials": {
			"uri": "postgres://myusername:mypassword@host.example.com:5432/serviceinstance"
		}
	}]
}
~~~

This example JSON is simplified; yours may contain additional properties.

### <a id='cfenv'></a>Parse with cfenv ###

The `cfenv` package provides access to Cloud Foundry application environment
settings by parsing all the relevant environment.
The settings are returned as JavaScript objects.
`cfenv` provides reasonable defaults when running locally, as well as when
running as a Cloud Foundry application.

* https://www.npmjs.org/package/cfenv

### <a id='parse-manually'></a>Manual Parsing ###

First, parse the VCAP_SERVICES environment variable.

For example:

~~~
var vcap_services = JSON.parse(process.env.VCAP_SERVICES)
~~~

Then pull out the credential information required to connect to your service.
Each service packages requires different information.
If you are working with Postgres, for example, you will need a `uri` to
connect.
You can assign the value of the `uri` to a variable as follows:

~~~
var uri = vcap_services.mypostgres[0].credentials.uri
~~~

Once assigned, you can use your credentials as you would normally in your
program to connect to your database.

## <a id='Connecting'></a> Connecting to a Service ##

You must include the appropriate package for the type of services your
application uses. For example:

* Rabbit MQ via the [ampq](https://github.com/postwait/node-amqp) module
* Mongo via the [mongodb](http://mongodb.github.com/node-mongodb-native/) and
[mongoose](http://mongoosejs.com/) modules
* MySQL via the [mysql](https://github.com/felixge/node-mysql) module
* Postgres via the [pg](https://github.com/brianc/node-postgres) module
* Redis via the [redis](https://github.com/mranney/node_redis) module

## <a id='add'></a> Add the Dependency to package.json ##

Edit `package.json` and add the intended module to the `dependencies` section.
Normally, only one would be necessary, but for the sake of the example we will
add all of them:

~~~json
{
  "name": "hello-node",
  "version": "0.0.1",
  "dependencies": {
    "express": "*",
    "mongodb": "*",
    "mongoose": "*",
    "mysql": "*",
    "pg": "*",
    "redis": "*",
    "ampq": "*"
  },
  "engines": {
    "node": "0.8.x"
  }
}
~~~

You must run `npm shrinkwrap` to regenerate your `npm-shrinkwrap.json` file
after you edit `package.json`.