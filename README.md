# OC4IDS Database Notebooks

A collection of notebooks for storing and querying OC4IDS data in a database.

Use the buttons below to open notebooks from the default branch:

Notebook | Colab link | Description
-- | -- | --
[Download, Check and Import](https://github.com/open-contracting/oc4ids_database/blob/main/OC4IDS_Database_Data_Import.ipynb) | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/open-contracting/oc4ids_database/blob/main/OC4IDS_Database_Data_Import.ipynb) | Use this notebook to, download data from OC4IDS publishers, check data using the OC4IDS Data Review Tool and import data and check results into the OC4IDS database.
[Delete Collections](https://github.com/open-contracting/oc4ids_database/blob/main/OC4IDS_Database_Delete_Collections.ipynb) | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/open-contracting/oc4ids_database/blob/main/OC4IDS_Database_Delete_Collections.ipynb) | Use this notebook to delete collections from the database.
[Data Feedback](https://github.com/open-contracting/oc4ids_database/blob/main/OC4IDS_Data_Feedback_Notebook.ipynb) | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/open-contracting/oc4ids_database/blob/main/OC4IDS_Data_Feedback_Notebook.ipynb) | Use this notebook to provide feedback on OC4IDS data.
[Indicator Coverage](https://github.com/open-contracting/oc4ids_database/blob/main/OC4IDS_Indicator_Coverage.ipynb) | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/open-contracting/oc4ids_database/blob/main/OC4IDS_Indicator_Coverage.ipynb) | Use this notebook to calculate the coverage of the [OC4IDS Indicators](https://docs.google.com/spreadsheets/d/1Vo6-Jis-J61PB_33QQx1YKnmQnI7M4rTq6CTMckAFsE/edit#gid=882740051).
[CoST IDS Coverage](https://github.com/open-contracting/oc4ids_database/blob/main/OC4IDS_CoST_IDS_Coverage.ipynb) | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://github.com/open-contracting/oc4ids_database/blob/main/OC4IDS_CoST_IDS_Coverage.ipynb) | Use this notebook to calculate the coverage of the CoST Infrastructure Data Standard (IDS), according to the [CoST IDS to OC4IDS mapping](https://standard.open-contracting.org/infrastructure/latest/en/cost/#cost-ids-to-oc4ids-mapping).

To open a notebook from a different branch, use Colab's [Github browser](https://colab.research.google.com/github/open-contracting/oc4ids_database/) to choose the notebook and branch you want to open.

Alternatively, you can use the Open in Colab browser extension ([Chrome](https://chrome.google.com/webstore/detail/open-in-colab/), [Firefox](https://addons.mozilla.org/en-US/firefox/addon/open-in-colab/)) to add a button that, when clicked when viewing a Jupyter notebook on github, will open that notebook in Colab.

## Database structure

### Entity relationship diagram

The following diagram shows the relationships between the main tables in the database.

Some tables are omitted from the diagram: those containing reference data used in checks ([`oc4ids_schema`](#oc4ids_schema), [`registered_prefixes`](#registered_prefixes) and [`exchange_rates`](#exchange_rates)) and those used to store temporary data as part of the import process ([`temp_data`](#temp_data) and [`temp_checks`](#temp_checks)).

```mermaid
erDiagram
    check_results {
        character_varying check_id PK 
        integer collection_id PK,FK 
        jsonb output 
        boolean result 
        character_varying run_id PK 
    }

    collection {
        timestamp_without_time_zone data_version 
        integer id PK 
        character_varying load_id UK 
        text source_id UK 
    }

    collection_check {
        integer collection_id FK 
        jsonb cove_output 
        integer id PK 
    }

    field_counts {
        integer array_count 
        integer collection_id PK,FK 
        integer distinct_projects 
        integer object_property 
        text path PK 
        ARRAY path_array 
    }

    indicator_coverage {
        numeric checks 
        integer collection_id PK,FK 
        jsonb fields 
        character_varying indicator PK 
        character_varying indicator_source PK 
        character_varying run_id PK 
        numeric successes 
    }

    project_fields {
        integer collection_id PK,FK 
        ARRAY paths 
        text project_id PK 
    }

    projects {
        integer collection_id FK 
        jsonb data 
        integer id PK 
        text project_id 
    }

    run_collection {
        integer collection_id FK,UK 
        integer id PK 
        character_varying run_id UK 
    }

    check_results }o--|| collection : "collection_id"
    collection_check }o--|| collection : "collection_id"
    field_counts }o--|| collection : "collection_id"
    indicator_coverage }o--|| collection : "collection_id"
    project_fields }o--|| collection : "collection_id"
    projects }o--|| collection : "collection_id"
    run_collection }o--|| collection : "collection_id"

```

### Tables

The following table lists all tables in the database. For information on the columns in each table, refer to the subheadings below the table.

| Table                | Description                                                                                                                                                                            |
| ------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [collection](#collection)          | A collection is an OC4IDS dataset.                                                                                                                                                     |
| [collection_check](#collection_check)    | libcoveoc4ids CLI output for each collection, i.e. the results reported by the OC4IDS Data Review Tool. For more information, see https://github.com/open-contracting/lib-cove-oc4ids/ |
| [projects](#projects)            | Projects in OC4IDS JSON format.                                                                                                                                                        |
| [field_counts](#field_counts)        | Counts of the occurrences of fields in each collection.                                                                                                                                  |
| [project_fields](#project_fields)      | The fields present in each project.                                                                                                                                                    |
| [check_results](#check_results)       | Results of running checks on data quality. For more information, see the quality criteria, checks and metrics notebook in https://github.com/open-contracting/notebooks-oc4ids         |
| [run_collection](#run_collection)      | Relationships between collections and check runs.                                                                                                                                      |
| [indicator_coverage](#indicator_coverage)  | Results of running checks on indicator coverage. That is, how often an indicator can be calcuated for a given collection.                                 |
| [oc4ids_schema](#oc4ids_schema)       | A copy of the OC4IDS schema in 'mapping-sheet' format. For more information, see https://ocdskit.readthedocs.io/en/latest/cli/schema.html#mapping-sheet                                |
| [registered_prefixes](#registered_prefixes) | Registered OC4IDS project prefixes. From https://standard.open-contracting.org/infrastructure/latest/en/reference/prefixes/                                                            |
| [exchange_rates](#exchange_rates)      | USD-base exchange rates for currency conversion.                                                                                                                                       |
| [temp_data](#temp_data)           | A temporary table used when importing data.                                                                                                                                            |
| [temp_checks](#temp_checks)         | A temporary table used for storing the libcoveoc4ids output when importing data.                                                                                                       |

#### collection

| Column       | Type                        | Description                                                                     |
| ------------ | --------------------------- | ------------------------------------------------------------------------------- |
| id           | integer                     | A unique identifier for this collection.                                        |
| source_id    | text                        | The OC4IDS publisher from which the data was collected.                         |
| data_version | timestamp without time zone | A timestamp of when the data was imported.                                      |
| load_id      | character varying           | An identifer for a group of collections loaded together.                        |

#### collection_check

| Column        | Type    | Description                                                                                                                      |
| ------------- | ------- | -------------------------------------------------------------------------------------------------------------------------------- |
| id            | integer | A unique identifier for this output.                                                                                             |
| collection_id | integer | The collection to which this output relates.                                                                                     |
| cove_output   | jsonb   | The libcoveoc4ids output. Documentation: https://github.com/open-contracting/lib-cove-ocds?tab=readme-ov-file#output-json-format |

#### projects

| Column        | Type    | Description                                     |
| ------------- | ------- | ----------------------------------------------- |
| id            | integer | A unique identifier for this project.           |
| collection_id | integer | The collection to which this project belongs.   |
| project_id    | text    | The `id` of the project from the JSON data.     |
| data          | jsonb   | The OC4IDS JSON data that descibes the project. |

#### field_counts

| Column            | Type    | Description                                                                                                    |
| ----------------- | ------- | -------------------------------------------------------------------------------------------------------------- |
| collection_id     | integer | The collection to which this count relates.                                                                    |
| path              | text    | The JSON Pointer of the field to which this count relates, excluding array indices.                            |
| path_array        | text[]  | The components of the JSON Pointer of the field to which this count relates.                                   |
| object_property   | integer | The number of occurrences of the field, across all array entries and all releases.                             |                                                                          |
| array_count       | integer | If the field is an array, the cumulative length of its occurrences, across all array entries and all releases. |                                                                          |
| distinct_projects | integer | The number of distinct projects in which the path appears.                                                     |

#### project_fields

| Column        | Type    | Description                                             |
| ------------- | ------- | ------------------------------------------------------- |
| collection_id | integer | The collection to which the project belongs.            |
| project_id    | text    | The `id` of the project.                                |
| paths         | text[]  | The JSON Pointers of the fields present in the project. |

#### check_results

| Column        | Type              | Description                                                                                                                  |
| ------------- | ----------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| run_id        | character varying | The run in which this result was calculated.                                                                                 |
| check_id      | character varying | The check that this result relates to.                                                                                       |
| result        | boolean           | The result of the check. TRUE represents success, FALSE represents failure and NULL indicates that no result was calculated. |
| output        | jsonb             | The output of the check.                                                                                                     |
| collection_id | integer           | The collection to which this check result relates.                                                                           |

#### run_collection

| Column        | Type              | Description                               |
| ------------- | ----------------- | ----------------------------------------- |
| run_id        | character varying | A run of data quality checks.             |
| collection_id | integer           | A collection that was checked in the run. |
| id            | integer           | A unique identifier for the relationship. |

#### indicator_coverage
        
| Column           | Type              | Description                                                   |
| ---------------- | ----------------- | ------------------------------------------------------------- |
| run_id           | character varying | The run in which this coverage result was calculated.         |
| collection_id    | integer           | The collection to which this coverage result relates.         |
| indicator_source | character varying | The source from which the indicator was drawn.                |
| indicator        | character varying | The indicator to which this coverage result relates.          |
| fields           | jsonb             | The fields needed to calculate the indicator.                 |
| successes        | numeric           | A count of objects for which the indicator can be calculated. |
| checks           | numeric           | The number of checks performed.                               |

#### oc4ids_schema

| Column           | Type | Description                                                                                                                                                                                                                                             |
| ---------------- | ---- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| section          | text | The first part of the JSON path to the field in the data, e.g. budget.                                                                                                                                                                                  |
| path             | text | The JSON path to the field in the data, e.g. budget/description.                                                                                                                                                                                        |
| title            | text | The field’s title in the JSON schema. If the field has no title, defaults to the field’s name followed by “*”.                                                                                                                                          |
| description      | text | The field’s description in the JSON schema. URLs are removed (see the links column).                                                                                                                                                                    |
| type             | text | A comma-separated list of the field’s type in the JSON schema, excluding “null”. If the field has no type, defaults to “unknown”.                                                                                                                       |
| range            | text | The field’s allowed number of occurrences: “0..1” if the field defines an optional literal value, “0..n” if the field defines an optional array, “1..1” if the field defines a required literal value, or “1..n” if the field defines a required array. |
| values           | text | If the field’s schema sets: format - the format; pattern - the pattern; enum - “Enum: “ followed by the enum as a comma-separated list, excluding null; items/enum - “Enum: “ followed by the items/enum as a comma-separated list, excluding null.     |
| links            | text | The URLs extracted from the field’s description.                                                                                                                                                                                                        |
| deprecated       | text | The OC4IDS minor version in which the field (or its parent) was deprecated.                                                                                                                                                                             |
| deprecationnotes | text | The explanation for the deprecation of the field.                                                                                                                                                                                                       |

#### registered_prefixes

| Column | Type                  | Description                                                                                                                                      |
| ------ | --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------ |
| prefix | character varying(50) | A registered OC4IDS project prefix. For more information, see https://standard.open-contracting.org/infrastructure/latest/en/reference/prefixes/ |

#### exchange_rates

| Column   | Type              | Description               |
| -------- | ----------------- | ------------------------- |
| date     | date              | The date of the rate.     |
| currency | character varying | The currency of the rate. |
| rate     | numeric           | The rate.                 |     

#### temp_data

| Column | Type  | Description        |
| ------ | ----- | ------------------ |
| data   | jsonb | An OC4IDS dataset. |

#### temp_checks

| Column      | Type  | Description                                |
| ----------- | ----- | ------------------------------------------ |
| cove_output | jsonb | The libcoveoc4ids output for a collection. |       

## Contributing

### Edit a notebook

#### Make your changes

1. Open the notebook in Colab.

2. Make your changes in Colab, following the [style guide for SQL statements](https://ocp-software-handbook.readthedocs.io/en/latest/python/code.html#sql-statements).

3. Format any SQL code you add or edit using [pgFormatter](http://sqlformat.darold.net/).

#### Commit your changes

1. [Create a branch](https://docs.github.com/en/free-pro-team@latest/github/collaborating-with-issues-and-pull-requests/creating-and-deleting-branches-within-your-repository#creating-a-branch).

In Colab:

2. Click Edit -> Clear all outputs.

3. Remove any database credentials you entered into the notebook.

4. Click File -> Save a copy in Github.

4. Uncheck 'Include a link to Colaboratory'

5. Select your branch, enter a commit message and click OK.

#### Request a review

1. [Create a pull request](https://docs.github.com/en/free-pro-team@latest/github/collaborating-with-issues-and-pull-requests/creating-a-pull-request).

2. Request a review from a helpdesk analyst.

3. If the reviewer requests changes, make the changes then repeat this step.

#### Merge your changes

Once approved, you can merge your changes yourself.

### Edit the database structure

1. Make your changes.
2. Add or update [PostgreSQL comments](https://www.postgresql.org/docs/current/sql-comment.html) on new or changed tables and columns.
3. Update any notebooks affected by your changes.
4. Update the [entity relationship diagram](#entity-relationship-diagram) using [mermerd](https://github.com/KarnerTh/mermerd).
5. Update the [table documentation](#tables), the [`psql`](https://www.postgresql.org/docs/current/app-psql.html) meta-commands `\dt+` (to list tables) and `\d+ table_name` (to list columns).

## Reviewing

### Review changes

[Review the changes](https://docs.github.com/en/free-pro-team@latest/github/collaborating-with-issues-and-pull-requests/reviewing-proposed-changes-in-a-pull-request).

For small changes, you can review the raw diff in the Github review interface.

For larger changes, you can review and comment on a visual diff by clicking the <img align="absmiddle"  alt="ReviewNB" height="28" class="BotMessageButtonImage" src="https://raw.githubusercontent.com/ReviewNB/support/master/images/button_reviewnb.png"/> button. You need to authorize the app the first time you open it.
