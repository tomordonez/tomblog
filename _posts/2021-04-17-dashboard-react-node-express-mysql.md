---
layout: post
title: "Building a Dashboard with React, Node, Express and MySQL"
description: "Building a dashboard with React, Node, Express and MySQL."
author: tom
image: assets/images/1.jpg
tags: [analytics, mysql, react, node, express]
published: false
---

Building a dashboard with React, Node, Express and MySQL.

## MySQL Setup

Make sure that MySQL (MariaDB) is installed. [Install MySQL on Linux with MariaDB](../install-mysql-linux-mariadb/).

Check that `MariaDB` is running:

	$ sudo systemctl status mariadb

Or start the process:

	 $ sudo systemctl start mariadb

Create the database. Go to `MySQL`:

	$ sudo mysql -u root -p
	mysql> create database Dashboard;

Create a user such as this:

    mysql> create user 'homer'@'localhost' identified by 'simpson';

Grant access:

    mysql> grant all on Dashboard.* to 'homer'@'localhost';
	mysql> exit

## Postman Setup

You can use Postman to send http requests to test the app.

Install Postman depending on your environment. On Linux, download the install file.

	$ cd Downloads/
	$ tar -xzf Postman-linux-x64-8.2.1.tar.gz
	$ sudo mv Postman /opt/
	$ sudo ln -s /opt/Postman/Postman /usr/local/bin/postman
	$ sudo vim /usr/share/applications/postman.desktop

Enter the following and save `:wq`:

	[Desktop Entry]
	Type=Application
	Name=Postman
	Icon=/opt/Postman/app/resources/app/assets/icon.png
	Exec="/opt/Postman/Postman"
	Comment=Postman Desktop App
	Categories=Development;Code;

Find the Postman app on your desktop environment. You need to have an account created to login.

Instead of Postman, you can use `curl` to send http requests. Run the localhost server in one shell and use curl in another shell to send http requests to the server. More about this later, but here is an example:

	$ curl http://localhost:8080/test/Los%20Angeles


## NodeJS setup

Follow the official docs for your environment. On Fedora:

	$ sudo dnf install nodejs

Check versions. Here are my versions on Apr 17, 2021.

	$ node -v
	v14.16.0

	$ npm -v
	7.10.0

## Fixing npm install permissions

You might encounter access permission errors when running `npm install`.

Follow this guideline as seen on Stackoverflow [here](https://stackoverflow.com/a/40905762)

	$ mkdir ~/.npm-global
	$ npm config set prefix '~/.npm-global'

Update `~/.bash_profile`:

	export PATH=~/.npm-global/bin:$PATH

Run:

	$ source ~/.bash_profile


## Auto reload node server with nodemon

The node server is loaded like this:

	$ node server.js

However, when you change files, you have to stop and start the server, which can get boring after a while.

Install `nodemon` to auto reload the node server:

	$ npm install nodemon -g

Then run node server like this:

	$ nodemon server.js


## Create a node.js app

Following many details of [this](https://bezkoder.com/node-js-express-sequelize-mysql/) tutorial.

Create a folder and initialize node:

	$ mkdir dashboard
	$ cd dashboard
	$ npm init

<details>
	<summary>Output for npm init</summary>
	<pre>

	$ npm init

	This utility will walk you through creating a package.json file.
	It only covers the most common items, and tries to guess sensible defaults.

	See `npm help init` for definitive documentation on these fields
	and exactly what they do.

	Use `npm install <pkg>` afterwards to install a package and
	save it as a dependency in the package.json file.

	Press ^C at any time to quit.
	package name: (dashboard) 
	version: (1.0.0) 
	description: Dashboard with React Node Express MySQL
	entry point: (index.js) server.js
	test command: 
	git repository: 
	keywords: react, node, express, mysql
	author: tom
	license: (ISC) 
	About to write to /home/tom/Downloads/dashboard/package.json:

	{
	  "name": "dashboard",
	  "version": "1.0.0",
	  "description": "Dashboard with React Node Express MySQL",
	  "main": "server.js",
	  "scripts": {
	    "test": "echo \"Error: no test specified\" && exit 1"
	  },
	  "keywords": [
	    "react",
	    "node",
	    "express",
	    "mysql"
	  ],
	  "author": "tom",
	  "license": "ISC"
	}


	Is this OK? (yes) yes
	</pre>

</details>

A new file is created:

	$ ls
	package.json

Install these modules. For more detail on these modules, see `Sources` at the end of this document:

* express
* sequelize
* mysql2
* body-parser
* cors

Run this:

	$ npm install express sequelize mysql2 body-parser cors --save


<details>
	<summary>Output when installing these modules</summary>
	<pre>

	npm notice created a lockfile as package-lock.json. You should commit this file.
	npm WARN dashboard@1.0.0 No repository field.

	+ express@4.17.1
	+ mysql2@2.2.5
	+ body-parser@1.19.0
	+ sequelize@6.6.2
	+ cors@2.8.5
	added 83 packages from 130 contributors and audited 84 packages in 8.033s
	found 0 vulnerabilities
	</pre>

</details>

These were the versions installed:

	express@4.17.1
	mysql2@2.2.5
	body-parser@1.19.0
	sequelize@6.6.2
	cors@2.8.5

List files in current directory:

	$ ls
	node_modules  package.json  package-lock.json

The file `package.json` was updated.

<details>
	<summary>Updated package.json</summary>
	<pre>


	$ cat package.json 
	{
	  "name": "dashboard",
	  "version": "1.0.0",
	  "description": "Dashboard with React Node Express MySQL",
	  "main": "server.js",
	  "scripts": {
	    "test": "echo \"Error: no test specified\" && exit 1"
	  },
	  "keywords": [
	    "react",
	    "node",
	    "express",
	    "mysql"
	  ],
	  "author": "tom",
	  "license": "ISC",
	  "dependencies": {
	    "body-parser": "^1.19.0",
	    "cors": "^2.8.5",
	    "express": "^4.17.1",
	    "mysql2": "^2.2.5",
	    "sequelize": "^6.6.2"
	  }
	}
	</pre>

</details>

Files were added to `node_modules`.

<details>
	<summary>node_modules contents</summary>
	<pre>

	$ ls node_modules/
	@types
	accepts
	any-promise
	array-flatten
	body-parser
	bytes
	content-disposition
	content-type
	cookie
	cookie-signature
	cors
	debug
	denque
	depd
	destroy
	dottie
	ee-first
	encodeurl
	escape-html
	etag
	express
	finalhandler
	forwarded
	fresh
	generate-function
	http-errors
	iconv-lite
	inflection
	inherits
	ipaddr.js
	is-property
	lodash
	long
	lru-cache
	media-typer
	merge-descriptors
	methods
	mime
	mime-db
	mime-types
	moment
	moment-timezone
	ms
	mysql2
	named-placeholders
	negotiator
	object-assign
	on-finished
	parseurl
	path-to-regexp
	proxy-addr
	pseudomap
	qs
	range-parser
	raw-body
	retry-as-promised
	safe-buffer
	safer-buffer
	semver
	send
	seq-queue
	sequelize
	sequelize-pool
	serve-static
	setprototypeof
	sqlstring
	statuses
	toidentifier
	toposort-class
	type-is
	unpipe
	utils-merge
	uuid
	validator
	vary
	wkx
	yallist
	</pre>

</details>

## Setup the project structure

The root directory must contain this `app` folder with sub-folders:

	app
		config
		controllers
		models
		routes
	node_modules
	package-lock.json
	package.json
	server.js

## Express Setup

Following the same details from [this](https://bezkoder.com/node-js-express-sequelize-mysql/) tutorial.

Create a `server.js` file in the project root folder:

	$ touch server.js

Add this code:

	const express = require("express");
	const bodyParser = require("body-parser");
	const cors = require("cors");

	const app = express();

	let corsOptions = {
		origin: "http://localhost:8081"
	};

	app.use(cors(corsOptions));
	app.use(bodyParser.json());
	app.use(bodyParser.urlencoded({ extended: true}));

	// test the server with this route
	app.get("/", (req, res) => {
		res.json({ message: "Express works"});
		});

	const PORT = process.env.PORT || 8080;
	app.listen(PORT, () => {
		console.log(`Server running on port ${PORT}`);
		});

Run the app:

	$ nodemon server.js
	Server running on port 8080

Open `localhost:8080`. Which shows:

	{"message":"Express works"}


## MySQL and Sequelize Setup

Following similar example from [this](https://bezkoder.com/node-js-express-sequelize-mysql/) tutorial.

Inside `app/config/` create the file `db.config.js`

	$ touch app/config/db.config.js

This configuration has to match your MySQL setup. As described previously we set up MySQL like this:

	$ sudo mysql -u root -p
	mysql> create database Dashboard;
	mysql> create user 'homer'@'localhost' identified by 'simpson';
    mysql> grant all on Dashboard.* to 'homer'@'localhost';
	mysql> exit;


### Configure MySQL connection

Add this code to `app/config/db.config.js`:

	module.exports = {
		HOST: "localhost",
		USER: "homer",
		PASSWORD: "simpson",
		DB: "Dashboard",
		dialect: "mysql",
		pool: {
			max: 5,
			min: 0,
			acquire: 30000,
			idle: 10000
		}
	};


### Configure Sequelize to map models to the database

Create the file `app/models/index.js`

	$ touch app/models/index.js

Add this code to `app/models/index.js`:

	const dbConfig = require("../config/db.config.js");

	const Sequelize = require("sequelize");
	const sequelize = new Sequelize(
		dbConfig.DB,
		dbConfig.USER,
		dbConfig.PASSWORD,
		{
			host: dbConfig.HOST,
			dialect: dbConfig.dialect,
			operatorsAliases: false,
			pool: {
				max: dbConfig.pool.max,
				min: dbConfig.pool.min,
				acquire: dbConfig.pool.acquire,
				idle: dbConfig.pool.idle
			}
		});

	const db = {};

	db.Sequelize = Sequelize;
	db.sequelize = sequelize;

	module.exports = db;


### Add the sequelize module to the server

Add this code to `server.js`:

	const db = require("./app/models");

Test if the connection works. Add this to `server.js`:

	const dbAuthenticate = async() => {
	    try {
	        await db.sequelize.authenticate();
	        console.log("MySQL authenticated");
	    } catch (error) {
	        console.error("MySQL did not connect", error);
	    }
	}

Initialize the authentication function. We will use the `initialize()` to add other async functions later:

	async function initialize() {
	    const jobs = [dbAuthenticate];
	    for (const job of jobs) {
	        await job()
	    }
	}

	initialize();


### DeprecationWarning: A boolean value was passed to options.operatorsAliases

Run the app:

	$ nodemon server.js

The console shows:

	(node:2570420) [SEQUELIZE0004] DeprecationWarning: A boolean value was passed 
		to options.operatorsAliases. This is a no-op with v5 and should be removed.
		(Use `node --trace-deprecation ...` to show where the warning was created)
	Server running on port 8080
	Executing (default): SELECT 1+1 AS result
	Executing (default): SELECT 1+1 AS result
	Connection works

This error can be corrected by updating `app/models/index.js` and changing `false` to `0` here:

	operatorsAliases: false,

Change to:

	operatorsAliases: 0,


### Testing MySQL and Sequelize setup

Run the app:

	$ nodemon server.js 
	Server running on port 8080
	Executing (default): SELECT 1+1 AS result
	Executing (default): SELECT 1+1 AS result
	MySQL authenticated

This runs `SELECT 1+1 AS result` to check the database connection.


### MySQL test the user is connected to the database

Go to MySQL and test that the user is connected to the database. In our setup, the user `homer` should be connected to the database `Dashboard`:

	$ sudo mysql -uroot -p
	MariaDB> use Dashboard;
	MariaDB [Dashboard]> show processlist;

Output shows:

	+----+-------------+-----------------+-----------+---------+-----
	| Id | User        | Host            | db        | Command | Time
	+----+-------------+-----------------+----------------+---------+
	|  1 | system user |                 | NULL      | Daemon  | NULL
	| 95 | root        | localhost       | Dashboard | Query   |    0
	| 97 | homer       | localhost:36752 | Dashboard | Sleep   |    4
	| 98 | homer       | localhost:36754 | Dashboard | Sleep   |    4
	+----+-------------+-----------------+----------------+---------+


## Synchronize the application models to MySQL database

Follow these steps:

* A schema has to be created in the database
* Create an async function using sequelize


### Create the database schema

As shown in [installing MySQL on Linux with MariaDB](../install-mysql-linux-mariadb/). You can import a `schema` directly into MySQL like this.

Create a database `db1`:

	$ sudo mysql -uroot -p
	mysql> create database db1;
	mysql> exit;

Import the schema:

    $ sudo mysql -uroot -p db1 < schema.sql

If the schema creates the user (example user `homer`) and grants them access. Then you can import a dataset into the database like this:

    $ mysql -u homer -p db1 < data.sql

Let's say the schema has these tables:

	CREATE TABLE City(
		City_Name varchar(20) NOT NULL,
		State_Name varchar(20) NOT NULL,
		Population int(15) NOT NULL,
		primary key (City_Name,State_Name)
	);

	CREATE TABLE State(
		State_Name varchar(20) NOT NULL,
		primary key (State_Name)
	);


### Create an async function with Sequelize

Create this function:

	// Sync the current db tables
	const dbSync = async() => {
	    try {
	        await db.sequelize.sync();
	        console.log("All models synchronized");
	    } catch (error) {
	        console.error("Models did not synchronize", error);
	    }
	}

Then modify the `initialize()` function:

	async function initialize() {
	    const jobs = [dbAuthenticate, dbSync];
	    for (const job of jobs) {
	        await job()
	    }
	}

	initialize();

Run the app:

	$ nodemon server.js 
	Server running on port 8080
	Executing (default): SELECT 1+1 AS result
	Executing (default): SELECT 1+1 AS result
	MySQL authenticated
	All models synchronized

## Test a SQL query from the application

We have to use raw queries as seen on the Sequelize doc [here](https://sequelize.org/v5/manual/raw-queries.html).

Add this function to `server.js`:

	// test a query to the database
	const querySelect = async() => {
	    try {
	        const query = "select count(*) from `State`";
	        const result = await db.sequelize
	            .query(query, {type: db.sequelize.QueryTypes.SELECT});
	        console.log(result);
	    } catch (error) {
	        console.error("Query did not work", error);
	    }
	}

Then modify the `initialize()` function:

	async function initialize() {
	    const jobs = [dbAuthenticate, dbSync, querySelect];
	    for (const job of jobs) {
	        await job()
	    }
	}

	initialize();

Run the app:

	$ nodemon server.js 
	Server running on port 8080
	Executing (default): SELECT 1+1 AS result
	Executing (default): SELECT 1+1 AS result
	MySQL authenticated
	All models synchronized
	Executing (default): select count(*) from `State`
	[ { 'count(*)': 50 } ]


## Test a SQL query with named parameters

You can use `replacements` to use named parameters in the query such as user input from the interface.

For example, adding this function to `server.js`:

	// test a query to the database with named parameter
	const queryNamedParameter = async() => {
	    try {
	        const query = "select * from City where City_Name= :user_input";
	        const result = await db.sequelize
	            .query(query,
	            {
	                replacements: { user_input: "Miami"},
	                type: db.sequelize.QueryTypes.SELECT
	            }
	            );
	        console.log(result);
	    } catch (error) {
	        console.error("Query did not work", error);
	    }
	}

Then modify the `initialize()` function:

	async function initialize() {
	    const jobs = [dbAuthenticate, dbSync, querySelect, queryNamedParameter];
	    for (const job of jobs) {
	        await job()
	    }
	}

	initialize();

Run the app:

	$ nodemon server.js 
	Server running on port 8080
	Executing (default): SELECT 1+1 AS result
	Executing (default): SELECT 1+1 AS result
	MySQL authenticated
	All models synchronized
	Executing (default): select count(*) from `State`
	[ { 'count(*)': 50 } ]
	Executing (default): select * from City where City_Name= 'Miami'
	[
		{ City_Name: 'Miami', State_Name: 'MT', Population: 2867463 },
		{ City_Name: 'Miami', State_Name: 'NY', Population: 893856 },
		{ City_Name: 'Miami', State_Name: 'WA', Population: 10058645 }
	]


## Other Query Types in Sequelize

Other `QueryTypes` as shown in the Sequelize source code [here](https://github.com/sequelize/sequelize/blob/main/lib/query-types.js)

	const QueryTypes = module.exports = {
	  SELECT: 'SELECT',
	  INSERT: 'INSERT',
	  UPDATE: 'UPDATE',
	  BULKUPDATE: 'BULKUPDATE',
	  BULKDELETE: 'BULKDELETE',
	  DELETE: 'DELETE',
	  UPSERT: 'UPSERT',
	  VERSION: 'VERSION',
	  SHOWTABLES: 'SHOWTABLES',
	  SHOWINDEXES: 'SHOWINDEXES',
	  DESCRIBE: 'DESCRIBE',
	  RAW: 'RAW',
	  FOREIGNKEYS: 'FOREIGNKEYS',
	  SHOWCONSTRAINTS: 'SHOWCONSTRAINTS'
	};


## Test using Postman

In Postman, send a GET request, like this example:

* Select `GET` and enter the URL `http://localhost:8080/test/Los%20Angeles`
* Select the `body` tag and select `raw` and `JSON` (drop down)
* Enter a JSON for the body: `{ "username": "homer", "password": "secret" }`
* Click `Send`
* Test the output in Postman and the output in the node server.


## Test using the terminal

Run the node server in one shell and send the http request in another shell.

Node server example and output:

	$ nodemon server.js

	Server running on port 8080
	Executing (default): SELECT 1+1 AS result
	MySQL authenticated

Http request example and output:

	$ curl http://localhost:8080/test/Los%20Angeles

	[{"City_Name":"Los Angeles","State_Name":"California","Population":39669360}]


## Long SQL queries

If you have long SQL queries using Sequelize raw queries, these can be assigned to a variable, concatenated as strings, and called by the query function.

	const long_query = "select long statement here and concatenate with plus sign" +
					   "concatenate with this other aweseome statement" +
					   "and then another one and so on"

As shown before, this query is added to the async function like this:

	const querySelect = async() => {
	    try {
	        const query = "select count(*) from `State`";
	        const result = await db.sequelize
	            .query(query, {type: db.sequelize.QueryTypes.SELECT});
	        console.log(result);
	    } catch (error) {
	        console.error("Query did not work", error);
	    }
	}


## Escaping strings within the SQL string when using LIKE

When using the `LIKE` keyword such as `LIKE "San%"`. The string needs to be inside quotes, but the SQL string is already within quotes. You can concatenate it like this:

	const escaping_string_query = "select * from City where City_Name like " +
								  `"San%"`

Use double quotes for the first string and use back tick quotes for the second string.



**REVIEWING SECTIONS BELOW**

## Create the controller (GET request/SELECT query)

We can move the above SQL query with named parameter example into a controller.

Create the file `app/controllers/test.controller.js`:

	$ touch app/controllers/test.controller.js

Add this code to `test.controller.js`:

	const db = require("../models");
	db.sequelize.sync();

	let city_name_parameter = "Los Angeles";
	let test_query = "select * from `City` where `City_Name` = :named_parameter"

	exports.findCity = (req, res) => {
		db.sequelize.query(test_query,
			{ replacements: {named_parameter: city_name_parameter}, type: db.sequelize.QueryTypes.SELECT})
			.then(city => {
				console.log("Testing a SQL query from a controller");
				console.log(city);
				res.send(city);
			})
			.catch(err => {
				res.status(500).send({
					message: err.message || "Error getting city"
				});
			});
	};


## Create the route (GET request/SELECT query)

Create the file `app/routes/test.routes.js`:

	$ touch app/routes/test.routes.js

Add this code to `test.routes.js`:

	module.exports = app => {
		const cities = require("../controllers/test.controller.js");

		let router = require("express").Router();
		router.get("/", cities.findCity);
		app.use('/api/cities', router);
	}

## Import route to server.js

Inside `server.js` add this code before `app.listen(PORT,...)`:

	require("./app/routes/test.routes")(app);

## Test using Postman

Run the app:

	$ nodemon server.js

Go to Postman. Choose `GET`. Enter this URL:

	http://localhost:8080/api/cities

Go to Headers tab and enter a new k,v pair:

	Content-type: application/json

Click `Send` and review JSON output

	[
	    {
	        "City_Name": "Los Angeles",
	        "State_Name": "California",
	        "Population": 3967000
	    }
	]

Review the output on nodejs console:

	Server running on port 8080
	Executing (default): SELECT 1+1 AS result
	Executing (default): SELECT 1+1 AS result
	Executing (default): SELECT 1+1 AS result
	Connection to MySQL works
	Executing (default): select * from `City` where `City_Name` = 'Los Angeles'
	Testing a SQL query from a controller
	[
	  {
	    City_Name: 'Los Angeles',
	    State_Name: 'California',
	    Population: 3967000
	  }
	]

## Route parameters

Instead of testing a named parameter like this:

	let city_name_parameter = "Los Angeles";
	let test_query = "select * from `City` where `City_Name` = :named_parameter"

I want to test getting this parameter from a URL like:

	http://localhost:8080/Los%20Angeles

The express routing doc [here](https://expressjs.com/en/guide/routing.html) shows that we can capture route parameters using `req.params` like this:

	Route path: /:id
	Request URL: http://localhost:8080/Los%20Angeles
	req.params: {"id": "Los Angeles"}

Where `:id` can be a string or a number, and you can have many parameters like this:

	Route path: /users/:userId/books/:bookId
	Request URL: http://localhost:8080/users/1/books/10
	req.params: {"userId": "1", "bookId": "10"}

Then the route can be defined like this:

	app.get('/users/:userId/books/:bookId', (req, res) => {
		res.send(req.params);
		});

### req.params vs req.query

Using `req.params`:

	Route path: /:id
	Request URL: /Los%20Angeles
	req.params.id
	=> 'Los Angeles'

Using `req.query`:

	Request URL: /search?q=Los+Angeles
	req.query.q
	=> 'Los Angeles'

	Request URL: /search?q=Los+Angeles&state=California
	req.query.q.state
	=> 'California'


### Update code with req.params

Update `router.get` in `test.routes.js`:

	module.exports = app => {
		const cities = require("../controllers/test.controller.js");

		let router = require("express").Router();
		router.get("/:id", cities.findCity);
		app.use('/api/cities', router);
	}

Update `test.controller.js`:

	const db = require("../models");
	db.sequelize.sync();

	// before: let city_name_parameter = "Los Angeles";
	let test_query = "select * from `City` where `City_Name` = :named_parameter"

	exports.findCity = (req, res) => {
		let city_name_parameter = req.params.id; //after

		db.sequelize.query(test_query,
			{ replacements: {named_parameter: city_name_parameter}, type: db.sequelize.QueryTypes.SELECT})
			.then(city => {
				console.log("Testing a SQL query from a controller");
				console.log(city);
				res.send(city);
			})
			.catch(err => {
				res.status(500).send({
					message: err.message || "Error getting city"
				});
			});
	};

Run the app:

	$ nodemon server.js

Go to Postman. Choose `GET`. Enter this URL:

	http://localhost:8080/api/cities/Los%20Angeles

Click `Send` and review JSON output

	[
	    {
	        "City_Name": "Los Angeles",
	        "State_Name": "California",
	        "Population": 3967000
	    }
	]


## Route to your home root

This is what we have inside `server.js`:

	const express = require("express");
	const bodyParser = require("body-parser");
	const cors = require("cors");

	const app = express();

	let corsOptions = {
	    origin: "http://localhost:8081"
	}

	app.use(cors(corsOptions));
	app.use(bodyParser.json());
	app.use(bodyParser.urlencoded({ extended: true}));

	const db = require("./app/models");
	db.sequelize.sync();

	db.sequelize
		.authenticate()
		.then(() => {
			console.log("Connection to MySQL works");
		})
		.catch(err => {
			console.error("Cannot connect to MySQL:", err);
		});

	// test the server with this route
	app.get("/", (req, res) => {
		res.json({ message: "Express works"});
	});

	// test controllers and routes
	require("./app/routes/test.routes")(app);


And this is inside `test.routes.js`:

	module.exports = app => {
		const cities = require("../controllers/test.controller.js");

		let router = require("express").Router();
		router.get("/:id", cities.findCity);
		app.use('/api/cities', router);
	}

Running this `http://localhost:8080` in Postman returns:

	{ message: "Express works"}

And running this `http://localhost:8080/api/cities/Los%20Angeles` returns:

	[
	    {
	        "City_Name": "Los Angeles",
	        "State_Name": "California",
	        "Population": 3967000
	    }
	]

I want to use the home root to route the cities request so that using `http://localhost:8080` returns the city data.

This confused me for a while because updating the router like this wouldn't work:

	app.use('/', router);

What I realized was that this path was already being used in `server.js` here:

	app.get("/", (req, res) => {
			res.json({ message: "Express works"});
		});

Then it ran this request from `server.js` ignoring the request in `test.route.js`.

The fix was as simple as commenting or removing the request to the home path in `server.js`. Now the files look like this.

Updated `server.js`:

	const express = require("express");
	const bodyParser = require("body-parser");
	const cors = require("cors");

	const app = express();

	let corsOptions = {
	    origin: "http://localhost:8081"
	}

	app.use(cors(corsOptions));
	app.use(bodyParser.json());
	app.use(bodyParser.urlencoded({ extended: true}));

	const db = require("./app/models");
	db.sequelize.sync();

	db.sequelize
		.authenticate()
		.then(() => {
			console.log("Connection to MySQL works");
		})
		.catch(err => {
			console.error("Cannot connect to MySQL:", err);
		});

	// test controllers and routes
	require("./app/routes/test.routes")(app);


Updated `test.routes.js`:

	module.exports = app => {
		const cities = require("../controllers/test.controller.js");

		let router = require("express").Router();
		router.get("/:id", cities.findCity);
		app.use('/', router);
	}

Then test with `http://localhost:8080/Los%20Angeles` to get:

	[
	    {
	        "City_Name": "Los Angeles",
	        "State_Name": "California",
	        "Population": 3967000
	    }
	]


## req.send vs req.write vs req.end

This gave me a hard time when using many callback functions. The Mozilla docs [here](https://developer.mozilla.org/en-US/docs/Learn/Server-side/Express_Nodejs/routes) have a good explanation of router functions.

Mozilla says that Router functions are Express middleware. They must complete/respond to the request or call the next function in the chain.

In this example:

	router.get('/about', (req, res) => {
		res.send('About Mozilla');
	})

This callback function uses `res.send` which is composed of `res.write` and `res.end`. You can also do this:

	router.get('/about', (req, res) => {
		res.write('About Mozilla');
		res.end();
	})

The function `res.send` takes an object, while `res.write` takes a string. Which means that you cannot use `res.write(Obj)` or it will throw an error saying that write needs a string.

Convert the object to string like this:

	router.get('/about', (req, res) => {
		something_here(with some parameters).
			.then(data => {
				res.write(JSON.stringify(data));
				res.end();
			});
	})

You can also send the output to both the console and the response body of the http request like this:

	router.get('/about', (req, res) => {
		something_here(with some parameters).
			.then(data => {
				console.log(data);
				res.write(JSON.stringify(data));
				res.end();
			});
	})

## One router with many callback functions (GET request/SELECT query)

This is our `test.routes.js`:

	module.exports = app => {
		const cities = require("../controllers/test.controller.js");

		let router = require("express").Router();
		router.get("/:id", cities.findCity);
		app.use('/', router);
	}

I want to update `router.get` to take `cities.findCity` and more callback functions.

This is what `test.controller.js` has:

	const db = require("../models");
	db.sequelize.sync();

	// before: let city_name_parameter = "Los Angeles";
	let test_query = "select * from `City` where `City_Name` = :named_parameter"

	exports.findCity = (req, res) => {
		const city_name_parameter = req.params.id; //after

		db.sequelize.query(test_query,
			{ replacements: {named_parameter: city_name_parameter}, type: db.sequelize.QueryTypes.SELECT})
			.then(city => {
				console.log("Testing a SQL query from a controller");
				console.log(city);
				res.send(city);
			})
			.catch(err => {
				res.status(500).send({
					message: err.message || "Error getting city"
				});
			});
	};

Update `test.controller.js` and create a new callback function:

	const db = require("../models");
	db.sequelize.sync();

	exports.findCity = (req, res, next) => {
		const city_name_parameter = req.params.id;
		let query = "select * from `City` where `City_Name` = :named_parameter;";

		db.sequelize.query(query,
			{ replacements: {named_parameter: city_name_parameter}, type: db.sequelize.QueryTypes.SELECT})
			.then(data => {
				console.log("Get City data");
				console.log(data);
				res.write(JSON.stringify(data));
				next();
			})
			.catch(err => {
				res.status(500).send({
					message: err.message || "Error getting data"
				});
			});
	};

	exports.cityCovidVaccine = (req, res) => {
		const city_name_parameter = req.params.id;
		let query = "select `Vaccinated_Percent` from `CovidVaccine` where `City_Name` = :named_parameter;";

		db.sequelize.query(query,
			{ replacements: {named_parameter: city_name_parameter}, type: db.sequelize.QueryTypes.SELECT})
			.then(data => {
				console.log("Get Vaccine data");
				console.log(data);
				res.write(JSON.stringify(data));
				res.end();
			})
			.catch(err => {
				res.status(500).send({
					message: err.message || "Error getting data"
				});
			});
	};

Now update `test.routes.js`:

	module.exports = app => {
		const cities = require("../controllers/test.controller.js");

		let router = require("express").Router();
		router.get("/:id", cities.findCity, cities.cityCovidVaccine);
		app.use('/', router);
	}

Run the app and see the output in console and Postman.

Console output:

	Server running on port 8080
	Executing (default): SELECT 1+1 AS result
	Executing (default): SELECT 1+1 AS result
	Executing (default): SELECT 1+1 AS result
	Connection to MySQL works
	Executing (default): select * from `City` where `City_Name` = 'Los Angeles'
	Get City data
	[
	  {
	    City_Name: 'Los Angeles',
	    State_Name: 'California',
	    Population: 3967000
	  }
	]
	Executing (default): select `Vaccinated_Percent` from `CovidVaccine` where `City_Name` = 'Los Angeles'
	Get Vaccine data
	[
	  {
	    Vaccinated_Percent: 20
	  }
	]

Postman output:

	[{ City_Name: 'Los Angeles', State_Name: 'California', Population: 3967000 }][{ Vaccinated_Percent: 20 }]

## Route and Controller for POST request/INSERT query

Following the sample code from [this](https://bezkoder.com/node-js-express-sequelize-mysql/) tutorial. We can adapt it to our previous route and controller.

Let's say we want to add an interface to add/view vaccine locations. When you enable the interface by default it shows a list of all vaccine locations. If you want to add a location it enables a date field and a text field to enter the name of the location.

For example. View locations can be:

	Malibu
	Long Beach
	Santa Monica

Adding locations can show:

	Date (dropdown or calendar)
	Location (text field)

Let's call these files: `vaccine-location.route.js` and `vaccine-location.controller.js`.

Update `server.js` and add:

	require("./app/routes/vaccine-location.route")(app);

Update `vaccine-location.route.js`:

	module.exports = app => {
		const vaccineLocation = require("../controllers/vaccine-location.controller.js");

		let router = require("express").Router();

		router.post("/", vaccineLocation.viewDate, vaccineLocation.addLocation);
		router.get("/", vaccineLocation.viewLocation);

		app.use('/vaccine', router);
	}

The callback functions `viewDate` and `viewLocation` for this controller are similar to the other one. I will omit this code but add the functions to see where they need to be called in sequence.


### body-parser and req.body

The `addLocation` callback function needs the user's input from the interface and it uses `body-parser` (Express [docs](http://expressjs.com/en/resources/middleware/body-parser.html) on body-parser) specified in `server.js` as:

	const bodyParser = require("body-parser");

It parses the body from the request using `req.body`. The document says that the content of the request body is based on user input, all values in this object are not trusted and must be validated.

For example:

	req.body.foo.toString()

This needs to be validated since `foo` might not exist (maybe the user didn't enter anything) or `foo` may be something else where you cannot apply `toString()`

The API says that the middleware populates `req.body` when any of these happen:

* The `Content-Type` request header matches the `type` option.
* When the object was empty and there was no body to parse.
* When the `Content-Type` was not matched.
* When there was an error.

Set `body-parser` before defining routes to make sure the route can access the parsed http GET body.

	const bodyParser = require('body-parser');

	app.use(bodyParser.urlencoded({ extended: true }));

	app.get("/", (req, res) => {
		console.log(req.body);
		res.json({ message: "Express works"});
	});

Using `extended: true` allows objects and arrays to be encoded.

You can also use JSON like this:

	app.use(bodyParser.json());
	app.use(bodyParser.urlencoded({ extended: true}));

If you send an HTTP POST request with JSON as the body, it will be parsed into a JSON object with `req.body`.


## addLocation callback function

**pending: update addLocation**

Update `vaccine-location.controller.js`:

	const db = require("../models");
	db.sequelize.sync();


	exports.viewDate = (req, res, next) => {
		
		awesome code here;
		next();
	};

	exports.addLocation = (req, res, next) => {
		
		let query = "INSERT INTO Location (Location_Name, Date) VALUES(@Location_Name, @Date);";

		db.sequelize.query(query, { type: db.sequelize.QueryTypes.INSERT})
			.then(data => {
				console.log("addLocation");
				console.log(data);
				res.write(JSON.stringify(data));
	            next();
			})
			.catch(err => {
				res.status(500).send({
					message: err.message || "Error getting addLocation"
				});
			});
	};

	exports.viewLocation = (req, res) => {
		
		awesome code here;
		res.end();
	};

Sources:

* [Build a web app with Node Part1](https://bezkoder.com/react-node-express-mysql/)
* [Build a web app with Node Part2](https://bezkoder.com/node-js-express-sequelize-mysql/)
* [Install Postman on Linux](https://www.tecmint.com/install-postman-on-linux-desktop/)
* [Setting up Postman](https://www.codecademy.com/articles/setting-up-postman)
* [What is body-parser](https://stackoverflow.com/questions/38306569/what-does-body-parser-do-with-express)
* [What are res and req in Express](https://stackoverflow.com/questions/4696283/what-are-res-and-req-parameters-in-express-functions)
* [What is CORS](https://stackabuse.com/handling-cors-with-node-js/)
* [Getting started with Sequelize](https://sequelize.org/v5/manual/getting-started.html)
* [DeprecationWarning: A boolean value was passed to options.operatorsAliases](https://stackoverflow.com/questions/58593200/deprecationwarning-a-boolean-value-was-passed-to-options-operatorsaliases-this)
* [MySQL list of connected users](https://www.tutorialkart.com/mysql/mysql-server-get-list-of-connected-users/)
* [Sequelize raw queries](https://sequelize.org/v5/manual/raw-queries.html)
* [Sequelize raw queries, query types](https://github.com/sequelize/sequelize/blob/main/lib/query-types.js)
* [MySQL creating tables](https://dev.mysql.com/doc/refman/8.0/en/creating-tables.html)
* [exports and module exports in nodejs](https://www.sitepoint.com/understanding-module-exports-exports-node-js/)
* [express routing](https://expressjs.com/en/guide/routing.html)
* [Parameters in expressjs](https://stackabuse.com/get-query-strings-and-parameters-in-express-js/)
* [Express request req properties](https://expressjs.com/en/4x/api.html#req)
* [Express req.param vs req.query](https://stackoverflow.com/a/49535358)
* [NodeJS naming conventions](https://stackoverflow.com/questions/18927298/node-js-project-naming-conventions-for-files-folders/44882336)
* [Write Middleware for Express.js](https://stormpath.com/blog/how-to-write-middleware-for-express-apps)
* [ExpressJS routes on Mozilla](https://developer.mozilla.org/en-US/docs/Learn/Server-side/Express_Nodejs/routes)
* [Difference between res.send and res.write](https://stackoverflow.com/a/50427881)
* [res.write convert object to string](https://stackoverflow.com/a/52223785)
* [Express body-parser and req.body docs](http://expressjs.com/en/resources/middleware/body-parser.html)
* [NodeJS events](https://nodejs.org/api/events.html#events_events)
* [NodeJS stream](https://nodejs.org/api/stream.html#stream_stream)
* [Anatomy of an HTTP transaction](https://nodejs.org/en/docs/guides/anatomy-of-an-http-transaction/)
* [JS let vs let vs const](https://www.freecodecamp.org/news/var-let-and-const-whats-the-difference/)
* [post body in expressjs](https://stackabuse.com/get-http-post-body-in-express-js/)
* [POST request in Postman](https://www.toolsqa.com/postman/post-request-in-postman/)
* [Reload node server when a file changes](https://stackoverflow.com/a/14406029)
* [nodemon to auto restart node server](https://github.com/remy/nodemon)
* [Fixing npm install permissions](https://stackoverflow.com/a/40905762)
* [Async/await functions in sequence](https://www.coreycleary.me/executing-arrays-of-async-await-javascript-functions-in-series-vs-concurrently/)

[![Ask me anything on Linkedin]({{ site.baseurl }}/assets/images/ama-linkedin-tomordonez.png)](https://www.linkedin.com/in/tomordonez/)

<details>
	<summary>Code to add Output</summary>
	<pre>


	</pre>

</details>