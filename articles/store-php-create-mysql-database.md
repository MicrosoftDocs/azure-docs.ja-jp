---
title: "Azure で MySQL データベースを作成して接続する"
description: "Azure ポータルを使用して MySQL データベースを作成し、Azure の PHP Web アプリからそのデータベースに接続する方法について説明します。"
documentationcenter: php
services: app-service\web
author: cephalin
manager: erikre
editor: 
tags: mysql
ms.assetid: 55465a9a-7e65-4fd9-8a65-dd83ee41f3e5
ms.service: multiple
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm;cephalin
ms.openlocfilehash: c072cb3a7d376d1e3c2b9f741f5410106e701256
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-connect-to-a-mysql-database-in-azure"></a>Azure で MySQL データベースを作成して接続する
このチュートリアルでは、[Azure Portal](https://portal.azure.com) (プロバイダーは [ClearDB](http://www.cleardb.com/)) で MySQL データベースを作成する方法と、[Azure App Service](app-service/app-service-web-overview.md) で実行されている PHP Web アプリからそのデータベースに接続する方法について説明します。

> [!NOTE]
> MySQL データベースは、 <a href="https://portal.azure.com/#create/WordPress.WordPress" target="_blank">Marketplace アプリ テンプレート</a>の一部として作成することもできます。
>
>

## <a name="create-a-mysql-database-in-azure-portal"></a>Azure ポータルで MySQL データベースを作成する
Azure ポータルで MySQL データベースを作成するには、次の手順を実行します。

1. [Azure ポータル](https://portal.azure.com)にログインします。
2. 左側のメニューで、**[新規]** > **[データ + ストレージ]** > **[MySQL データベース]** の順にクリックします。

    ![Create a MySQL database in Azure - start](./media/store-php-create-mysql-database/create-db-1-start.png)
3. [新しい MySQL データベース] [ブレード](azure-portal-overview.md)で、次のように新しい MySQL データベースを構成します (*ブレード*: 横並びで表示されるポータル ページ)。

   * **[データベース名]**: 一意に識別できる名前を入力します。
   * **[サブスクリプション]**: 使用するサブスクリプションを選択します。
   * **[データベースの種類]**: 低コストまたは無料のレベルの場合は **[共有]** を選択し、専用リソースを取得する場合は **[専用]** を選択します。
   * **[リソース グループ]**: MySQL データベースを既存の [リソース グループ](azure-resource-manager/resource-group-overview.md) に追加するか、新しいリソース グループに配置します。 同じグループ内のリソースは、まとめて簡単に管理できます。
   * **[場所]**: 近くの場所を選択します。 既存のリソース グループに追加する場合は、リソース グループの場所に固定されます。
   * **[価格レベル]**: **[価格レベル]** をクリックし、価格オプション (**Mercury** レベルは無料) を選択して、**[選択]** をクリックします。
   * **[法律条項]**: **[法律条項]** をクリックし、購入の詳細を確認して、**[購入]** をクリックします。
   * **[ダッシュボードにピン留めする]**: ダッシュボードから直接アクセスする場合に選択します。 これは、ポータルのナビゲーションにまだ慣れていない場合に特に便利です。

     次のスクリーンショットは、MySQL データベースの構成方法のほんの一例です。  
     ![Create a MySQL database in Azure - configure](./media/store-php-create-mysql-database/create-db-2-configure.png)
4. 構成が完了したら、 **[作成]**をクリックします。

    ![Create a MySQL database in Azure - create](./media/store-php-create-mysql-database/create-db-3-create.png)

    デプロイが開始されたことを知らせるポップアップ通知が表示されます。

    ![Create a MySQL database in Azure - in progress](./media/store-php-create-mysql-database/create-db-4-started-status.png)

    デプロイに成功すると、別のポップアップが表示されます。 ポータルでは、MySQL データベースのブレードも自動的に開かれます。

<a name="connect"></a>

## <a name="connect-to-your-mysql-database"></a>MySQL データベースに接続する
新しい MySQL データベースの接続情報を表示するには、Web アプリのブレードで **[プロパティ]** をクリックするだけです。

![Create a MySQL database in Azure - MySQL database blade](./media/store-php-create-mysql-database/create-db-5-finished-db-blade.png)

これで、どの Web アプリでもその接続情報を使用できるようになりました。 単純な PHP アプリから接続情報を使用する方法を示すサンプルは、 [こちら](https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/tasklist-mysql)で入手できます。

## <a name="next-steps"></a>次のステップ
詳細については、 [PHP デベロッパー センター](/develop/php/)を参照してください。
