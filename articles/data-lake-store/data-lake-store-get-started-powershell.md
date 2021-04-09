---
title: Azure Data Lake Storage Gen1 の使用を開始する - PowerShell | Microsoft Docs
description: Azure PowerShell を使用し、Azure Data Lake Storage Gen1 アカウントを作成し、基本的な操作を実行します。
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 06/27/2018
ms.author: twooley
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 485b14f85d68290c5447c885b9bc4974318f7952
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92103732"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-azure-powershell"></a>Azure PowerShell を使用して Azure Data Lake Storage Gen1 の使用を開始する

> [!div class="op_single_selector"]
> * [ポータル](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Azure CLI](data-lake-store-get-started-cli-2.0.md)
>
>

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Azure PowerShell を使用して、Azure Data Lake Storage Gen1 アカウントを作成し、フォルダーの作成、データ ファイルのアップロードとダウンロード、アカウントの削除などの基本操作を行う方法について説明します。Data Lake Storage Gen1 の詳細については、[Data Lake Storage Gen1 の概要](data-lake-store-overview.md)に関する記事をご覧ください。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
* **Azure PowerShell 1.0 以上**。 「 [Azure PowerShell のインストールと構成の方法](/powershell/azure/)」を参照してください。

## <a name="authentication"></a>認証

この記事では、Data Lake Storage Gen1 に対する認証に、Azure アカウントの資格情報を入力する比較的単純な方法を使用しています。 その後、Data Lake Storage Gen1 アカウントとファイル システムに対するアクセス レベルは、そのログイン ユーザーのアクセス レベルで管理されます。 ただし、Data Lake Storage Gen1 には他の認証方法も存在します (エンド ユーザー認証とサービス間認証)。 認証方法の詳細については、[エンドユーザー認証](data-lake-store-end-user-authenticate-using-active-directory.md)または[サービス間認証](./data-lake-store-service-to-service-authenticate-using-active-directory.md)に関するページを参照してください。

## <a name="create-a-data-lake-storage-gen1-account"></a>Data Lake Storage Gen1 アカウントを作成する

1. デスクトップで、新しい Windows PowerShell ウィンドウを開きます。 次のスニペットを入力して Azure アカウントにログインし、サブスクリプションを設定して、Data Lake Storage Gen1 プロバイダーを登録します。 ログインを求められたら、必ず、サブスクリプションの管理者または所有者としてログインしてください。

    ```PowerShell
    # Log in to your Azure account
    Connect-AzAccount

    # List all the subscriptions associated to your account
    Get-AzSubscription

    # Select a subscription
    Set-AzContext -SubscriptionId <subscription ID>

    # Register for Azure Data Lake Storage Gen1
    Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
    ```

1. Data Lake Storage Gen1 アカウントは、Azure リソース グループに関連付けられます。 まず、リソース グループを作成します。

    ```PowerShell
    $resourceGroupName = "<your new resource group name>"
    New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"
    ```

    ![Azure リソース グループを作成する](./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "Azure リソース グループを作成する")

1. Data Lake Storage Gen1 アカウントを作成します。 指定する名前には、小文字と数字のみを含める必要があります。

    ```PowerShell
    $dataLakeStorageGen1Name = "<your new Data Lake Storage Gen1 account name>"
    New-AzDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStorageGen1Name -Location "East US 2"
    ```

    ![Data Lake Storage Gen1 アカウントを作成する](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "Data Lake Storage Gen1 アカウントを作成する")

1. アカウントが正常に作成されたことを確認します。

    ```PowerShell
    Test-AzDataLakeStoreAccount -Name $dataLakeStorageGen1Name
    ```

    コマンドレットの出力は **True** になります。

## <a name="create-directory-structures"></a>ディレクトリ構造を作成する

ご自分の Data Lake Storage Gen1 アカウントにディレクトリを作成し、データの管理と保存を行うことができます。

1. ルート ディレクトリを指定します。

    ```PowerShell
    $myrootdir = "/"
    ```

1. 指定したルートの下に **mynewdirectory** という新しいディレクトリを作成します。

    ```PowerShell
    New-AzDataLakeStoreItem -Folder -AccountName $dataLakeStorageGen1Name -Path $myrootdir/mynewdirectory
    ```

1. 新しいディレクトリが正常に作成されたことを確認します。

    ```PowerShell
    Get-AzDataLakeStoreChildItem -AccountName $dataLakeStorageGen1Name -Path $myrootdir
    ```

    次のスクリーンショットに示すように、出力が表示されます。

    ![ディレクトリを確認する](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "ディレクトリを確認する")

## <a name="upload-data"></a>データをアップロードする

データは、ルート レベルで直接 Data Lake Storage Gen1 に、またはアカウント内で作成したディレクトリにアップロードすることができます。 このセクションのスニペットは、前のセクションで作成したディレクトリ (**mynewdirectory**) にいくつかのサンプル データをアップロードする方法を示しています。

アップロードするいくつかのサンプル データを探している場合は、 **Azure Data Lake Git リポジトリ** から [Ambulance Data](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData)フォルダーを取得できます。 ファイルをダウンロードし、コンピューター上のローカル ディレクトリ (C:\sampledata\ など) に保存します。

```PowerShell
Import-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
   -Path "C:\sampledata\vehicle1_09142014.csv" `
   -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv
```

## <a name="rename-download-and-delete-data"></a>データの名前変更、ダウンロード、削除

ファイルの名前を変更するには、次のコマンドを使用します。

```PowerShell
Move-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv `
    -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv
```

ファイルをダウンロードするには、次のコマンドを使用します。

```PowerShell
Export-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv `
    -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"
```

ファイルを削除するには、次のコマンドを使用します。

```PowerShell
Remove-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv
```

確認を求めるメッセージが表示されたら、「 **Y** 」と入力して、項目を削除します。 削除する複数のファイルがある場合は、すべてのパスをコンマで区切って指定できます。

```PowerShell
Remove-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv
```

## <a name="delete-your-account"></a>アカウントの削除

Data Lake Storage Gen1 アカウントを削除するには、次のコマンドを使用します。

```PowerShell
Remove-AzDataLakeStoreAccount -Name $dataLakeStorageGen1Name
```

確認を求めるメッセージが表示されたら、「 **Y** 」と入力して、アカウントを削除します。

## <a name="next-steps"></a>次のステップ

* [Azure Data Lake Storage Gen1 で PowerShell を使用するためのパフォーマンス チューニング ガイダンス](data-lake-store-performance-tuning-powershell.md)
* [Data Lake Storage Gen1 を使用してビッグ データの要件に対応する](data-lake-store-data-scenarios.md)
* [Data Lake Storage Gen1 でのデータのセキュリティ保護](data-lake-store-secure-data.md)
* [Data Lake Storage Gen1 で Azure Data Lake Analytics を使用する](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Data Lake Storage Gen1 で Azure HDInsight を使用する](data-lake-store-hdinsight-hadoop-use-portal.md)