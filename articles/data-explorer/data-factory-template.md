---
title: Azure Data Factory テンプレートを使用してデータベースから Azure Data Explorer に一括コピーする
description: この記事では、Azure Data Factory テンプレートを使用して、データベースから Azure Data Explorer への一括コピーを行う方法について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/08/2019
ms.openlocfilehash: ca50a1ecd4d2a21593ddd11f83337ae7476cf916
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300442"
---
# <a name="copy-in-bulk-from-a-database-to-azure-data-explorer-by-using-the-azure-data-factory-template"></a>Azure Data Factory テンプレートを使用してデータベースから Azure Data Explorer に一括コピーする 

Azure Data Explorer は、フル マネージドの高速データ分析サービスです。 アプリケーション、Web サイト、IoT デバイスなど、さまざまなソースからストリーム配信される大量のデータをリアルタイムに分析することができます。 

Azure Data Factory は、フル マネージドのクラウドベースのデータ統合サービスです。 これを使用して、既存のシステムから Azure Data Explorer データベースにデータを設定することができます。 また、分析ソリューションの構築にかかる時間を短縮する効果もあります。 

[Azure Data Factory テンプレート](/azure/data-factory/solution-templates-introduction)は、定義済みの Data Factory パイプラインです。 テンプレートを通じて Data Factory をすぐに使い始めることができ、また、データ統合プロジェクトでの開発時間を短縮することができます。 

"*データベースから Azure Data Explorer への一括コピー*" テンプレートは、*Lookup* アクティビティと *ForEach* アクティビティを使用して作成します。 このテンプレートを使用して、データベースまたはテーブルごとにさまざまなパイプラインを作成し、データのコピーを高速化することができます。 

> [!IMPORTANT]
> 必ず、コピーするデータの量に見合ったツールを使用してください。
> * SQL Server や Google BigQuery などのデータベースから Azure Data Explorer に大量のデータをコピーするには、"*データベースから Azure Data Explorer への一括コピー*" テンプレートを使用します。 
> * 少量または中程度の量のデータを含む少数のテーブルを Azure Data Explorer にコピーするには、[*Data Factory のデータのコピー ツール*](data-factory-load-data.md)を使用します。 

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。
* [Azure Data Explorer クラスターとデータベース](create-cluster-database-portal.md)。
* [データ ファクトリを作成](data-factory-load-data.md#create-a-data-factory)します。
* データベース内のデータのソース。

## <a name="create-controltabledataset"></a>ControlTableDataset を作成する

*ControlTableDataset* は、パイプラインでソースからターゲットにコピーされるデータを示します。 行数は、データのコピーに必要なパイプラインの合計数を示します。 ControlTableDataset は、ソース データベースの一部として定義する必要があります。

SQL Server ソースのテーブル形式の例を次のコードに示します。
    
```sql   
CREATE TABLE control_table (
PartitionId int,
SourceQuery varchar(255),
ADXTableName varchar(255)
);
```

各コード要素について次の表で説明します。

|プロパティ  |説明  | 例
|---------|---------| ---------|
|PartitionId   |  コピー順序 | 1  |  
|SourceQuery   |  パイプライン ランタイム中にコピーされるデータを示すクエリ | <br>`select * from table where lastmodifiedtime  LastModifytime >= ''2015-01-01 00:00:00''>` </br>    
|ADXTableName  |  ターゲット テーブル名 | MyAdxTable       |  

ControlTableDataset の形式が異なる場合は、ご自分の形式に対応する ControlTableDataset を作成します。

## <a name="use-the-bulk-copy-from-database-to-azure-data-explorer-template"></a>データベースから Azure Data Explorer への一括コピー テンプレートを使用する

1. **[Let's get started]\(始めましょう\)** ウィンドウで **[Create pipeline from template]\(テンプレートからパイプラインを作成\)** を選択して **[テンプレート ギャラリー]** ウィンドウを開きます。

    ![Azure Data Factory の [Let's get started]\(始めましょう\) ウィンドウ](media/data-factory-template/adf-get-started.png)

1. **[データベースから Azure Data Explorer への一括コピー]** テンプレートを選択します。
 
    !["データベースから Azure Data Explorer への一括コピー" テンプレート](media/data-factory-template/pipeline-from-template.png)

1.  **[データベースから Azure Data Explorer への一括コピー]** ウィンドウの **[ユーザーによる入力]** で、次の手順に従ってデータセットを指定します。 

    a. ソースからターゲットにコピーされるデータと、ターゲット内のコピー先とを示す制御テーブルへのリンクされたサービスを **[ControlTableDataset]** ボックスの一覧から選択します。 

    b. **[SourceDataset]** ボックスの一覧から、ソース データベースへのリンクされたサービスを選択します。 

    c. **[AzureDataExplorerTable]** ボックスの一覧から Azure Data Explorer テーブルを選択します。 データセットが存在しない場合は、[Azure Data Explorer のリンクされたサービスを作成](data-factory-load-data.md#create-the-azure-data-explorer-linked-service)して、データセットを追加します。

    d. **[このテンプレートを使用]** を選択します。

    ![[データベースから Azure Data Explorer への一括コピー] ウィンドウ](media/data-factory-template/configure-bulk-copy-adx-template.png)

1. アクティビティの外側のキャンバス内の領域を選択して、テンプレート パイプラインにアクセスします。 **[パラメーター]** タブを選択して、 **[名前]** (制御テーブル名) と **[既定値]** (列名) を含むテーブルのパラメーターを入力します。

    ![パイプラインのパラメーター](media/data-factory-template/pipeline-parameters.png)

1.  **Lookup** アクティビティの **GetPartitionList** を選択して、既定の設定を表示します。 クエリが自動的に作成されます。
1.  Command アクティビティ **ForEachPartition** を選択し、 **[設定]** タブを選択して、次の手順を実行します。

    a. **[Batch count]\(バッチ カウント\)** ボックスに 1 から 50 の数値を入力します。 この選択により、*ControlTableDataset* の行数に達するまで、並列で実行されるパイプラインの数が決まります。 

    b. パイプライン バッチを確実に並列実行するため、 *[シーケンシャル]* チェック ボックスをオンにすることは**避けてください**。

    ![ForEachPartition の設定](media/data-factory-template/foreach-partition-settings.png)

    > [!TIP]
    > ベスト プラクティスは、データをより迅速にコピーできるように、多くのパイプラインを並列に実行することです。 効率を高めるためには、ソース テーブルのデータをパーティション分割し、日付とテーブルに従ってパイプラインごとに 1 つのパーティションを割り当てます。

1. **[すべて検証]** を選択して Azure Data Factory パイプラインを検証し、 **[Pipeline Validation Output]\(パイプラインの検証の出力\)** ウィンドウで結果を確認します。

    ![テンプレート パイプラインを検証する](media/data-factory-template/validate-template-pipelines.png)

1. 必要に応じて **[デバッグ]** 、 **[トリガーの追加]** の順に選択してパイプラインを実行します。

    ![[デバッグ] ボタンと [Run pipeline]\(パイプラインの実行\) ボタン](media/data-factory-template/trigger-run-of-pipeline.png)    

テンプレートを使用して、データベースとテーブルから大量のデータを効率的にコピーできるようになりました。

## <a name="next-steps"></a>次の手順

* [Azure Data Factory を使用して Azure Data Explorer にデータをコピーする](data-factory-load-data.md)方法について確認する。
* Azure Data Factory 内の [Azure Data Explorer コネクタ](/azure/data-factory/connector-azure-data-explorer)について学習する。
* データのクエリのための [Azure Data Explorer のクエリ](/azure/data-explorer/web-query-data)について確認する。






