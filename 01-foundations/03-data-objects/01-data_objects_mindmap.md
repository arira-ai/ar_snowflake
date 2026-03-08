Snowflake data objects form a hierarchical structure used to organize, store, and manage data. The mindmap below shows how different objects relate to each other inside a Snowflake environment. It starts from the Snowflake account and expands into databases, schemas, and the different data objects used for storage, transformation, ingestion, and automation.

```mermaid
mindmap
  root((Snowflake Data Objects))
    Account
      Database
        Schema
          Storage
            Tables
              Permanent
              Transient
              Temporary
              External
              Iceberg
          Views_and_Logic
            Views
              Standard
              Secure
              Materialized
            Dynamic_Tables
            Procedures_and_UDFs
          Data_Ingestion
            Stages
              Internal
              External_eg_AWS_S3
            Pipes_Snowpipe
            File_Formats
          Data_Pipelines
            Streams_CDC
            Tasks_Scheduling
          Data_Governance
            Masking_Policies
            Row_Access_Policies
```
