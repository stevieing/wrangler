# Wrangler

![Docker CI](https://github.com/sanger/wrangler/workflows/Docker%20CI/badge.svg)
[![Code style: black](https://img.shields.io/badge/code%20style-black-000000.svg)](https://github.com/psf/black)

A micro service to deal with tube racks. Currently, the two main features are:

* lookup and parse a CSV file named after the tube rack barcode
* determine if a tube rack is part of project Heron and if so, request Sequencescape to create
samples

## Requirements

* [pyenv](https://github.com/pyenv/pyenv)
* [pipenv](https://pipenv.pypa.io/en/latest/)
* mySQL

## Setup

* Use pyenv or something similar to install the version of python
defined in the `Pipfile`:
  1. `brew install pyenv`
  2. `pyenv install <python_version>`
* Use pipenv or something similar to install python packages:
`brew install pipenv`
* To install the required packages (and dev packages) run: `pipenv install --dev`

## Running

1. Create a `.env` file with the following contents (or use `.env.example` - rename to `.env`):
    * `FLASK_ENV=development`
    * `FLASK_APP=wrangler`
    * `TUBE_RACK_DIR=<dir>`
    * `MLWH_DB_USER`
    * `MLWH_DB_PASSWORD`
    * `MLWH_DB_HOST`
    * `MLWH_DB_PORT`
    * `MLWH_DB_DBNAME`
    * `MLWH_DB_TABLE`
    * `SS_URL_HOST=http://example.com/api/blah`
    * `SS_API_KEY=123`
1. Enter the python virtual environment using `pipenv shell`
1. Run the app using `flask run`

__NB:__ When adding or changing environmental variables, remember to exit and re-enter the virtual
environment.

## Testing

Make sure to be in the virtual environment before running the tests:

1. Create a database locally called 'mlwarehouse_test' and a table in it called 'heron'
1. Update the credentials for your database in the file `tests/conftest.py`
1. Run the tests using `python -m pytest`

## Contributing

This project uses [black](https://github.com/psf/black) to check for code format, the use it run:
`black .`
