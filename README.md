<!-- markdownlint-disable MD041 -->
<p align="center"><a href="https://github.com/fredrikhgrelland/vagrant-hashistack-template" alt="Built on"><img src="https://img.shields.io/badge/Built%20from%20template-Vagrant--hashistack--template-blue?style=for-the-badge&logo=github"/></a><p align="center"><a href="https://github.com/fredrikhgrelland/vagrant-hashistack" alt="Built on"><img src="https://img.shields.io/badge/Powered%20by%20-Vagrant--hashistack-orange?style=for-the-badge&logo=vagrant"/></a></p></p>

# Terraform-nomad-postgres
This module is IaC - infrastructure as code which contains a nomad job of [postgres](https://www.postgresql.org/).

## Usage
```text
make test
```
The command will run a standalone instance of postgres found in the [example]() folder.

## Requirements
### Required software
- [GNU make](https://man7.org/linux/man-pages/man1/make.1.html)

### Providers
- [Nomad](https://registry.terraform.io/providers/hashicorp/nomad/latest/docs)

## Inputs
| Name | Description | Type | Default | Required |
|------|-------------|------|---------|:--------:|
| nomad\_datacenters | Nomad data centers | list(string) | ["dc1"] | yes |
| nomad\_namespace | [Enterprise] Nomad namespace | string | "default" | yes |
| service\_name | Postgres service name | string | "postgres" | yes |
| container\_port | Postgres port | number | 5432 | yes |
| container\_image | Postgres docker image | string | "postgres:12-alpine" | yes |
| admin\_user | Postgres admin username | string or data obj | data.vault_generic_secret.postgres_secrets.data.username | yes |
| admin\_password | Postgres admin password | string or data obj | data.vault_generic_secret.postgres_secrets.data.password | yes |
| admin\_password | Postgres database name | string | "metastore" | yes |
| container\_environment\_variables | Postgres container environement variables | list(string) | ["PGDATA=/var/lib/postgresql/data"] | yes |

## Outputs
| Name | Description | Type |
|------|-------------|------|
| service\_name | Postgres service name | string |
| username | Postgres username | string |
| password | Postgres password | string |
| database\_name | Postgres database name | string |
| port | Postgres port | number | 

## Example usage
The example-code shows the minimum of what you need to use this module. 

```hcl-terraform
module "postgres" {
  source = "github.com/fredrikhgrelland/terraform-nomad-postgres.git?ref=0.0.2"

  # nomad
  nomad_datacenters = ["dc1"]
  nomad_namespace   = "default"

  # postgres
  postgres_service_name                    = "postgres"
  postgres_container_image                 = "postgres:12-alpine"
  postgres_container_port                  = 5432
  postgres_admin_user                      = "postgres"
  postgres_admin_password                  = "postgres"
  postgres_database                        = "metastore"
  postgres_container_environment_variables = ["PGDATA=/var/lib/postgresql/data"]
}
```

### Verifying setup
You can verify that postgres is running by checking the connection. This can be done using the `consul` binary to set up a proxy. Check out the [required software](#required-software) section.
```shell script
make proxy-to-postgres
```

## Vault secrets
The postegres username and password is generated and put in `/secret/postgres` inside Vault.

To get the username and password from Vault you can login to the [Vault-UI](http://localhost:8200/) with token `master` and reveal the username and password in `/secret/postgres`.
Alternatively, you can ssh into the vagrant box with `vagrant ssh`, and use the vault binary to get the username and password. See the following commands:
```sh
# get username
vault kv get -field='username' secret/postgres

# get password
vault kv get -field='password' secret/postgres
```

## License
This work is licensed under Apache 2 License. See [LICENSE](./LICENSE) for full details.