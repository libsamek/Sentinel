# Sentinel

Sentinel is an application written in Python that may be used to perform sentiment analysis on a piece of text. It leverages an external API for this purpose and provides a safe default when the API is unavailable.

## Commands

The following commands describe how the application may be built, run and tested. Note that [Python 3.10](https://docs.python.org/3/whatsnew/3.10.html) and [Poetry](https://python-poetry.org/) are required.

**Setup**

```sh
poetry install
```

**Run**

```sh
uvicorn sentinel.api:app --host 0.0.0.0 --port 8000 --reload
```

From another terminal window, you may query the application with:

```sh
curl -XPOST http://localhost:8000/analyze -H "Content-Type: application/json" -d '{"text": "This is a test."}'
```

**Unit tests and coverage**

```sh
poetry run coverage run -m pytest tests/test_unit.py
poetry run coverage report --fail-under 90
```

**Regression tests**

By default, regression tests run against an application running on `localhost:8000`. However, you may use the `BASE_URL` environment variable to run against a different URL.

```sh
BASE_URL=http://localhost:8000 poetry run pytest tests/test_regression.py
```

**Integration tests**

```sh
poetry run pytest tests/test_integration.py
```

## CI/CD

To submit new code fork this repository and create a pull request.

Your code must pass `test` status check. This is a Github job with the following steps:
* runs unit tests,
* checks if code coverage is above 90%,
* runs integrations tests.

Pull requests will be reviewed and merged by the owner. When code is accepted to the `main` branch all tests are run again. After the test job succeeds we try to deploy new code to dev environment (https://sentineldev.herokuapp.com) and run regression tests on deployed code. This ensures that breaking changes are not promoted to prod. If all goes well code is promoted to prod environment (https://sentinelprod.herokuapp.com) and once again we run regression tests just to make sure deploy was successful.

The initial idea was to deploy to dev environment and run regression tests on it at pull request time, but there is a security issue with forked repositories, so a trade-off was made and dev deploy was moved after merge due to simplicity.