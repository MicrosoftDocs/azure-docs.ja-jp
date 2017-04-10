---
title: "Azure コマンド ライン インターフェイスを使用して Azure Data Lake Analytics を管理する | Microsoft Docs"
description: "Azure CLI を使用して、Azure Data Lake Analytics のアカウント、データ ソース、ジョブ、およびユーザーを管理する方法について説明します。"
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: 4e5a3a0a-6d7f-43ed-aeb5-c3b3979a1e0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 66834674e37d3b46a4a754b2b743cb223a6961d7
ms.lasthandoff: 03/21/2017


---
# <a name="manage-azure-data-lake-analytics-using-azure-command-line-interface-cli"></a>Azure コマンド ライン インターフェイス (CLI) を使用して Azure Data Lake Analytics を管理する
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Azure を使用して、Azure Data Lake Analytics のアカウント、データ ソース、ユーザー、およびジョブを管理する方法について説明します。 他のツールを使用する管理のトピックを表示するには、上のタブ セレクターをクリックします。

**前提条件**

このチュートリアルを読み始める前に、次の項目を用意する必要があります。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
* **Azure CLI**。 「 [Azure CLI のインストールと構成](../cli-install-nodejs.md)」をご覧ください。
  * このデモを完了するためには、 **プレリリース版の** [Azure CLI ツール](https://github.com/MicrosoftBigData/AzureDataLake/releases) をダウンロードしてインストールします。
* **認証**。次のコマンドを使用します。
  
        azure login
    職場か学校のアカウントを使用した認証の詳細については、「 [Azure CLI から Azure サブスクリプションへの接続する](../xplat-cli-connect.md)」をご覧ください。
* 次のコマンドを使用して、**Azure リソース マネージャー モードに切り替えます**。
  
        azure config mode arm

**Data Lake Store と Data Lake Analytics のコマンド一覧を表示するには:**

    azure datalake store
    azure datalake analytics

<!-- ################################ -->
<!-- ################################ -->
## <a name="manage-accounts"></a>アカウントの管理
Data Lake Analytics ジョブを実行するには、Data Lake Analytics アカウントが必要です。 Azure HDInsight とは異なり、ジョブを実行しなければ、Analytics アカウントには課金されません。  ジョブの実行時にのみ課金されます。  詳細については、「 [Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)」を参照してください。  

### <a name="create-accounts"></a>アカウントの作成
      azure datalake analytics account create "<Data Lake Analytics Account Name>" "<Azure Location>" "<Resource Group Name>" "<Default Data Lake Account Name>"


### <a name="update-accounts"></a>アカウントの更新
次のコマンドは、既存の Data Lake Analytics アカウントのプロパティを更新します。

    azure datalake analytics account set "<Data Lake Analytics Account Name>"


### <a name="list-accounts"></a>アカウントの一覧表示
Data Lake Analytics アカウントを一覧表示します。 

    azure datalake analytics account list

特定のリソース グループ内の Data Lake Analytics アカウントをリストします。

    azure datalake analytics account list -g "<Azure Resource Group Name>"

特定の Data Lake Analytics アカウントの詳細を取得します。

    azure datalake analytics account show -g "<Azure Resource Group Name>" -n "<Data Lake Analytics Account Name>"

### <a name="delete-data-lake-analytics-accounts"></a>Data Lake Analytics アカウントの削除
      azure datalake analytics account delete "<Data Lake Analytics Account Name>"


<!-- ################################ -->
<!-- ################################ -->
## <a name="manage-account-data-sources"></a>アカウント データ ソースの管理
Data Lake Analytics では現在、以下のデータ ソースがサポートされています。

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Azure Storage](../storage/storage-introduction.md)

Analytics アカウントを作成する際には、既定のストレージ アカウントとして Azure Data Lake Storage アカウントを指定する必要があります。 既定の ADL ストレージ アカウントは、ジョブ メタデータとジョブ監査ログを保存するために使用されます。 Analytics アカウントを作成したら、さらに Data Lake Storage アカウントや Azure Storage アカウントを追加することができます。 

### <a name="find-the-default-adl-storage-account"></a>既定の ADL ストレージ アカウントの検索
    azure datalake analytics account show "<Data Lake Analytics Account Name>"

値は、properties:datalakeStoreAccount:name の下に表示されます。

### <a name="add-additional-azure-blob-storage-accounts"></a>他の Azure BLOB ストレージ アカウントの追加
      azure datalake analytics account datasource add -n "<Data Lake Analytics Account Name>" -b "<Azure Blob Storage Account Short Name>" -k "<Azure Storage Account Key>"

> [!NOTE]
> BLOB ストレージの短い名のみがサポートされます。  "myblob.blob.core.windows.net" などの FQDN 名は使用しないでください。
> 
> 

### <a name="add-additional-data-lake-store-accounts"></a>他の Data Lake Store アカウントの追加
      azure datalake analytics account datasource add -n "<Data Lake Analytics Account Name>" -l "<Data Lake Store Account Name>" [-d]

[-d] は追加される Data Lake が既定の Data Lake アカウントかどうかを示すスイッチであり、省略可能です。 

### <a name="update-existing-data-source"></a>既存のデータ ソースの更新
既存の Data Lake Store アカウントを既定として設定するには:

      azure datalake analytics account datasource set -n "<Data Lake Analytics Account Name>" -l "<Azure Data Lake Store Account Name>" -d

既存の BLOB ストレージ アカウント キーを更新するには:

      azure datalake analytics account datasource set -n "<Data Lake Analytics Account Name>" -b "<Blob Storage Account Name>" -k "<New Blob Storage Account Key>"

### <a name="list-data-sources"></a>データ ソースの一覧表示:
    azure datalake analytics account show "<Data Lake Analytics Account Name>"

![データ ソースを一覧表示している Data Lake Analytics](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-data-source.png)

### <a name="delete-data-sources"></a>データ ソースの削除:
Data Lake Store アカウントを削除するには:

      azure datalake analytics account datasource delete "<Data Lake Analytics Account Name>" "<Azure Data Lake Store Account Name>"

BLOB ストレージ アカウントを削除するには:

      azure datalake analytics account datasource delete "<Data Lake Analytics Account Name>" "<Blob Storage Account Name>"

## <a name="manage-jobs"></a>ジョブの管理
ジョブを作成するには、Data Lake Analytics アカウントが必要です。  詳細については、「 [Data Lake Analytics アカウントの管理](#manage-accounts)」を参照してください。

### <a name="list-jobs"></a>ジョブのリスト
      azure datalake analytics job list -n "<Data Lake Analytics Account Name>"

![データ ソースを一覧表示している Data Lake Analytics](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-jobs.png)

### <a name="get-job-details"></a>ジョブの詳細の取得
      azure datalake analytics job show -n "<Data Lake Analytics Account Name>" -j "<Job ID>"

### <a name="submit-jobs"></a>ジョブの送信
> [!NOTE]
> ジョブの既定の優先度は 1000 で、ジョブの並列処理の既定の次数は 1 です。
> 
> 

    azure datalake analytics job create  "<Data Lake Analytics Account Name>" "<Job Name>" "<Script>"

### <a name="cancel-jobs"></a>ジョブの取り消し
list コマンドを使用してジョブ ID を検索した後、cancel を使用してそのジョブを取り消します。

      azure datalake analytics job list -n "<Data Lake Analytics Account Name>"
      azure datalake analytics job cancel "<Data Lake Analytics Account Name>" "<Job ID>"

## <a name="manage-catalog"></a>カタログの管理
U-SQL カタログを使用して、U-SQL スクリプトで共有できるように、データとコードを構成します。 カタログでは、Azure Data Lake のデータを使用して可能な限り最高のパフォーマンスを実現できます。 詳細については、「 [U-SQL カタログの使用](data-lake-analytics-use-u-sql-catalog.md)」を参照してください。

### <a name="list-catalog-items"></a>カタログ項目のリスト
    #List databases
    azure datalake analytics catalog list -n "<Data Lake Analytics Account Name>" -t database

    #List tables
    azure datalake analytics catalog list -n "<Data Lake Analytics Account Name>" -t table

タイプには、データベース、スキーマ、アセンブリ、外部データ ソース、テーブル、テーブル値関数、またはテーブルの統計情報が含まれます。

### <a name="create-catalog-secret"></a>カタログ シークレットの作成
    azure datalake analytics catalog secret create -n "<Data Lake Analytics Account Name>" <databaseName> <hostUri> <secretName>

### <a name="modify-catalog-secret"></a>カタログ シークレットの変更
      azure datalake analytics catalog secret set -n "<Data Lake Analytics Account Name>" <databaseName> <hostUri> <secretName>

### <a name="delete-catalog-secret"></a>カタログ シークレットの削除
    azure datalake analytics catalog secrete delete -n "<Data Lake Analytics Account Name>" <databaseName> <hostUri> <secretName>

<!-- ################################ -->
<!-- ################################ -->
## <a name="use-arm-groups"></a>ARM グループの使用
アプリケーションは通常、Web アプリケーション、データベース、データベース サーバー、ストレージ、サード パーティのサービスなどの、複数のコンポーネントで構成されます。 Azure リソース マネージャー (ARM) を使用すると、アプリケーション内の複数のリソースを&1; つのグループ (Azure リソース グループと呼ばれます) と見なして作業できます。 アプリケーションのこれらすべてのリソースを、1 回の連携した操作でデプロイ、更新、監視、または削除できます。 デプロイメントにはテンプレートを使用しますが、このテンプレートは、テスト、ステージング、運用環境などのさまざまな環境に使用できます。 グループ全体のロールアップ コストを表示すると、組織の課金ついて明確に把握できます。 詳細については、「 [Azure リソース マネージャーの概要](../azure-resource-manager/resource-group-overview.md)」を参照してください。 

Data Lake Analtyics サービスには、次のコンポーネントを含めることができます。

* Azure Data Lake Analytics アカウント
* 必要な既定の Azure Data Lake Storage アカウント
* 追加の Azure Data Lake Storage アカウント
* 追加の Azure Storage アカウント

管理しやすくするために&1; つの ARM グループの下にこれらすべてのコンポーネントを作成することができます。

![Azure Data Lake Analytics のアカウントとストレージ](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

Data Lake Analytics アカウントと従属するストレージ アカウントは同じ Azure データ センターに配置する必要があります。
ただし、ARM グループは別のデータ センターに配置できます。  

## <a name="see-also"></a>関連項目
* [Microsoft Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)
* [Azure ポータルで Azure Data Lake Analytics の使用を開始する](data-lake-analytics-get-started-portal.md)
* [Azure ポータルを使用する Azure Data Lake Analytics の管理](data-lake-analytics-manage-use-portal.md)
* [Azure ポータルを使用する Azure Data Lake Analytics ジョブの監視とトラブルシューティング](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)


