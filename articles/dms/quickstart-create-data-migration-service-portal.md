---
title: クイック スタート:Azure ポータルを使用して Azure Database Migration Service インスタンスを作成する | Microsoft Docs
description: Azure ポータルを使用して Azure Database Migration Service のインスタンスを作成します。
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: quickstart
ms.date: 03/12/2019
ms.openlocfilehash: af5ffdb1c1f030c2bbc0616d027c06b59f1a34de
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58177468"
---
# <a name="quickstart-create-an-instance-of-the-azure-database-migration-service-by-using-the-azure-portal"></a>クイック スタート:Azure Portal を使用して Azure Database Migration Service のインスタンスを作成する
このクイック スタートでは、Azure Portal を使用して Azure Database Migration Service のインスタンスを作成します。  サービスを作成したら、それを使用してオンプレミスの SQL Server から Azure SQL Database にデータを移行できます。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="log-in-to-the-azure-portal"></a>Azure Portal にログインする
Web ブラウザーを開いて [Microsoft Azure Portal](https://portal.azure.com/) にアクセスし、資格情報を入力して Portal にサインインします。

既定のビューはサービス ダッシュボードです。

## <a name="register-the-resource-provider"></a>リソース プロバイダーの登録
Database Migration Service の最初のインスタンスを作成する前に、Microsoft.DataMigration リソース プロバイダーを登録します。

1. Azure Portal で、**[すべてのサービス]** を選択してから、**[サブスクリプション]** を選択します。

2. Azure Database Migration Service のインスタンスを作成するサブスクリプションを選択して、**[リソース プロバイダー]** を選択します。

3. 移行を検索し、**Microsoft.DataMigration** の右側にある **[登録]** を選択します。

    ![リソース プロバイダーの登録](media/quickstart-create-data-migration-service-portal/dms-register-provider.png)

## <a name="create-an-instance-of-the-service"></a>サービスのインスタンスを作成します。
1. + **[リソースの作成]** を選択して、Azure Database Migration Service のインスタンスを作成します。

2. マーケットプレースで "migration" を検索し、**[Azure Database Migration Service]** を選択します。**[Azure Database Migration Service]** 画面で **[作成]** を選択します。

3. **[移行サービスの作成]** 画面で: 

    - Azure Database Migration Service のインスタンスを識別する、覚えやすい一意の**サービス名**を選択します。
    - インスタンスを作成する Azure **サブスクリプション**を選択します。
    - 既存の**リソース グループ**を選択するか、新しいリソース グループを作成します。
    - ソースまたはターゲット サーバーに最も近い **[場所]** を選択します。
    - 既存の**仮想ネットワーク** (VNET) を選択するか、新たに VNET を作成します。

        VNET によって、ソース データベースとターゲット環境へのアクセスが Azure Database Migration Service に提供されます。

        Azure portal で VNET を作成する方法の詳細については、[Azure portal を使用した仮想ネットワークの作成](https://aka.ms/vnet)に関する記事を参照してください。

    - [Basic:1 vCore]\(Basic: 1 仮想コア\) を **[価格レベル]** で選択します。

        ![移行サービスの作成](media/quickstart-create-data-migration-service-portal/dms-create-service1.png)

4. **作成**を選択します。

    しばらくすると、Azure Database Migration Service のインスタンスが作成され、使用できるようになります。 Database Migration Service は、次の図のように表示されます。

    ![作成した Migration Service](media/quickstart-create-data-migration-service-portal/dms-service-created.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ
このクイック スタートで作成したリソースは、[Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)を削除することでクリーンアップできます。 リソース グループを削除するには、作成した Azure Database Migration Service のインスタンスに移動します。 **リソース グループ**名を選択し、**[リソース グループの削除]** を選択します。 この操作により、リソース グループ内のすべての資産だけでなく、グループ自体も削除されます。

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"]
> [オンプレミスの SQL Server を Azure SQL Database に移行する](tutorial-sql-server-to-azure-sql.md)