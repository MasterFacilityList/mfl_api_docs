Installing for production
============================
This server has been developed and tested on `Ubuntu`_ Linux ( any Ubuntu
that is currently "in support" will do ). It should be *trivial* to get it
working on any ``*NIX`` ( including OS X ).

Kindly note that this restriction applies to the servers only, and not to
any of the API clients e.g browsers and third party systems. Clients can
run on any modern operating system.

We supply an `Ansible`_ playbook that automates this entire process.

.. _`Ansible`: http://www.ansible.com/home
.. _`Ubuntu`: http://www.ubuntu.com/

Setting up the environment
----------------------------
This server is built as a `Twelve-Factor App`_. For that reason, the key
configuration parameters are stored in the environment - set up directly in
the operating system as environment variables or as a `.env` file in the
application's root folder.

The ``.env`` file holds confidential configuration information. For that
reason, it is not tracked in version control ( version control has an example
``.env`` whose values should **not** be used in production ).

A proper ``.env`` file should set the following values up:

.. code-block:: text

    SECRET_KEY=pleasechangetoanewlygeneratedsecretkey
    DEBUG=off  # NEVER run with Debug=True in production

    # Use real email settings here e.g from Amazon SES
    EMAIL_HOST=''
    EMAIL_HOST_USER=''
    EMAIL_HOST_PASSWORD=''


    # Here because the original user was too lazy to write ruby code for the VagrantFile
    DATABASE_USER=mfl  # Change this
    DATABASE_PASSWORD=mfl  # **CHANGE** this, no matter how lazy you feel
    DATABASE_NAME=mfl  # Change this

    # Make sure you change this in lockstep with the three DATABASE_* vars above
    DATABASE_URL='postgres://mfl:mfl@localhost:5432/mfl'


    # Location where the administration frontend is running
    FRONTEND_URL='http://localhost:8062'
    DEBUG=False
    REALTIME_INDEX=True  # ** set to true to update the search index in realtime**
    HTTPS_ENABLED=True # ** Set to true if HTTPS will be used
    AWS_ACCESS_KEY_ID=<AWS access key>
    AWS_SECRET_ACCESS_KEY=<AWS secret key>
    AWS_STORAGE_BUCKET_NAME=<AWS bucket name>
    STORAGE_BACKEND=<storage backend e.g storages.backends.s3boto.S3BotoStorage>

.. _Twelve-Factor App: http://12factor.net/

.. warning::

    Please make sure that you have set up secure values.

    You will need to save a copy of the ``.env`` at a secure location ( not in
    the code repository ). If you loose the ``.env`` / forget the values, you
    could lose the ability to maintain the deployed production system.

The pre-deploy checklist
---------------------------
You **MUST** work your way through the `Django deployment checklist`_.

.. _`Django deployment checklist`: https://docs.djangoproject.com/en/1.8/howto/deployment/checklist/

Configuring the ansible inventory
-------------------------------------
There is an ``inventory`` file in the ``playbooks`` folder. This file should
be edited to have a line for each server that is managed by Ansible.

The following is an example:

.. code-block:: text

    azure_test_server             ansible_ssh_host=mfl.slade360.co.ke     ansible_ssh_port=22     ansible_ssh_user=azureuser     ansible_ssh_private_key_file=/home/ngurenyaga/.ssh/id_rsa

The template breaks down roughly to this:

.. code-block:: text

    <a descriptive name we choose for the server>
    ansible_ssh_host=<an IP address or host name>
    ansible_ssh_port=<the port over which the SSL daemon is listening on the remote machine>
    ansible_ssh_user=<the username to log in with on the remote machine>
    ansible_ssh_private_key_file=<a path to a local SSH private key>

.. warning::

    The SSH private key must be kept private.

In the ``site.yml`` file inside the ``playbooks`` folder ensure that the relevant variables are updated e.g

.. code-block :: text

        load_demo_data: false , //set to true if demonstration data needs to be loaded
        warm_cache: false, // set this to true if the cache needs to be refreshed
        server_url: "https://testdomain.com, //THe API-server URL
        username: <Public user username>
        password: <public password>,
        client_id: <OAUTH client id for the public user >,
        client_secret:<OAUTH client secret for the public user>

Once all the deployment attributes have been set, cd into the playbooks folder and run the command below:

    ``ansible-playbook --tags='api-server' site.yml``


The above command deploys the API code, setups nginx, supervisor and the firewall. The API server should be available online, from the server URL provided during deployment, once the command has finished executing.


.. warning::

    If you are working off a recent Ubuntu Linux on your laptop, you should
    comment out ``SendEnv LANG LC_*`` in ``/etc/ssh/ssh_config``.

    The forwarding of language environment variables from the local computer
    is known to cause mischief on the remote server.

.. warning::

    This server should only be run on a non-threaded server e.g ``gunicorn``
    in the standard multi-process configuration.

    This is because the geographic features rely on ``GDAL``, which is not
    thread safe.


.. toctree::
    :maxdepth: 2
