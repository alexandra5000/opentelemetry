To retrieve your {{motlp}} endpoint address and API key, follow these steps:

:::::{applies-switch}
::::{applies-item} serverless:
1. In {{ecloud}}, create an Observability project or open an existing one.
2. Go to **Add data**, select **Applications** and then select **OpenTelemetry**.
3. Copy the endpoint and authentication headers values.

Alternatively, you can retrieve the endpoint from the **Manage project** page and create an API key manually from the **API keys** page.
::::

::::{applies-item} ech:
1. Log in to the {{ecloud}} Console.
2. From the home page, find your deployment in **Hosted deployments**, and select **Manage**. Or, on the **Hosted deployments** page, select your deployment.
3. In the **Application endpoints, cluster and component IDs** section, select **Managed OTLP**.
4. Copy the public endpoint value.
::::
:::::

You can also create an API key manually from the {{kib}} UI or using the {{es}} [create API key API](https://www.elastic.co/docs/api/doc/elasticsearch/operation/operation-security-create-api-key).

::::{dropdown} Unrestricted key

The following is an example of an API key with access to all pipelines:

```bash
POST /_security/api_key
{
  "name": "my-motlp-api-key",
  "role_descriptors": {
    "motlp_write_role": {
      "applications": [
        {
          "application": "ingest",
          "privileges": ["write"],
          "resources": ["*"]
        }
      ]
    }
  }
}
```
::::

::::{dropdown} Pipeline-scoped key

The following is an example of an API key restricted to a specific pipeline. You can use one when you want to limit a client, such as an {{product.elastic-agent}} managed by {{product.fleet}}, to ingesting into a single agent policy pipeline:

```bash
POST /_security/api_key
{
  "name": "fleet_agent_policy_123-api-key",
  "expiration": "1d",
  "role_descriptors": {
    "motlp_pipeline_write_role": {
      "applications": [
        {
          "application": "ingest",
          "privileges": ["write"],
          "resources": ["pipeline:.fleet_agent_policy_1"]
        }
      ]
    }
  }
}
```

The `pipeline:` prefix in the resource name restricts the key to a named pipeline. To grant access to multiple pipelines, list multiple resources in the `resources` array.
::::
