---
keywords: cloud storage destination;cloud storage
title: Cloud Storage destinations overview
description: Adobe Experience Platform can deliver your segments as data files to your Amazon S3, AWS Kinesis, Azure Event Hubs, or SFTP cloud storage locations.
exl-id: d29f0a6e-b323-4f78-bbd0-dee2f1e0fedb
---
# Cloud storage destinations overview {#cloud-storage-destinations}

## Overview {#overview}

Adobe Experience Platform can deliver your segments as data files to your cloud storage locations. This enables you to send audiences and their profile attributes to your internal systems, via CSV files for [!DNL Amazon S3], [!DNL Azure Blob] and SFTP. For [!DNL Amazon Kinesis] and [!DNL Azure Event Hubs] destinations, data is streamed out of Experience Platform in [!DNL JSON] format.

![Adobe cloud storage destinations](../../assets/catalog/cloud-storage/cloud-storage-destinations.png)

## Supported cloud storage destinations {#supported-destinations}

Adobe Experience Platform supports the following cloud storage destinations:

* [Amazon Kinesis connection](amazon-kinesis.md)
* [Amazon S3 connection](amazon-s3.md)
* [Azure Blob connection](azure-blob.md)
* [Azure Event Hubs connection](azure-event-hubs.md)
* [SFTP connection](sftp.md)

## Connect to a new cloud storage destination {#connect-destination}

To send segments to cloud storage destinations for your campaigns, Platform must first connect to the destination. See the [destination creation tutorial](../../ui/connect-destination.md) for detailed information on setting up a new destination.


## Use macros to create a folder in your storage location {#use-macros}

>[!NOTE]
>
> The functionality described in this section is currently available for [Amazon S3](amazon-s3.md) destinations only.

To create a custom folder per segment file in your storage location, you can use macros in the folder path input field. Insert the macros at the end of the input field, as shown below.

![How to use macros to create a folder in your storage](../../assets/catalog/cloud-storage/workflow/macros-folder-path.png)  

The examples below reference a sample segment `Luxury Audience` with ID `25768be6-ebd5-45cc-8913-12fb3f348615`.

**Macro 1: `%SEGMENT_NAME%`**

Input: `acme/campaigns/2021/%SEGMENT_NAME%`
Folder path in your storage location: `acme/campaigns/2021/Luxury Audience`

**Macro 2: `%SEGMENT_ID%`**

Input: `acme/campaigns/2021/%SEGMENT_ID%`
Folder path in your storage location: `acme/campaigns/2021/25768be6-ebd5-45cc-8913-12fb3f348615`

**Macro 3: `%SEGMENT_NAME%/%SEGMENT_ID%`**

Input: `acme/campaigns/2021/%SEGMENT_NAME%/%SEGMENT_ID%`
Folder path in your storage location: `acme/campaigns/2021/Luxury Audience/25768be6-ebd5-45cc-8913-12fb3f348615`

## Data export type {#export-type}

Cloud storage destinations support **Profile-based export**. This means that you are exporting details about the individuals in the audience. These details are needed for personalization and can include attributes, events, segment memberships, and more.