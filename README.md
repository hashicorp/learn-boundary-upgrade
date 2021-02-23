# learn-boundary-upgrade

This directory contains an example deployment of Boundary using docker-compose and Terraform.

In this example, Boundary is deployed using the [hashicorp/boundary](https://hub.docker.com/r/hashicorp/boundary) Dockerhub image. The Boundary service ports are forwarded to the host machine to mimic being in a "public" network. Docker-Compose creates and initializes a Postgres database and a Boundary Controller. Boundary is then provisioned via Terraform to include several test users and some sample targets.

All of these targets are on containers that are not port forwarded to the host machine in order to mimic them residing in a private network. Boundary is configured to reach these targets via Docker DNS (domain names defined by their service name in docker-compose). Clients can reach these targets via Boundary. 

Note for this minimal setup that no workers are configured by default in the compose/boundary.hcl

## Getting Started 

There is a helper script called `run` in this directory. You can use this script to deploy, login, and cleanup.

Start the docker-compose deployment:

```bash
./run all
```

To login your Boundary CLI:

```bash
./run login
```

To stop all containers and start from scratch:

```bash
./run cleanup
```

Login to the UI:
  - Open browser to localhost:9200
  - Login Name: <any user from var.users>
  - Password: foofoofoo
  - Auth method ID: find this in the UI when selecting the auth method or from TF output

```bash
$ boundary authenticate password -login-name jeff -password foofoofoo -auth-method-id <get_from_console_or_tf>

Authentication information:
  Account ID:      apw_gAE1rrpnG2
  Auth Method ID:  ampw_Qrwp0l7UH4
  Expiration Time: Fri, 06 Nov 2020 07:17:01 PST
  Token:           at_NXiLK0izep_s14YkrMC6A4MajKyPekeqTTyqoFSg3cytC4cP8sssBRe5R8cXoerLkG7vmRYAY5q1Ksfew3JcxWSevNosoKarbkWABuBWPWZyQeUM1iEoFcz6uXLEyn1uVSKek7g9omERHrFs
```

## Connect to SSH Target

Once the deployment is live, you can connect to the containers (assuming their clients are
installed on your host system). For example, you can ping the SSH target via Boundary:

```bash
$ boundary connect ssh -target-id ttcp_jkE03ST3cJ -- -p {{boundary.port}} ping
PONG

