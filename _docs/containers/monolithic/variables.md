---
title: Environment Variables
description: An overview of the available monolithic environment variables
permalink: /docs/containers/monolithic/variables/
draft: true
---

Monolithic and Generic's behaviour is controlled by a wide variety of environment variables. They are detailed here along with the default values. 

## Changing Environment Variables
<div class="note info">
  <h5>Recreating is ok</h5>
   <p>
Changing environment variables in docker requires you to delete and recreate your containers. Don't worry, your cache data is safely stored OUTSIDE in a volume and will be reused by the new containers
   </p>
</div>

If you are using docker-compose these variables can be changed by editing the .env file provided. If a specific variable does not exist in your version of the .env just add a new line.

If you are using docker from the command line environment variables are specified using `-e VARIABLE=VALUE` format when you launch the container.

## General Settings
{% include docs_variables_table.html scope=site.data.lancache_env_vars.monolithic %}
{% include docs_variables_table.html scope=site.data.lancache_env_vars.generic %}

## Advanced Settings
<div class="note warning">
  <h5>HERE BE DRAGONS!!!</h5>
   <p>
These settings really aren't meant to be changed very often, it is highly unlikely you want to change them unless it's been recommended by a team member or you REALLY know what you are doing
   </p>
</div>

{% include docs_variables_table.html scope=site.data.lancache_env_vars.monolithic-advanced %}
{% include docs_variables_table.html scope=site.data.lancache_env_vars.generic-advanced %}
