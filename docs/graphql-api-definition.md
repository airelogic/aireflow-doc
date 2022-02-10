# GraphQL Api Definition

The following document outlines the AireFlow GraphQL API

## Tasks

The tasks query can be used to read all instantiated tasks. There are currently no mutations available via GraphQL and any changes will need to be made via the REST API.

### Query Structure

The following properties can be retrieved via the GraphQL API

```
query {
    tasks {
        pageCount
        nodes {
            id
            name
            created
            updated
            lastRun
            subjectId
            workflowDefinitionVersion
            workflowDefinitionId
            filters
            context
            activityContext {
                status
                    ...on ManualActivityContext {
                        due
                    }
                    ...on EmailActivityContext {
                        to
                        subject
                        body
                    }
                    ...on HttpActivityContext {
                        url
                        contentType
                        body
                        correlationId
                    }
            }
            description
            assigneeId
            state {
                key
                value
            }
            taskType
            triggeredUpdates {
                routingKey
                update
            }
        }
    }
}
```

### Filtering

The filter parameter is used as follows. Each item in the filter array must be true for a task to be included in the result:

```
query {
    tasks (
        filter:[{
            property: "activityContext.status"
            condition: EQ
            value: ["New", "InProgress"]
    },
    {
            property: "assigneeId"
            condition: EQ
            value: "1"
    },
    {
            property: "state[comments]"
            condition: INCLUDES
            value: "Some Comment"
    }
    ]) {
        ...
    }
}
```

Where _property_ is the name of the property to filter, _condition_ defines what type of filter is applied and _value_ is the value the condition is checked against. The value can be a string or an array of strings (in some cases). In the above example, the status must be either new or in progress, the assigneeId must be 1 and there is a state item called "comments" who's value includes the substring "Some Comment".

In order to access a nested property, separate them using a '.' as above.

For dictionaries, use `{property}.key` to filter on the keys, use `{property}[{key name}]` to filter on the value of a specific key.

#### Types of Filter

Each property type has a subset of valid conditions listed here.

| Property Type    | Valid Conditions | Accepts Array Value | True When                                                                     |
| ---------------- | ---------------- | ------------------- | ----------------------------------------------------------------------------- |
| string           | EQ               | True                | Property value equals one of the input values (Case-Sensitive)                |
|                  | NOT              | True                | Property value does not equal one of the input values (Case-Sensitive)        |
|                  | INCLUDES         | True                | One of the input values is a substring of the property value (Case-Sensitive) |
| DateTime         | LT               | False               | Property value is before than the input value                                 |
|                  | GT               | False               | Property value is after the input value                                       |
| Int              | EQ               | False               | Property value is equal to the input value                                    |
|                  | GT               | False               | Property value is greater than the input value                                |
|                  | LT               | False               | Property value is less than the input value                                   |
| List             | AnyEq            | False               | Property list contains the input value                                        |
|                  | NoneEq           | False               | Property list does not contain the input value                                |
| Enum             | EQ               | True                | Property value equals one of the input values                                 |
|                  | NOT              | True                | Property value does not equal one of the input values                         |
|                  | INCLUDES         | True                | One of the input values is a substring of the property value                  |
| Dictionary Key   | AnyEq            | True                | Input value is a key in the dictionary                                        |
|                  | NoneEq           | True                | Input value is not a key in the dictionary                                    |
| Dictionary Value | EQ               | True                | Property value equals one of the input values (Case-Sensitive)                |
|                  | NOT              | True                | Property value does not equal one of the input values (Case-Sensitive)        |
|                  | INCLUDES         | True                | One of the input values is a substring of the property value (Case-Sensitive) |

### Sorting

The sorting parameter is used as follows. The direction is either ASCENDING or DESCENDING.

```
query {
    tasks (
        sort: {
            property: "activityContext.due"
            direction: ASCENDING
    }) {
        ...
    }
}
```

### Paging

The paging parameter is used as follows.

```
query {
    tasks (
        paging: {
            pageNumber: 1,
            pageSize: 50
    }) {
        ...
    }
}
```
