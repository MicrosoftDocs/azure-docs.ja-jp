---
title: "Azure Portal を使用して Azure データ ファクトリを作成する | Microsoft Docs"
description: "Azure データ ファクトリを作成して、データをクラウド データ ストア (Azure Blob Storage) から別のクラウド データ ストア (Azure SQL Databse) にコピーします。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 09/19/2017
ms.author: jingwang
ms.openlocfilehash: b884d7f08311cc60dc3af500f552d525d23b91e6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-data-factory-using-the-azure-portal"></a>Azure Portal を使用してデータ ファクトリを作成する
Azure Data Factory は、データドリブン型のワークフローをクラウドに作成することでデータの移動と変換を制御し、自動化することができるクラウドベースのデータ統合サービスです。 Azure Data Factory を使えば、データ主導型のワークフロー (パイプライン) を作成し、スケジューリングできます。具体的には、各種データ ストアからデータを取り込む、そのデータを各種コンピューティング サービス (Azure HDInsight Hadoop、Spark、Azure Data Lake Analytics、Azure Machine Learning など) で処理/変換する、データ ストア (Azure SQL Data Warehouse など) に出力データを公開して、それを利用するビジネス インテリジェンス (BI) アプリケーションに提供するという一連の処理を行えるワークフローです。 

このクイック スタートでは、Azure Portal を使用してデータ ファクトリを作成します。 データ ファクトリの作成後、他のクイック スタートのように PowerShell、.NET SDK、Python SDK、または REST API を使用して、データをソース データ ストアからターゲット データ ストアにコピーするデータ パイプラインを作成する必要があります。 現時点では、Azure Portal を使用してデータ ファクトリにパイプラインを作成することはできません。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="log-in-to-the-azure-portal"></a>Azure ポータルにログインする
[Azure Portal](https://portal.azure.com/) にログインします。

## <a name="create-a-data-factory"></a>Data Factory を作成する。
このクイック スタートの一部として実行する手順を次に示します。
1. 左側のメニューで **[新規]** をクリックし、**[データ + 分析]**、**[Data Factory]** の順にクリックします。 
   
   ![New->DataFactory](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)
2. **[新しいデータ ファクトリ]** ブレードで、**[名前]** フィールドに「**ADFTutorialDataFactory**」と入力します。 
      
     ![[新しいデータ ファクトリ] ブレード](./media/quickstart-create-data-factory-portal/new-azure-data-factory.png)
 
   Azure データ ファクトリの名前は **グローバルに一意**にする必要があります。 次のエラーが発生した場合は、データ ファクトリの名前を変更して (yournameADFTutorialDataFactory など) 作成し直してください。 Data Factory アーティファクトの名前付け規則については、 [Data Factory - 名前付け規則](naming-rules.md) に関するトピックを参照してください。
  
       `Data factory name “ADFTutorialDataFactory” is not available`
3. データ ファクトリを作成する Azure **サブスクリプション**を選択します。 
4. **[リソース グループ]** について、次の手順のいずれかを行います。
     
      - **[Use existing (既存のものを使用)]**を選択し、ドロップダウン リストから既存のリソース グループを選択します。 
      - **[新規作成]**を選択し、リソース グループの名前を入力します。   
         
      このクイック スタートの一部の手順は、**ADFTutorialResourceGroup** という名前のリソース グループを使用することを前提としています。 リソース グループの詳細については、 [リソース グループを使用した Azure のリソースの管理](../azure-resource-manager/resource-group-overview.md)に関するページを参照してください。  
4. **バージョン**として **[V2 (プレビュー)]** を選択します。
5. データ ファクトリの **場所** を選択します。 現時点では、**米国東部**リージョンだけで V2 データ ファクトリを作成することができます。 ただし、データ ファクトリで使用されるコンピューティングとデータ ストレージは、他のリージョンでも利用できます。 
6. **[ダッシュボードにピン留めする]** をオンにします。     
7. **Create** をクリックしてください。
      
      > [!IMPORTANT]
      > Data Factory インスタンスを作成するには、サブスクリプション/リソース グループ レベルで [Data Factory の共同作業者](../active-directory/role-based-access-built-in-roles.md#data-factory-contributor) ロールのメンバーである必要があります。
      > 
      > データ ファクトリの名前は今後、DNS 名として登録される可能性があるため、一般ユーザーに表示される場合があります。             
3. ダッシュボードに、**[Deploying data factory]\(データ ファクトリをデプロイしています\)** というステータスを示したタイルが表示されます。 

    ![[Deploying data factory]\(データ ファクトリをデプロイしています\) タイル](media//quickstart-create-data-factory-portal/deploying-data-factory.png)
1. 作成が完了すると、図に示されているような **[Data Factory]** ブレードが表示されます。
   
   ![データ ファクトリのホーム ページ](./media/quickstart-create-data-factory-portal/data-factory-home-page.png)


## <a name="next-steps"></a>次のステップ
このサンプルのパイプラインは、Azure BLOB ストレージ内のある場所から別の場所にデータをコピーするものです。 より多くのシナリオで Data Factory を使用する方法については、[チュートリアル](tutorial-copy-data-dot-net.md)を参照してください。 