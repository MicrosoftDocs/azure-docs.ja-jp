---
title: Azure App Service - ネットワーク構成の同期 | Microsoft Docs
description: この記事では、Azure App Service のホスティング プラン用にネットワーク構成を同期する方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
manager: craigg
ms.date: 12/13/2018
ms.openlocfilehash: 0d7920080fd61389741fbe785f5141003bef5251
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59797286"
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan"></a>ネットワーク構成を Azure App Service のホスティング プラン用に同期する

場合によっては、[アプリを Azure Virtual Network と統合したにもかかわらず](../app-service/web-sites-integrate-with-vnet.md)、マネージド インスタンスへの接続を確立できないことがあります。 その場合の対処の 1 つとして、使用するサービス プラン用にネットワーク構成を更新するという対処方法があります。

## <a name="sync-network-configuration-for-app-service-hosting-plan"></a>ネットワーク構成を App Service のホスティング プラン用に同期する

そのためには、次の手順に従います。  

1. Web アプリの App Service プランに移動します。

   ![App Service プラン](./media/sql-database-managed-instance-sync-networking/app-service-plan.png)

2. **[ネットワーク]** をクリックし、**[管理するにはここをクリック]** をクリックします。

   ![サービス プランの管理](./media/sql-database-managed-instance-sync-networking/manage-plan.png)

3. 目的の **VNet** を選択し、**[ネットワークの同期]** をクリックします。

   ![ネットワークの同期](./media/sql-database-managed-instance-sync-networking/sync.png)

4. 同期が完了するまで待機します。
  
   ![同期の完了](./media/sql-database-managed-instance-sync-networking/sync-done.png)

これで、マネージド インスタンスへの接続を再確立する準備が整いました。

## <a name="next-steps"></a>次の手順

- VNet をマネージド インスタンス用に構成する方法については、[マネージド インスタンス VNet アーキテクチャ](sql-database-managed-instance-connectivity-architecture.md)および[既存の VNet を構成する方法](sql-database-managed-instance-configure-vnet-subnet.md)に関する記事を参照してください。
