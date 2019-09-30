---
title: データベースから Azure Data Explorer への一括コピーに Azure Data Factory テンプレートを使用する
description: このトピックでは、データベースから Azure Data Explorer への一括コピーに Azure Data Factory テンプレートを使用する方法について学習します
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/08/2019
ms.openlocfilehash: 5a6aebd276ef8658da9ca763be7da5c38a9c772a
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2019
ms.locfileid: "70873419"
---
# <a name="use-azure-data-factory-template-for-bulk-copy-from-database-to-azure-data-explorer"></a>データベースから Azure Data Explorer への一括コピーに Azure Data Factory テンプレートを使用する

Azure Data Explorer は、アプリケーション、Web サイト、IoT デバイスなどの多くのソースからの大量のデータ ストリーミングをリアルタイムに分析するためのフル マネージドのデータ分析サービスです。 Azure Data Factory は、フル マネージドのクラウドベースのデータ統合サービスです。 Azure Data Factory サービスを使用して、既存のシステムからのデータを Azure Data Explorer データベースに取り込み、分析ソリューションを構築する際の時間を節約できます。 

[Azure Data Factory テンプレート](/azure/data-factory/solution-templates-introduction)は、Data Factory をすぐに使い始めることができるように、あらかじめ定義された Azure Data Factory パイプラインで、データ統合プロジェクトを構築するための開発時間を短縮できます。 **データベースから Azure Data Explorer への一括コピー** テンプレートは、**Lookup** アクティビティと **ForEach** アクティビティを使用して作成されます。 このテンプレートを使用すると、データのコピーを高速化するために、データベースまたはテーブルごとに多数のパイプラインを作成できます。 

> [!IMPORTANT]
> * SQL Server や Google BigQuery などのデータベースから Azure Data Explorer に大量のデータをコピーするには、**データベースから Azure Data Explorer への一括コピー** テンプレートを使用します。 
> * [コピー ツール](data-factory-load-data.md)を使用して、少量または中程度の量のデータを含む少数のテーブルを Azure Data Explorer にコピーします。 

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。
* [Azure Data Explorer クラスターとデータベース](create-cluster-database-portal.md)
* [データ ファクトリの作成](data-factory-load-data.md#create-a-data-factory)
* データベース内のデータのソース

## <a name="create-controltabledataset"></a>ControlTableDataset を作成する

**ControlTableDataset** は、パイプラインでソースからターゲットにコピーされるデータを示します。 行数は、データのコピーに必要なパイプラインの合計数を示します。 **ControlTableDataset** は、ソース データベースの一部として定義する必要があります。

SQL Server ソース テーブル形式の例を次に示します。
    
```sql   
CREATE TABLE control_table (
PartitionId int,
SourceQuery varchar(255),
ADXTableName varchar(255)
);
```
    
|プロパティ  |説明  | 例
|---------|---------| ---------|
|PartitionId   |   コピー順序 | 1  |  
|SourceQuery   |   パイプライン ランタイム中にコピーされるデータを示すクエリ | <br>`select * from table where lastmodifiedtime  LastModifytime >= ''2015-01-01 00:00:00''>` </br>    
|ADXTableName  |  ターゲット テーブル名 | MyAdxTable       |  

**ControlTableDataset** の形式が異なる場合は、ご自分の形式に対応する **ControlTableDataset** を作成します。

## <a name="use-bulk-copy-from-database-to-azure-data-explorer-template"></a>データベースから Azure Data Explorer への一括コピー テンプレートを使用する

1. **[Let's get started]\(始めましょう\)** ページで、 **[Create pipeline from template]\(テンプレートからパイプラインを作成する\)** タイルを選択するか、右側の鉛筆アイコン ( **[作成者]** タブ) > **+**  >  **[Pipeline from template]\(テンプレートのパイプライン\)** の順に選択して、テンプレート ギャラリーを開きます。

    ![Azure Data Factory の [Let's get started]\(始めましょう\)](media/data-factory-template/adf-get-started.png)

1. **データベースから Azure Data Explorer への一括コピー** テンプレートを選択します。
 
    ![テンプレートからパイプラインを選択](media/data-factory-template/pipeline-from-template.png)

1.  **[データベースから Azure Data Explorer への一括コピー]** ウィンドウで、ドロップダウンから既存のデータセットを選択します。 

    * **ControlTableDataset** - ソースからコピー先にコピーされるデータと、コピー先の場所を示す制御テーブルへのリンクされたサービス。 
    * **SourceDataset** – ソース データベースへのリンクされたサービス。 
    * **AzureDataExplorerTable** - Azure Data Explore テーブル。 データセットが存在しない場合は、[Azure Data Explorer のリンクされたサービスを作成](data-factory-load-data.md#create-the-azure-data-explorer-linked-service)して、データセットを追加します。
    * **[このテンプレートを使用]** を選択します。

    ![Azure Data Explorer テンプレートの一括コピーの構成](media/data-factory-template/configure-bulk-copy-adx-template.png)

1. アクティビティの外側のキャンバス内の領域を選択して、テンプレート パイプラインにアクセスします。 **[パラメーター]** を選択して、 **[名前]** (制御テーブル名) と **[既定値]** (列名) を含むテーブルのパラメーターを入力します。

    ![パイプラインのパラメーター](media/data-factory-template/pipeline-parameters.png)

1.  Lookup アクティビティ **GetPartitionList** を選択して、既定の設定を表示します。 クエリが自動的に作成されます。
1.  Command アクティビティ **ForEachPartition** を選択し、 **[設定]** を選択します。
    * **バッチ カウント**:1 - 50 の数値を選択します。 この選択により、**ControlTableDataset** の行数に達するまで、並列で実行されるパイプラインの数が決まります。 
    * パイプライン バッチが並列実行されるようにするため、 **[シーケンシャル]** チェックボックスはオンにしないでください。

    ![ForEachPartition の設定](media/data-factory-template/foreach-partition-settings.png)

    > [!TIP]
    > ベスト プラクティスは、データをより迅速にコピーできるように、多くのパイプラインを並列に実行することです。 ソース テーブルのデータをパーティション分割し、日付とテーブルに従ってパイプラインごとにパーティションを割り当てて、効率を高めます。

1. **[すべて検証]** を選択して ADF パイプラインを検証します。 **[Pipeline Validation Output]\(パイプラインの検証の出力\)** が表示されます。

    ![テンプレート パイプラインを検証する](media/data-factory-template/validate-template-pipelines.png)

1. 必要に応じて **[デバッグ]** 、 **[トリガーの追加]** の順に選択してパイプラインを実行します。

    ![パイプラインの実行](media/data-factory-template/trigger-run-of-pipeline.png)    


これで**データベースから Azure Data Explorer への一括コピー** テンプレートを使用して、データベースとテーブルから大量のデータを効率的にコピーできるようになりました。

## <a name="next-steps"></a>次の手順

* [Azure Data Factory を使用して Azure Data Explorer にデータをコピーする](data-factory-load-data.md)手順について学習する。

* Azure Data Factory 内の [Azure Data Explorer コネクタ](/azure/data-factory/connector-azure-data-explorer)について学習する。

* データのクエリのための [Azure Data Explorer のクエリ](/azure/data-explorer/web-query-data)について確認する。






