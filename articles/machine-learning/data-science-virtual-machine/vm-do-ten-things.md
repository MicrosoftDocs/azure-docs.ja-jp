---
title: Windows でのデータとモデルの探索
titleSuffix: Azure Data Science Virtual Machine
description: Windows Data Science Virtual Machine 上でデータ探索およびモデリングのタスクを実行します。
services: machine-learning
ms.service: data-science-vm
ms.custom: devx-track-python
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: c44c40f2ddab53cf0beb5ecd48127d126fdb4333
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101657402"
---
# <a name="data-science-with-a-windows-data-science-virtual-machine"></a>Windows Data Science Virtual Machine を使用したデータ サイエンス

Windows Data Science Virtual Machine (DSVM) は強力なデータ サイエンス開発環境であり、ここではデータ探索およびモデリング タスクを実行することができます。 この環境は既に構築されていて、いくつかの一般的なデータ分析ツールがバンドルされているため、オンプレミス、クラウド、ハイブリッドの各デプロイの分析を簡単に開始することができます。 

DSVM は Azure サービスと密接に連携します。 Azure 上の Azure Synapse (旧称 SQL DW)、Azure Data Lake、Azure Storage、または Azure Cosmos DB に既に保存されているデータを読み取って処理することができます。 Azure Machine Learning など、その他の分析ツールを利用することもできます。

この記事では、DSVM を使用してデータ サイエンス タスクを実行したり、他の Azure サービスとやりとりしたりする方法について説明します。 DSVM では次のことを実行できます。

- Jupyter Notebook を使用し、Python 2、Python 3、Microsoft R を使用してご自分のデータをブラウザーで実験します。 (Microsoft R は、パフォーマンスを考慮して設計された、エンタープライズ対応の R バージョンです)。
- DSVM 上で Microsoft Machine Learning Server および Python を使用してデータの探索およびモデルの開発をローカルで行います。
- Azure portal または PowerShell を使用して Azure リソースを管理します。
- ご利用の DSVM にマウント可能なドライブとして Azure Files 共有を作成することによってストレージ スペースを拡張し、大規模なデータセット/コードをチーム全体で共有します。
- GitHub を使用してチームとコードを共有します。 インストール済みの次の Git クライアントを使用してリポジトリにアクセスします: Git Bash と Git GUI。
- Azure Blob Storage、Azure Cosmos DB、Azure Synapse (旧称 SQL DW)、Azure SQL Database など、Azure のデータおよび分析サービスにアクセスします。
- DSVM にプレインストールされた Power BI Desktop インスタンスを使用してレポートとダッシュボードを作成し、それらをクラウドにデプロイします。

- ご利用の仮想マシン上に追加のツールをインストールします。   

> [!NOTE]
> この記事に一覧したデータ ストレージ サービスおよび分析サービスの多くには、追加の使用料金が適用されます。 詳細については、「[Azure の価格](https://azure.microsoft.com/pricing/)」を参照してください。
> 
> 

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションが必要です。 [無料試用版にサインアップ](https://azure.microsoft.com/free/)できます。
* Azure portal で Data Science Virtual Machine をプロビジョニングする手順については、[仮想マシンの作成](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016)に関するページをご覧ください。


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]


## <a name="use-jupyter-notebooks"></a>Jupyter Notebook を使用する
Jupyter Notebook には、データ探索とモデリング用のブラウザーベースの IDE が用意されています。 Jupyter Notebook では Python 2、Python 3、R (オープン ソースおよび Microsoft R Server の両方) のいずれを使用することもできます。

Jupyter Notebook を開始するには、 **[スタート]** メニューまたはデスクトップで **[Jupyter Notebook]** アイコンを選択します。 また、DSVM コマンド プロンプトで既存の Notebook が配置されているディレクトリ、または新しいノートブックを作成するディレクトリから、コマンド ```jupyter notebook``` を実行することもできます。  

Jupyter を開始すると、DSVM にあらかじめパッケージ化されているサンプル ノートブックの `/notebooks` ディレクトリに移動します。 ここでは、次の操作を実行できます。

* ノートブックを選択してコードを表示します。
* Shift + Enter キーを選択して、各セルを実行します。
* **[セル]**  >  **[実行]** の順にクリックしてノートブック全体を実行します。
* 新しいノートブックを作成します。それには、Jupyter アイコン (左上隅) を選択し、右側の **[新規]** ボタンを選択して、ノートブックの言語 (カーネルとも呼ばれます) を選択します。   

> [!NOTE]
> 現在は、Jupyter の Python 2.7、Python 3.6、R、Julia、および PySpark カーネルがサポートされています。 R カーネルでは、オープンソース R および Microsoft R の両方でのプログラミングがサポートされています。   
> 
> 

ノートブックを開いているときは、ご自分で選んだライブラリを使用して、ご利用のデータの探索、モデルの構築、モデルのテストを行うことができます。

## <a name="explore-data-and-develop-models-with-microsoft-machine-learning-server"></a>Microsoft Machine Learning Server を使用してデータを探索し、モデルを開発する
R や Python などの言語を使用して、DSVM 上で直接データ分析を実行できます。

R の場合は、スタート メニューまたはデスクトップにある RStudio などの IDE を使用できます。 または、R Tools for Visual Studio を使用することもできます。 Microsoft は、オープン ソース CRAN R の上に追加のライブラリを提供することで、スケーラブルな分析を可能にし、並列チャンク分析で許容されたメモリ サイズを超える大きなデータを分析する機能を有効にしました。 

Python に関しては、Python Tools for Visual Studio (PTVS) 拡張機能がプレインストールされる Visual Studio Community Edition などの IDE を使用できます。 既定では、ルート Conda 環境である Python 3.6 のみが PTVS で構成されます。 Anaconda Python 2.7 を有効にするには、次の手順を行います。

1. Visual Studio Community Edition 内で **[ツール]**  >  **[Python ツール]**  >  **[Python 環境]** の順に移動し、 **[+ カスタム]** を選択して、各バージョンのカスタム環境を作成します。
1. 説明を入力し、環境プレフィックス パス (Anaconda Python 2.7 の場合は **c:\anaconda\envs\python2**) を設定します。
1. **[自動検出]**  >  **[適用]** の順に選択して環境を保存します。

Python 環境の作成方法の詳細については、[PTVS のドキュメント](/visualstudio/python/)を参照してください。

これで、新しい Python プロジェクトを作成する準備が整いました。 **[ファイル]**  >  **[新規作成]**  >  **[プロジェクト]**  >  **[Python]** の順に移動し、作成する Python アプリケーションの種類を選択します。 現在のプロジェクトの Python 環境を目的のバージョン (Python 2.7 または 3.6) に設定できます。これには、 **[Python 環境]** を右クリックして、 **[Python 環境の追加/削除]** を選択します。 PTVS の操作方法の詳細については、製品の[製品のドキュメント](/visualstudio/python/)を参照してください。



## <a name="manage-azure-resources"></a>Azure のリソースを管理する
DSVM では、仮想マシン上でローカルに分析ソリューションを構築することはできません。 また、Azure クラウド プラットフォーム上のサービスには、アクセスすることができます。 Azure には、コンピューティング サービス、ストレージ サービス、データ分析など、ご利用の DSVM から管理してアクセスできるさまざまなサービスが用意されています。

Azure サブスクリプションとクラウド リソースを管理するには、次の 2 つの方法があります。
+ Web ブラウザーを使用して、[Azure portal](https://portal.azure.com) に移動します。

+ PowerShell スクリプトを使用します。 Azure PowerShell は、デスクトップ上のショートカットから、または **[スタート]** メニューから実行します。 詳細については、[Microsoft Azure PowerShell のドキュメント](../../azure-resource-manager/management/manage-resources-powershell.md)を参照してください。 

## <a name="extend-storage-by-using-shared-file-systems"></a>共有ファイル システムを使用してストレージを拡張する
データ サイエンティストは、大きなデータセットやコードなどのリソースをチーム内で共有することができます。 DSVM には約 45 GB の使用可能な領域があります。 ご利用のストレージを拡張するには、Azure Files を使用できます。これを 1 つまたは複数の DSVM インスタンスにマウントするか、または REST API 経由でアクセスします。 また、[Azure portal](../../virtual-machines/windows/attach-managed-disk-portal.md) を使用するか、または [Azure PowerShell](../../virtual-machines/windows/attach-disk-ps.md) を使用して、専用の追加データ ディスクを追加することも可能です。 

> [!NOTE]
> Azure Files 共有の最大領域は 5 TB です。 各ファイルのサイズの上限は 1 TB です。 

このスクリプトを Azure PowerShell で使用すれば、Azure Files 共有を作成できます。

```powershell
# Authenticate to Azure.
Connect-AzAccount
# Select your subscription
Get-AzSubscription –SubscriptionName "<your subscription name>" | Select-AzSubscription
# Create a new resource group.
New-AzResourceGroup -Name <dsvmdatarg>
# Create a new storage account. You can reuse existing storage account if you want.
New-AzStorageAccount -Name <mydatadisk> -ResourceGroupName <dsvmdatarg> -Location "<Azure Data Center Name For eg. South Central US>" -Type "Standard_LRS"
# Set your current working storage account
Set-AzCurrentStorageAccount –ResourceGroupName "<dsvmdatarg>" –StorageAccountName <mydatadisk>

# Create an Azure Files share
$s = New-AzStorageShare <<teamsharename>>
# Create a directory under the file share. You can give it any name
New-AzStorageDirectory -Share $s -Path <directory name>
# List the share to confirm that everything worked
Get-AzStorageFile -Share $s
```

これで Azure Files 共有が作成されましたので、それを Azure 内の任意の仮想マシンにマウントすることができます。 待機時間とデータ転送の料金を回避するために、VM を、ストレージ アカウントと同じ Azure データ センターに置くことを強くお勧めします。 DSVM にドライブをマウントする Azure PowerShell コマンドを次に示します。

```powershell
# Get the storage key of the storage account that has the Azure Files share from the Azure portal. Store it securely on the VM to avoid being prompted in the next command.
cmdkey /add:<<mydatadisk>>.file.core.windows.net /user:<<mydatadisk>> /pass:<storage key>

# Mount the Azure Files share as drive Z on the VM. You can choose another drive letter if you want.
net use z:  \\<mydatadisk>.file.core.windows.net\<<teamsharename>>
```

これで、このドライブには、VM 上の通常のドライブとしてアクセスすることができます。

## <a name="share-code-in-github"></a>GitHub でコードを共有する
GitHub はコードのリポジトリです。ここでは、開発者コミュニティで共有されるテクノロジを使用してさまざまなツールのコード サンプルとソースを検索することができます。 GitHub には、コード ファイルのバージョンを追跡して保存する技術として Git が使用されています。 GitHub は、独自のリポジトリを作成するためのプラットフォームとしても利用できます。チームで共有されるコードやドキュメントを保存したり、バージョン管理を導入したり、コードを閲覧できるユーザーとコードを投稿できるユーザーを制御したりすることができます。 

Git の使用の詳細については、[GitHub のヘルプ ページ](https://help.github.com/)をご覧ください。 GitHub は、チームで協業したり、コミュニティによって開発されたコードを利用したり、逆にコードをコミュニティに投稿したりするための 1 つの方法として活用できます。

DSVM には、GitHub リポジトリにアクセスするためのクライアント ツールとして、コマンド ライン版と GUI 版が付属しています。 Git と GitHub を操作するコマンド ライン ツールは、Git Bash と呼ばれています。 Visual Studio は DSVM にインストールされ、Git 拡張機能を備えています。 これらのツールのアイコンは、 **[スタート]** メニューとデスクトップにあります。

GitHub リポジトリからコードをダウンロードするには、```git clone``` コマンドを使います。 たとえば、Microsoft が発行しているデータ サイエンス リポジトリを現在のディレクトリにダウンロードするには、Git Bash で次のコマンドを実行します。

```bash
git clone https://github.com/Azure/DataScienceVM.git
```

Visual Studio から同じクローン操作を行うこともできます。 下のスクリーンショットでは、Visual Studio で Git と GitHub ツールにアクセスする方法を示しています。

![GitHub 接続が表示される Visual Studio のスクリーンショット](./media/vm-do-ten-things/VSGit.png)

Git を使った GitHub リポジトリの操作の詳細については、github.com で公開されているリソースをご覧ください。 [チート シート](https://services.github.com/on-demand/downloads/github-git-cheat-sheet.pdf) を参照すると便利です。

## <a name="access-azure-data-and-analytics-services"></a>Azure のデータ サービスと分析サービスにアクセスする
### <a name="azure-blob-storage"></a>Azure BLOB ストレージ
Azure Blob Storage は、大規模データにも小規模データにも対応する信頼性と経済性に優れたクラウド ストレージ サービスです。 このセクションでは、データを BLOB ストレージに移動し、Azure BLOB の格納データにアクセスする方法について説明します。

#### <a name="prerequisites"></a>前提条件

* [Azure portal](https://portal.azure.com) から Azure Blob Storage アカウントを作成します。

   ![Azure portal でのストレージ アカウント作成プロセスのスクリーンショット](./media/vm-do-ten-things/create-azure-blob.png)

* コマンドライン AzCopy ツールが ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe``` にプレインストールされていることを確認します。 azcopy.exe を含むディレクトリが既に PATH 環境変数上にあるので、このツールの実行時に完全なコマンド パスを入力せずに済みます。 AzCopy ツールの詳細については、[AzCopy のドキュメント](../../storage/common/storage-use-azcopy-v10.md)を参照してください。
* Azure Storage Explorer ツールを起動します。 これは [Storage Explorer の Web ページ](https://storageexplorer.com/)からダウンロードできます。 

   ![ストレージ アカウントにアクセスする Azure Storage Explorer のスクリーンショット](./media/vm-do-ten-things/AzureStorageExplorer_v4.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azcopy"></a>VM から Azure BLOB にデータを移動する: AzCopy

ローカル ファイルと BLOB ストレージとの間のデータ移動には、次のように、コマンド ラインまたは PowerShell から AzCopy を使用します。

```powershell
AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt
```

**C:\myfolder** は対象ファイルが保存されているパスに、**mystorageaccount** はご自分の BLOB ストレージ アカウント名に、**mycontainer** はコンテナー名に、**storage account key** はご自分の BLOB ストレージ アクセス キーにそれぞれ置き換えてください。 ストレージ アカウントの資格情報は、[Azure portal](https://portal.azure.com) で確認できます。

AzCopy コマンドは、PowerShell またはコマンド プロンプトから実行します。 AzCopy コマンドの使用例をいくつか次に示します。

```powershell
# Copy *.sql from a local machine to an Azure blob
"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:"c:\Aaqs\Data Science Scripts" /Dest:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /DestKey:[ENTER STORAGE KEY] /S /Pattern:*.sql

# Copy back all files from an Azure blob container to a local machine

"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Dest:"c:\Aaqs\Data Science Scripts\temp" /Source:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /SourceKey:[ENTER STORAGE KEY] /S
```

AzCopy コマンドを実行して Azure BLOB にコピーすると、対象ファイルが Azure Storage Explorer に表示されます。

![アップロードされた CSV ファイルを表示する、ストレージ アカウントのスクリーンショット](./media/vm-do-ten-things/AzCopy_run_finshed_Storage_Explorer_v3.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azure-storage-explorer"></a>VM から Azure BLOB にデータを移動する: Azure Storage Explorer

Azure Storage Explorer を使用して、ご利用の VM 内のローカル ファイルからデータをアップロードすることもできます。

* コンテナーにデータをアップロードするには、ターゲット コンテナーを選択し、 **[アップロード]** ボタンを選択します。![Azure Storage Explorer の [アップロード] ボタンのスクリーンショット](./media/vm-do-ten-things/storage-accounts.png)
* **[ファイル]** ボックスの右側の省略記号 **[...]** を選択し、アップロードする 1 つまたは複数のファイルをファイル システムから選択します。さらに、 **[アップロード]** を選択して、ファイルのアップロードを開始します。![ファイルのアップロード ダイアログ ボックスのスクリーンショット](./media/vm-do-ten-things/upload-files-to-blob.png)

#### <a name="read-data-from-an-azure-blob-python-odbc"></a>Azure BLOB からデータを読み取る: Python ODBC

BlobService ライブラリを使用すると、Jupyter Notebook または Python プログラムで BLOB から直接データを読み取ることができます。

まず、必須のパッケージをインポートします。

```python
import pandas as pd
from pandas import Series, DataFrame
import numpy as np
import matplotlib.pyplot as plt
from time import time
import pyodbc
import os
from azure.storage.blob import BlobService
import tables
import time
import zipfile
import random
```

次に、ご利用の BLOB ストレージ アカウント資格情報をプラグインし、BLOB からデータを読み取ります。

```python
CONTAINERNAME = 'xxx'
STORAGEACCOUNTNAME = 'xxxx'
STORAGEACCOUNTKEY = 'xxxxxxxxxxxxxxxx'
BLOBNAME = 'nyctaxidataset/nyctaxitrip/trip_data_1.csv'
localfilename = 'trip_data_1.csv'
LOCALDIRECTORY = os.getcwd()
LOCALFILE =  os.path.join(LOCALDIRECTORY, localfilename)

#download from blob
t1 = time.time()
blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILE)
t2 = time.time()
print(("It takes %s seconds to download "+BLOBNAME) % (t2 - t1))

#unzip downloaded files if needed
#with zipfile.ZipFile(ZIPPEDLOCALFILE, "r") as z:
#    z.extractall(LOCALDIRECTORY)

df1 = pd.read_csv(LOCALFILE, header=0)
df1.columns = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime','passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude']
print 'the size of the data is: %d rows and  %d columns' % df1.shape
```

データはデータ フレームとして読み取られます。

![データの最初の 10 行のスクリーンショット](./media/vm-do-ten-things/IPNB_data_readin.png)


### <a name="azure-synapse-analytics-and-databases"></a>Azure Synapse Analytics とデータベース
Azure Synapse Analytics は、エンタープライズ規模の SQL Server エクスペリエンスを備えた、サービスとしてのエラスティック データ ウェアハウスです。

Azure Synapse Analytics をプロビジョニングするには、こちらの[記事](../../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md)に記載されている手順に従ってください。 Azure Synapse Analytics をプロビジョニングした後、[こちらのチュートリアル](../team-data-science-process/sqldw-walkthrough.md)を利用して、Azure Synapse Analytics 内のデータを使用してデータのアップロード、探索、およびモデリングを行うことができます。

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB
Azure Cosmos DB は、クラウドにおける NoSQL データベースです。 それを使用すれば、JSON などのドキュメントを操作することや、そうしたドキュメントを格納してクエリを実行することができます。

次の前提条件となる手順を使用して、DSVM から Azure Cosmos DB にアクセスする:

1. Azure Cosmos DB Python SDK は DSVM に既にインストールされています。 これを更新するには、コマンド プロンプトから ```pip install pydocumentdb --upgrade``` を実行します。
2. [Azure portal](https://portal.azure.com) で Azure Cosmos DB アカウントとデータベースを作成します。
3. [Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=53595)から Azure Cosmos DB データ移行ツールをダウンロードして、任意のディレクトリに展開します。
4. 移行ツールへの次のコマンド パラメーターを使用して、[パブリック BLOB](https://data.humdata.org/dataset/a60ac839-920d-435a-bf7d-25855602699d/resource/7234d067-2d74-449a-9c61-22ae6d98d928/download/volcano.json) に格納されている JSON データ (火山に関するデータ) を Azure Cosmos DB にインポートします (Azure Cosmos DB データ移行ツールをインストールしたディレクトリから dtui.exe を使用します)。これらのパラメーターでインポート元とインポート先の場所を入力します。
   
    `/s:JsonFile /s.Files:https://data.humdata.org/dataset/a60ac839-920d-435a-bf7d-25855602699d/resource/7234d067-2d74-449a-9c61-22ae6d98d928/download/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1`

データをインポートしたら、Jupyter にアクセスして、*DocumentDBSample* というタイトルのノートブックを開くことができます。 それには、Azure Cosmos DB にアクセスして何らかの基本的なクエリを実行するための Python コードが含まれています。 Azure Cosmos DB の詳細については、サービスの[ドキュメント ページ](../../cosmos-db/index.yml)をご覧ください。

## <a name="use-power-bi-reports-and-dashboards"></a>Power BI レポートとダッシュボードを使用する 
先ほど示した Azure Cosmos DB の例での Volcano JSON ファイルを Power BI Desktop で視覚化することで、データの本質を視覚的に把握することができます。 詳しい手順については、[Power BI の記事](../../cosmos-db/powerbi-visualize.md)を参照してください。 手順の概要は次のとおりです。

1. Power BI Desktop を開いて、 **[データの取得]** を選びます。 URL に `https://cahandson.blob.core.windows.net/samples/volcano.json` を指定します。
2. リストとしてインポートされた JSON レコードが表示されます。 そのリストをテーブルに変換して、Power BI で処理できるようにします。
4. [展開] (矢印) アイコンを選択して、列を展開します。
5. その位置が **[レコード]** フィールドであることに注目します。 そのレコードを展開し、coordinates のみを選択してください。 **[座標]** はリスト列です。
6. 新しい列を追加して、リスト座標列をコンマ区切りの **LatLong** 列に変換します。 式 ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})``` を使用して、座標リストフィールド内の 2 つの要素を連結します。
7. **[昇格]** 列を decimal に変換し、 **[閉じる]** ボタン、 **[適用]** ボタンの順に選択します。

上記の手順を行うのでなく、次のコードを貼り付けることもできます。 これは、データ変換をクエリ言語で記述するために Power BI の詳細エディターで使用される手順をスクリプト化したものです。

```pqfl
let
    Source = Json.Document(Web.Contents("https://cahandson.blob.core.windows.net/samples/volcano.json")),
    #"Converted to Table" = Table.FromList(Source, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
    #"Expanded Column1" = Table.ExpandRecordColumn(#"Converted to Table", "Column1", {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}, {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}),
    #"Expanded Location" = Table.ExpandRecordColumn(#"Expanded Column1", "Location", {"coordinates"}, {"coordinates"}),
    #"Added Custom" = Table.AddColumn(#"Expanded Location", "LatLong", each Text.From([coordinates]{1})&","&Text.From([coordinates]{0})),
    #"Changed Type" = Table.TransformColumnTypes(#"Added Custom",{{"Elevation", type number}})
in
    #"Changed Type"
```

これで Power BI データ モデルのデータができました。 ご利用の Power BI Desktop インスタンスは、次のように表示されます。

![Power BI Desktop](./media/vm-do-ten-things/PowerBIVolcanoData.png)

このデータ モデルを使ってレポートの作成と視覚化の作業を開始できます。 レポートを作成するには、こちらの [Power BI の記事](../../cosmos-db/powerbi-visualize.md#build-the-reports)に記載された手順に従います。

## <a name="scale-the-dsvm-dynamically"></a>DSVM を動的にスケーリングする 
プロジェクトのニーズを満たすために、DSVM はスケールアップしたりスケールダウンしたりすることができます。 夜間や週末に VM を使用する必要がない場合は、[Azure portal](https://portal.azure.com) から VM をシャットダウンすることができます。

> [!NOTE]
> VM 上でオペレーティング システムのシャットダウン ボタンを使用しただけであっても、コンピューティングの料金は発生します。 代わりに、Azure portal または Cloud Shell を使用して DSVM の割り当てを解除する必要があります。
> 
> 

大規模な分析を処理する必要が生じると、より多くの CPU、メモリ、またはディスク容量を必要とする場合があります。 その場合は、CPU コア数、ディープ ラーニング用の GPU ベース インスタンス、メモリ容量、ディスクの種類 (ソリッド ステート ドライブなど) の観点から、処理能力や予算のニーズに応じて VM のサイズを選ぶことができます。 VM とその時間単位のコンピューティング料金をまとめた一覧については、「[Azure Virtual Machines の価格](https://azure.microsoft.com/pricing/details/virtual-machines/)」ページをご覧ください。


## <a name="add-more-tools"></a>ツールをさらに追加する
DSVM に事前に組み込まれているツールを使用すると、多くの一般的なデータ分析ニーズに対応できます。 これにより、ご利用の環境を 1 つずつインストールして構成する必要がなくなるため、時間の節約になります。 また、使用するリソースについてのみ支払いを行うので、コストも節約できます。

この記事の中で取り上げた他の Azure データ サービスと Azure 分析サービスを使用して、分析環境を強化することができます。 場合によっては、独自のパートナー ツールなど、追加のツールが必要になることがあります。 必要となった新しいツールをインストールするには、仮想マシンに対するフル管理者アクセス権が必要です。 プレインストールされない、Python と R の追加パッケージをインストールすることもできます。 Python の場合、```conda``` または ```pip``` を使用できます。 R の場合は、R コンソールで ```install.packages()``` を使用することも、IDE を使用して **[パッケージ]**  >  **[パッケージのインストール]** の順に選択することもできます。

## <a name="deep-learning"></a>ディープ ラーニング

フレームワーク ベースのサンプルに加えて、DSVM で検証済みの包括的なチュートリアルのセットもご利用いただけます。 これらのチュートリアルは、イメージやテキスト/言語の理解などの分野でディープ ラーニング アプリケーションの開発をすぐに開始するのに役立ちます。   


- [さまざまなフレームワークでのニューラル ネットワークの実行](https://github.com/ilkarman/DeepLearningFrameworks): このチュートリアルは、あるフレームワークから別のフレームワークにコードを移行する方法を示します。 フレームワーク間でモデルと実行時のパフォーマンスを比較する方法も示します。 

- [イメージ内で製品を検出するエンド ツー エンド ソリューションを構築するための攻略ガイド](https://github.com/Azure/cortana-intelligence-product-detection-from-images): イメージ検出は、イメージ内のオブジェクトを特定して分類する手法です。 このテクノロジには、多くの実際のビジネス ドメインに大きなメリットをもたらす可能性があります。 たとえば、小売り業者は、この手法を使用して、顧客が棚から選ぶ製品を判断できます。 この情報は、店舗が製品在庫を管理するのに役立ちます。 

- [音声のディープ ラーニング](/archive/blogs/machinelearning/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure): このチュートリアルでは、[都市音声データセット](https://serv.cusp.nyu.edu/projects/urbansounddataset/urbansound8k.html)で発生する音声イベントを検出するためのディープ ラーニング モデルをトレーニングする方法を説明します。 また、音声データを操作する方法の概要についても説明します。

- [テキスト ドキュメントの分類](https://github.com/anargyri/lstm_han): このチュートリアルでは、2 つのニューラル ネットワーク アーキテクチャを構築してトレーニングする方法を示します。Hierarchical Attention Network と Long Short Term Memory (LSTM) ネットワークです。 これらのニューラル ネットワークでは、ディープ ラーニング用の Keras API を使用して、テキスト ドキュメントを分類します。 

## <a name="summary"></a>まとめ
この記事では、Microsoft Data Science Virtual Machine 上でできることの一部について説明しました。 DSVM を効果的な分析環境にするためにできることは他にも多数あります。
