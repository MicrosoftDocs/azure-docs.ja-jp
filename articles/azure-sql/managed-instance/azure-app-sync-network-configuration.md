---
title: Azure App Service のネットワーク構成の同期
titleSuffix: Azure SQL Managed Instance
description: この記事では、Azure SQL Managed Instance を使用して、Azure App Service のホスティング プラン用にネットワーク構成を同期する方法について説明します。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 12/13/2018
ms.openlocfilehash: a0192f204fdb0797d98947a174bf6d669034b666
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84695455"
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan-with-azure-sql-managed-instance"></a>Azure SQL Managed Instance を使用してネットワーク構成を Azure App Service のホスティング プラン用に同期する
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

場合によっては、[アプリを Azure Virtual Network と統合したにもかかわらず](../../app-service/web-sites-integrate-with-vnet.md)、SQL Managed Instance への接続を確立できないことがあります。 サービス プランのネットワーク構成を更新または同期することで、この問題を解決できる場合があります。 

## <a name="sync-network-configuration"></a>ネットワーク構成の同期 

そのためには、次の手順に従います。  

1. Web アプリの App Service プランに移動します。

   ![App Service プランのスクリーンショット](./media/azure-app-sync-network-configuration/app-service-plan.png)

2. **[ネットワーク]** を選択し、 **[管理するにはここをクリック]** を選択します。

   ![サービス プランの管理のスクリーンショット](./media/azure-app-sync-network-configuration/manage-plan.png)

3. 目的の **VNet** を選択し、 **[ネットワークの同期]** をクリックします。

   ![ネットワークの同期のスクリーンショット](./media/azure-app-sync-network-configuration/sync.png)

4. 同期が完了するまで待機します。
  
   ![同期の完了のスクリーンショット](./media/azure-app-sync-network-configuration/sync-done.png)

これで、SQL Managed Instance への接続を再確立する準備が整いました。

## <a name="next-steps"></a>次のステップ

- VNet を SQL Managed Instance 用に構成する方法については、[SQL Managed Instance VNet アーキテクチャ](connectivity-architecture-overview.md)および[既存の VNet を構成する方法](vnet-existing-add-subnet.md)に関する記事を参照してください。
