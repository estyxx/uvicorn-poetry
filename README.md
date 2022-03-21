[![codecov](https://codecov.io/gh/max-pfeiffer/uvicorn-poetry/branch/main/graph/badge.svg?token=WQI2SJJLZN)](https://codecov.io/gh/max-pfeiffer/uvicorn-poetry)
# uvicorn-poetry
This Docker image provides a platform to run Python applications with [Uvicorn](https://github.com/encode/uvicorn) on [Kubernetes](https://kubernetes.io/) container orchestration system.
It provides [Poetry](https://python-poetry.org/) for managing dependencies and setting up a virtual environment in the container.

This image aims to follow the best practices for a production grade container image for hosting Python web applications based
on micro frameworks like [FastAPI](https://fastapi.tiangolo.com/).
Therefore source and documentation contain a lot of references to documentation of dependencies used in this project, so users
of this image can follow up on that.

Any feedback is highly appreciated and will be considered.

Docker Hub: [pfeiffermax/uvicorn-poetry](https://hub.docker.com/r/pfeiffermax/uvicorn-poetry)

GitHub Repository: [https://github.com/max-pfeiffer/uvicorn-poetry](https://github.com/max-pfeiffer/uvicorn-poetry)

## Docker Image Features
1. Supported architectures:
   1. Python v3.9.11, Debian or Debian-slim
   2. Python v3.10.3, Debian or Debian-slim
2. Poetry is available as Python package dependency management tool
3. A virtual environment for the application and application server
4. An entrypoint for running the Python application with Uvicorn
5. Additional entrypoints for [pytest](https://github.com/max-pfeiffer/uvicorn-poetry/blob/main/build/scripts/pytest_entrypoint.sh)
   and [black](https://github.com/max-pfeiffer/uvicorn-poetry/blob/main/build/scripts/black_entrypoint.sh) which can be used in
   multi stage builds for building docker executables

## Usage
The image just provides a platform that you can use to build upon your own multistage builds. So it consequently does not contain an
application itself. Please check out the [example application](https://github.com/max-pfeiffer/uvicorn-poetry/tree/main/examples/fast_api_multistage_build)
on how to use that image and build containers efficiently.

Please be aware that your application needs an application layout without src folder which is proposed in
[fastapi-realworld-example-app](https://github.com/nsidnev/fastapi-realworld-example-app).
The application and test structure needs to be like that:
```bash
├── Dockerfile
├── app
│    ├── __init__.py
│    └── main.py
├── poetry.lock
├── pyproject.toml
└── tests
    ├── __init__.py
    ├── conftest.py
    └── test_api
        ├── __init__.py
        ├── test_items.py
        └── test_root.py
```
Please be aware that you need to provide a pyproject.toml file to specify your Python package dependencies for Poetry and configure
dependencies like Pytest. Poetry dependencies must at least contain the following to work:
* python = "3.9.11"
* uvicorn = "0.17.6"

If your application uses FastAPI framework this needs to be added as well:
* fastapi = "0.75.0"

**IMPORTANT:** make sure you have a [.dockerignore file](https://github.com/max-pfeiffer/uvicorn-poetry/blob/main/examples/fast_api_multistage_build/.dockerignore)
in your application root which excludes your local virtual environment in .venv! Otherwise you will have an issue activating that virtual
environment when running the container.

## Configuration
Configuration is done through command line arguments in the entrypoint for running the Python application.
For everything else Uvicorn uses it's defaults.
Since [Uvicorn v0.16.0](https://github.com/encode/uvicorn/releases/tag/0.16.0) you can configure everything else via
[environment variables](https://www.uvicorn.org/settings/) with the prefix `UVICORN_`. 
For all the following configuration options please see always the
[official Uvicorn documentation](https://www.uvicorn.org/settings/) if you would like to do a deep dive.

### Important change since V2.0.0
The custom environment variables are not supported any more: 
1. `LOG_LEVEL` : The granularity of Error log outputs.
2. `LOG_CONFIG_FILE` : Logging configuration file.
3. `RELOAD` : Enable auto-reload.
