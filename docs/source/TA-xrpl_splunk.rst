TA-xrpl_splunk
==============

.. _overview:
.. _installation:

Overview
--------

This app contains knowledge objects required to ingest XRPL transaction data. The primary sourcetype for XRPL is called AccountTx_transactions.

This sourcetype does the following:
#. Ingest logs with line separators
#. Parses the timestamp from transaction data at index time (i.e. before the data is written to disk) using a transform. This step is important as XRPL uses Ripple Epoch time https://xrpl.org/basic-data-types.html

Installation
------------

Options:
#. Install latest build xrplModularInput.sql file from the latest_builds/ folder
#. Clone the repo (optionally edit) and copy to $SPLUNK_HOME/etc/apps/ folder


Configurations
--------------

props.conf

```
[AccountTx_transactions]
#INDEXED_EXTRACTION = JSON
NO_BINARY_CHECK = true
SHOULD_LINEMERGE = false
pulldown_type = 1
TRANSFORMS = xrpl_epoch_time,replace_time
```

This configuration directs each event with this sourcetype to two transforms xrpl_epoch_time and replace_time


transforms.conf

```
[xrpl_epoch_time]
REGEX = \"date\":\s(?<xrpl_epoch_time>\d+)
FORMAT = xrpl_epoch_time::$1
WRITE_META = true

[replace_time]
INGEST_EVAL = _time=xrpl_epoch_time+946684800
```

xrpl_epoch_time - this writes a field xrpl_epoch_time to the event metadata

replace_time - this converts the xrpl_epoch_time to UNIX epoch time and sets the Splunk search time configuration to this value
