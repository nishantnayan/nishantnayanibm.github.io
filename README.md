# BDC Downloader

<p style="color: red;">Webapp for ordering, downloading and slicing measurement data files.</p>

The app is hosted on [eXtollo](https://wiki.mercedes-benz.polygran.de/x/7QACH)

We use FastAPI to serve the backend using SQLAlchemy to access the SQL database.
Locally (usually on Windows) we use uvicorn as a web server.
In production, we use gunicorn running w/ multiple workers.

## Relevant links

Further documentation can be found in [Confluence](https://gsep.daimler.com/confluence/pages/viewpage.action?pageId=638497518)

The following tech stack is used:

- [Python 3.9](https://www.python.org/downloads/release/python-390/)
- [FastAPI](https://fastapi.tiangolo.com/)
- [SQLAlchemy 1.4](https://www.sqlalchemy.org/)
- [Pydantic model](https://pydantic-docs.helpmanual.io/)
- [Gunicorn](https://gunicorn.org/) / [Uvicorn](https://www.uvicorn.org/)
- MS SQL (on premises) and [Azure SQL](https://azure.microsoft.com/en-us/products/azure-sql/database/) on DEV / PROD
- [tox](https://tox.readthedocs.io/en/latest/) for testing
- [Sphinx](https://www.sphinx-doc.org/en/master/) documentation
- [Azure pipelines](https://docs.microsoft.com/en-us/azure/devops/pipelines/get-started/what-is-azure-pipelines?view=azure-devops):
  CI/CD, deployment, [blackduck](https://bdscan.i.mercedes-benz.com), [secret-scanner](https://git.i.mercedes-benz.com/secret-scanner/)
- [Azure Identity](https://pypi.org/project/azure-identity/) for ADLS access
- [Azure Storage Blob](https://pypi.org/project/azure-storage-blob/) for ADLS access
- [Application Insights](https://docs.microsoft.com/en-us/azure/azure-monitor/app/app-insights-overview) (logging)
- [GitHub](https://git.i.mercedes-benz.com/use-cases/2021017_downloader)
- [flake8](https://flake8.pycqa.org/en/latest/) for Style Guide Enforcement
- [black](https://black.readthedocs.io/en/stable/) for code formatting
- [WARP API Lib](https://git.i.mercedes-benz.com/warp/warp-api-lib) for WARP integration
- [BlackDuck](https://bdscan.i.mercedes-benz.com/api/projects/62255628-cffa-4ade-8d01-b742355bb8e1/versions/88e0a7fd-7216-48bf-8f76-2a49a4c5b4d1/components) for license validation
- SecHub
- [Loguru](https://github.com/Delgan/loguru) for logging

## Guidelines

We use _google-style_ docstrings and follow `pep8` as well as `flake8` guidelines.
Import order style is also _google_. `black` is used for code formatting.

## Installation

Install package locally:

```sh
cd 2021017_downloader
pip install -e .
```

for dev and prod:
Use the devops pipelines.

## BDC Cli

See [BDC CLI Readme](https://git.i.mercedes-benz.com/use-cases/2021017_downloader/blob/dev/src/devtools/README.md)

Install package locally:

make sure to install the test and dev libs:

```sh
cd 2021017_downloader
pip install -e .[test|dev]
```

You should now be able to type `bdc` to see the helper function of the bdc cli.
You can find the source code in `/src/devtools/cli.py`.
For local development via dockerfiles for mssql and maildev, create a .env file in the
root directory of the project and specify the following:

```
BDC_DOWNLOADER_ENVIRONMENT=LOCAL
BDC_DOWNLOADER_BACKEND_LOG_PATH=./logs

BDC_DOWNLOADER_DB_NAME=Downloader_LOCAL
BDC_DOWNLOADER_DB_SERVER=localhost
BDC_DOWNLOADER_DB_USER=SA
BDC_DOWNLOADER_DB_PASSWORD=sweudjiekdjezsrduejhs1!
BDC_DOWNLOADER_DB_USE_LOCAL=True
MSSQL_SA_PASSWORD=sweudjiekdjezsrduejhs1!
BDC_DOWNLOADER_WEBAPP_URL=http://localhost:8000

BDC_DOWNLOADER_WARP_URL=https://warpapi.app.corpinter.net/warp-azure/v4
BDC_DOWNLOADER_WARP_KEY=
BDC_DOWNLOADER_DEBUG_SQL=False
BDC_DOWNLOADER_MAX_ALLOWED_CHANNELS=100
BDC_DOWNLOADER_MAX_ALLOWED_SESSIONS=30
BDC_DOWNLOADER_MAX_ALLOWED_JOBS_PER_ORDER=10
BDC_DOWNLOADER_MAX_ALLOWED_DIRECT_SESSIONS_PER_USER=100
BDC_DOWNLOADER_MAX_ALLOWED_DIRECT_SESSIONS_PER_USER_DAYS_RANGE=5
BDC_DOWNLOADER_MAX_ALLOWED_API_JOBS_PER_USER=100
BDC_DOWNLOADER_MAX_ALLOWED_API_JOBS_PER_USER_DAYS_RANGE=5

BDC_DOWNLOADER_SMTP_HOST=localhost:1025
BDC_DOWNLOADER_SMTP_PORT=1025
BDC_DOWNLOADER_SMTP_SENDER=connected-services@mercedes-benz.com
BDC_DOWNLOADER_SMTP_REPLY_TO=noreply@mercedes-benz.com
BDC_DOWNLOADER_LOCAL_USER_EMAIL=knapf@mercedes-benz.com
BDC_DOWNLOADER_ADMINS=MY_EMEA_NAME;knapf

BDC_DOWNLOADER_AZURE_WEB_APP_DEV=RD-eu-web-dev-downloader
BDC_DOWNLOADER_AZURE_WEB_APP_PROD=RD-eu-web-prod-downloader
BDC_DOWNLOADER_AZURE_RESOURCE_GROUP_DEV=RD-eu-rdppe-non-customer-int-linux-app
BDC_DOWNLOADER_AZURE_RESOURCE_GROUP_PROD=rd-eu-rdppe-non-customer-linux-app
BDC_DOWNLOADER_AZURE_SUBSCRIPTION=GC313_Sub_eXtollo-BU-RD
BDC_DOWNLOADER_ADLS_RESOURCE_GROUP=RD-eu-rsv-rdppe-non-customer-int

BDC_DOWNLOADER_TENANT_ID=9652d7c2-1ccf-4940-8151-4a92bd474ed0

BDC_DOWNLOADER_MANAGED_IDENTITY_CLIENT_ID=not-set
BDC_DOWNLOADER_ADLS_RESOURCE_GROUP=
BDC_DOWNLOADER_STORAGE_ACCOUNT_NAME=
BDC_DOWNLOADER_TENANT_ID=
BDC_DOWNLOADER_BEHAVE_COOKIE_DEV=
BDC_DOWNLOADER_BEHAVE_COOKIE_PROD=
BDC_DOWNLOADER_BEHAVE_USER=MY_EMEA_NAME
BDC_DOWNLOADER_BEHAVE_EMAIL=
```

After setting the .env variables you should be able to start the db and maildev containers with
`bdc start-containers`. The db files are linked to a volume in `devtools/db/db-volume`.

## Helpful Tools

- [Azure Storage Explorer](https://azure.microsoft.com/en-us/products/storage/storage-explorer)
- [Vector MDF Validator](https://www.vector.com/int/en/support-downloads/download-center/#product=%5B%22109%22%5D&downloadType=%5B%22freeware%22%5D&tab=1&pageSize=15&sort=date&order=desc)

## Usage

For development:

```sh
uvicorn --app-dir src bdc_downloader.application:app --host 127.0.0.1 --reload
```

or right click src/bdc_downloader/application.py in pycharm and select run or debug.

## Test API / Swagger Documentation

See `http://127.0.0.1:8000/`

## Documentation

To create the Sphinx documentation:

```sh
pip install -e .[doc]
cd docs
```

Either install make (depends on your OS) and call:

```sh
make html
```

or call this w/o make:

```sh
sphinx-build -b html -d ..\build\sphinx\doctrees . .\build\sphinx\html
```

To view the docs see:

```sh
docs\build\sphinx\html\index.html
```

## Logging

We use [Loguru](https://github.com/Delgan/loguru) instead of standard python logging.  
The individual actors log to proper logfiles which can be configured in `common/configuration.py`.
To use the loguru logger you do **not** need to call getLogger("some_name"). The system automatically
knows the context in which it is running. So all you need call is this:

```py
from loguru import logger

logger.info("This is an info")
logger.warning("This is a warning")
```

To log additional context variables you can add them as keyword parameter to the call:

```python
logger.info("This is an info w/ variables", user="KNAPF", session_id=42)

context: dict = {"foo": "bar", "gaga": "banane"}
logger.info("This is an info w/ variables from a dictionary", **context)
```

The log is automatically written to the corresponding logfile:

```log
2022-05-13 08:56.25 |INFO   |234ABC123|routers.infos:get_health:22   - This is an info
2022-05-13 08:56.25 |WARNING|234ABC123|routers.infos:get_health:23   - This is a warning
2022-05-13 08:56.25 |INFO   |234ABC123|routers.infos:get_health:24   - This is an info w/ variables {"user": "KNAPF", "session_id": 42}
2022-05-13 08:56.25 |INFO   |234ABC123|routers.infos:get_health:27   - This is an info w/ variables from a dictionary {"foo": "bar", "gaga": "banane"}
```

The 3rd column contains a shortened concatenation of the following values:

- orderID: last three digits of the orderID
- jobID: the first three digits of the JobID
- warpJobID: the first three digits of the warpJobID

To set those values you need to bind the context to the logger once available:

```py
with logger.contextualize(orderID="1234", jobID="ABCD",  warpJobId=1234):
    logger.info("This is message")
```

Furthermore, you can track the process and thread ID as well as the count of the actor instances,
e.g. if you have two scheduler running in parallel the first one is 0 and the 2nd one the 1.
To track those values you need to set `track_pid` to True in the common/configuration.py:

```py
self.LOG_FILES = {
    "default": {"filename": "backend"},
    "house_keeping": {"filename": "housekeeping"},
    "scheduler": {"filename": "scheduler", "track_pid": True},
    "streaming_handler": {"filename": "streaminghandler", "track_pid": True},
}
```

results in:

```log
streaminghandler.log:2022-05-23 18:51.30|   ...   |StreamHandler: done. {"APT": "1/2034/139707587905344"}
```

where A=ActorID,P=ProcessID,T=ThreadID.

**Note**: Azure Application Insights logging is currently disabled. Waiting for a decision on how to proceed.

## Testing

### Unit-Tests

To run the all tests

```sh
pip install -e .[test]
tox
```

calls the following commands which can also can be invoked individually:

```sh
tox -e test           # run pytest
tox -e isort          # sort import order
tox -e flake8         # style guide enforcment
tox -e black          # code formatter
tox -e bandit         # security linter
```

To get a coverage map to check if all your methods are covered by your tests run the following command:

```sh
tox -e coverage
```

After this command you can find the result in:

```sh
tests/coverage_html_report/index.html
```

### End-To-End Tests

End-To-End-Tests are executed with the [behave framework](https://behave.readthedocs.io/en/stable/).
All tests are located in the folder `features`. The human-readable test descriptions are all files
with the file ending `.feature`. All functions linked to the human-readable descriptions can be
found in `features/steps`.
In order for the behave framework to find all functions, it is necessary to import them in
`features/steps/all_steps.py`.
Steps (functions linked to descriptions) which are used by multiple tests should be placed in `features/steps/common_steps.py`.
Other shared code/functions which are used by multiple tests should be placed in `features/steps/environment.py`.
Entity specific steps and code are placed in separate folders, i.e. `features/steps/orders_steps`.
Make sure to set your environment variables (set them or place them in .env file - loaded with python-dotenv).
Test downloads are saved in the folder `features/downloads`.

### Run tests

**Run all scenarios of a feature**:

- execute: `behave features/<NAME_OF_FEATURE>.feature`
- example: `behave features/orders_crud.feature`

**Run one scenario of a feature**:

- execute: `behave features/<NAME_OF_FEATURE>.feature -n "<NAME_OF_SCENARIO>`
- example: `behave features/orders_crud.feature -n "Delete order`

**Run scenarios using tags**:

- execute: `behave --tags=<@TAG1>,<@TAG2>`
- example: `behave --tags=@order`

see [behave.ini](./behave.ini) for available tags and additional options.

#### Behave against dev env

Set the `env=DEV` behave variables in [behave.ini](./behave.ini).
**Note**: In order to get arrafinity_cookie, open the dev or prod url in your
browser, make a request and open dev tools, tab headers and copy everything after `Cookie: ARRAffinity=`
and use this inside .env file.

### Locust Tests

[Locust](https://locust.io/) (Load & Performance) Test are executed with the following framework: `https://locust.io`.
All tests are located in the folder `tests/locust`.

#### Setup locust

- `pip install -e .[dev]`
- set the following env variables on local env (copy and paste the cookie value from azure using developer tools on the browser, ex: ARRAffinity=xxxxxxxxxx)
  - BDC_DOWNLOADER_BEHAVE_COOKIE_DEV
  - BDC_DOWNLOADER_BEHAVE_COOKIE_PROD

#### Run locust

- open terminal
- source .env file
- go to ./tests/locust
- run the following command:
  - locust
  - Go to http://localhost:8089/ on browser to see the UI
  - Enter the following values:
    - Number of users, e.g. 1
    - Spawn rate, e.g. 1
    - Host, e.g. https://rd-eu-web-dev-downloader.azurewebsites.net
      <br>**Note**: the host URL must be **without** trailing slash.
