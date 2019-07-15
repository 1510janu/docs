Configuration in the Mattermost Database
=========================================
A new configuration database was added in `5.10 <https://docs.mattermost.com/administration/changelog.html#configuration-in-database>`_ to allow using the database for the single source of truth of the active configuration of your Mattermost installation. This provides a convenient way to manage configuration changes from the system console and provides a failsafe in high availability deployments when a server in the node goes down.  Additionally, in high availability deployments, configuration changes are applied to all nodes instantaneously as possible removing the need to update individual configuration files on each server and the read-only state of the system console is removed.   

To change your server from reading the config.json file to using the configuration in database: 

1. Use the ``--config`` `command <https://docs.mattermost.com/administration/command-line-tools.html#mattermost>`_ to change the binary from default config.json file and location  to the config database store by specifying a supported provider uri. Alternatively, the you may specify the ``MM_CONFIG`` variable to configure the same flag. 
  
  a. The provider uri specifies the driver name and data source name used to resolve the database. For example: ``postgres://mmuser:mostest@dockerhost:5432/mattermost_test?sslmode=disable\u0026connect_timeout=10``
  b. Alternatively, you can  use the ``--migrate config`` `command <https://docs.mattermost.com/administration/command-line-tools.html#mattermost-config-migrate>`_ for installs that are already using the ``config.json`` default file. 
  
2. Set the ``--disableconfigwatch`` `flag <https://docs.mattermost.com/administration/command-line-tools.html#mattermost>`_ to true to disable automatically watching this default config.json and effecting changes.

Once you are using the configuration in the database, changes to configuration settings will create a new entry in the configuration database, storing a copy of the previous configuration as a json blob. 

Any environment variable configurations will continue to override settings from config.json, triggering them to appear read-only in the system console. When reading the configuration in the database, ClusterSettings.ReadOnlyConfig setting is automatically disabled as the database configuration ignores the config read only flag. 

.. note::
    If using SAML, ensure the SAML certificates and keys are accessible to also migrate into the database.

Troubleshooting
-----------------

Server fails to start 
~~~~~~~~~~~~~~~~~~~~~
Providing the --disableconfigwatch flag while not actually pointing at a file will fail to start the server with an appropriate error message.
