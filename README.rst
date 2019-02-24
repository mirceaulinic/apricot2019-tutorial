Event-driven Network Automation and Orchestration: APRICOT 2019 tutorial
========================================================================

Docker and Docker compose files to easily startup with Salt for Event-driven
Network Automation and Orchestration.

The files in this repository are used in my `APRICOT 2019 tutorial
tutorial <https://2019.apricot.net/program/schedule/#/day/10/event-driven-network-automation-and-orchestration>`_,
*Event-driven Network Automation and Orchestration*.

Please follow the installation notes for `Docker 
<https://docs.docker.com/install/>`_ and `Docker Compose 
<https://docs.docker.com/compose/install/>`_ to be able to reproduce the steps 
below.

Usage (briefly)
---------------

You can use the steps below to validate your environment, and ensure
that the Proxy Minion is up and usable:

1. Clone this repo:

   .. code-block:: bash

     git clone https://github.com/mirceaulinic/apricot2019-tutorial.git

2. Change dir:

   .. code-block:: bash

     cd apricot2019-tutorial

3. Edit the file ``pillar/device1_pillar.sls`` with the correct credentials of
   your device you want to manage.

   Ensure your device sends the syslog messages to the host where napalm-logs 
   is running, over port ``17171`` (as configured in the ``napalm-logs.conf`` 
   file).
   Configuration example for Junos: ``set system syslog host 10.10.10.1 port 
   17171 any any``. For more detailed notes for other platforms, check the 
   `Supported devices and configuration 
   <https://napalm-logs.com/en/latest/device_config/index.html>`_ section in 
   the napalm-logs documentation.

4. Start using it:

   .. code-block:: bash

     make PROXYID=device1

Example output:

.. code-block:: bash

  $ make PROXYID=device1
  docker-compose up -d
  Creating salt-proxy-device1 ... done
  Creating napalm-logs        ... done
  Creating salt-master        ... done

Check that the ``device1`` Proxy Minion is up:

.. code-block:: bash

  $ docker exec -it salt-master bash
  root@salt-master:/# salt device1 test.ping
  device1:
      True

If the device does not return, you might want to start the containers in debug
mode (i.e., execute ``$ make PROXYID=device1 LOG_LEVEL=debug``), and check why
the Minion does not come up (most frequently the device is unreachable, e.g.,
blocked by a firewall, incorrect credentials etc.): ``$ docker logs -f salt-proxy-device1``.

Run a test State:

.. code-block:: bash

  $ docker exec -it salt-master bash
  root@salt-master:/# salt device1 state.apply test
  device1:
  ----------
            ID: Yay it works
      Function: test.succeed_without_changes
        Result: True
       Comment: Success!
       Started: 11:57:21.295204
      Duration: 0.76 ms
       Changes:

  Summary for device1
  ------------
  Succeeded: 1
  Failed:    0
  ------------
  Total states run:     1
  Total run time:   0.760 ms
  root@salt-master:/# %
