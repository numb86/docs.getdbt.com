---
title: "About config variable"
sidebar_label: "config"
id: "config"
description: "Read this guide to understand the config Jinja function in dbt."
---

The `config` variable exists to handle end-user configuration for custom <Term id="materialization">materializations</Term>. Configs like  `unique_key` can be implemented using the `config` variable in your own materializations.

For example, code in the `incremental` materialization like this:
```
{% materialization incremental, default -%}
  {%- set unique_key = config.get('unique_key') -%}
  ...
```

is responsible for handling model code that looks like this:
```
{{
  config(
    materialized='incremental',
    unique_key='id'
  )
}}
```

Review [Model configurations](/reference/model-configs) for examples and more information on valid arguments.

## config.get
__Args__:

 * `name`: The name of the configuration variable (required)
 * `default`: The default value to use if this configuration is not provided (optional)

The `config.get` function is used to get configurations for a model from the end-user. Configs defined in this way are optional, and a default value can be provided.

There are 3 cases:
1. The configuration variable exists, it is not `None`
1. The configuration variable exists, it is `None`
1. The configuration variable does not exist

Example usage:
```sql
{% materialization incremental, default -%}
  -- Example w/ no default. unique_key will be None if the user does not provide this configuration
  {%- set unique_key = config.get('unique_key') -%}

  -- Example w/ alternate value. Use alternative of 'id' if 'unique_key' config is provided, but it is None
  {%- set unique_key = config.get('unique_key') or 'id' -%}

  -- Example w/ default value. Default to 'id' if the 'unique_key' config does not exist
  {%- set unique_key = config.get('unique_key', default='id') -%}
  ...
```

## config.require
__Args__:

 * `name`: The name of the configuration variable (required)

The `config.require` function is used to get configurations for a model from the end-user. Configs defined using this function are required, and failure to provide them will result in a compilation error.

Example usage:
```sql
{% materialization incremental, default -%}
  {%- set unique_key = config.require('unique_key') -%}
  ...
```
