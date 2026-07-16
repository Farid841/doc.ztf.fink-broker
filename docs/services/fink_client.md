# Fink client

The [fink-client](https://github.com/astrolabsoftware/fink-client) is a thin wrapper around low level functionalities in Apache Kafka. The idea is to make stream consuming easy within Fink without the need to develop extra piece of code. 

The fink-client is used in the context of 3 services: Livestream, Data Transfer, and Xmatch. This page explains how to install the client on a computer. 

## Installation of fink-client

`fink_client` requires a version of Python 3.9+.

### Install with pip

From a terminal, you can install fink-client simply using `pip`:

```bash
pip install fink-client --upgrade
```

### Use or develop in a controlled environment

For development, we recommend the use of a virtual environment:

```bash
git clone https://github.com/astrolabsoftware/fink-client.git
cd fink-client
python -m venv .fc_env
source .fc_env/bin/activate
pip install -r requirements.txt
pip install .
```

## Registering

In order to connect and poll alerts from Fink, you first need to get your credentials. Subscribe by filling this [form](https://forms.gle/2td4jysT4e9pkf889) (same than for the livestream service -- so you do not need to it twice). After filling the form, we will send your credentials. Register them on your laptop by simply running on a terminal:

```bash
# access help using `finkctl auth register -h`
finkctl auth register \
    -survey ztf \ # or lsst
    -username <USERNAME> \ # given privately
    -groupid <GROUP_ID> \ # given privately
    -servers kafka-ztf.fink-broker.org:24499 \
    -maxtimeout 10 # in seconds
```

where `<USERNAME>` and `<GROUP_ID>` have been sent to you privately. Note that each survey (`ztf` or `lsst`) has its own configuration file, and you should run `finkctl auth register` for each survey you use. Once registered for the Livestream service, add the topics you want to poll:

```bash
# see https://doc.ztf.fink-broker.org/en/latest/broker/filters/
finkctl topic subscribe -survey ztf -name <topic name>
```

By default, the credentials are installed in the home:

```bash
cat ~/.finkclient/ztf_credentials.yml
cat ~/.finkclient/lsst_credentials.yml
```

You can also inspect your current configuration at any time with `finkctl auth show -survey ztf`.

## Available tools

Everything is now exposed through a single command-line entry point, `finkctl`, with one subcommand per service:

- The `Livestream` service relies on `finkctl stream`
- The `Data Transfer` service relies on `finkctl transfer`
- The `Xmatch` service relies on `finkctl transfer` as well.

For each subcommand, you can access its documentation by using the `-h` option:

```bash
$ finkctl stream -h
Usage: finkctl stream [OPTIONS]

  Poll alerts from the Fink Livestream service and save or redirect alerts
  using Fink bots

  The list of available topics can be seen from `finkctl topic list`.

Options:
  -survey [ztf|lsst]     Survey name.  [required]
  -limit INTEGER         If specified, download only `limit` alerts. Default
                         is None.
  -start_at TEXT         If specified, reset offsets to 0 (`earliest`) or
                         empty queue (`latest`).
  -outdir TEXT           Folder to store incoming alerts if --save is set.
                         It must exist.
  -ext_schema TEXT       Path to Avro schema to decode the incoming alerts.
                         Default is None (version taken from each alert)
  --display_statistics   If specified, print on screen information about
                         queues, and exit.
  --display              If specified, print on screen information about
                         incoming alert.
  --save                 If specified, save alert data on disk (Avro). See
                         also -outdir.
  --telegram             If specified, redirect alerts on a Telegram
                         channel.
  --slack                If specified, redirect alerts on a Slack channel.
  --dump_schema          If specified, save the schema on disk (json file)
                         before polling.
  -h, --help             Show this message and exit.
```
