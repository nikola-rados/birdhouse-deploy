# Optional components

## Monitor all components in Canarie node, both public and internal url

So that the url `https://<PAVICS_FQDN>/canarie/node/service/stats` also return
what the end user really see (a component might work but is not accessible to
the end user).

This assume all the WPS services are public.  If not the case, make a copy of
this config and adjust accordingly.

How to enable this config in `env.local` (a copy from
[`env.local.example`](../env.local.example)):

* Add `./optional-components/canarie-api-full-monitoring` to `EXTRA_CONF_DIRS`.


## Database external ports

In order to grant access to databases defined by containers `postgres`, `postgres-magpie` and `mongodb`, it is possible
to extend the `docker-compose` configuration. By default, their connection ports will not be exposed externally for 
security purpose. Only other containers in the stack can connect to them using the `links` definitions. It is only 
recommended to expose ports in this manner for test instances where debugging becomes easier with direct access to 
live data in the databases. 

How to enable database external ports in `env.local` (a copy from
[`env.local.example`](../env.local.example)):

* Add `./optional-components/database-external-ports` to `EXTRA_CONF_DIRS`.

The databases will then be accessible remotely with ports defined in 
`./optional-components/database-external-ports/docker-compose-extra.yml`.
A different set of ports can also be defined in a similar fashion using a custom `docker-compose-extra.yml` and 
similarly adding its location to `EXTRA_CONF_DIRS`.


## Emu WPS service for testing

Preconfigured for Emu but can also be used to quickly deploy any birds
temporarily without changing code.  Good to preview new birds or test
alternative configuration of existing birds.

No Postgres DB configured.  If need Postgres DB, use generic_bird component
instead.

How to enable Emu in `env.local` (a copy from
[`env.local.example`](../env.local.example)):

* Add `./optional-components/emu` to `EXTRA_CONF_DIRS`.
* Optionally set `EMU_IMAGE`, `EMU_PORT`,
  `EMU_NAME`, `EMU_INTERNAL_PORT`,
  `EMU_WPS_OUTPUTS_VOL` in `env.local` for further customizations.
  Default values are in [`emu/default.env`](emu/default.env).

Emu service will be available at `http://PAVICS_FQDN:EMU_PORT/wps` or
`https://PAVICS_FQDN_PUBLIC/TWITCHER_PROTECTED_PATH/EMU_NAME` where
`PAVICS_FQDN`, `PAVICS_FQDN_PUBLIC` and `TWITCHER_PROTECTED_PATH` are defined
in your `env.local`.

Magpie will be automatically configured to give complete public anonymous
access for this Emu WPS service.

Canarie monitoring will also be automatically configured for this Emu WPS
service.


## A second Thredds server for testing

How to enable in `env.local` (a copy from
[`env.local.example`](../env.local.example)):

* Add `./optional-components/testthredds` to `EXTRA_CONF_DIRS`.

* Optionally set `TESTTHREDDS_IMAGE`, `TESTTHREDDS_PORT`,
  `TESTTHREDDS_CONTEXT_ROOT`, `TESTTHREDDS_WARFILE_NAME`,
  `TESTTHREDDS_INTERNAL_PORT`, `TESTTHREDDS_NAME`,  in `env.local` for further
  customizations.  Default values are in
  [`testthredds/default.env`](testthredds/default.env).

Test Thredds service will be available at
`http://PAVICS_FQDN:TESTTHREDDS_PORT/TESTTHREDDS_CONTEXT_ROOT` or
`https://PAVICS_FQDN_PUBLIC/TESTTHREDDS_CONTEXT_ROOT` where `PAVICS_FQDN` and
`PAVICS_FQDN_PUBLIC` are defined in your `env.local`.

Use same docker image as regular Thredds by default but can be customized.

New container have new `TestDatasets` with volume-mount to `/data/testdatasets`
on the host.  So your testing `.nc` and `.ncml` files should be added to
`/data/testdatasets` on the host for them to show up on this Test Thredds
server.

`TestWps_Output` dataset is for other WPS services to write to, similar to
`birdhouse/wps_outputs` dataset in the production Thredds.  With Emu, add
`export EMU_WPS_OUTPUTS_VOL=testwps_outputs` to `env.local` for Emu to write to
`TestWps_Output` dataset.

No Twitcher/Magpie access control, this Test Thredds is directly behind the
Nginx proxy.

Canarie monitoring will also be automatically configured for this second
Thredds server.


## A generic bird WPS service

Can be used to quickly deploy any birds temporarily without changing code.
Good to preview new birds or test alternative configuration of existing birds.

How to enable in `env.local` (a copy from
[`env.local.example`](../env.local.example)):

* Add `./optional-components/generic_bird` to `EXTRA_CONF_DIRS`.

* Optionally set `GENERIC_BIRD_IMAGE`, `GENERIC_BIRD_PORT`,
  `GENERIC_BIRD_NAME`, `GENERIC_BIRD_INTERNAL_PORT`,
  `GENERIC_BIRD_POSTGRES_IMAGE` in `env.local` for further customizations.
  Default values are in [`generic_bird/default.env`](generic_bird/default.env).

The WPS service will be available at `http://<PAVICS_FQDN>:<GENERIC_BIRD_PORT>/wps`
or `https://<PAVICS_FQDN_PUBLIC>/<TWITCHER_PROTECTED_PATH>/<GENERIC_BIRD_NAME>` where
`PAVICS_FQDN`, `PAVICS_FQDN_PUBLIC` and `TWITCHER_PROTECTED_PATH` are defined
in your `env.local`.

Use same docker image as regular Finch by default but can be customized.

Use a separate Postgres DB for this optional component to be completely
self-contained and to allow experimenting with different versions of Postgres
DB.

Magpie will be automatically configured to give complete public anonymous
access for this WPS service.

Canarie monitoring will also be automatically configured for this WPS service.


## Give public access to all resources for testing purposes

By enabling this component, all WPS services and data on Thredds are completely public, please beware. 
Once enabled, if you need to revert the change, you have to do it manually by logging into Magpie. 
Just disabling this component will not revert the change.

This optional component is required for the test suite at
https://github.com/Ouranosinc/PAVICS-e2e-workflow-tests.

How to enable in `env.local` (a copy from
[`env.local.example`](../env.local.example)):

* Add `./optional-components/all-public-access` to `EXTRA_CONF_DIRS`.

The anonymous user will now have all the permissions described in [`./optional-components/all-public-access/all-public-access-magpie-permission.cfg`](all-public-access/all-public-access-magpie-permission.cfg).


## WPS Health Checks

By enabling this component, all WPS services will include and `healthcheck` definition to evaluate if corresponding 
services respond to a basic `GetCapabilities` request at their respective endpoints.

How to enable in `env.local` (a copy from
[`env.local.example`](../env.local.example)):

* Add `./optional-components/wps-healthchecks` to `EXTRA_CONF_DIRS`.

Docker compose will now regularly run health check WPS requests to validate the services are responding.

