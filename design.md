# Design: Introducing hopsworks client SDK

## Why it would make sense

### Reason 1: Remove code duplication

There is a need for having a shared library to house code used for the hsfs and hsml library. 

Right now both hsfs and hsml have their own implementations for using the datasets API for example, and soon hsml will be using the kafka API which will duplicate more, and ~600 lines client code which can be unified.

In the future this technical debt will grow and it will be harder to unify the common code so I argue we should do something about it now.

### Reason 2: Provide object-oriented API for services which are not exclusive to hsfs or hsml

A recent use-case surfaced in AF where they need to be able to create/run jobs from an external environment such as Jenkins.

In the past we've seen several cases where users want to work directly with APIs like datasets, secrets, kafka, jobs etc.

The current *hops* library does however support the aforementioned APIs, but it is not consistent with the object-oriented *hsfs* and *hsml* libraries.

**And on a marketing/coding/logical note, *hops* is a confusing library name.**

# Installation


```python
!pip install hopsworks
```

## An object-oriented approach to working with a project

### Connect to your instance


```python
import hopsworks

# connect to a instance
conn = hopsworks.connection()
```

## Get project object


```python
# get reference to project object
project = conn.get_project("demo_project")
```

## Get access to an API of the project (e.g. Jobs)


```python
# return client for job creation/retrieval/deletion
jobs_handler = project.get_jobs_api()

job = jobs_handler.create_job(...)

execution = job.start(arguments="1 2 3")

execution.wait(...)

# You get it, an object-oriented approach to the APIs
```

# Okay, but how do I work with the feature store or model registry/serving?

Currently the hsfs and hsml modules are self-contained libraries and and suggesting to move the source to the hopsworks library would result in my imminent demise. As such we could provide hsfs and hsml as submodules to hopsworks, which means we would add hsfs and hsml as a dependency of the library.

## hsfs


```python
import hopsworks.hsfs as hsfs

# Create a connection
connection = hsfs.connection()

# Get the feature store handle for the project's feature store
fs = connection.get_feature_store()
```

## hsml


```python
import hopsworks.hsml as hsml

# Create a connection
connection = hsml.connection()

# Get the model registry handle for the project's model registry
mr = connection.get_model_registry()
```
