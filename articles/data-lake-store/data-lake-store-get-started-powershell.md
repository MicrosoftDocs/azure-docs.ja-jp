---
title: "PowerShell で Azure Data Lake Store の使用を開始する | Microsoft Docs"
description: "Azure PowerShell を使用して、Data Lake Store アカウントを作成し、基本的な操作を実行する"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: bf85f369-f9aa-4ca1-9ae7-e03a78eb7290
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/06/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: a939a0845d7577185ff32edd542bcb2082543a26
ms.openlocfilehash: ebe8724d73769eb55e40b8af2056880a5a4007ce
ms.lasthandoff: 01/24/2017


---
# <a name="get-started-with-azure-data-lake-store-using-azure-powershell"></a>Azure PowerShell で Azure Data Lake Analytics の使用を開始する
> [!div class="op_single_selector"]
> * [ポータル](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Azure CLI](data-lake-store-get-started-cli.md)
> * [Node.JS](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Azure PowerShell を使用して、Azure Data Lake Store アカウントを作成し、フォルダーの作成、データ ファイルのアップロードとダウンロード、アカウントの削除などの基本操作を行う方法について説明します。Data Lake Store の詳細については、[Data Lake Store の概要](data-lake-store-overview.md)に関する記事をご覧ください。

## <a name="prerequisites"></a>前提条件
このチュートリアルを読み始める前に、次の項目を用意する必要があります。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
* **Azure PowerShell 1.0 以上**。 「 [Azure PowerShell のインストールと構成の方法](/powershell/azureps-cmdlets-docs)」を参照してください。

## <a name="authentication"></a>認証
この記事では、Data Lake Store に対する認証に、Azure アカウントの資格情報を入力する比較的単純な方法を使用しています。 その後、Data Lake Store アカウントとファイル システムに対するアクセス レベルは、そのログイン ユーザーのアクセス レベルで管理されます。 ただし、Data Lake Store には他の認証方法も存在します (**エンド ユーザー認証**と**サービス間認証**)。 認証方法の手順と詳しい情報については、「 [Authenticate with Data Lake Store using Azure Active Directory (Azure Active Directory を使用した Data Lake Store)](data-lake-store-authenticate-using-active-directory.md)」を参照してください。

## <a name="create-an-azure-data-lake-store-account"></a>Azure Data Lake Store アカウントを作成する
1. デスクトップで、新しい Windows PowerShell ウィンドウを開き、次のスニペットを入力して Azure アカウントにログインし、サブスクリプションを設定して、Data Lake Store プロバイダーを登録します。 ログインを求められたら、必ず、サブスクリプションの管理者または所有者としてログインしてください。

        # Log in to your Azure account
        Login-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Azure Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
2. Azure Data Lake Store アカウントは、Azure リソース グループに関連付けられます。 まず、Azure リソース グループを作成します。

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    ![Azure リソース グループの作成](./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "Azure リソース グループの作成")
3. Azure Data Lake Store アカウントを作成します。 指定する名前には、小文字と数字のみを含める必要があります。

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

    ![Azure Data Lake Store アカウントの作成](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "Azure Data Lake Store アカウントの作成")
4. アカウントが正常に作成されたことを確認します。

        Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

    この出力は **True**になります。

## <a name="create-directory-structures-in-your-azure-data-lake-store"></a>Azure Data Lake Store でディレクトリ構造を作成する
Azure Data Lake Store アカウントにディレクトリを作成し、データを管理したり格納したりすることができます。

1. ルート ディレクトリを指定します。

        $myrootdir = "/"
2. 指定したルートの下に **mynewdirectory** という新しいディレクトリを作成します。

        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/mynewdirectory
3. 新しいディレクトリが正常に作成されたことを確認します。

        Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -Path $myrootdir

    出力は次のように表示されます。

    ![ディレクトリの確認](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "ディレクトリの確認")

## <a name="upload-data-to-your-azure-data-lake-store"></a>Azure Data Lake Store にデータをアップロードする
データは、ルート レベルで直接 Data Lake Store に、またはアカウント内で作成したディレクトリにアップロードすることができます。 以下のスニペットは、前のセクションで作成したディレクトリ (**mynewdirectory**) にいくつかのサンプル データをアップロードする方法を示します。

アップロードするいくつかのサンプル データを探している場合は、 **Azure Data Lake Git リポジトリ** から [Ambulance Data](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData)フォルダーを取得できます。 ファイルをダウンロードし、コンピューター上のローカル ディレクトリ (C:\sampledata\. など) に保存します。

    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:\sampledata\vehicle1_09142014.csv" -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv


## <a name="rename-download-and-delete-data-from-your-data-lake-store"></a>Data Lake Store からデータの名前変更、ダウンロード、および削除を行う
ファイルの名前を変更するには、次のコマンドを使用します。

    Move-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

ファイルをダウンロードするには、次のコマンドを使用します。

    Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"

ファイルを削除するには、次のコマンドを使用します。

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

確認を求めるメッセージが表示されたら、「 **Y** 」と入力して、項目を削除します。 削除する複数のファイルがある場合は、すべてのパスをコンマで区切って指定できます。

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv

## <a name="delete-your-azure-data-lake-store-account"></a>Azure Data Lake Store アカウントを削除する
Data Lake Store アカウントを削除するには、以下のコマンドを使用します。

    Remove-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

確認を求めるメッセージが表示されたら、「 **Y** 」と入力して、アカウントを削除します。

## <a name="performance-guidance-while-using-powershell"></a>PowerShell 使用時のパフォーマンス ガイダンス

PowerShell を使用して Data Lake Store を操作する際にパフォーマンスを最大限に引き出すように調整できる最も重要な設定を以下に示します。

| プロパティ            | 既定値 | 説明 |
|---------------------|---------|-------------|
| PerFileThreadCount  | 10      | このパラメーターを使用すると、各ファイルをアップロードまたはダウンロードする場合の並列スレッドの数を選択できます。 この数値は、1 ファイルに割り当てることができる最大スレッド数を表しますが、シナリオに応じてスレッドが少なくなる場合があります (たとえば、1 KB のファイルをアップロードする場合、20 個のスレッドを要求してもスレッドは 1 つになります)。  |
| ConcurrentFileCount | 10      | このパラメーターは、ファイルのアップロードまたはダウンロード専用です。 このパラメーターによって、同時にアップロードまたはダウンロードできるファイルの数が決まります。 この数値は、一度にアップロードまたはダウンロードできる同時実行ファイルの最大数を表しますが、シナリオに応じて同時実行の数が少なくなる場合があります (たとえば、2 つのファイルをアップロードしている場合、15 を要求しても同時に行われるファイルのアップロードは 2 つになります)。 |

**例**

次のコマンドは、1 ファイルにつき 20 個のスレッドと、100 個の同時実行ファイルを使用して、ファイルを Azure Data Lake Store からユーザーのローカル ドライブにダウンロードします。

    Export-AzureRmDataLakeStoreItem -AccountName <Data Lake Store account name> -PerFileThreadCount 20-ConcurrentFileCount 100 -Path /Powershell/100GB/ -Destination C:\Performance\ -Force -Recurse

### <a name="how-do-i-determine-the-value-to-set-for-these-parameters"></a>これらのパラメーターに設定する値を決める方法

使用できるガイダンスがいくつかあります。

* **手順 1: 合計スレッド数を決める** - まず、使用する合計スレッド数を計算する必要があります。 一般的なガイドラインとして、各物理コアに 6 個のスレッドを使用する必要があります。

        Total thread count = total physical cores * 6

    **例**

    16 コアが搭載された D14 VM から PowerShell コマンドを実行しているとします。

        Total thread count = 16 cores * 6 = 96 threads


* **手順 2: PerFileThreadCount を計算する** - ファイルのサイズに基づいて PerFileThreadCount を計算します。 2.5 GB 未満のファイルの場合、既定値の 10 で十分なため、このパラメーターを変更する必要はありません。 2.5 GB を超えるファイルの場合、最初の 2.5 GB に基準として 10 個のスレッドを使用し、ファイル サイズが 256 MB 増加するたびにスレッドを 1 つ追加する必要があります。 コピーするフォルダーにさまざまなファイル サイズが含まれている場合は、似たようなファイル サイズに分類することを検討してください。 異なるファイル サイズがあると、最適なパフォーマンスを得られない可能性があります。 似たようなファイル サイズをグループ化できない場合は、最も大きいファイル サイズに基づいて PerFileThreadCount を設定する必要があります。

        PerFileThreadCount = 10 threads for the first 2.5GB + 1 thread for each additional 256MB increase in file size

    **例**

    1 GB から 10 GB までさまざまなファイルが 100 個あると仮定して、式には最大ファイル サイズとして 10 GB を使用します。式は次のようになります。

        PerFileThreadCount = 10 + ((10GB - 2.5GB) / 256MB) = 40 threads

* **手順 3: ConcurrentFilecount を計算する** - 合計スレッド数と PerFileThreadCount を使用して、次の式に基づいて ConcurrentFileCount を計算します。

        Total thread count = PerFileThreadCount * ConcurrentFileCount

    **例**

    ここまでに使用してきた例の値に基づきます。

        96 = 40 * ConcurrentFileCount

    結果として、**ConcurrentFileCount** は **2.4** になります。これは、**2** に丸めることができます。

### <a name="further-tuning"></a>さらなる調整

作業対象のファイル サイズがさまざまであるため、さらなる調整が必要になる場合があります。 上記の計算は、すべてのファイルまたはほとんどのファイルが 10 GB を超えているか 10 GB に近い場合に適しています。 そうではなく、多くのファイルがもっと小さく、ファイル サイズが多様な場合は、PerFileThreadCount を小さくすることができます。 PerFileThreadCount を小さくすると、ConcurrentFileCount を大きくすることができます。 そのため、ファイルの大半が 5 GB より小さいと想定した場合、次のように計算をやり直すことができます。

    PerFileThreadCount = 10 + ((5GB - 2.5GB) / 256MB) = 20

結果として、**ConcurrentFileCount** は 96/20 (つまり 4.8) になり、**4** に丸められます。

ファイル サイズの分布に応じて **PerFileThreadCount** を増減することで、引き続きこれらの設定を調整できます。

### <a name="limitation"></a>制限事項

* **ファイルの数が ConcurrentFileCount より少ない**: アップロードしているファイルの数が、計算した **ConcurrentFileCount** より小さい場合、ファイル数と等しくなるように **ConcurrentFileCount** を小さくする必要があります。 残りのスレッドすべてを使用して、**PerFileThreadCount** を大きくすることができます。

* **スレッドが多すぎる**: クラスターのサイズを大きくせずにスレッド数を増やしすぎると、パフォーマンスの低下というリスクがあります。 CPU でのコンテキスト切り替え時に競合の問題が発生する可能性があります。

* **同時実行が不十分**: 同時実行が十分でない場合、クラスターが小さすぎる可能性があります。 クラスター内のノード数を増やすことで、同時実行を増加させることができます。

* **調整エラー**: 同時実行があまりに多くなると、調整エラーが表示される場合があります。 調整エラーが表示される場合は、同時実行を減らすか、Microsoft にお問い合わせください。

## <a name="next-steps"></a>次のステップ
* [Data Lake Store のデータをセキュリティで保護する](data-lake-store-secure-data.md)
* [Data Lake Store で Azure Data Lake Analytics を使用する](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Data Lake Store で Azure HDInsight を使用する](data-lake-store-hdinsight-hadoop-use-portal.md)


