# Honeycomb Marker Exporter
<!-- status autogenerated section -->
| Status        |           |
| ------------- |-----------|
| Stability     | [alpha]: logs   |
| Distributions | [contrib] |
| Issues        | [![Open issues](https://img.shields.io/github/issues-search/open-telemetry/opentelemetry-collector-contrib?query=is%3Aissue%20is%3Aopen%20label%3Aexporter%2Fhoneycombmarker%20&label=open&color=orange&logo=opentelemetry)](https://github.com/open-telemetry/opentelemetry-collector-contrib/issues?q=is%3Aopen+is%3Aissue+label%3Aexporter%2Fhoneycombmarker) [![Closed issues](https://img.shields.io/github/issues-search/open-telemetry/opentelemetry-collector-contrib?query=is%3Aissue%20is%3Aclosed%20label%3Aexporter%2Fhoneycombmarker%20&label=closed&color=blue&logo=opentelemetry)](https://github.com/open-telemetry/opentelemetry-collector-contrib/issues?q=is%3Aclosed+is%3Aissue+label%3Aexporter%2Fhoneycombmarker) |
| [Code Owners](https://github.com/open-telemetry/opentelemetry-collector-contrib/blob/main/CONTRIBUTING.md#becoming-a-code-owner)    | [@TylerHelmuth](https://www.github.com/TylerHelmuth), [@fchikwekwe](https://www.github.com/fchikwekwe) |

[alpha]: https://github.com/open-telemetry/opentelemetry-collector#alpha
[contrib]: https://github.com/open-telemetry/opentelemetry-collector-releases/tree/main/distributions/otelcol-contrib
<!-- end autogenerated section -->

This exporter allows creating [markers](https://docs.honeycomb.io/working-with-your-data/markers/), via the [Honeycomb Markers API](https://docs.honeycomb.io/api/tag/Markers#operation/createMarker), based on the look of incoming telemetry. 

The following configuration options are supported:

* `api_key` (Required): This is the API key for your Honeycomb account.
* `api_url` (Optional): This sets the hostname to send marker data to. If not set, will default to `https://api.honeycomb.io/`
* `markers` (Required): This is a list of configurations to create an event marker. 
  * `type` (Required): Specifies the marker type.
  * `rules` (Required): This is a list of [OTTL](https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/pkg/ottl) rules that determine when to create an event marker. 
    * `log_conditions` (Required): A list of [OTTL log](https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/pkg/ottl/contexts/ottllog) conditions that determine a match. The marker will be created if **ANY** condition matches.
  * `dataset_slug` (Optional): The dataset in which to create the marker. If not set, will default to `__all__`.
  * `message_key` (Optional): The key of the attribute whose value will be used as the marker's message. If necessary the value will be converted to a string.
  * `url_key` (Optional): The key of the attribute whose value will be used as the marker's url. If necessary the value will be converted to a string.

Example:
```yaml
exporters:
  honeycombmarker:
    api_key: {{env:HONEYCOMB_API_KEY}}
    markers:
      # Creates a new marker anytime the exporter sees a k8s event with a reason of Backoff
      - type: k8s-backoff-events
        rules:
          - log_conditions:
              - IsMap(body) and IsMap(body["object"]) and body["object"]["reason"] == "Backoff"
```