# Data Contract Specification

The Data Contract Specification is an open initiative to define a common data contract format.
It can be used on its own, or in combination with the [Data Product Specification](https://dataproduct-specification.com).

A [data contract](https://www.datamesh-manager.com/learn/what-is-a-data-contract) is a formal agreement between two parties (the data product provider and the data product consumer) to use a data product. It specifies the guarantees about a provided data set, the purpose of data usage, and costs. Data contracts have a lifecycle to allow the evolution of data products, and they can be used to automate setting of permissions or monitoring data quality via tests.

The specification is based on [PayPal's Data Contract Template](https://github.com/paypal/data-contract-template/blob/main/docs/README.md) and Data Mesh Manager's [Data Contract API](https://app.datamesh-manager.com/swagger/index.html).




Version
---

0.0.1

Example
---

You can use this example as a data contract template for your own data contracts.

```yaml
dataContractSpecification: 0.0.1
info:
  id: 640864de-83d4-4619-afba-ccea8037ed3a
  status: approved
  startDate: 2023-04-01
  endDate:
  noticePeriod: P3M
  nextReassessmentDate: 2024-04-01
provider:
  teamId: checkout
  teamName: Checkout
  dataProductId: orders
  dataProductName: Orders
  outputPortId: bigquery_orders_latest_pii_v1
  outputPortName: bigquery_orders_latest_pii_v1
consumer:
  teamId: marketing
  teamName: Marketing
  dataProductId: funnel_analytics
  dataProductName: Funnel Analytics
terms:
  purpose: "Funnel analysis to understand user behaviors throughout the customer journey and identify conversion problems."
  usage: "Max queries per minute: 10, Max data processing per day: 1 TiB"
  limitations:
  billing: "$500 per month"
schema:
  type: dbt  # the specification format: dbt, jsonschema, protobuf, paypal
  specification:
    version: 2          # the version of the specification
    description: The subset of the output port's data model that we agree to use
    models:
      - name: orders
        description: >
          One record per order. Includes cancelled and deleted orders.
        columns:
          - name: order_id
            type: string
            description: Primary key of the orders table
            tests:
              - unique
              - not_null
          - name: order_timestamp
            type: timestamptz
            description: The business timestamp in UTC when the order was successfully registered in the source system and the payment was successful.
            tests:
              - not_null
      - name: line_items
        description: >
          The items that are part of an order
        columns:
          - name: lines_item_id
            type: string
            description: Primary key of the lines_item_id table
          - name: order_id
            type: string
            description: Foreign key to the orders table
serviceLevelAgreements:
  intervalOfChange: Continuous streaming
  latency: < 60 seconds
  completeness: All orders since 2020-01-01T00:00:00Z
  freshness: Near real time, max. 60 seconds delay
  availability: 99.9%
  performance: Query all orders of last 12 months < 30 seconds
  dataVolume: 5,000-10,000 orders per day expected, ~50 KiB / order
tags:
- business-critical
links:
  schema: https://catalog.example.com/search/search-queries
  catalog: https://catalog.example.com/search/search-queries
custom:
  iamRole: serviceAccount:marketing-data-consumer@example-prod-data.iam.gserviceaccount.com
```

Template
---
We're currently working on providing a template to help you fill out the data contract specification. Stay tuned! 

Tooling
---
- [Data Mesh Manager](https://www.datamesh-manager.com/) supports the data contract specification and allows the user to import or export data contracts using this specification. The lifecycle, including an approval flow, of those data contracts can be managed within the tool as well.

License
---
[MIT License](LICENSE)
