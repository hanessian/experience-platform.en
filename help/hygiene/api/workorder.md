---
title: Work order API Endpoint
description: The /workorder endpoint in the Data Hygiene API allows you to programmatically manage deletion tasks for consumer identities.
exl-id: f6d9c21e-ca8a-4777-9e5f-f4b2314305bf
---
# Work order endpoint

>[!IMPORTANT]
>
>Data hygiene capabilities in Adobe Experience Platform are currently only available for organizations that have purchased Adobe Healthcare Shield.

The `/workorder` endpoint in the Data Hygiene API allows you to programmatically manage consumer delete requests in Adobe Experience Platform.

## Getting started

The endpoint used in this guide is part of the Data Hygiene API. Before continuing, please review the [overview](./overview.md) for links to related documentation, a guide to reading the sample API calls in this document, and important information regarding required headers that are needed to successfully make calls to any Experience Platform API.

## Create a consumer delete request {#delete-consumers}

You can delete one or more consumer identities from a single dataset or all datasets by making a POST request to the `/workorder` endpoint.

**API format**

```http
POST /workorder
```

**Request**

Depending on the value of the `datasetId` provided in the request payload, the API call will delete consumer identities from all datasets or a single dataset that you specify. The following request deletes three consumer identities from a specific dataset.

```shell
curl -X POST \
  https://platform.adobe.io/data/core/hygiene/workorder \
  -H 'Authorization: Bearer {ACCESS_TOKEN}' \
  -H 'x-api-key: {API_KEY}' \
  -H 'x-gw-ims-org-id: {ORG_ID}' \
  -H 'Content-Type: application/json' \
  -H 'x-sandbox-name: {SANDBOX_NAME}' \
  -d '{
        "action": "delete_identity",
        "datasetId": "c48b51623ec641a2949d339bad69cb15",
        "displayName": "Example Consumer Delete Request",
        "description": "Cleanup identities required by Jira request 12345.",
        "identities": [
          {
            "namespace": {
              "code": "email"
            },
            "id": "poul.anderson@example.com"
          },
          {
            "namespace": {
              "code": "email"
            },
            "id": "cordwainer.smith@gmail.com"
          },
          {
            "namespace": {
              "code": "email"
            },
            "id": "cyril.kornbluth@yahoo.com"
          }
        ]
      }'
```

| Property | Description |
| --- | --- |
| `action` | The action to be performed. The value must be set to `delete_identity` for consumer deletes. |
| `datasetId` | If you are deleting from a single dataset, this value must be the ID of the dataset in question. If you are deleting from all datasets, set the value to `ALL`.<br><br>If you are specifying a single dataset, the dataset's associated Experience Data Model (XDM) schema must have a primary identity defined. |
| `displayName` | The display name for the consumer delete request. |
| `description` | A description for the consumer delete request. |
| `identities` | An array containing the identities of at least one user whose information you would like to delete. Each identity is comprised of an [identity namespace](../../identity-service/namespaces.md) and a value:<ul><li>`namespace`: Contains a single string property, `code`, which represents the identity namespace. </li><li>`id`: The identity value.</ul>If `datasetId` specifies a single dataset, each entity under `identities` must use the same identity namespace as the schema's primary identity.<br><br>If `datasetId` is set to `ALL`, the `identities` array is not constrained to any single namespace since each dataset might be different. However, your requests are still constrained the namespaces available to your organization, as reported by [Identity Service](https://developer.adobe.com/experience-platform-apis/references/identity-service/#operation/getIdNamespaces). |

{style="table-layout:auto"}

**Response**

A successful response returns the details of the consumer delete.

```json
{
  "workorderId": "a15345b8-a2d6-4d6f-b33c-5b593e86439a",
  "orgId": "{ORG_ID}",
  "bundleId": "BN-35c1676c-3b4f-4195-8d6c-7cf5aa21efdd",
  "action": "identity-delete",
  "createdAt": "2022-07-21T18:05:28.316029Z",
  "updatedAt": "2022-07-21T17:59:43.217801Z",
  "status": "received",
  "createdBy": "{USER_ID}",
  "datasetId": "c48b51623ec641a2949d339bad69cb15",
  "displayName": "Example Consumer Delete Request",
  "description": "Cleanup identities required by Jira request 12345."
}
```

| Property | Description |
| --- | --- |
| `workorderId` | The ID of the deletion order. This can be used to look up the status of the deletion later. |
| `orgId` | Your organization ID. |
| `bundleId` | The ID of the bundle this deletion order is associated with, used for debugging purposes. Multiple deletion orders are bundled together to be processed by downstream services. |
| `action` | The action being performed by the work order. For consumer deletes, the value is `identity-delete`. |
| `createdAt` | A timestamp of when the deletion order was created. |
| `updatedAt` | A timestamp of when the deletion order was last updated. |
| `status` | The current status of the deletion order. |
| `createdBy` | The user that created the deletion order. |
| `datasetId` | The ID of the dataset that is subject to the request. If the request is for all datasets, the value will be set to `ALL`. |

{style="table-layout:auto"}

## Retrieve the status of a consumer delete (#lookup)

After [creating a consumer delete request](#delete-consumers), you can check on its status using a GET request.

**API format**

```http
GET /workorder/{WORK_ORDER_ID}
```

| Parameter | Description |
| --- | --- |
| `{WORK_ORDER_ID}` | The `workorderId` of the consumer delete you are looking up. |

{style="table-layout:auto"}

**Request**

```shell
curl -X GET \
  https://platform.adobe.io/data/core/hygiene/workorder/BN-35c1676c-3b4f-4195-8d6c-7cf5aa21efdd \
  -H 'Authorization: Bearer {ACCESS_TOKEN}' \
  -H 'x-api-key: {API_KEY}' \
  -H 'x-gw-ims-org-id: {ORG_ID}' \
  -H 'x-sandbox-name: {SANDBOX_NAME}'
```

**Response**

A successful response returns the details of the delete operation, including its current status.

```json
{
  "workorderId": "a15345b8-a2d6-4d6f-b33c-5b593e86439a",
  "orgId": "{ORG_ID}",
  "bundleId": "BN-35c1676c-3b4f-4195-8d6c-7cf5aa21efdd",
  "action": "identity-delete",
  "createdAt": "2022-07-21T18:05:28.316029Z",
  "updatedAt": "2022-07-21T17:59:43.217801Z",
  "status": "received",
  "createdBy": "{USER_ID}",
  "datasetId": "c48b51623ec641a2949d339bad69cb15",
  "displayName": "Example Consumer Delete Request",
  "description": "Cleanup identities required by Jira request 12345.",
  "productStatusDetails": [
    {
        "productName": "Data Management",
        "productStatus": "success",
        "createdAt": "2022-08-08T16:51:31.535872Z"
    },
    {
        "productName": "Identity Service",
        "productStatus": "success",
        "createdAt": "2022-08-08T16:43:46.331150Z"
    },
    {
        "productName": "Profile Service",
        "productStatus": "waiting",
        "createdAt": "2022-08-08T16:37:13.443481Z"
    }
  ]
}
```

| Property | Description |
| --- | --- |
| `workorderId` | The ID of the deletion order. This can be used to look up the status of the deletion later. |
| `orgId` | Your organization ID. |
| `bundleId` | The ID of the bundle this deletion order is associated with, used for debugging purposes. Multiple deletion orders are bundled together to be processed by downstream services. |
| `action` | The action being performed by the work order. For consumer deletes, the value is `identity-delete`. |
| `createdAt` | A timestamp of when the deletion order was created. |
| `updatedAt` | A timestamp of when the deletion order was last updated. |
| `status` | The current status of the deletion order. |
| `createdBy` | The user that created the deletion order. |
| `datasetId` | The ID of the dataset that is subject to the request. If the request is for all datasets, the value will be set to `ALL`. |
| `productStatusDetails` | An array that lists the current status of downstream processes related to the request. Each array object contains the following properties:<ul><li>`productName`: The name of the downstream service.</li><li>`productStatus`: The current processing status of the request from the downstream service.</li><li>`createdAt`: A timestamp of when the most recent status was posted by the service.</li></ul> |

## Update a consumer delete request

You can update the `displayName` and `description` for a consumer delete by making a PUT request.

**API format**

```http
PUT /workorder{WORK_ORDER_ID}
```

| Parameter | Description |
| --- | --- |
| `{WORK_ORDER_ID}` | The `workorderId` of the consumer delete you are looking up. |

{style="table-layout:auto"}

**Request**

```shell
curl -X GET \
  https://platform.adobe.io/data/core/hygiene/workorder/BN-35c1676c-3b4f-4195-8d6c-7cf5aa21efdd \
  -H 'Authorization: Bearer {ACCESS_TOKEN}' \
  -H 'x-api-key: {API_KEY}' \
  -H 'x-gw-ims-org-id: {ORG_ID}' \
  -H 'x-sandbox-name: {SANDBOX_NAME}' \
  -d '{
        "displayName" : "Update - displayName",
        "description" : "Update - description"
      }'
```

| Property | Description |
| --- | --- |
| `displayName` | An updated display name for the consumer delete request. |
| `description` | An updated description for the consumer delete request. |

{style="table-layout:auto"}

**Response**

A successful response returns the details of the consumer delete.

```json
{
  "workorderId": "a15345b8-a2d6-4d6f-b33c-5b593e86439a",
  "orgId": "{ORG_ID}",
  "bundleId": "BN-35c1676c-3b4f-4195-8d6c-7cf5aa21efdd",
  "action": "identity-delete",
  "createdAt": "2022-07-21T18:05:28.316029Z",
  "updatedAt": "2022-07-21T17:59:43.217801Z",
  "status": "received",
  "createdBy": "{USER_ID}",
  "datasetId": "c48b51623ec641a2949d339bad69cb15",
  "displayName" : "Update - displayName",
  "description" : "Update - description",
  "productStatusDetails": [
    {
        "productName": "Data Management",
        "productStatus": "success",
        "createdAt": "2022-08-08T16:51:31.535872Z"
    },
    {
        "productName": "Identity Service",
        "productStatus": "success",
        "createdAt": "2022-08-08T16:43:46.331150Z"
    },
    {
        "productName": "Profile Service",
        "productStatus": "waiting",
        "createdAt": "2022-08-08T16:37:13.443481Z"
    }
  ]
}
```

| Property | Description |
| --- | --- |
| `workorderId` | The ID of the deletion order. This can be used to look up the status of the deletion later. |
| `orgId` | Your organization ID. |
| `bundleId` | The ID of the bundle this deletion order is associated with, used for debugging purposes. Multiple deletion orders are bundled together to be processed by downstream services. |
| `action` | The action being performed by the work order. For consumer deletes, the value is `identity-delete`. |
| `createdAt` | A timestamp of when the deletion order was created. |
| `updatedAt` | A timestamp of when the deletion order was last updated. |
| `status` | The current status of the deletion order. |
| `createdBy` | The user that created the deletion order. |
| `datasetId` | The ID of the dataset that is subject to the request. If the request is for all datasets, the value will be set to `ALL`. |
| `productStatusDetails` | An array that lists the current status of downstream processes related to the request. Each array object contains the following properties:<ul><li>`productName`: The name of the downstream service.</li><li>`productStatus`: The current processing status of the request from the downstream service.</li><li>`createdAt`: A timestamp of when the most recent status was posted by the service.</li></ul> |

{style="table-layout:auto"}
