---
title: Azure CLI で Azure Data Lake Analytics の使用を開始する
description: Azure コマンド ライン インターフェイスを使用して Azure Data Lake Analytics アカウントを作成し、U-SQL ジョブを送信する方法について説明します。
ms.service: data-lake-analytics
services: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.topic: conceptual
ms.date: 06/18/2017
ms.openlocfilehash: 9d9d5a7232529989901709013dcfac12f94afad0
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2019
ms.locfileid: "57433078"
---
# <a name="get-started-with-azure-data-lake-analytics-using-azure-cli"></a>Azure CLI で Azure Data Lake Analytics の使用を開始する
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

この記事では、Azure CLI コマンド ライン インターフェイスを使用して、Azure Data Lake Analytics のアカウントを作成し、USQL ジョブおよびカタログを送信する方法について説明します。 このジョブでは、タブ区切り値 (TSV) ファイルを読み取り、コンマ区切り値 (CSV) ファイルに変換します。 

## <a name="prerequisites"></a>前提条件
インストールを開始するには、次の項目が必要です。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
* この記事では、Azure CLI バージョン 2.0 以降を実行している必要があります。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。 



## <a name="log-in-to-azure"></a>Azure にログインする

Azure サブスクリプションにログインするには:

```
azurecli
az login
```

URL に移動し、認証コードを入力するように求められます。  次に、指示に従って、資格情報を入力します。

ログイン後は、login コマンドでサブスクリプションの一覧が表示されます。

特定のサブスクリプションを使用するには:

```
az account set --subscription <subscription id>
```

## <a name="create-data-lake-analytics-account"></a>Data Lake Analytics アカウントを作成する
ジョブを実行するには、Data Lake Analytics アカウントが必要です。 Data Lake Analytics アカウントを作成するには、以下の項目を指定する必要があります。

* **Azure リソース グループ**。 Data Lake Analytics アカウントは、Azure リソース グループ内に作成する必要があります。 [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) を使用すると、アプリケーション内の複数リソースを 1 つのグループとして操作できます。 アプリケーションのこれらすべてのリソースを、1 回の連携した操作でデプロイ、更新、または削除できます。  

サブスクリプションの既存のリソース グループを一覧表示するには:

```
az group list
```

新しいリソース グループを作成するには:

```
az group create --name "<Resource Group Name>" --location "<Azure Location>"
```

* **Data Lake Analytics アカウント名**。 各 Data Lake Analytics アカウントには、名前があります。
* **場所**。 Data Lake Analytics をサポートしているいずれかの Azure データ センターを使用します。
* **既定の Data Lake Store アカウント**: 各 Data Lake Analytics アカウントには、既定の Data Lake Store アカウントがあります。

既存の Data Lake Store アカウントを一覧表示するには:

```
az dls account list
```

新しい Data Lake Store アカウントを作成するには:

```azurecli
az dls account create --account "<Data Lake Store Account Name>" --resource-group "<Resource Group Name>"
```

Data Lake Analytics アカウントを作成するには、次の構文を使用します。

```
az dla account create --account "<Data Lake Analytics Account Name>" --resource-group "<Resource Group Name>" --location "<Azure location>" --default-data-lake-store "<Default Data Lake Store Account Name>"
```

アカウントを作成した後、以下のコマンドを使用して、アカウントを一覧表示し、アカウントの詳細を表示することができます。

```
az dla account list
az dla account show --account "<Data Lake Analytics Account Name>"            
```

## <a name="upload-data-to-data-lake-store"></a>Data Lake Store にデータをアップロードする
このチュートリアルでは、いくつかの検索ログを処理します。  検索ログは、Data Lake Store または Azure Blob Storage に格納できます。

Azure Portal には、検索ログ ファイルを含むサンプル データ ファイルを既定の Data Lake Store アカウントにコピーするためのユーザー インターフェイスが用意されています。 「 [ソース データの準備](data-lake-analytics-get-started-portal.md) 」を参照して、データを既定の Data Lake Store アカウントにアップロードします。

Azure CLI を使用してファイルをアップロードするには、次のコマンドを使用します。

```
az dls fs upload --account "<Data Lake Store Account Name>" --source-path "<Source File Path>" --destination-path "<Destination File Path>"
az dls fs list --account "<Data Lake Store Account Name>" --path "<Path>"
```

Data Lake Analytics は、Azure BLOB ストレージにもアクセスできます。  Azure BLOB ストレージへのデータのアップロードについては、「 [Azure ストレージでの Azure CLI の使用](../storage/common/storage-azure-cli.md)」を参照してください。

## <a name="submit-data-lake-analytics-jobs"></a>Data Lake Analytics ジョブを送信する
Data Lake Analtyics ジョブは U-SQL 言語で記述されます。 U-SQL の詳細については、[U-SQL 言語の使用](data-lake-analytics-u-sql-get-started.md)に関する記事および「[U-SQL Language Reference (U-SQL 言語リファレンス)](https://go.microsoft.com/fwlink/?LinkId=691348)」をご覧ください。

**Data Lake Analytics ジョブ スクリプトを作成するには**

次の U-SQL スクリプトでテキスト ファイルを作成し、ワークステーションにテキスト ファイルを保存します。

```
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

この U-SQL スクリプトでは、**Extractors.Tsv()** を使用してソース データ ファイルを読み取ってから、**Outputters.Csv()** を使用して csv ファイルを作成します。

ソース ファイルを別の場所にコピーしない限り、2 つのパスを変更しないでください。  出力フォルダーがない場合は、Data Lake Analytics によって作成されます。

既定の Data Lake Store アカウントに保存されたファイルの相対パスを使用する方が簡単です。 絶対パスを使用することもできます。  例: 

```
adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
```

リンクされたストレージ アカウント内のファイルへのアクセスには、絶対パスを使用する必要があります。  リンクされた Azure Storage アカウントに格納されているファイルの構文は以下のとおりです。

```
wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv
```

> [!NOTE]
> Azure BLOB コンテナーのパブリック BLOB はサポートされていません。      
> Azure BLOB コンテナーのパブリック コンテナーはサポートされていません。      
>

**ジョブを送信するには**

ジョブを送信するには、次の構文を使用します。

```
az dla job submit --account "<Data Lake Analytics Account Name>" --job-name "<Job Name>" --script "<Script Path and Name>"
```

例: 

```
az dla job submit --account "myadlaaccount" --job-name "myadlajob" --script @"C:\DLA\myscript.txt"
```

**ジョブを一覧表示し、ジョブの詳細を表示するには**

```
azurecli
az dla job list --account "<Data Lake Analytics Account Name>"
az dla job show --account "<Data Lake Analytics Account Name>" --job-identity "<Job Id>"
```

**ジョブを取り消すには**

```
az dla job cancel --account "<Data Lake Analytics Account Name>" --job-identity "<Job Id>"
```

## <a name="retrieve-job-results"></a>ジョブの結果を取得する

ジョブが完了したら、以下のコマンドを使用し、出力ファイルを一覧表示して、ファイルをダウンロードできます。

```
az dls fs list --account "<Data Lake Store Account Name>" --source-path "/Output" --destination-path "<Destination>"
az dls fs preview --account "<Data Lake Store Account Name>" --path "/Output/SearchLog-from-Data-Lake.csv"
az dls fs preview --account "<Data Lake Store Account Name>" --path "/Output/SearchLog-from-Data-Lake.csv" --length 128 --offset 0
az dls fs download --account "<Data Lake Store Account Name>" --source-path "/Output/SearchLog-from-Data-Lake.csv" --destination-path "<Destination Path and File Name>"
```

例: 

```
az dls fs download --account "myadlsaccount" --source-path "/Output/SearchLog-from-Data-Lake.csv" --destination-path "C:\DLA\myfile.csv"
```

## <a name="next-steps"></a>次の手順

* Data Lake Analytics CLI のリファレンス ドキュメントについては、「[Data Lake Analytics](/cli/azure/dla)」を参照してください。
* Data Lake Store CLI のリファレンス ドキュメントについては、「[Data Lake Store](/cli/azure/dls)」を参照してください。
* より複雑なクエリを表示する場合は、「 [チュートリアル: Azure Data Lake Analytics を使用して Web サイトのログを分析する](data-lake-analytics-analyze-weblogs.md)」をご覧ください。
