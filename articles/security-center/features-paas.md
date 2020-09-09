---
title: サポートされている Azure PaaS リソース用の Azure Security Center 機能。
description: このページでは、サポートされている Azure PaaS リソース用の Azure Security Center 機能の可用性について説明します。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2020
ms.author: memildin
ms.openlocfilehash: 539b663366cd2820eec62a18d1fc2989c6b3c9b0
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2020
ms.locfileid: "86042358"
---
# <a name="feature-coverage-for-azure-paas-services"></a><a name="paas-services">Azure PaaS サービスを対象とする機能</a>

次の表に、サポートされている Azure PaaS リソース用の Azure Security Center 機能の可用性を示します。

|サービス|推奨事項 (無料)|セキュリティ アラート (Standard)|脆弱性評価 (標準)|
|:----|:----:|:----:|:----:|
|Azure App Service|✔|✔|-|
|Azure Automation アカウント|✔|-|-|
|Azure Batch アカウント|✔|-|-|
|Azure Blob Storage|✔|✔|-|
|Azure Cache for Redis|✔|-|-|
|Azure クラウド サービス|✔|-|-|
|Azure Cognitive Search|✔|-|-|
|Azure Container Registry|-|-|✔|
|Azure Cosmos DB*|-|✔|-|
|Azure Data Lake Analytics|✔|-|-|
|Azure Data Lake Storage|✔|-|-|
|Azure Database for MySQL*|✔|✔|-|
|Azure Database for PostgreSQL*|✔|✔|-|
|Azure Event Hubs 名前空間|✔|-|-|
|Azure Functions アプリ|✔|-|-|
|Azure Key Vault|✔|✔ *|-|
|Azure Kubernetes Service|✔|✔|-|
|Azure Load Balancer|✔|-|-|
|Azure Logic Apps|✔|-|-|
|Azure SQL データベース|✔|✔|✔|
|Azure SQL Managed Instance|✔|✔|✔|
|Azure Service Bus 名前空間|✔|-|-|
|Azure Service Fabric アカウント|✔|-|-|
|Azure Storage アカウント|✔|-|-|
|Azure Stream Analytics|✔|-|-|
|Azure サブスクリプション|✔ **|✔|-|
|Azure Virtual Network</br> (サブネット、NIC、ネットワーク セキュリティ グループを含む)|✔|-|-|

\* 現在、これらの機能はプレビューでサポートされています。

\*\* Azure Active Directory (Azure AD) のレコメンデーションは、Standard サブスクリプションでのみ使用できます。