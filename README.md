This is an experimental RabbitMQ plugin to provide SNMP statistics.

DOES NOT BUILD

based off of: http://www.rabbitmq.com/plugin-development.html

1. Setup your rabbitmq build environment

   	sudo apt-get install mercurial libxslt1-dev libjudy-dev

   	# Ubuntu 11.04 no erlang package yet
	cd /usr/include
	sudo ln -s /usr/local/lib/erlang/usr/include/* .
	cd -

	hg clone http://hg.rabbitmq.com/rabbitmq-public-umbrella
	make co
	make release  # the website is wrong the umbrella readme is right
	git clone # snmp_plugin
	mv rabbitmq_snmp_plugin rabbitmq-snmp-plugin

	# Switch to the rabbitmq_v1_7_0 tag for rabbitmq-server(a dependency for rabbit_snmp)

	cd rabbitmq-server
	hg checkout -r rabbitmq_v1_7_0

	# Build the rabbitmq_snmp_plugin

	cd ../rabbitmq-snmp-plugin
	make

	# Fails with:
	# make: *** No rule to make target `ebin/rabbitmq_snmp_plugin.app.in', needed by `ebin/rabbitmq_snmp_plugin.app'.  Stop.


5: Install the dist/rabbitmq-snmp.ez plugin into your server.

6: Add the following to you RabbitMQ configuration (usually /etc/rabbitmq/rabbitmq.config):

	  {rabbit_snmp,
	   [
	    % set the desired data update interval in milliseconds
	    {update_interval, 10000}
	   ]
	  },
	  {snmp, [
	    {agent, [
	      % db_dir will be set by the snmp plugin before it starts snmp so leave this commented out
	      %{db_dir, ""},
	      {versions, [v1, v2]},
	      % dir will be added by the snmp plugin before it starts snmp, so don't add it to the config block below
	      {config, [{verbosity, silence}]},
	      {net_if, [{verbosity, silence}]}
	    ]},
	    {verbosity, silence}
	  ]}

Test instructions.

1: snmp walk
	  $ snmpwalk -v 1 -c public 127.0.0.1:4001 1.3.6.1.4

Notes:
  The oids are named off the vhost/name combo so you can add/remove them without worrying about changing their indexes.


Contact:
Brendan Doyle <brendan.doyle@epicadvertising.com>