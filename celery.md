Celery
======

[Celery](https://docs.celeryq.dev/) is a distributed task queue used to run background tasks.

It is optional, but is required for the "background task" example to work.

**If you're using [Docker in development](/docker/) then Celery should automatically be configured and running.
The following instructions are for running Celery outside of Docker, or in production.**

## Quick Start

The easiest way to get going is to [download and install Redis](https://redis.io/download) 
(if you don't already have it) and then run:

```python
celery -A {{ project_name }} worker -l info
```

### Running Celery on Windows

Celery 4.x [no longer officially supports Windows](https://docs.celeryq.dev/en/4.0/whatsnew-4.0.html#removed-features). 
To use Celery on Windows for development or test purposes, change the concurrency pool implementation to ``gevent`` instead.

``` console
pip install gevent
celery -A {{ project_name }} worker -l info -P gevent
```

## Setup and Configuration

The above setup uses [Redis](https://redis.io/) as a message broker and result backend.
If you want to use a different message broker, for example [RabbitMQ](https://www.rabbitmq.com/),
you will need to modify the `CELERY_BROKER_URL` and `CELERY_RESULT_BACKEND` values in `settings.py`.

More details can be found in the [Celery documentation](https://docs.celeryq.dev/en/stable/getting-started/backends-and-brokers/index.html).
