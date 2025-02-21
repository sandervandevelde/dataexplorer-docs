---
title: arg_max() (aggregation function) - Azure Data Explorer
description: This article describes arg_max() (aggregation function) in Azure Data Explorer.
ms.reviewer: alexans
ms.topic: reference
ms.date: 07/05/2022
---
# arg_max() (aggregation function)

Finds a row in the group that maximizes *ExprToMaximize*.

[!INCLUDE [data-explorer-agg-function-summarize-note](../../includes/data-explorer-agg-function-summarize-note.md)]

## Syntax

`arg_max` `(`*ExprToMaximize*`,` *\** | *ExprToReturn*  [`,` ...]`)`

## Arguments

| Name | Type | Required | Description |
|--|--|--|--|
| *ExprToMaximize* | string | &check; | Expression used for aggregation calculation. |
| *ExprToReturn* | string | &check; | Expression used for returning the value when *ExprToMaximize* is maximum.  Use a `*` to return all columns of the input table. |

## Returns

Returns a row in the group that maximizes *ExprToMaximize*, and the values of columns specified in *ExprToReturn*.

## Examples

The following examples demonstrate how to use this function.

**Example 1**

Find the northern most location of a storm event in each state.

**\[**[**Click to run query**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAwsuyS/KdS1LzSspVuCqUSguzc1NLMqsSlVILEqPz02s0HBKTc/M80ks0VGAsPKTE0sy8/M0FZIqFYJLEktSATqyPZtCAAAA)**\]**

```kusto
StormEvents 
| summarize arg_max(BeginLat, BeginLocation) by State
```

**Results**

The results table displays only the first 10 rows.

| State                | BeginLat | BeginLocation        |
| -------------------- | -------- | -------------------- |
| MISSISSIPPI          | 34.97    | BARTON               |
| VERMONT              | 45       | NORTH TROY           |
| AMERICAN SAMOA       | -14.2    | OFU                  |
| HAWAII               | 22.2113  | PRINCEVILLE          |
| MINNESOTA            | 49.35    | ARNESEN              |
| RHODE ISLAND         | 42       | WOONSOCKET           |
| INDIANA              | 41.73    | FREMONT              |
| WEST VIRGINIA        | 40.62    | CHESTER              |
| SOUTH CAROLINA       | 35.18    | LANDRUM              |
| TEXAS                | 36.4607  | DARROUZETT           |
| ...             | ...    | ...            |

**Example 2**

Find the first time an event with a direct death happened in each state showing all the columns.

**\[**[**Click to run query**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAwsuyS/KdS1LzSsp5qpRKM9ILUpVcElNLMkodsksSk0uUbBTMABKFJfm5iYWZValKiQWpcfnJlZoBJckFpWEZOam6ihoaSokVSoABUpSAQPollZPAAAA)**\]**

```kusto
StormEvents
| where DeathsDirect > 0
| summarize arg_max(StartTime, *) by State
```

**Results**

The results table displays only the first 10 rows and first 3 columns.

| State          | StartTime            | EndTime              | ... |
| -------------- | -------------------- | -------------------- | --- |
| GUAM           | 2007-01-27T11:15:00Z | 2007-01-27T11:30:00Z | ... |
| MASSACHUSETTS  | 2007-02-03T22:00:00Z | 2007-02-04T10:00:00Z | ... |
| AMERICAN SAMOA | 2007-02-17T13:00:00Z | 2007-02-18T11:00:00Z | ... |
| IDAHO          | 2007-02-17T13:00:00Z | 2007-02-17T15:00:00Z | ... |
| DELAWARE       | 2007-02-25T13:00:00Z | 2007-02-26T01:00:00Z | ... |
| WYOMING        | 2007-03-10T17:00:00Z | 2007-03-10T17:00:00Z | ... |
| NEW MEXICO     | 2007-03-23T18:42:00Z | 2007-03-23T19:06:00Z | ... |
| INDIANA        | 2007-05-15T14:14:00Z | 2007-05-15T14:14:00Z | ... |
| MONTANA        | 2007-05-18T14:20:00Z | 2007-05-18T14:20:00Z | ... |
| LAKE MICHIGAN  | 2007-06-07T13:00:00Z | 2007-06-07T13:00:00Z | ... |
|... | ... | ...| ... |

**Example 3**

The following example demonstrates null handling.

**\[**[**Click to run query**](https://dataexplorer.azure.com/clusters/kvc6bc487453a064d3c9de.northeurope/databases/new-free-database?query=H4sIAAAAAAAAA31PwQrCMAy97ytCT530osfdnKBX8SCIiHQsjEKWjrRjKH68nWwoguYdkry8l5DaxoSKUG+ld7GAEMVxY2Djycu7PaIE57kAxzGHcwYp1LrrCJUBdcA6paX5oneCyKlIHs09UT4JSssJo+KERH74K/m1ZI9WxnkpfuCP6zM/+1Ymu2QPCH3bWnF3BCvNtXWsp5cMLHKobvD6/wlU5dHuDwEAAA==)**\]**

```kusto
datatable(Fruit: string, Color: string, Version: int) [
    "Apple", "Red", 1,
    "Apple", "Green", int(null),
    "Banana", "Yellow", int(null),
    "Banana", "Green", int(null),
    "Pear", "Brown", 1,
    "Pear", "Green", 2,
]
| summarize arg_max(Version, *) by Fruit
```
**Results**

| Fruit | Version | Color |
|--|--|--|
| Apple | 1 | Red |
| Banana |  | Yellow |
| Pear | 2 | Green |
