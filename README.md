vault-in-a-box
==============

Provides a simple Docker environment for tinkering with Vault with Consul as the
storage backend.

Requirements
------------

-   [direnv](https://github.com/direnv/direnv)

-   [docker](https://www.docker.com/get-docker) 

Quick Start
-----------

Allow `direnv` to get the environment right

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
$ direnv allow
direnv: loading .envrc
direnv: using hashicorp consul 1.1.0
direnv: using hashicorp vault 0.10.1
direnv: export +LOCAL_IP +PROJECT_DIR +VAULT_ADDR ~PATH
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Bring the environemnt up with `docker-compose`

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
$ docker-compose up -d
Creating vault  ... done
Creating consul ... done
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Initialize Vault with only 1 key. We're just tinkering here! A production environment should *minimally* utilize the default key share to threshold ratio of 5:3.

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
$ vault operator init -key-shares=1 -key-threshold=1`
Unseal Key 1: /6CIyLXyZYJ1jIvfeWFL5CD/pKmuCuFaETelW86adPU=

Initial Root Token: d7cb154b-c54b-dc63-d955-29ef7b79ad00

Vault initialized with 1 key shares and a key threshold of 1. Please securely
distribute the key shares printed above. When the Vault is re-sealed,
restarted, or stopped, you must supply at least 1 of these keys to unseal it
before it can start servicing requests.

Vault does not store the generated master key. Without at least 1 key to
reconstruct the master key, Vault will remain permanently sealed!

It is possible to generate new unseal keys, provided you have a quorum of
existing unseal keys shares. See "vault rekey" for more information.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Unseal vault with the provided key

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
$ vault operator unseal /6CIyLXyZYJ1jIvfeWFL5CD/pKmuCuFaETelW86adPU=
Key                    Value
---                    -----
Seal Type              shamir
Sealed                 false
Total Shares           1
Threshold              1
Version                0.10.1
Cluster Name           vault-cluster-e1e5103e
Cluster ID             0b09acc2-677e-f2e1-ac1f-7fc2cd6031a2
HA Enabled             true
HA Cluster             n/a
HA Mode                standby
Active Node Address    <none>
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Export the provided root token so we are able to operate our Vault

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
$ export VAULT_TOKEN=d7cb154b-c54b-dc63-d955-29ef7b79ad000
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Check the status of the Vault

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
$ vault status
Key             Value
---             -----
Seal Type       shamir
Sealed          false
Total Shares    1
Threshold       1
Version         0.10.1
Cluster Name    vault-cluster-e1e5103e
Cluster ID      0b09acc2-677e-f2e1-ac1f-7fc2cd6031a2
HA Enabled      true
HA Cluster      https://10.10.0.10:444
HA Mode         active
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

List the default secrets

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
$ vault secrets list
Path          Type         Description
----          ----         -----------
cubbyhole/    cubbyhole    per-token private secret storage
identity/     identity     identity store
secret/       kv           key/value secret storage
sys/          system       system endpoints used for control, policy and debugging
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
