# Wrangler

![CI python](https://github.com/sanger/wrangler/workflows/CI%20python/badge.svg)
![CI docker](https://github.com/sanger/wrangler/workflows/CI%20docker/badge.svg)
[![Code style: black](https://img.shields.io/badge/code%20style-black-000000.svg)](https://github.com/psf/black)
[![codecov](https://codecov.io/gh/sanger/wrangler/branch/develop/graph/badge.svg)](https://codecov.io/gh/sanger/wrangler)

A micro service to create labware in Sequencescape based on data stored in CSV files and databases.
Currently, the two main features are:

* lookup and parse a CSV file named after a tube rack barcode provided, return the data in the CSV
if found
* determine if a provided barcode is a plate or a tube rack and create the entity in Sequencescape

The routes of the service are:

    Endpoint                           Methods  Rule
    ---------------------------------  -------  ------------------------------
    labware.wrangle                    POST     /wrangle/<labware_barcode>
    racks.get_tubes_from_rack_barcode  GET      /tube_rack/<tube_rack_barcode>
    static                             GET      /static/<path:filename>

The service uses a scheduled job to periodically query the multi-LIMS warehouse (MLWH) cgap_heron table for rows with a
destination of "CGAP Extraction" that haven't yet been wrangled (i.e. the wrangled column is `null`). It will create
each plate or tube rack in Sequencescape, and subsequently mark the rows in the MLWH as wrangled (i.e. wrangled will be
updated with a timestamp).

Plates and tube racks created within this scheduled job will have the following plate purposes (set in the
`constants.py` file):

- Plate: `Heron Lysed Plate`
- Tube Rack: `Heron Lysed Tube Rack`

This scheduled job can be enabled or disabled by setting the `ENABLE_SCHEDULER` config variable to `True` or `False`
respectively.

## Requirements

* [pyenv](https://github.com/pyenv/pyenv)
* [pipenv](https://pipenv.pypa.io/en/latest/)
* mySQL

## Setup

* Use pyenv or something similar to install the version of python
defined in the `Pipfile`:
  1. `brew install pyenv`
  2. `pyenv install <python_version>`
* Use pipenv to install python packages: `brew install pipenv`
* To install the required packages (and dev packages) run: `pipenv install --dev`

## Running

1. Create a `.env` file with the following contents (or use `.env.example` - rename to `.env`):
    * `FLASK_APP=wrangler`
    * `FLASK_ENV=development`
    * `SETTINGS_PATH=config/development.py`

1. To setup the database and table (schema defined in 'sql/schema.sql'):

        flask init-db

1. Enter the python virtual environment using:

        pipenv shell

1. Run the app using:

        flask run

__NB:__ When adding or changing environmental variables, remember to exit and re-enter the virtual
environment.

## Testing

1. Verify the credentials for your database in the settings file 'config/test.py'
1. Create the test database and table and insert test data (found in 'sql/test_data'):

        SETTINGS_PATH=config/test.py flask init-db

1. Run the tests using pytest (flags are for verbose, exit early and capture output):

        python -m pytest -vsx

__NB__: Make sure to be in the virtual environment (`pipenv shell`) before running the tests:

## Type checking

Type checking is done using mypy, to run it, execute `mypy .`

## Contributing

This project uses [black](https://github.com/psf/black) to check for code format, the use it run:
`black .`
