# Beats

## Creating Custom

[Creating New  Beat](https://www.elastic.co/guide/en/beats/devguide/current/new-beat.html)

[Examples](https://www.elastic.co/blog/build-your-own-beat)

#### Know Errors

[bash: mage: command not found](https://discuss.elastic.co/t/error-on-make-setup/147473)

#### Use vendoring

We recommend to use vendoring for your beat. This means the dependencies are put into your beat folder. The beats team currently uses [govendor](https://github.com/kardianos/govendor) for vendoring.

```sh
govendor init
govendor update +e
```

This will create a directory `vendor` inside your repository. To make sure all dependencies for the Makefile commands are loaded from the vendor directory, find the following line in your Makefile:

```sh
ES_BEATS=${GOPATH}/src/github.com/elastic/beats
```

Replace it with:

```sh
ES_BEATS=./vendor/github.com/elastic/beats
```

To Fetch: 

```sh
govendor fetch  github.com/vmware/govmomi/^ +out
```