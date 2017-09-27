# Firehose-to-syslog

This nifty util aggregates all the events from the firehose feature in
CloudFoundry.

	./firehose-to-syslog \
              --api-address="https://api.10.244.0.34.xip.io" \
              --skip-ssl-validation \
              --debug
	....
	....
	{"cf_app_id":"c5cb762b-b7bb-44b6-97d1-2b612d4baba9","cf_app_name":"lattice","cf_org_id":"fb5777e6-e234-4832-8844-773114b505b0","cf_org_name":"GWENN","cf_origin":"firehose","cf_space_id":"3c910823-22e7-41ff-98de-094759594398","cf_space_name":"GWENN-SPACE","event_type":"LogMessage","level":"info","message_type":"OUT","msg":"Lattice-app. Says Hello. on index: 0","origin":"rep","source_instance":"0","source_type":"APP","time":"2015-06-12T11:46:11+09:00","timestamp":1434077171244715915}

# Options

```
usage: firehose-to-syslog --api-endpoint=API-ENDPOINT [<flags>]

Flags:
  --help              Show help (also see --help-long and --help-man).
  --debug             Enable debug mode. This disables forwarding to syslog
  --api-endpoint=API-ENDPOINT  
                      Api endpoint address. For bosh-lite installation of CF: https://api.10.244.0.34.xip.io
  --doppler-endpoint=DOPPLER-ENDPOINT  
                      Overwrite default doppler endpoint return by /v2/info
  --syslog-server=SYSLOG-SERVER  
                      Syslog server.
  --subscription-id="firehose"  
                      Id for the subscription.
  --user="admin"      Admin user.
  --password="admin"  Admin password.
  --skip-ssl-validation  
                      Please don't
  --events="LogMessage"  
                      Comma seperated list of events you would like. Valid options are LogMessage, ValueMetric, CounterEvent, Error, ContainerMetric, Heartbeat, HttpStart,
                      HttpStop, HttpStartStop
  --boltdb-path="my.db"  
                      Bolt Database path
  --cc-pull-time=60s  CloudController Pooling time in sec
  --version           Show application version.
```

# Endpoint definition

We use [gocf-client](https://github.com/cloudfoundry-community/go-cfclient) which will call the CF endpoint /v2/info to get Auth., doppler endpoint.

But for doppler endpoint you can overwrite it with ``` --doppler-address ``` as we know some people use different endpoint.

# Event documentation

See the [dropsonde protocol documentation](https://github.com/cloudfoundry/dropsonde-protocol/tree/master/events) for details on what data is sent as part of each event.

# Caching
We use [boltdb](https://github.com/boltdb/bolt) for caching application name, org and space name.

We have 3 caching strategies:
* Pull all application data on start.
* Pull application data if not cached yet.
* Pull all application data every "cc-pull-time".

# To test and build


    # Setup repo
    go get github.com/cloudfoundry-community/firehose-to-syslog
    cd $GOPATH/src/github.com/cloudfoundry-community/firehose-to-syslog

    # Test
	ginkgo -r .

    # Build binary
    godep go build

# Deploy with Bosh

[logsearch-for-cloudfoundry](https://github.com/logsearch/logsearch-for-cloudfoundry)

# Run agains a bosh-lite CF deployment

    godep go run main.go \
		--debug \
		--skip-ssl-validation \
		--api-address="https://api.10.244.0.34.xip.io"

# Parsing the logs with Logstash

[logsearch-for-cloudfoundry](https://github.com/logsearch/logsearch-for-cloudfoundry)

# Devel

This is a
[Git Flow](http://nvie.com/posts/a-successful-git-branching-model/)
project. Please fork and branch your features from develop.

# Contributors

* [Ed King](https://github.com/teddyking) - Added support to skip ssl
validation.
* [Mark Alston](https://github.com/malston) - Added support for more
  events and general code cleaup.
* [Etourneau Gwenn](https://github.com/shinji62) - Added validation of
  selected events and general code cleanup, caching system..
