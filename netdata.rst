.. _netdata-section:

=======
Netdata
=======

This application installs and configures a `Netdata Agent`__ instance
on the chosen NethServer 8 cluster node.

__ https://learn.netdata.cloud/docs/netdata-agent/

A Netdata Agent collects and displays local system metrics in a web
user interface. Although it can be part of broad deployments, the provided
configuration is tailored for the local system only, aiming to assist in the
analysis of basic resource usage of the node (CPU, RAM, disks, network...)
and is not intended for monitoring specific applications or integration with
Netdata Cloud.

Configuration
=============

To access the Netdata web interface, navigate to the Status
page and follow the ``Application URL`` link.

The random URL path component is generated at installation time and never
changes. It can also be obtained from the cluster Settings page, under the
"HTTP routes" section, by searching for ``netdata``.

.. warning::

    The web interface has no authentication. While it does not contain any
    inherently sensitive information, be sure to keep the random URL path
    secret.

After installation, the application does not require any special configuration.

- Choose the option ``skip and use the dashboard anonymously`` at the bottom right corner of the login screen to immediately access the node metrics.
- As alternative, refer to the next section to connect Netdata Cloud.


Netdata Cloud
-------------

The Netdata instance can be connected to Netdata Cloud.

To do this, you must create a free Netdata Cloud account (or log in if you already have one).
This account allows you to securely claim your node (by pasting a unique identifier) and access its metrics remotely via https://app.netdata.cloud.

The file ``netdata_random_session_id`` contains the string used to identify your node.
To retrieve it, run the following command:

  podman exec netdata1 cat /var/lib/netdata/netdata_random_session_id


Metrics storage
===============

The Netdata Agent configuration is derived from the stock Netdata official
Docker image. It has three tiers of storage with different metrics
resolution and retention policies: second, minute, hour. 

.. csv-table:: Configured retention limits per storage tier
   :header: "Tier", "Resolution", "Retention period", "Disk retention"

    "Tier 0", "1 second", "14 days",  "1 GB"
    "Tier 1", "1 minute", "3 months", "1 GB"
    "Tier 2", "1 hour",   "2 years", "1 GB"

The stock configuration provides up to 14 days of fine-grained metrics
with one-second resolution. The minute-level tier can cover up to 3 months
of recordings, while the hour-level tier can cover up to 2 years. Actual
retention may vary depending on system activity and the specific metrics
collected. The actual disk space used depends on the data's compression
efficiency.

Data is deleted once it reaches either the time limit or the disk space
limit, whichever comes first.
