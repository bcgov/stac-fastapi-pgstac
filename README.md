# stac-fastapi-pgstac


<p align="center">
  <img src="https://user-images.githubusercontent.com/10407788/174893876-7a3b5b7a-95a5-48c4-9ff2-cc408f1b6af9.png" style="vertical-align: middle; max-width: 400px; max-height: 100px;" height=100 />
  <img src="https://fastapi.tiangolo.com/img/logo-margin/logo-teal.png" alt="FastAPI" style="vertical-align: middle; max-width: 400px; max-height: 100px;" width=200 />
</p>

[PgSTAC](https://github.com/stac-utils/pgstac) backend for [stac-fastapi](https://github.com/stac-utils/stac-fastapi), the [FastAPI](https://fastapi.tiangolo.com/) implementation of the [STAC API spec](https://github.com/radiantearth/stac-api-spec)

## Overview

**stac-fastapi-pgstac** is an HTTP interface built in FastAPI.
It validates requests and data sent to a [PgSTAC](https://github.com/stac-utils/pgstac) backend, and adds [links](https://github.com/radiantearth/stac-spec/blob/master/item-spec/item-spec.md#link-object) to the returned data.
All other processing and search is provided directly using PgSTAC procedural sql / plpgsql functions on the database.
PgSTAC stores all collection and item records as jsonb fields exactly as they come in allowing for any custom fields to be stored and retrieved transparently.

## `PgSTAC` version

`stac-fastapi-pgstac` depends on [`pgstac`](https://stac-utils.github.io/pgstac/pgstac/) database schema and [`pypgstac`](https://stac-utils.github.io/pgstac/pypgstac/) python package.

| stac-fastapi-pgstac Version  |     pgstac |
|                            --|          --|
|                          2.5 | >=0.7,<0.8 |
|                          3.0 | >=0.8,<0.9 |
|                          4.0 | >=0.8,<0.10 |
## Openshift
Postgres bitnami db can be deployed with helm charts/pgstac
```
helm upgrade --install charts/pgstac/
```
after deployment migrate to pgstac

```
Python -m venv venv
Source venv/bin/activate
python -m pip install pypgstac[psycopg]
export PGHOST='127.0.0.1'
export PGPORT='5432'
export PGUSER='quickuser'
export PGDATABASE='bcstac'
export PGPASSWORD='quickpass'
pypgstac migrate
```
#
Debugging stac-fastapi-pgstac with docker (need to figure out networking to openshift)
? 
would this flag work  
--add-host=host.docker.internal:host-gateway

```
docker build -t bc-stac-api .
docker run \
    -p 8080:8080 \
    --name=bc-stac-api \
    --rm \
    --detach \
    --env-file=./.env \
    bc-stac-api
```
Debug from inside container
```
docker run -it --env-file=./.env bc-stac-api bash
uvicorn stac_fastapi.pgstac.app:app --host 127.0.0.1 --port 8080
```


## Usage

PgSTAC is an external project and may be used by multiple front ends.
For Stac FastAPI development, a Docker image (which is pulled as part of the docker-compose) is available via the [Github container registry](https://github.com/stac-utils/pgstac/pkgs/container/pgstac/81689794?tag=latest).
The PgSTAC version required by **stac-fastapi-pgstac** is found in the [setup](http://github.com/stac-utils/stac-fastapi-pgstac/blob/main/setup.py) file.

### Sorting

While the STAC [Sort Extension](https://github.com/stac-api-extensions/sort) is fully supported, [PgSTAC](https://github.com/stac-utils/pgstac) is particularly enhanced to be able to sort by datetime (either ascending or descending).
Sorting by anything other than datetime (the default if no sort is specified) on very large STAC repositories without very specific query limits (ie selecting a single day date range) will not have the same performance.
For more than millions of records it is recommended to either set a low connection timeout on PostgreSQL or to disable use of the Sort Extension.

### Hydration

To configure **stac-fastapi-pgstac** to [hydrate search result items in the API](https://stac-utils.github.io/pgstac/pgstac/#runtime-configurations), set the `USE_API_HYDRATE` environment variable to `true` or explicitly set the option in the PGStac Settings object.

### Migrations

There is a Python utility as part of PgSTAC ([pypgstac](https://stac-utils.github.io/pgstac/pypgstac/)) that includes a migration utility.
To use:

```shell
pypgstac migrate
```

## Contributing

See [CONTRIBUTING](https://github.com/stac-utils/stac-fastapi-pgstac/blob/main/CONTRIBUTING.md) for detailed contribution instructions.

To install:

```shell
git clone https://github.com/stac-utils/stac-fastapi-pgstac
cd stac-fastapi-pgstac
python -m pip install -e ".[dev,server,docs]"
```

To test:

```shell
make test
```

Use Github [Pull Requests](https://github.com/stac-utils/stac-fastapi-pgstac/pulls) to provide new features or to request review of draft code, and use [Issues](https://github.com/stac-utils/stac-fastapi-pgstac/issues) to report bugs or request new features.

### Documentation

To build the docs:

```shell
make docs
```

Then, serve the docs via a local HTTP server:

```shell
mkdocs serve
```

## History

**stac-fastapi-pgstac** was initially added to **stac-fastapi** by [developmentseed](https://github.com/developmentseed).
In April of 2023, it was removed from the core **stac-fastapi** repository and moved to its current location (<http://github.com/stac-utils/stac-fastapi-pgstac>).

## License

[MIT](https://github.com/stac-utils/stac-fastapi-pgstac/blob/main/LICENSE)

<!-- markdownlint-disable-file MD033 -->
