vdsm-prometheus
===============

This role will install the the latest version of `vdsm-prometheus` from
[copr](https://copr.fedorainfracloud.org/coprs/rfenkhuber/vdsm-prometheus/),
configure systemd, the firewall and finally starts vdsm-prometheus.

When the role is applied to a host it will be possible to access metrics collected by VDSM in prometheus format at port `8181`. By default TLS and client certificates are used to secure te access to `vdsm-prometheus`. Every client certificate signed by the ovirt-engine CA will be accepted by `vdsm-prometheus`.

It is possible to customize the `vdsm-prometheus` configuration in playbooks.

Visit the [vdsm-prometheus role page on
github](https://github.com/rmohr/ansible-vdsm-prometheus) to view the sources
or to report issues regarding to the ansible role.

Visit the [vdsm-prometheus page on
github](https://github.com/rmohr/vdsm-prometheus) to view the sources or to
report issues regarding to `vdsm-prometheus`.

Requirements
------------

None.

Role Variables
--------------

There only variable available is the `opts` variable in
[vars/main.yml](vars/main.yml). The variable allows to set commandline
arguments for `vdsm-prometheus` on the host. These arguments are passed to
`vdsm-prometheus` when it is started by `systemd`. To see all possible
configuration options run `vdsm-prometheus --help`.

Dependencies
------------

None.

Example Playbook
----------------

To install `vdsm-prometheus` on the `vdsm` host group with the commandline
arguments `-no-verify` run the following playbook:

    ---
    - hosts: vdsm
      roles:
        - { role: vdsm-prometheus, opts: "-no-verify" }

An example with disable TLS and without client certificate authentication would
look like this:

    ---
    - hosts: vdsm
      roles:
        - { role: vdsm-prometheus, opts: "-no-verify -no-prom-auth" }

Run `vdsm-prometheus --help` to see all possible configuration options for
`vdsm-prometheus`.

oVirt inventory
---------------

To create an initial oVirt inventory, query the ovirt-engine REST-API:

    export ENGINE_HOST=http://localhost:8080
    export ENGINE_PWD=engine
    echo "[vdsm]" > hosts && \ 
    curl -H "Accept: application/json" -H "Content-type: application/json" -X GET --user admin@internal:$ENGINE_PWD $ENGINE_HOST/ovirt-engine/api/hosts \
    | jq '.host[].address' -r >> hosts

Using ansible on these hosts is easy. oVirt already installed an ssh key there
and because of VDSM the python interpreter is installed on the host too.

The following command runs the sample playbook with the engine ssh key:

    ansible-playbook -i hosts -u root --private-key $ENGINE_HOME/etc/pki/ovirt-engine/keys/engine_id_rsa vdsm-prometheus.yml


License
-------

GPLv3

Author Information
------------------

Roman Mohr <rmohr@redhat.com>
