---
title: Azure portal:単一データベースを作成する - Azure SQL Database | Microsoft Docs
description: Azure portal を使用して Azure SQL Database で単一データベースを作成してクエリを実行します。
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: ninarn
ms.reviewer: carlrab
manager: craigg
ms.date: 04/11/2019
ms.openlocfilehash: b8395b5e67660f2b6fb1b671a7be6a20b4fceddd
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "60004977"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-portal"></a>クイック スタート:Azure portal を使用して Azure SQL Database で単一データベースを作成する

[単一データベース](sql-database-single-database.md)の作成は、Azure SQL Database でデータベースを作成する場合の最も迅速かつ簡単なデプロイ オプションです。 このクイック スタートでは、Azure portal を使用して単一データベースを作成した後、クエリを実行する方法について説明します。

Azure サブスクリプションをお持ちでない場合は、[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

このクイック スタートのすべての手順では、[Azure portal](https://portal.azure.com/) にサインインします。

## <a name="create-a-single-database"></a>単一データベースを作成する

単一データベースには、2 種類の[購入モデル](sql-database-purchase-models.md)のいずれかを使用して定義されたコンピューティング、メモリ、IO、ストレージのリソースのセットがあります。 単一データベースを作成するときは、それを管理するための [SQL Database サーバー](sql-database-servers.md)も定義し、指定したリージョンの [Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)内にそれを配置します。

Adventure Works LT サンプル データを含む単一データベースを作成するには:

1. Azure Portal の左上隅にある **[リソースの作成]** を選択します。
2. **[データベース]** を選択してから、**[SQL Database]** を選択し、**[SQL データベースの作成]** ページを開きます。 

   ![単一データベースの作成](./media/sql-database-get-started-portal/create-database-1.png)

1. **[基本]** タブの **[プロジェクトの詳細]** セクションで、次の値を入力または選択します。

   - **サブスクリプション**:表示されていない場合は、ドロップ ダウンして適切なサブスクリプションを選択します。
   - **[リソース グループ]**:**[新規作成]** を選択し、「`myResourceGroup`」と入力して、**[OK]** を選択します。

   ![新しい SQL データベース - 基本タブ](media/sql-database-get-started-portal/new-sql-database-basics.png)


1. **[データベースの詳細]** セクションで、次の値を入力または選択します。 

   - **データベース名**: 「 `mySampleDatabase` 」を入力します。
   - **サーバー**: **[新規作成]** を選択して次の値を入力し、**[選択]** を選択します。 
       - **サーバー名**: 一意性を確保するためにいくつかの数字とともに「`mysqlserver`」と入力します。 
       - **サーバー管理者ログイン**:「 `azureuser`」と入力します。
       - **Password**:パスワードの要件を満たす複雑なパスワードを入力します。 
       - **[場所]**:ドロップダウンから場所 (`West US 2` など) を選択します。 

       ![新しいサーバー](media/sql-database-get-started-portal/new-server.png)

        > [!IMPORTANT]
        > 忘れずにサーバー管理者のログインとパスワードを記録して、このクイック スタートと他のクイック スタートのためにサーバーとデータベースにログインできるようにします。 ログインまたはパスワードを忘れた場合は、**[SQL サーバー]** ページでログイン名を取得するかパスワードをリセットします。 **[SQL サーバー]** ページを開くには、データベースの作成後にデータベースの **[概要]** ページでサーバー名を選択します。

      ![SQL データベースの詳細](media/sql-database-get-started-portal/sql-db-basic-db-details.png)

   - **[SQL エラスティック プールを使用しますか?]**:**[いいえ]** オプションを選択します。 
   - **[コンピューティングとストレージ]**:**[データベースの構成]** を選択し、このクイック スタートでは、**Standard** サービス レベルを選択したうえで、スライダーを使用して **10 DTU (S0)** と **1** GB のストレージを選択します。 **[適用]** を選択します。 

    ![サービス レベルの構成](media/sql-database-get-started-portal/create-database-s1.png) 


      > [!NOTE]
      > このクイック スタートでは、[DTU ベースの購入モデル](sql-database-service-tiers-dtu.md)を使用しますが、[仮想コアベースの購入モデル](sql-database-service-tiers-vcore.md)も利用できます。
      > [!IMPORTANT]
      > 現在、1 TB を超える Premium レベルのストレージは、中国東部、中国北部、ドイツ中部、ドイツ北東部、米国中西部、米国 DoD の各リージョンと、米国政府中部を除くすべてのリージョンで利用できます。 これらのリージョンでは、Premium レベルのストレージの最大容量は 1 TB です。  詳しくは、[P11-P15 の現在の制限事項](sql-database-single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb)に関するページをご覧ください。  

    



1. **[追加設定]** タブを選択します。 
1. **[データ ソース]** セクションの **[既存のデータを使用します]** で、`Sample` を選択します。 

   ![追加の SQL DB 設定](media/sql-database-get-started-portal/create-sql-database-additional-settings.png)

   > [!IMPORTANT]
   > このクイック スタートとこのデータを使用する他の Azure SQL Database クイック スタートを簡単に実行できるように、必ず **Sample (AdventureWorksLT)** を選択します。

1. 残りの値は既定値のままにして、フォームの下部にある **[確認および作成]** を選択します。 
1. 最終設定を確認し、**[作成]** を選択します。 

8. **[SQL Database]** フォームで **[作成]** を選択して、リソース グループ、サーバー、データベースをデプロイし、プロビジョニングします。


## <a name="query-the-database"></a>データベースのクエリを実行する

データベースを作成したので、Azure portal に組み込まれているクエリ ツールを使用して、データベースへの接続とデータへのクエリを実行します。

1. データベースの **[SQL Database]** ページで、左側のメニューの **[クエリ エディター (プレビュー)]** を選択します。

   ![クエリ エディターにサインインする](./media/sql-database-get-started-portal/query-editor-login.png)

2. ログイン情報を入力し、**[OK]** を選択します。
3. **[クエリ エディター]** ウィンドウに次のクエリを入力します。

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

4. **[実行]** を選択し、**[結果]** ウィンドウでクエリの結果を確認します。

   ![クエリ エディターの結果](./media/sql-database-get-started-portal/query-editor-results.png)

5. **[クエリ エディター]** ページを閉じ、未保存の編集を破棄するかどうかを確認するプロンプトが表示されたら **[OK]** をクリックします。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

「[次の手順](#next-steps)」に進む場合は、このリソース グループ、データベース サーバー、単一データベースをそのままにしてください。 次のステップでは、データベースに接続してクエリを実行するさまざまな方法を紹介しています。

これらのリソースの使用が終了したら、次のように削除できます。

1. Azure portal の左側のメニューで、**[リソース グループ]**、**[myResourceGroup]** の順に選択します。
2. リソース グループ ページで **[リソース グループの削除]** を選択します。
3. フィールドに「*myResourceGroup*」と入力してから、**[削除]** を選択します。

## <a name="next-steps"></a>次の手順

- オンプレミスまたはリモート ツールから単一データベースに接続するために、サーバー レベルのファイアウォール規則を作成します。 詳細については、「[サーバーレベルのファイアウォール規則を作成する](sql-database-server-level-firewall-rule.md)」を参照してください。
- サーバーレベルのファイアウォール規則を作成した後に、いくつかの異なるツールと言語を使用して、データベースに[接続し、クエリを実行](sql-database-connect-query.md)します。
  - [SQL Server Management Studio を使用して接続およびクエリを実行する](sql-database-connect-query-ssms.md)
  - [Azure Data Studio を使用して接続およびクエリを実行する](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Azure CLI を使用して単一データベースを作成するには、「[Azure CLI のサンプル](sql-database-cli-samples.md)」をご覧ください。
- Azure PowerShell を使用して単一データベースを作成するには、「[Azure PowerShell サンプル](sql-database-powershell-samples.md)」をご覧ください。
