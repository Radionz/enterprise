## System Requirements

Stoplight currently requires Ubuntu 16.04 LTS or CentOS 7 as the base Linux system for all on-premise installations. A minimum of one server is required to run the Stoplight application, however, for a production installation, we recommend at least four servers (excluding monitoring and backup servers). The system specifications for each server can be found below under each component.

For the recommended Docker-based installation path, the only application requirements are:

- [Nginx](http://nginx.org/) v1.10.3
- [Docker CE](https://www.docker.com/) v17.00

If Docker is not allowed within your operating environment, the application requirements are:

- [Nginx](http://nginx.org/) v1.10.3
- [NodeJS](https://nodejs.org/) v8.9.1
- [Gitlab CE](https://about.gitlab.com/) v10.0.3
- [PostgreSQL](https://www.postgresql.org/) v9.6 (optionally included with Gitlab)
- [Redis](https://redis.io/) v2.8 (optionally included with Gitlab)

For automation and configuration purposes, [Ansible](https://www.ansible.com/) v2.2 is also recommended. For monitoring purposes, Stoplight recommends the following applications:

- [InfluxDB](https://www.influxdata.com/time-series-platform/influxdb/) v1.3 for metrics storage and aggregation
- [Kapacitor](https://www.influxdata.com/time-series-platform/kapacitor/) v1.3 for alerting and metrics processing
- [Telegraf](https://www.influxdata.com/time-series-platform/telegraf/) v1.4 for metrics collection
- [Mtail](https://github.com/google/mtail) v3.0 for whitebox monitoring of application logs

Please note that the above recommendations are entirely optional if your organization already has a monitoring and alerting solution in place.

## Components

The Stoplight platform is broken up in to five main components, each described below. Each component has a folder in the "components" folder of this repository, with a basic `start.sh` script. Additionally, each component has a environment file in the "env" folder, that defines the various variables required for the component to function.

### App

*Required*

The app server serves up the Stoplight UI. This is the primary point of ingress for most users using Stoplight. It is what they will load in their web browser, and connect the desktop app to.

#### System Specifications

For app servers, we recommend a minimum of the following system specifications:

- 2 CPUs
- 2GB Memory

#### Networking

The app service must be able to contact the following components:

- API
- Prism
- Exporter
- Gitlab CE

### API

*Required*

The API server is what the App server (described above) connects to to fetch and persist data.

#### System Specifications

For API servers, we recommend co-locating with Gitlab and using a minimum of the following system specifications:

- 4 CPUs
- 8GB Memory

#### Networking

The API service must be able to contact the following components:

- App
- Gitlab CE (+ PostgreSQL)

### Prism

*Optional. Required to run scenarios (testing and orchestration) or prism instances (mock servers, proxy servers).*

The Prism server runs scenario collections.

Please note that Prism must be setup with a wildcard subdomain (CNAME DNS record), for example `*.prism.example.com`. Each Prism instance that is created gets a unique hostname associated with it, for example `service1-mock.prism.example.com`. This is preferrable to storing the instance ID in a path (`prism.example.com/service1-mock`) because it means that you don't need to change your API code - all you need to do is change your host variable.

#### System Specifications

For Prism servers, we recommend a minimum of the following system specifications:

- 2 CPUs
- 4GB Memory

#### Networking

The Prism service must be able to contact the following components:

- Exporter

### Exporter

*Required*

The Exporter server dereferences OAS and Swagger files to ensure all referenced specs and external data sources are resolvable by runtime.

#### System Specifications

For Exporter servers, we recommend a minimum of the following system specifications:

- 2 CPUs
- 2GB Memory

#### Networking

The Exporter service must be able to contact the following components:

- API

### Gitlab CE

*Required*

Gitlab CE is the backing datastore for all files stored within Stoplight. In addition to storing files, Gitlab CE is responsible for:

* Interfacing with the Stoplight API
* User-facing notifications (including password reset emails, group/project invitations, etc)
* Tracking all changes between different files, and storing them within a Git repository

Packaged within the Gitlab CE is an installation of PostgreSQL v9.6 and Redis v2.8. These two sub-components can be broken out into external services if your organization is already familiar with running these (or similar) services. You may also break out these services if you plan on using a managed hosting solution, for example Amazon RDS (for PostgreSQL) or Amazon ElastiCache (for Redis).

#### System Specifications

For Gitlab servers, we recommend co-locating with the API and using a minimum of the following system specifications:

- 4 CPUs
- 8GB Memory
- SSD-backed redundant storage

#### Networking

The Gitlab service has no outgoing network dependencies unless the Redis or PostgreSQL sub-components are broken out separately.
