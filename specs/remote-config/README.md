# Remote CKAN configuration

Main goals:

* Make easier to install CKAN in an automated way, eg with deployment and orchestration tools and/or in a cloud environment with multiple CKAN instances
* Allow authorized users or scripts to change (some) configuration options without having to login to the server, via the API or web interface.

## Current implementation

* CKAN can be configured using [several](http://docs.ckan.org/en/latest/maintaining/configuration.html) configuration options that are generally stored on the main ini file (usually located at `/etc/ckan/default/production.ini` or `development.ini`).

* There is already a `sytem_info` table in the database, some logic to update its values and an admin interface accessible at '/ckan-admin/config'.
 The relevant code is mostly located in the [`admin`](https://github.com/ckan/ckan/blob/master/ckan/controllers/admin.py) controller and the [`app_globals`](https://github.com/ckan/ckan/blob/master/ckan/lib/app_globals.py) module. Whenever one of the supported config options is updated:

  - Its value is stored in the database
  - It is added to the pylons `config` object
  - It is added to the pylons `app_globals` variable (accessible in templates via `g`)

 There are no actions or auth functions defined to handle this.

* There is a command line paster command to add / set options on the CKAN ini file ([#1881](https://github.com/ckan/ckan/pull/1881))

## Proposal

### Support for environment variables on critical settings


| Config setting                      | Env var name
|------------------------------------|---------------------------------------------
| `sqlalchemy.url`                  |  `CKAN_SQLALCHEMY_URL`
| `ckan.datastore.write_url` | `CKAN_DATASTORE_WRITE_URL`
| `ckan.datastore.read_url` | `CKAN_DATASTORE_READ_URL`
| `solr_url`                              | `CKAN_SOLR_URL`

Potential candidates:
* Filestore
* SMTP server


Env vars are always uppercase and prefixed with `CKAN_` (this prefix is added even if the setting in the ini file does not have it), and replacing dots with underscores.

Note that there is already support for setting the DB connection URL via the `CKAN_DB` env var. We propose to keep support for it but add the new `CKAN_SQLALCHEMY_URL` for consistency.

The pylons config object will be updated at startup time with whatever env var is available, but they won't we accessed any more during the lifetime of the application. Config values set via env vars won't be stored on the database.

On a first instance we will focus on the above ones, later on we can decide if we expand this logic to allow setting any config option via env vars.

**Q:** CKAN supports passing extra SQLAlchemy configurations like `sqlalchemy.pool_size=10` or `ckan.datastore.sqlalchemy.max_overflow=20`, should we add support for `CKAN_SQLALCHEMY_*` or `CKAN_DATASTORE_SQLALCHEMY_*` env vars?


### Whitelist "instance" settings to separate them from "runtime" (more sensitive) ones

**TODO**: agree on a proper name for "instance" and "runtime" settings

This essentially is meant to identify configuration settings that are safe to update on the go without potentially without causing major disruptions to the site. For instance they should not have the potential to cause exceptions or prevent restarting (they might still cause minor issues, eg changing the site title affects the theme).

The current equivalent would be [this list](https://github.com/ckan/ckan/blob/e7f87acdeb754280a7b7457217a9734812de4224/ckan/lib/app_globals.py#L24) on `app_globals`.

Obvious examples of "safe" settings are the site title, logo, homepage layout, etc. Other settings like the authorization ones could still be exposed but they might have wider potential implications. For instance if creating datasets that don't belong to an organization was turn on and is set to False, existing unowned datasets might not be able to be edited any more by their creators.

Things to take into account:

* It should be modifiable: different instances might want to expose different configuration settings and extensions might want to add their own
* Maybe not on a first stage but it probably would need to support defining some sort of validation for config options


### Persist "instance" config options

**TODO**: expand

The most straightforward option (always assuming that we are not storing critical settings) seems to be the existing `system_info` table on the main CKAN database. There already is a model with methods to store and get values from the database.

Config values stored on the database take precedence over the ones defined in the ini file (and via env var, although this situation should not occur in a first instance)


### API actions for listing / modifying config options

These actions should be available to CKAN sysadmins only.


These essentially should wrap the existing functions (or similar functionality) on the [`app_globals`](https://github.com/ckan/ckan/blob/master/ckan/lib/app_globals.py) module (eg `set_global`). The admin controller should be refactored to use these.

* `config_option_list`

 params:

   -

 Lists all available configuration options that are whitelisted to be modified remotely

* `config_option_show` :

 params:

   `id`: configuration option key

  Shows the current value and the default value of a particular configuration option

* `config_option_update` :

 params:

   `id`: configuration option key
   `value`: new value for the configuration option

  Sets the value of a particular configuration option

* `config_option_reset` :

 params:

   `id`: configuration option key

  Resets the value of a particular configuration option to the default value

Potentially:
* `config_option_reset_all`
* `config_option_export_all`

- validation / schemas (?)

### Frontend for updating "instance" config options

**TODO**: expand

- Defined from extensions ?
- Show default value (values on ini)

## Previous relevant work and discussions

* https://github.com/ckan/ideas-and-roadmap/issues/88

* https://github.com/ckan/ckan/pull/1881

* https://github.com/ckan/ckan/pull/1828

* https://github.com/boxkite/ckan-multisite

* https://github.com/datacats/datacats/pull/82

## Roles

* CKAN Server Administrator: an authorized user with login access to the server responsible of deploying and maintaining the CKAN instance
* CKAN Instance Administrator: a sysadmin CKAN user with full control of the CKAN instance but no access to the server
* CKAN Developer: a developer that customizes the CKAN instance via extensions

## User stories


* As a CKAN Server Administrator I want to be able to specify the database configuration in an environment variable so that I can use it in 12 factor app solutions
* As a CKAN Server Administrator I want to be able to specify the search engine configuration in an environment variable so that I can use it in a 12 factor app solution
* As a CKAN Server Administrator I want to be able to specify the file storage path configuration in an environment variable so that I can use it in 12 factor app solutions
* As a CKAN Instance Administrator I want a way to configure what settings are made available on the frontend so that the configuration doesn't have to be hardcoded into CKAN core
* As a CKAN Developer I want a way to configure what settings are made available on the frontend so that I can make new config options available from extensions
* As a CKAN Instance Administrator I want to have a web interface where I can configure my CKAN instance based on the previously determined settings so that I don't have to ssh into a server and update an ini file, but can just use CKAN itself to configure it
* As a CKAN Instance Adminstrator or CKAN Server Administrator I want to see what settings I can configure via the API so that I don't spend time guessing what can and can't be updated
* As a CKAN Instance Adminstrator or CKAN Server Administrator I want to have an API where I can configure my CKAN instance so that I can quickly update the pre-determined CKAN instance configurations from something like a commandline script (e.g. if I manage many instances at the same time)
* As a CKAN Instance Administrator I want configurations to be persisted so that they can be changed on the fly and via a web interface without restarting the CKAN instance
