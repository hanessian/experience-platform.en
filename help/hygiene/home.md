---
title: Data Hygiene Overview
description: Adobe Experience Platform Data Hygiene allows you to manage the lifecycle of your data by updating or purging outdated or inaccurate records.
exl-id: 104a2bb8-3242-4a20-b98d-ad6df8071a16
---
# Data hygiene in Adobe Experience Platform

>[!IMPORTANT]
>
>Data hygiene is currently only available for organizations that have purchased Adobe Healthcare Shield.

Adobe Experience Platform provides a robust set of tools to manage large, complicated data operations in order to orchestrate consumer experiences. As data is ingested into the system over time, it becomes increasingly important to manage your data stores so that data is used as expected, is updated when incorrect data needs correcting, and is deleted when organizational policies deem it necessary.

Platform's data hygiene capabilities allow you to manage your stored consumer data through the following:

* Scheduling automated dataset expirations
* Deleting consumer data based on ingested identities

These activities can be performed using the [[!UICONTROL Data Hygiene] UI workspace](#ui) or the [Data Hygiene API](#api). When a data hygiene job executes, the system provides transparency updates at each step of process. See the section on [timelines and transparency](#timelines-and-transparency) for more information on how each job type is represented in the system.

## [!UICONTROL Data Hygiene] UI workspace {#ui}

The [!UICONTROL Data Hygiene] workspace in the Platform UI allows you to configure and schedule data hygiene operations, helping to ensure that your records are being maintained as expected.

For detailed steps on managing data hygiene tasks in the UI, see the [Data Hygiene UI guide](./ui/overview.md).

## Data Hygiene API {#api}

The [!UICONTROL Data Hygiene] UI is built on top of the Data Hygiene API, whose endpoints are available for you to use directly if you prefer to automate your data hygiene activities. See the [Data Hygiene API guide](./api/overview.md) for more information.

## Timelines and transparency

Consumer delete and dataset expiration requests each have their own processing timelines and provide transparency updates at key points in their respective workflows. Refer to the sections below for details on each job type.

### Dataset expirations {#dataset-expiration-transparency}

The following takes place when a [dataset expiration request](./ui/dataset-expiration.md) is created:

| Stage | Time after scheduled expiration | Description |
| --- | --- | --- |
| Request is submitted | 0 hours | A data steward or privacy analyist submits a request for a dataset to expire at a given time. The request is visible in the [!UICONTROL Data Hygiene UI] after it has been submitted, and remains in a pending status until the scheduled expiration time, after which the request will execute. |
| Dataset is dropped | 1 hour | The dataset is dropped from the [dataset inventory page](../catalog/datasets/user-guide.md) in the UI. The data within the data lake is only soft deleted, and will remain so until the end of the process, after which it will be hard deleted. |
| Profile count updated | 30 hours | The change in profile counts caused by the dataset expiration are reflected in [dashboard widgets](../dashboards/guides/profiles.md#profile-count-trend) and other reports. |
| Segments updated | 48 hours | Once profiles are removed, all related [segments](../segmentation/home.md) are updated to reflect their new size. |
| Journeys and destinations updated | 50 hours | [Journeys](https://experienceleague.adobe.com/docs/journey-optimizer/using/orchestrate-journeys/about-journeys/journey.html), [campaigns](https://experienceleague.adobe.com/docs/journey-optimizer/using/campaigns/get-started-with-campaigns.html), and [destinations](../destinations/home.md) are updated according to changes in related segments. |
| Hard deletion complete | 14 days | All data related to the dataset is hard deleted from the data lake. The [status of the hygiene job](./ui/browse.md#view-details) that deleted the dataset is updated to reflect this. |

{style="table-layout:auto"}

### Consumer deletes {#consumer-delete-transparency}

The following takes place when a [consumer delete request](./ui/delete-consumer.md) is created:

| Stage | Time after request submission | Description |
| --- | --- | --- |
| Request is submitted | 0 hours | A data steward or privacy analyist submits a consumer delete request. The request is visible in the [!UICONTROL Data Hygiene UI] after it has been submitted. |
| Profile lookups updated | 3 hours | The change in profile counts caused by the deleted identity are reflected in [dashboard widgets](../dashboards/guides/profiles.md#profile-count-trend) and other reports. |
| Segments updated | 24 hours | Once profiles are removed, all related [segments](../segmentation/home.md) are updated to reflect their new size. |
| Journeys and destinations updated | 26 hours | [Journeys](https://experienceleague.adobe.com/docs/journey-optimizer/using/orchestrate-journeys/about-journeys/journey.html), [campaigns](https://experienceleague.adobe.com/docs/journey-optimizer/using/campaigns/get-started-with-campaigns.html), and [destinations](../destinations/home.md) are updated according to changes in related segments. |
| Records soft deleted in data lake | 7 days | The data is soft deleted from the data lake. |
| Data vacuuming completed | 14 days | The [status of the hygiene job](./ui/browse.md#view-details) updates to indicate that the job has completed, meaning that data vacuuming has been completed on the data lake and the relevant records have been hard deleted. |

{style="table-layout:auto"}

## Next steps

This document provided an overview of Platform's data hygiene capabilities. To get started making data hygiene requests in the UI, refer to the [UI guide](./ui/overview.md). To learn how to create data hygiene jobs programmatically, refer to the [Data Hygiene API guide](./api/overview.md)
