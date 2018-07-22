
http://192.168.0.215:9200/ | 
http://localhost:9100/?base_uri=http://192.168.0.215:9200/

h2. A web front end for an Elasticsearch cluster

h3. http://mobz.github.io/elasticsearch-head

h2. Running

There are multiple ways of running elasticsearch-head.

h4. Running with built in server

* @git clone git://github.com/mobz/elasticsearch-head.git@
* @cd elasticsearch-head@
* @npm install@
* @npm run start@

* @open@ http://localhost:9100/

This will start a local webserver running on port 9100 serving elasticsearch-head

h4. Running with docker

* for Elasticsearch 5.x: @docker run -p 9100:9100 mobz/elasticsearch-head:5@
* for Elasticsearch 2.x: @docker run -p 9100:9100  mobz/elasticsearch-head:2@
* for Elasticsearch 1.x: @docker run -p 9100:9100  mobz/elasticsearch-head:1@
* for fans of alpine there is @mobz/elasticsearch-head:5-alpine@
  
* @open@ http://localhost:9100/

h4. Running as a Chrome extension

* Install "ElasticSearch Head":https://chrome.google.com/webstore/detail/elasticsearch-head/ffmkiejjmecolpfloofpjologoblkegm/ from the Chrome Web Store.
* Click the extension icon in the toolbar of your web browser.

h4. Running as a plugin of Elasticsearch (deprecated)

* for Elasticsearch 5.x: site plugins are not supported. Run "as a standalone server":#running-with-built-in-server
* for Elasticsearch 2.x: @sudo elasticsearch/bin/plugin install mobz/elasticsearch-head@
* for Elasticsearch 1.x: @sudo elasticsearch/bin/plugin -install mobz/elasticsearch-head/1.x@
* for Elasticsearch 0.x: @sudo elasticsearch/bin/plugin -install mobz/elasticsearch-head/0.9@

* @open http://localhost:9200/_plugin/head/@

This will automatically download the appropriate version of elasticsearch-head from github and run it as a plugin within the elasticsearch cluster. In this mode elasticsearch-head automatically connects to the node that is running it

h4. Running with the local proxy

This is an _experimental feature_ which creates a local proxy for many remote elasticsearch clusters

* configure clusters in proxy/clusters
* create a file per remote cluster you want to connect to ( see @localhost9200.json@ as example )
* requires node >= 6.0 
* @npm install@
* @npm run proxy@

At the moment it only works with @grunt server@ running on http://localhost:9100

h4. Alternatives

* File System: elastisearch-head is a standalone webapp written in good-ol' html5. This means, you can put it up on any webserver, run it directly from the filesystem. It'll even fit on a floppy disk.
* DEB package: There is an unofficial deb package. the plugin executable will be available at @/usr/share/elasticsearch/bin/plugin@.
* Homebrew: There is an unofficial keg. The plugin executable will be available at @/usr/local/Cellar/elasticsearch/(elasticsearch version)/libexec/bin/plugin@.

h3. Connecting to elasticsearch

By default elasticsearch exposes a http rest API on port 9200 which elasticsearch-head connects to.

h4. Enable CORS in elasticsearch

When not running as a plugin of elasticsearch (which is not even possible from version 5) you must enable 
"CORS": http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/modules-http.html in elasticsearch otherwise your browser will rejects requests which appear insecure.

In elasticsearch configuration;

* add  
```
http.cors.enabled: true  
http.cors.allow-origin: "*"  
```
* you must also set @http.cors.allow-origin@ because no origin allowed by default. @http.cors.allow-origin: "*"@ is valid value, however it's considered as a security risk as your cluster is open to cross origin from *anywhere*.

```
$ cat elasticsearch.yml | grep -v "#"

cluster.name: elasticsearch

network.host: 0.0.0.0
http.port: 9200
transport.tcp.port: 9300
transport.host: localhost

http.cors.enabled: true
http.cors.allow-origin: "*"
```

h4. Basic Authentication

elasticsearch-head will add basic auth headers to each request if you pass in the "correct url parameters":#url-parameters
You will also need to add @http.cors.allow-headers: Authorization@ to the elasticsearch configuration

h4. x-pack

elasticsearch x-pack requires basic authentication _and_ CORS as described above. 
Make sure you have the correct CORS setup and then open es-head with a url like "http://localhost:9100/?auth_user=elastic&auth_password=changeme"

h4. URL Parameters

Parameters may be appended to the url to set an initial state eg. @head/index.html?base_uri=http://node-01.example.com:9200@

* @base_uri@ force elasticsearch-head to connect to a particular node.
* @dashboard@ experimental feature to open elasticsearch-head in a mode suitable for dashboard / radiator. Accepts one parameter @dashboard=cluster@
* @auth_user@ adds basic auth credentials to http requests ( requires "elasticsearch-http-basic":https://github.com/karussell/elasticsearch-http-basic plugin or a reverse proxy )
* @auth_password@ basic auth password as above (note: without "additional security layers":http://security.stackexchange.com/questions/988/is-basic-auth-secure-if-done-over-https, passwords are sent over the network *in the clear* )
* @lang@ force elasticsearch-head to use specified ui language (eg: en, fr, pt, zh, tr, ja)

h4. Contributing

To contribute to elasticsearch-head you will need the following developer tools

# git and a "github":https://github.com/ account
# "node ( including npm )":http://nodejs.org/download
# "grunt-cli":http://gruntjs.com/getting-started
# (to run jasmine tests) "phantomjs":http://phantomjs.org

Then

# create a fork of elasticsearch-head on github
# clone your fork to your machine
# @cd elasticsearch-head@
# @npm install@ # downloads node dev dependencies
# @grunt dev@ # builds the distribution files, then watches the src directory for changes (if you have an warning like "Warning: Task "clean" failed. Use --force to continue.", well use --force ;) )

Changes to both _site and src directories *must* be committed, to allow people to 
run elasticsearch-head without running dev tools and follow existing dev patterns, 
such as indenting with tabs.

!http://mobz.github.com/elasticsearch-head/screenshots/clusterOverview.png(ClusterOverview Screenshot)!
