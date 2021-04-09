---
title: Azure CLI を使用して Azure Data Lake Analytics を管理する
description: この記事では、Azure CLI を使用して、Data Lake Analytics のジョブ、データ ソース、ユーザーを管理する方法について説明します。
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 01/29/2018
ms.openlocfilehash: 19b471d85a52fe38b72ad55847d022fb56b3c4f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92220926"
---
# <a name="manage-azure-data-lake-analytics-using-the-azure-command-line-interface-cli"></a>Azure コマンド ライン インターフェイス (CLI) を使用して Azure Data Lake Analytics を管理する

[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Azure CLI を使用して、Azure Data Lake Analytics のアカウント、データ ソース、ユーザー、およびジョブを管理する方法について説明します。 他のツールを使用する管理のトピックを表示する場合は、上のタブ セレクターをクリックします。

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に、次のリソースを用意する必要があります。

- Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

- Azure CLI。 「 [Azure CLI のインストールと構成](/cli/azure/install-azure-cli)」をご覧ください。

  - このデモを完了するためには、**プレリリース版の** [Azure CLI ツール](https://github.com/MicrosoftBigData/AzureDataLake/releases)をダウンロードしてインストールします。

- `az login` コマンドを使用して認証し、使用するサブスクリプションを選択します。 職場か学校のアカウントを使用した認証の詳細については、「 [Azure CLI から Azure サブスクリプションへの接続する](/cli/azure/authenticate-azure-cli)」をご覧ください。

   ```azurecli
   az login
   az account set --subscription <subscription id>
   ```

   これで、Data Lake Analytics と Data Lake Store のコマンドにアクセスできます。 Data Lake Store と Data Lake Analytics のコマンドを一覧表示するには、次のコマンドを実行します。

   ```azurecli
   az dls -h
   az dla -h
   ```

## <a name="manage-accounts"></a>アカウントの管理

Data Lake Analytics ジョブを実行するには、Data Lake Analytics アカウントが必要です。 Azure HDInsight とは異なり、ジョブを実行しなければ、Analytics アカウントには課金されません。 ジョブの実行時にのみ課金されます。  詳細については、「 [Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)」を参照してください。

### <a name="create-accounts"></a>アカウントの作成

次のコマンドを実行して、Data Lake アカウントを作成します。

   ```azurecli
   az dla account create --account "<Data Lake Analytics account name>" --location "<Location Name>" --resource-group "<Resource Group Name>" --default-data-lake-store "<Data Lake Store account name>"
   ```

### <a name="update-accounts"></a>アカウントの更新

次のコマンドは、既存の Data Lake Analytics アカウントのプロパティを更新します。

   ```azurecli
   az dla account update --account "<Data Lake Analytics Account Name>" --firewall-state "Enabled" --query-store-retention 7
   ```

### <a name="list-accounts"></a>アカウントの一覧表示

特定のリソース グループ内の Data Lake Analytics アカウントをリストします。

   ```azurecli
   az dla account list "<Resource group name>"
   ```

## <a name="get-details-of-an-account"></a>アカウントの詳細を取得する

   ```azurecli
   az dla account show --account "<Data Lake Analytics account name>" --resource-group "<Resource group name>"
   ```

### <a name="delete-an-account"></a>アカウントの削除

   ```azurecli
   az dla account delete --account "<Data Lake Analytics account name>" --resource-group "<Resource group name>"
   ```

## <a name="manage-data-sources"></a>データ ソースを管理する

Data Lake Analytics では、現在、2 つのデータ ソースがサポートされています。

- [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
- [Azure ストレージ](../storage/common/storage-introduction.md)

Analytics アカウントを作成する際には、既定のストレージ アカウントとして Azure Data Lake Storage アカウントを指定する必要があります。 既定の Data Lake ストレージ アカウントは、ジョブ メタデータとジョブ監査ログの格納に使用されます。 Analytics アカウントを作成したら、さらに Data Lake Storage アカウントや Azure Storage アカウントを追加することができます。

### <a name="find-the-default-data-lake-store-account"></a>既定の Data Lake Store アカウントの検索

`az dla account show` コマンドを実行することで、使用される既定の Data Lake Store アカウントを表示できます。 既定のアカウント名は defaultDataLakeStoreAccount プロパティの下に表示されます。

   ```azurecli
   az dla account show --account "<Data Lake Analytics account name>"
   ```

### <a name="add-additional-blob-storage-accounts"></a>他の BLOB ストレージ アカウントの追加

   ```azurecli
   az dla account blob-storage add --access-key "<Azure Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Storage account name>"
   ```

> [!NOTE]
> BLOB ストレージの短い名のみがサポートされます。 "myblob.blob.core.windows.net" などの FQDN 名は使用しないでください。
>

### <a name="add-additional-data-lake-store-accounts"></a>他の Data Lake Store アカウントの追加

次のコマンドは、指定された Data Lake Analytics アカウントを、他の Data Lake Store アカウントを追加して更新します。

   ```azurecli
   az dla account data-lake-store add --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Data Lake Store account name>"
   ```

### <a name="update-existing-data-source"></a>既存のデータ ソースの更新

既存の BLOB ストレージ アカウント キーを更新するには:

   ```azurecli
   az dla account blob-storage update --access-key "<New Blob Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

### <a name="list-data-sources"></a>データ ソースの一覧表示

Data Lake Store アカウントを一覧表示するには:

   ```azurecli
   az dla account data-lake-store list --account "<Data Lake Analytics account name>"
   ```

BLOB ストレージ アカウントを一覧表示するには:

   ```azurecli
   az dla account blob-storage list --account "<Data Lake Analytics account name>"
   ```

!["dataLakeStoreAccounts:" 情報が強調表示された Azure CLI を示すスクリーンショット。](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-data-source.png)

### <a name="delete-data-sources"></a>データ ソースの削除

Data Lake Store アカウントを削除するには:

   ```azurecli
   az dla account data-lake-store delete --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Azure Data Lake Store account name>"
   ```

BLOB ストレージ アカウントを削除するには:

   ```azurecli
   az dla account blob-storage delete --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

## <a name="manage-jobs"></a>ジョブの管理

ジョブを作成するには、Data Lake Analytics アカウントが必要です。  詳細については、「 [Data Lake Analytics アカウントの管理](#manage-accounts)」を参照してください。

### <a name="list-jobs"></a>ジョブのリスト

   ```azurecli
   az dla job list --account "<Data Lake Analytics account name>"
   ```

   ![データ ソースを一覧表示している Data Lake Analytics](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-jobs.png)

### <a name="get-job-details"></a>ジョブの詳細の取得

   ```azurecli
   az dla job show --account "<Data Lake Analytics account name>" --job-identity "<Job Id>"
   ```

### <a name="submit-jobs"></a>ジョブの送信

> [!NOTE]
> ジョブの既定の優先度は 1000 で、ジョブの並列処理の既定の次数は 1 です。
>
>    ```azurecli
>    az dla job submit --account "<Data Lake Analytics account name>" --job-name "<Name of your job>" --script "<Script to submit>"
>    ```

### <a name="cancel-jobs"></a>ジョブの取り消し

list コマンドを使用してジョブ ID を検索した後、cancel を使用してそのジョブを取り消します。

   ```azurecli
   az dla job cancel --account "<Data Lake Analytics account name>" --job-identity "<Job Id>"
   ```

## <a name="pipelines-and-recurrences"></a>パイプラインと繰り返し

### <a name="get-information-about-pipelines-and-recurrences"></a>パイプラインと繰り返しについての情報を取得する

`az dla job pipeline` コマンドを使って前に送信したジョブのパイプライン情報を確認します。

```azurecli
az dla job pipeline list --account "<Data Lake Analytics Account Name>"

az dla job pipeline show --account "<Data Lake Analytics Account Name>" --pipeline-identity "<Pipeline ID>"
```

`az dla job recurrence` コマンドを使って前に送信したジョブの繰り返し情報を確認します。

```azurecli
az dla job recurrence list --account "<Data Lake Analytics Account Name>"

az dla job recurrence show --account "<Data Lake Analytics Account Name>" --recurrence-identity "<Recurrence ID>"
```

## <a name="next-steps"></a>次のステップ

- [Microsoft Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)
- [Azure Portal で Azure Data Lake Analytics の使用を開始する](data-lake-analytics-get-started-portal.md)
- [Azure  Portal を使用して Azure Data Lake Analytics を管理する](data-lake-analytics-manage-use-portal.md)
- [Azure Portal を使用して Azure Data Lake Analytics ジョブの監視とトラブルシューティングを行う](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
