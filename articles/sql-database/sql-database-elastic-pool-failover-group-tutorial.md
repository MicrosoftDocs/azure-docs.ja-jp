---
title: チュートリアル:フェールオーバー グループに Azure SQL Database エラスティック プールを追加する | Microsoft Docs
description: Azure portal、PowerShell、または Azure CLI を使用して Azure SQL Database のエラスティック プールをフェールオーバー グループに追加します。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 06/19/2019
ms.openlocfilehash: 5dd241fed757669cf8bccd96a1de948e8d73a021
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/15/2019
ms.locfileid: "69033266"
---
# <a name="tutorial-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>チュートリアル:フェールオーバー グループに Azure SQL Database エラスティック プールを追加する

Azure SQL Database エラスティック プールのフェールオーバー グループを構成し、Azure portal を使用してフェールオーバーをテストします。  このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> - Azure SQL Database の単一データベースを作成する。
> - 単一データベースをエラスティック プールに追加する。 
> - 2 つの論理 SQL サーバー間にエラスティック プールの[フェールオーバー グループ](sql-database-auto-failover-group.md)を作成する。
> - フェールオーバーをテストする。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下のものが必要です。 

- Azure サブスクリプション。 [無料のアカウントを作成](https://azure.microsoft.com/free/)します (まだお持ちでない場合)。


## <a name="1---create-a-single-database"></a>1 - 単一データベースを作成する 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---add-single-database-to-elastic-pool"></a>2 - 単一データベースとエラスティック プールを追加する

1. [Azure portal](https://portal.azure.com) の左上隅にある **[リソースの作成]** を選択します。
1. 検索ボックスに「`elastic pool`」と入力して Enter キーを押し、 **[SQL エラスティック データベース プール]** アイコンを選択して、 **[作成]** を選択します。 

    ![マーケットプレースからエラスティック プールを選択する](media/sql-database-elastic-pool-create-failover-group-tutorial/elastic-pool-market-place.png)

1. 次の値を使用してエラスティック プールを構成します。
   - **[名前]** :エラスティック プールに一意の名前を指定します (例: `myElasticPool`)。 
   - **サブスクリプション**:ドロップダウン リストからサブスクリプションを選択します。
   - **ResourceGroup**:セクション 1 で作成したリソース グループである `myResourceGroup` をドロップダウン リストから選択します。 
   - **サーバー**: セクション 1 で作成したサーバーをドロップダウン リストから選択します。  

       ![エラスティック プール用の新しいサーバーを作成する](media/sql-database-elastic-pool-create-failover-group-tutorial/use-existing-server-for-elastic-pool.png)

   - **[コンピューティングとストレージ]** : **[エラスティック プールの構成]** を選択して、コンピューティングとストレージを構成し、単一データベースをエラスティック プールに追加します。 **[プールの設定]** タブで、Gen5、2 個の仮想コア、32 GB の既定値はそのままにします。 

1. **[構成]** ページで、 **[データベース]** タブを選択して **[データベースの追加]** を選択します。 セクション 1 で作成したデータベースを選択してから、 **[適用]** を選択してそれをエラスティック プールに追加します。 **[適用]** をもう一度選択して、エラスティック プールの設定を適用し、 **[構成]** ページを閉じます。 

    ![SQL DB をエラスティック プールに追加する](media/sql-database-elastic-pool-create-failover-group-tutorial/add-database-to-elastic-pool.png)

1. **[確認と作成]** を選択して、エラスティック プールの設定を確認し、 **[作成]** を選択してエラスティック プールを作成します。 


## <a name="3---create-the-failover-group"></a>3 - フェールオーバー グループを作成する 
この手順では、既存の Azure SQL サーバーと別のリージョンの新しい Azure SQL サーバーの間に[フェールオーバー グループ](sql-database-auto-failover-group.md)を作成します。 その後、そのフェールオーバー グループにエラスティック プールを追加します。 


1. [Azure portal](https://portal.azure.com) の左上隅にある **[すべてのサービス]** を選択します。 
1. 検索ボックスに、「`sql servers`」と入力します。 
1. (省略可能) SQL Server の横にある星のアイコンを選択して **[SQL サーバー]** をお気に入りに追加し、左側のナビゲーション ウィンドウに追加します。 
    
    ![SQL サーバーを見つける](media/sql-database-single-database-create-failover-group-tutorial/all-services-sql-servers.png)

1. **[SQL サーバー]** を選択し、セクション 1 で作成したサーバーを選択します。
1. **[設定]** ウィンドウで **[フェールオーバー グループ]** を選択し、 **[グループの追加]** を選択して新しいフェールオーバー グループを作成します。 

    ![新しいフェールオーバー グループの追加](media/sql-database-single-database-create-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. **[フェールオーバー グループ]** ページで、次の値を入力するか選択してから、 **[作成]** を選択します。
    - **フェールオーバー グループ名**:一意のフェールオーバー グループ名 (`failovergrouptutorial` など) を入力します。 
    - **セカンダリ サーバー**:*必要な設定を構成*するオプションを選択してから、 **[新しいサーバーの作成]** を選択します。 または、既に存在しているサーバーをセカンダリ サーバーとして選択することもできます。 新しいセカンダリ サーバーに対して次の値を入力した後、 **[選択]** を選択します。 
        - **サーバー名**: セカンダリ サーバーの一意の名前 (`mysqlsecondary` など) を入力します。 
        - **サーバー管理者ログイン**:「`azureuser`」と入力します
        - **Password**:パスワードの要件を満たす複雑なパスワードを入力します。
        - **[場所]** :ドロップダウンから場所 (米国東部 2 など) を選択します。 この場所をプライマリ サーバーと同じ場所にすることはできません。

       > [!NOTE]
       > サーバーのログインとファイアウォールの設定が、プライマリ サーバーの設定と一致している必要があります。 
    
       ![フェールオーバー グループのセカンダリ サーバーを作成する](media/sql-database-single-database-create-failover-group-tutorial/create-secondary-failover-server.png)

1. **[グループ内のデータベース]** を選択してから、セクション 2 で作成したエラスティック プールを選択します。 セカンダリ サーバー上にエラスティック プールを作成するよう求める警告が表示されます。 警告を選択し、 **[OK]** を選択して、セカンダリ サーバー上にエラスティック プールを作成します。 
        
    ![エラスティック プールをフェールオーバー グループに追加する](media/sql-database-elastic-pool-create-failover-group-tutorial/add-elastic-pool-to-failover-group.png)
        
1. **[選択]** を選択してエラスティック プール設定をフェールオーバー グループに適用した後、 **[作成]** を選択してフェールオーバー グループを作成します。 フェールオーバー グループにエラスティック プールを追加すると、geo レプリケーション プロセスが自動的に開始されます。 


## <a name="4---test-failover"></a>4 - フェールオーバーをテストする 
この手順では、フェールオーバー グループをセカンダリ サーバーにフェールオーバーしてから、Azure portal を使用してフェールバックします。 

1. [Azure portal](https://portal.azure.com) 内でお使いの **SQL サーバー**に移動します。 
1. **[設定]** ウィンドウで **[フェールオーバーグループ]** を選択してから、セクション 2 で作成したフェールオーバー グループを選択します。 
  
   ![ポータルからフェールオーバー グループを選択](media/sql-database-single-database-create-failover-group-tutorial/select-failover-group.png)

1. どのサーバーがプライマリで、どのサーバーがセカンダリかを確認します。 
1. 作業ウィンドウで **[フェールオーバー]** を選択し、エラスティック プールを含むフェールオーバー グループをフェールオーバーします。 
1. TDS セッションが切断されることを通知する警告で **[はい]** を選択します。 

   ![SQL データベースを含むフェールオーバー グループをフェールオーバーする](media/sql-database-single-database-create-failover-group-tutorial/failover-sql-db.png)

1. どのサーバーがプライマリで、どのサーバーがセカンダリかを確認します。 フェールオーバーが成功すると、2 つのサーバー ロールがスワップされているはずです。 
1. フェールオーバー グループを元の設定に戻すには、 **[フェールオーバー]** をもう一度選択します。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ 
リソース グループを削除して、リソースをクリーンアップします。 

1. [Azure Portal](https://portal.azure.com) で、リソース グループに移動します。
1. グループ内のすべてのリソースと、リソース グループ自体を削除するには、 **[リソースグループの削除]** を選択します。 
1. リソース グループの名前 (`myResourceGroup`) をテキストボックスに入力し、 **[削除]** を選択してリソース グループを削除します。  


## <a name="next-steps"></a>次の手順

このチュートリアルでは、フェールオーバー グループに Azure SQL Database の単一データベースを追加し、フェールオーバーをテストしました。 以下の方法について学習しました。

> [!div class="checklist"]
> - Azure SQL Database の単一データベースを作成する。
> - 単一データベースをエラスティック プールに追加する。 
> - 2 つの論理 SQL サーバー間にエラスティック プールの[フェールオーバー グループ](sql-database-auto-failover-group.md)を作成する。
> - フェールオーバーをテストする。

DMS を使用した移行方法について取り上げた次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [チュートリアル:DMS を使用して SQL Server をプールされたデータベースに移行する](../dms/tutorial-sql-server-to-azure-sql.md?toc=/azure/sql-database/toc.json)
