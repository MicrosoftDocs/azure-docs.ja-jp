---
title: データ探索とデータ サイエンス仮想マシンを使用したモデリング
titleSuffix: Azure
description: データの探索とモデリングに伴うさまざまな作業をデータ サイエンス仮想マシンで実行します。
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
editor: cgronlun
ms.custom: seodec18
ms.assetid: 145dfe3e-2bd2-478f-9b6e-99d97d789c62
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: gokuma
ms.openlocfilehash: e1e59b9a34d075002e8129fe1588c95e207f8273
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359032"
---
# <a name="ten-things-you-can-do-on-the-windows-data-science-virtual-machine"></a>Windows データ サイエンス仮想マシンでできる 10 のこと

Windows データ サイエンス仮想マシン (DSVM) は、データの探索とモデリングに関連したさまざま作業に対応する強力なデータ サイエンス開発環境です。 あらかじめ環境が構築され、広く使われているいくつかのデータ分析ツールが同梱されているため、初めて利用する方でも、オンプレミスのクラウド デプロイとハイブリッド デプロイの分析をすばやく行うことができます。 DSVM は、さまざまな Azure サービスと密接に連携します。Azure SQL Data Warehouse、Azure Data Lake、Azure Storage、Azure Cosmos DB として既に Azure に保存されているデータを読み取って処理することが可能です。 Azure Machine Learning や Azure Data Factory などのその他の分析ツールを活用することもできます。

この記事では、DSVM を使用してさまざまなデータ サイエンス タスクを実行したり、他の Azure サービスと連携したりする方法について説明します。 DSVM では次のことを実行できます。

1. DSVM 上で Microsoft ML Server または Python を使用してローカルからデータを探索し、モデルを開発する
2. ブラウザーから Jupyter Notebook を使用し、Python 2、Python 3、Microsoft R (パフォーマンスを重視して設計された企業向けの R) を使用してデータを探索する
3. R や Python を使って構築されたモデルを Azure Machine Learning でデプロイして、クライアント アプリケーションからシンプルな Web サービス インターフェイスを使ってモデルにアクセスできるようにする
4. Azure Portal または PowerShell を使用して Azure リソースを管理する
5. DSVM にマウント可能なドライブとして Azure File Storage を作成することによって記憶域を拡張し、大規模なデータセット/コードをチーム全体で共有する
6. GitHub を使ってチームでコードを共有し、プレインストールされる Git クライアント (Git Bash、Git GUI) を使ってリポジトリにアクセスする。
7. Azure Blob Storage、Azure Data Lake、Azure HDInsight (Hadoop)、Azure Cosmos DB、Azure SQL Data Warehouse、Azure SQL データベースなど、Azure のさまざまなデータ サービスと分析サービスにアクセスする
8. DSVM にプレインストールされる Power BI Desktop を使ってレポートとダッシュボードを作成し、クラウドにデプロイする
9. DSVM を動的に拡張してプロジェクトのニーズを満たす
10. 仮想マシンに追加のツールをインストールする   

> [!NOTE]
> この記事に掲載されているその他のデータ ストレージ サービスや分析サービスの多くでは、追加の使用料金が適用されます。 詳細については、「[Azure の価格](https://azure.microsoft.com/pricing/)」をご覧ください。
> 
> 

**前提条件**

* Azure サブスクリプションが必要です。 [こちら](https://azure.microsoft.com/free/)で無料試用版にサインアップできます。
* Azure ポータルでデータ サイエンス仮想マシンをプロビジョニングする手順については、 [仮想マシンの作成](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016)に関するページをご覧ください。


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="1-explore-data-and-develop-models-using-microsoft-ml-server-or-python"></a>1.Microsoft ML Server または Python を使用してデータを探索し、モデルを開発する
R や Python などの言語を使用して、DSVM 上で直接データ分析を実行できます。

R に関しては、スタート メニュー、デスクトップ、または R Tools for Visual Studio で見つけられる RStudio などの IDE を使用できます。 マイクロソフトでは、並列チャンク分析を実行することによってメモリ サイズを超える大きなデータの分析とスケーラブルな分析に対応した特別なライブラリを、オープン ソース R や CRAN R に追加する形で提供してきました。 

Python に関しては、Python Tools for Visual Studio (PTVS) 拡張機能がプレインストールされる Visual Studio Community Edition などの IDE を使用できます。 既定では、Python 3.6 のみ、ルート Conda 環境が PTVS 上に構成されます。 Anaconda Python 2.7 を有効にするために、次の手順を実行する必要があります。

* Visual Studio Community Edition の **[ツール]**  ->  **[Python ツール]**  ->  **[Python 環境]** の順に移動し、 **[+ カスタム]** をクリックして、各バージョンのカスタム環境を作成します。
* 環境の説明を入力し、プレフィックス パス (Anaconda Python 2.7 の場合は *c:\anaconda\envs\python2*) を設定します。
* **[Auto Detect (自動検出)]** をクリックし、 **[Apply (適用)]** をクリックして環境を保存します。

以下のスクリーンショットは、Visual Studio でカスタム環境のセットアップを行っているところです。

![選択した Python Tools for Visual Studio を使用する Visual Studio のスクリーンショット](./media/vm-do-ten-things/PTVSSetup.png)

Python 環境の作成方法の詳細については、 [PTVS のドキュメント](https://aka.ms/ptvsdocs) を参照してください。

これで、新しい Python プロジェクトを作成する準備が整いました。 **[ファイル]**  ->  **[新規作成]**  ->  **[プロジェクト]**  ->  **[Python]** の順に移動し、作成している Python アプリケーションの種類を選択します。 現在のプロジェクトの Python 環境を目的のバージョン (Python 2.7 または 3.6) に設定できます。これには、 **[Python 環境]** を右クリックして、 **[Python 環境の追加/削除]** を選択し、目的の環境を選択します。 PTVS の操作方法の詳細については、製品の[ドキュメント](https://aka.ms/ptvsdocs)を参照してください。

## <a name="2-using-a-jupyter-notebook-to-explore-and-model-your-data-with-python-or-r"></a>2.Jupyter Notebook から Python または R を使用してデータの探索とモデリングを行う
Jupyter Notebook は、ブラウザー ベースの "IDE" でデータの探索とモデリングを行うことができる強力な環境です。 Jupyter Notebook では Python 2 と Python 3 のほか R (オープン ソース R と Microsoft R Server の両方に対応) を使用できます。

Jupyter Notebook を起動するには、[スタート] メニューまたはデスクトップにある **[Jupyter Notebook]** というアイコンをクリックします。 また、DSVM コマンド プロンプトで既存の Notebook が配置されているディレクトリ、または新しい Notebook を作成するディレクトリから、コマンド ```jupyter notebook``` を実行することもできます。  

Jupyter が開始されたら、DSVM に同梱されているいくつかのサンプル Notebook を含んだディレクトリが表示されます。 ここでは、次の操作を実行できます。

* Notebook をクリックしてコードを参照する。
* **Shift キーを押しながら Enter キー**を押すことで各セルを実行する。
* **[Cell (セル)]**  ->  **[Run (実行)]** をクリックして Notebook 全体を実行する。
* 新しい Notebook を作成する。これには、Jupyter アイコン (左上隅) をクリックし、右側の **[New (新規)]** ボタンをクリックして、Notebook の言語 (カーネルとも呼ばれます) を選択します。   

> [!NOTE]
> 現在は、Jupyter の Python 2.7、Python 3.6、R、Julia、および PySpark カーネルがサポートされています。 R カーネルでは、Open source R および パフォーマンスに優れた Microsoft R の両方でのプログラミングをサポートしています。   
> 
> 

Notebook から、任意のライブラリを使って、データの探索、モデルの構築、モデルのテストを実行することができます。

## <a name="3-build-models-using-r-or-python-and-operationalize-them-using-azure-machine-learning"></a>手順 3.R または Python を使ってモデルを構築し、Azure Machine Learning を使って運用可能な状態にする
モデルを構築して検証したら、次にそれを運用環境にデプロイすることになります。 クライアント アプリケーションは、運用環境にデプロイされたモデルを通じて、リアルタイムまたはバッチ モードでそのモデルの予測を呼び出すことができます。 R または Python で構築されたモデルを運用可能な状態にするためのメカニズムは、Azure Machine Learning によって提供されます。

Azure Machine Learning でモデルを運用可能な状態にすると、REST 形式の Web サービスが公開されます。クライアントは、この Web サービスに入力パラメーターを渡し、モデルに基づく予測を出力として受け取ることが可能です。   

> [!NOTE]
> Azure Machine Learning にまだサインアップしていない場合、[Azure Machine Learning Studio](https://studio.azureml.net/) のホーム ページにアクセスして [Get Started] をクリックすると、Free ワークスペースまたは Standard ワークスペースを利用できます。   
> 
> 

### <a name="build-and-operationalize-python-models"></a>Python のモデルを構築して運用可能な状態にする
Python Jupyter Notebook で開発された、SciKit-learn ライブラリを使用して単純なモデルを作成するコード スニペットを次に示します。

```python
# IRIS classification
from sklearn import datasets
from sklearn import svm
clf = svm.SVC()
iris = datasets.load_iris()
X, y = iris.data, iris.target
clf.fit(X, y)
```

Python のモデルを Azure Machine Learning にデプロイするには、モデルの予測を関数にラップし、プレインストールされている Azure Machine Learning Python ライブラリに用意された属性でその関数を修飾します。この属性は、Azure Machine Learning のワークスペース ID、API キー、入力パラメーター、戻り値パラメーターを指定します。  

```python
from azureml import services
@services.publish(workspaceid, auth_token)
@services.types(sep_l=float, sep_w=float, pet_l=float, pet_w=float)
@services.returns(int)  # 0, or 1, or 2
def predictIris(sep_l, sep_w, pet_l, pet_w):
    inputArray = [sep_l, sep_w, pet_l, pet_w]


return clf.predict(inputArray)
```

これでクライアントから Web サービスを呼び出すことができます。 REST API 要求はラッパーによって簡単に構築することができます。 以下、Web サービスを利用するためのサンプル コードを示します。

```python
# Consume through web service URL and keys
from azureml import services
@services.service(url, api_key)
@services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
@services.returns(float)
def IrisPredictor(sep_l, sep_w, pet_l, pet_w):
pass

IrisPredictor(3,2,3,4)
```

> [!NOTE]
> 現時点では、Azure Machine Learning ライブラリは Python 2.7 のみでサポートされています。   
> 
> 

### <a name="build-and-operationalize-r-models"></a>R のモデルを構築して運用可能な状態にする
データ サイエンス仮想マシンやその他の環境で構築された R のモデルは、Python の場合と同様の方法で Azure Machine Learning にデプロイすることができます。 手順は次のようになります。

* ワークスペース ID と認証トークンを提供する settings.json ファイルを作成します。 
* モデルの予測関数のラッパーを作成します。
* Azure Machine Learning ライブラリで ```publishWebService``` を呼び出し、関数ラッパーを渡します。  

Azure Machine Learning でモデルを Web サービスとして設定、ビルド、発行、利用するために使用できる手順とコード スニペットを次に示します。

#### <a name="setup"></a>セットアップ

* ホーム ディレクトリの ```.azureml``` というディレクトリの下に settings.json ファイルを作成し、Azure Machine Learning ワークスペースのパラメーターを入力します。

settings.json ファイルの構造は次のとおりです。

```json
{"workspace":{
"id"                  : "ENTER YOUR AZUREML WORKSPACE ID",
"authorization_token" : "ENTER YOUR AZUREML AUTH TOKEN"
}}
```

#### <a name="build-a-model-in-r-and-publish-it-in-azure-machine-learning"></a>R でモデルを構築して Azure Machine Learning で発行する

```r
library(AzureML)
ws <- workspace(config="~/.azureml/settings.json")

if(!require("lme4")) install.packages("lme4")
library(lme4)
set.seed(1)
train <- sleepstudy[sample(nrow(sleepstudy), 120),]
m <- lm(Reaction ~ Days + Subject, data = train)

# Define a prediction function to publish based on the model:
sleepyPredict <- function(newdata){
        predict(m, newdata=newdata)
}

ep <- publishWebService(ws, fun = sleepyPredict, name="sleepy lm", inputSchema = sleepstudy, data.frame=TRUE)
```

#### <a name="consume-the-model-deployed-in-azure-machine-learning"></a>Azure Machine Learning にデプロイされたモデルを利用する
クライアント アプリケーションからモデルを利用するには、発行した Web サービスを Azure Machine Learning ライブラリを使って検索します。具体的には、`services` API 呼び出しを使って名前で検索することによって、Web サービスのエンドポイントを特定します。 後は、`consume` 関数を呼び出して、予測の対象となるデータ フレームを渡すだけです。
Azure Machine Learning Web サービスとして発行されたモデルを利用するには、次のコードを使用します。

```r
library(AzureML)
library(lme4)
ws <- workspace(config="~/.azureml/settings.json")

s <-  services(ws, name = "sleepy lm")
s <- tail(s, 1) # use the last published function, in case of duplicate function names

ep <- endpoints(ws, s)

# OK, try this out, and compare with raw data
ans = consume(ep, sleepstudy)$ans
```

Azure Machine Learning R ライブラリの詳細については、 [こちら](https://cran.r-project.org/web/packages/AzureML/AzureML.pdf)をご覧ください。

## <a name="4-administer-your-azure-resources-using-azure-portal-or-powershell"></a>4.Azure ポータルまたは PowerShell を使用して Azure リソースを管理する
DSVM を使用すると、仮想マシンに対してローカルに分析ソリューションを構築できるだけでなく、Microsoft Azure クラウド上のさまざまなサービスを利用することができます。 Azure には、コンピューティング サービス、ストレージ サービス、データ分析サービスなど、DSVM から管理してアクセスできるさまざまなサービスが用意されています。

Azure サブスクリプションとクラウド リソースは、ブラウザーから [Azure ポータル](https://portal.azure.com)にアクセスして管理できます。 Azure PowerShell スクリプトを使って、Azure サブスクリプションとリソースを管理することもできます。
Azure PowerShell は、デスクトップ上のショートカットから実行できるほか、"Microsoft Azure Powershell" というスタート メニューから実行できます。 Windows PowerShell スクリプトを使って Azure サブスクリプションとリソースを管理する方法の詳細については、 [Microsoft Azure PowerShell のドキュメント](../../powershell-azure-resource-manager.md) をご覧ください。

## <a name="5-extend-your-storage-space-with-a-shared-file-system"></a>5.共有ファイル システムで記憶域を拡張する
データ サイエンティストは、大きなデータセットやコードなどのリソースをチーム内で共有することができます。 DSVM 自体は、約 45 GB の領域が利用できます。 記憶域を拡張するには、Azure File Service を使用して、記憶域を 1 つまたは複数の DSVM インスタンスにマウントするか、REST API 経由で記憶域にアクセスします。  また、[Azure Portal](../../virtual-machines/windows/attach-managed-disk-portal.md) を使用するか、または [Azure Powershell](../../virtual-machines/windows/attach-disk-ps.md) を使用して、専用の追加データ ディスクを追加することも可能です。 

> [!NOTE]
> Azure ファイル サービス共有の最大領域は 5 TB です。個々のファイル サイズの上限は 1 TB となります。 
> 
> 

Azure File Service の共有場所は、Azure PowerShell を使用して作成することができます。 Azure ファイル サービスの共有場所を作成するには、Azure PowerShell で次のスクリプトを実行します。

```powershell
# Authenticate to Azure.
Connect-AzAccount
# Select your subscription
Get-AzSubscription –SubscriptionName "<your subscription name>" | Select-AzSubscription
# Create a new resource group.
New-AzResourceGroup -Name <dsvmdatarg>
# Create a new storage account. You can reuse existing storage account if you wish.
New-AzStorageAccount -Name <mydatadisk> -ResourceGroupName <dsvmdatarg> -Location "<Azure Data Center Name For eg. South Central US>" -Type "Standard_LRS"
# Set your current working storage account
Set-AzCurrentStorageAccount –ResourceGroupName "<dsvmdatarg>" –StorageAccountName <mydatadisk>

# Create an Azure File Service Share
$s = New-AzStorageShare <<teamsharename>>
# Create a directory under the FIle share. You can give it any name
New-AzStorageDirectory -Share $s -Path <directory name>
# List the share to confirm that everything worked
Get-AzStorageFile -Share $s
```

作成した Azure ファイル共有は、Azure 内の任意の仮想マシンにマウントすることができます。 待機時間を短くし、データ転送にかかる料金を低く抑えるために、VM は、ストレージ アカウントと同じ Azure データ センターに置くことを強くお勧めします。 DSVM にドライブをマウントするコマンドは以下のとおりです。Azure PowerShell で実行してください。

```powershell
# Get storage key of the storage account that has the Azure file share from Azure portal. Store it securely on the VM to avoid prompted in next command.
cmdkey /add:<<mydatadisk>>.file.core.windows.net /user:<<mydatadisk>> /pass:<storage key>

# Mount the Azure file share as Z: drive on the VM. You can chose another drive letter if you wish
net use z:  \\<mydatadisk>.file.core.windows.net\<<teamsharename>>
```

これで、このドライブには、VM 上の通常のドライブとしてアクセスすることができます。

## <a name="6-share-code-with-your-team-using-github"></a>6.GitHub を使ってチームでコードを共有する
GitHub は、開発者コミュニティが共有するコードのリポジトリです。さまざまなテクノロジを利用した各種ツール用のサンプル コードとソースが豊富に格納されています。 GitHub には、コード ファイルのバージョンを追跡して保存する技術として Git が使用されています。 GitHub は、独自のリポジトリを作成するためのプラットフォームとしても利用できます。チームで共有されるコードやドキュメントを保存したり、バージョン管理を導入したり、コードを閲覧できるユーザーとコードを投稿できるユーザーを制御したりすることができます。 Git の使用の詳細については、[GitHub のヘルプ ページ](https://help.github.com/)をご覧ください。 GitHub は、チームで協業したり、コミュニティによって開発されたコードを利用したり、逆にコードをコミュニティに投稿したりするための一つの方法として活用できます。

DSVM には、GitHub リポジトリにアクセスするためのクライアント ツールとして、コマンド ライン版と GUI 版が付属しています。 Git と GitHub を扱うためのコマンド ライン ツールは、Git Bash と呼ばれています。 DSVM にインストールされている Visual Studio には、Git 拡張機能が備わっています。 これらのツールを起動するためのアイコンは、[スタート] メニューとデスクトップにあります。

GitHub リポジトリからコードをダウンロードするには、```git clone``` コマンドを使います。 たとえば、マイクロソフトが発行しているデータ サイエンス リポジトリを現在のディレクトリにダウンロードするには、```git-bash``` から次のコマンドを実行します。

    git clone https://github.com/Azure/DataScienceVM.git

Visual Studio から同じクローン操作を行うこともできます。 下のスクリーンショットでは、Visual Studio で Git と GitHub ツールにアクセスする方法を示しています。

![GitHub 接続が表示される Visual Studio のスクリーンショット](./media/vm-do-ten-things/VSGit.PNG)

Git を使った GitHub リポジトリの操作の詳細については、github.com で公開されているさまざまなリソースをご覧ください。 [チート シート](https://services.github.com/on-demand/downloads/github-git-cheat-sheet.pdf) を参照すると便利です。

## <a name="7-access-various-azure-data-and-analytics-services"></a>7.Azure のさまざまなデータ サービスと分析サービスにアクセスする
### <a name="azure-blob"></a>Azure BLOB
Azure BLOB は、大規模データにも小規模データにも対応する信頼性と経済性に優れたクラウド ストレージです。 このセクションでは、データを Azure BLOB に移動し、Azure BLOB に格納されたデータにアクセスする方法について説明します。

**前提条件**

* **[Azure ポータル](https://portal.azure.com)から Azure BLOB ストレージ アカウントを作成します。**

![Azure portal でのストレージ アカウント作成プロセスのスクリーンショット](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* プレインストールされたコマンド ライン AzCopy ツールが ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe```にあることを確認します。 azcopy.exe を含むディレクトリが既に PATH 環境変数にあり、このツールの実行時に完全なコマンド パスを入力せずに済みます。 AzCopy ツールの詳細については、[AzCopy のドキュメント](../../storage/common/storage-use-azcopy.md)をご覧ください。
* Azure ストレージ エクスプローラー ツールを起動します。 このツールは、 [Microsoft Azure ストレージ エクスプローラー](https://storageexplorer.com/)のページからダウンロードできます。 

![ストレージ アカウントにアクセスする Azure ストレージ エクスプローラーのスクリーンショット](./media/vm-do-ten-things/AzureStorageExplorer_v4.png)

**VM から Azure BLOB にデータを移動する: AzCopy**

ローカル ファイルと Blob Storage との間のデータ移動には、次のように、コマンド ラインまたは PowerShell から AzCopy を使用します。

    AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt

**C:\myfolder** は対象ファイルが保存されているパスに、**mystorageaccount** は BLOB ストレージ アカウント名に、**mycontainer** はコンテナー名に、**storage account key** は BLOB ストレージ アクセス キーにそれぞれ置き換えてください。 ストレージ アカウントの資格情報は、 [Azure ポータル](https://portal.azure.com)で確認できます。

![Azure portal でのストレージ アカウント キーとコンテナー情報のスクリーン ショット](./media/vm-do-ten-things/StorageAccountCredential_v2.png)

AzCopy コマンドは、PowerShell またはコマンド プロンプトから実行します。 以下、AzCopy コマンドの使用例をいくつか紹介します。

```powershell
# Copy *.sql from local machine to an Azure Blob
"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:"c:\Aaqs\Data Science Scripts" /Dest:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /DestKey:[ENTER STORAGE KEY] /S /Pattern:*.sql

# Copy back all files from Azure Blob container to Local machine

"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Dest:"c:\Aaqs\Data Science Scripts\temp" /Source:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /SourceKey:[ENTER STORAGE KEY] /S
```

AzCopy コマンドを実行して Azure BLOB にコピーするとすぐに、対象ファイルが Azure ストレージ エクスプローラーに表示されます。

![アップロードされた CSV ファイルを表示する、ストレージ アカウントのスクリーン ショット](./media/vm-do-ten-things/AzCopy_run_finshed_Storage_Explorer_v3.png)

**VM から Azure BLOB にデータを移動する: Azure Storage Explorer**

Azure ストレージ エクスプローラーを使用して、VM 内のローカル ファイルからデータをアップロードすることもできます。

* コンテナーにデータをアップロードするには、対象のコンテナーを選択し、 **[アップロード]** ボタンをクリックします。![Azure Storage Explorer の [アップロード] ボタンのスクリーンショット](./media/vm-do-ten-things/storage-accounts.png)
* **[ファイル]** ボックスの右側の **[...]** をクリックし、アップロードする 1 つまたは複数のファイルをファイル システムから選択します。 **[アップロード]** をクリックして、ファイルのアップロードを開始します。![ファイルのアップロード ダイアログのスクリーンショット](./media/vm-do-ten-things/upload-files-to-blob.png)

**Azure Blob からデータの読み取り: Machine Learning リーダー モジュール**

Azure Machine Learning Studio では、 **データのインポート モジュール** を使用して BLOB からデータを読み取ることができます。

![Machine Learning Studio でのデータのインポート モジュールのスクリーンショット](./media/vm-do-ten-things/AML_ReaderBlob_Module_v3.png)

**Azure Blob からデータの読み取り: Python ODBC**

**BlobService** ライブラリを使用すると、Jupyter Notebook または Python プログラムで BLOB から直接データを読み取ることができます。

まず、必要なパッケージをインポートします。

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

次に、Azure BLOB アカウントの資格情報を追加し、BLOB からデータを読み取ります。

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

#unzipping downloaded files if needed
#with zipfile.ZipFile(ZIPPEDLOCALFILE, "r") as z:
#    z.extractall(LOCALDIRECTORY)

df1 = pd.read_csv(LOCALFILE, header=0)
df1.columns = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime','passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude']
print 'the size of the data is: %d rows and  %d columns' % df1.shape
```

データはデータ フレームとして読み取られます。

![データの最初の 10 行のスクリーンショット](./media/vm-do-ten-things/IPNB_data_readin.PNG)

### <a name="azure-data-lake"></a>Azure Data Lake
Azure Data Lake Storage は、ビッグ データ分析ワークロード用のハイパースケール リポジトリであり、Hadoop 分散ファイルシステム (HDFS) と互換性があります。 Hadoop、Spark、および Azure Data Lake Analytics で動作します。 このセクションでは、データを Azure Data Lake Store に移動し、Azure Data Lake Analytics を使用して分析を実行する方法について説明します。

**前提条件**

* [Azure ポータル](https://portal.azure.com)で Azure Data Lake Analytics を作成します。

![Azure portal から Data Lake Analytics の作成のスクリーンショット](./media/vm-do-ten-things/Azure_Data_Lake_Create_v2.png)

* こちらの[リンク](https://www.microsoft.com/download/details.aspx?id=49504)から入手できる **Azure Data Lake Tools** for **Visual Studio** は、仮想マシン上の Visual Studio Community Edition に既にインストールされています。 Visual Studio を起動して Azure サブスクリプションにログインすると、Visual Studio の左パネルに Azure Data Analytics のアカウントとストレージが表示されます。

![Visual Studio での Data Lake Tools のスクリーンショット](./media/vm-do-ten-things/Azure_Data_Lake_PlugIn_v2.PNG)

**VM から Data Lake へのデータの移動: Azure Data Lake エクスプローラー**

**Azure Data Lake エクスプローラー** を使用して仮想マシン内のローカル ファイルから Data Lake Storage にデータをアップロードできます。

![ファイルのアップロードに Data Lake エクスプローラーを使用しているスクリーンショット](./media/vm-do-ten-things/Azure_Data_Lake_UploadData.PNG)

また、[Azure Data Factory (ADF)](https://azure.microsoft.com/services/data-factory/) を使用して、Azure Data Lake との間のデータ移動を運用可能にするデータ パイプラインを作成することもできます。 データ パイプラインを作成する手順については、こちらの[記事](https://azure.microsoft.com/blog/creating-big-data-pipelines-using-azure-data-lake-and-azure-data-factory/)をご覧ください。

**Azure BLOB から Data Lake にデータを読み取る: U-SQL**

データが Azure Blob Storage に存在する場合、U-SQL クエリで Azure Storage BLOB から直接データを読み取ることができます。 U-SQL クエリを作成する前に、ご利用の BLOB ストレージ アカウントが Azure Data Lake にリンクされていることを確認します。 **Azure Portal** に移動して [Azure Data Lake Analytics] ダッシュボードを探し、 **[データ ソースの追加]** をクリックします。ストレージの種類に **[Azure Storage]** を選択し、Azure ストレージ アカウントの名前とキーを追加します。 これで、ストレージ アカウントに保存されているデータを参照できます。

![[データ ソースの追加] ダイアログのスクリーンショット](./media/vm-do-ten-things/Link_Blob_to_ADLA_v2.PNG)

Visual Studio では、Blob Storage からのデータ読み取り、各種データ操作、特徴エンジニアリングを実行するほか、結果として得られたデータを Azure Data Lake または Azure Blob Storage に出力することができます。 BLOB ストレージ内のデータを参照するときは **wasb://** を使用し、Azure Data Lake 内のデータを参照するときは **swbhdfs://** を使用します。

![強調表示されている WASB エントリがあるクエリのスクリーンショット](./media/vm-do-ten-things/USQL_Read_Blob_v2.PNG)

Visual Studio で次の U-SQL クエリを使用することができます。

```usql
@a =
    EXTRACT medallion string,
            hack_license string,
            vendor_id string,
            rate_code string,
            store_and_fwd_flag string,
            pickup_datetime string,
            dropoff_datetime string,
            passenger_count int,
            trip_time_in_secs double,
            trip_distance double,
            pickup_longitude string,
            pickup_latitude string,
            dropoff_longitude string,
            dropoff_latitude string

    FROM "wasb://<Container name>@<Azure Blob Storage Account Name>.blob.core.windows.net/<Input Data File Name>"
    USING Extractors.Csv();

@b =
    SELECT vendor_id,
    COUNT(medallion) AS cnt_medallion,
    SUM(passenger_count) AS cnt_passenger,
    AVG(trip_distance) AS avg_trip_dist,
    MIN(trip_distance) AS min_trip_dist,
    MAX(trip_distance) AS max_trip_dist,
    AVG(trip_time_in_secs) AS avg_trip_time
    FROM @a
    GROUP BY vendor_id;

OUTPUT @b   
TO "swebhdfs://<Azure Data Lake Storage Account Name>.azuredatalakestore.net/<Folder Name>/<Output Data File Name>"
USING Outputters.Csv();

OUTPUT @b   
TO "wasb://<Container name>@<Azure Blob Storage Account Name>.blob.core.windows.net/<Output Data File Name>"
USING Outputters.Csv();
```

クエリがサーバーに送信されると、ジョブのステータスを示すダイアグラムが表示されます。

![ジョブ ダイアログの状態のスクリーンショット](./media/vm-do-ten-things/USQL_Job_Status.PNG)

**Data Lake 内のデータを照会する: U-SQL**

Azure Data Lake にデータセットを取り込んだ後、[U-SQL 言語](../../data-lake-analytics/data-lake-analytics-u-sql-get-started.md)を使用してデータを照会、探索することができます。 U-SQL 言語は T-SQL と似ていますが、C# の機能が一部組み合わされているため、独自のモジュールやユーザー定義関数を作成することが可能です。先ほどの手順で紹介したスクリプトを使用することができます。

サーバーにクエリを送信するとすぐに、**Azure Data Lake エクスプローラー**に tripdata_summary.CSV が表示されるので、そのファイルを右クリックしてデータをプレビューすることができます。

![Data Lake エクスプローラーでの csv ファイルのスクリーンショット](./media/vm-do-ten-things/USQL_create_summary.png)

ファイルの情報が次のように表示されます。

![ファイル概要情報のスクリーンショット](./media/vm-do-ten-things/USQL_tripdata_summary.png)

### <a name="hdinsight-hadoop-clusters"></a>HDInsight Hadoop クラスター
Azure HDInsight は、クラウド上で管理されている Apache Hadoop、Spark、HBase、および Storm サービスです。 Azure HDInsight クラスターは、データ サイエンス仮想マシンから簡単に操作できます。

**前提条件**

* [Azure ポータル](https://portal.azure.com)から Azure BLOB ストレージ アカウントを作成します。 HDInsight クラスターのデータは、このストレージ アカウントを使用して格納されます。

![Azure portal からの HDInsight の作成のスクリーンショット](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* [Azure ポータル](../team-data-science-process/customize-hadoop-cluster.md)
  
  * 作成したストレージ アカウントは、この HDInsight クラスターに対して作成時にリンクします。 このストレージ アカウントは、クラスター内で処理可能なデータにアクセスするために使用されます。

![作成したストレージ アカウントを HDInsight クラスターにリンクする](./media/vm-do-ten-things/Create_HDI_v4.PNG)

* 作成したら、クラスターのヘッド ノードへの**リモート アクセス**を有効にします。 ここで指定するリモート アクセスの資格情報は、以降の手順で必要になるため、記憶しておいてください。

![HDInsight クラスターへのリモート アクセスを有効にします。](./media/vm-do-ten-things/Create_HDI_dashboard_v3.PNG)

* Azure Machine Learning ワークスペースを作成します。 Machine Learning の実験は、この Machine Learning ワークスペースに保存されます。 下のスクリーンショットに示されるように、ポータルで強調表示されているオプションを選択します。

![Azure Machine Learning ワークスペースの作成](./media/vm-do-ten-things/Create_ML_Space.PNG)

* 次に、ワークスペースのパラメーターを入力します。

![Machine Learning ワークスペース パラメーターの入力](./media/vm-do-ten-things/Create_ML_Space_step2_v2.PNG)

* IPython Notebook を使用してデータをアップロードします。 まず、お使いのストレージ アカウントで、必要なパッケージをインポートし、資格情報を追加して、データベースを作成した後、データを HDI クラスターに読み込みます。

```python
# Import required Packages
import pyodbc
import time as time
import json
import os
import urllib
import urllib2
import warnings
import re
import pandas as pd
import matplotlib.pyplot as plt
from azure.storage.blob import BlobService
warnings.filterwarnings("ignore", category=UserWarning, module='urllib2')


# Create the connection to Hive using ODBC
SERVER_NAME = 'xxx.azurehdinsight.net'
DATABASE_NAME = 'nyctaxidb'
USERID = 'xxx'
PASSWORD = 'xxxx'
DB_DRIVER = 'Microsoft Hive ODBC Driver'
driver = 'DRIVER={' + DB_DRIVER + '}'
server = 'Host=' + SERVER_NAME + ';Port=443'
database = 'Schema=' + DATABASE_NAME
hiveserv = 'HiveServerType=2'
auth = 'AuthMech=6'
uid = 'UID=' + USERID
pwd = 'PWD=' + PASSWORD
CONNECTION_STRING = ';'.join(
    [driver, server, database, hiveserv, auth, uid, pwd])
connection = pyodbc.connect(CONNECTION_STRING, autocommit=True)
cursor = connection.cursor()


# Create Hive database and tables
queryString = "create database if not exists nyctaxidb;"
cursor.execute(queryString)

queryString = """
                create external table if not exists nyctaxidb.trip
                (
                    medallion string,
                    hack_license string,
                    vendor_id string,
                    rate_code string,
                    store_and_fwd_flag string,
                    pickup_datetime string,
                    dropoff_datetime string,
                    passenger_count int,
                    trip_time_in_secs double,
                    trip_distance double,
                    pickup_longitude double,
                    pickup_latitude double,
                    dropoff_longitude double,
                    dropoff_latitude double)  
                PARTITIONED BY (month int)
                ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\\n'
                STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');
            """
cursor.execute(queryString)

queryString = """
                create external table if not exists nyctaxidb.fare
                (
                    medallion string,
                    hack_license string,
                    vendor_id string,
                    pickup_datetime string,
                    payment_type string,
                    fare_amount double,
                    surcharge double,
                    mta_tax double,
                    tip_amount double,
                    tolls_amount double,
                    total_amount double)
                PARTITIONED BY (month int)
                ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\\n'
                STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');
            """
cursor.execute(queryString)


# Upload data from blob storage to HDI cluster
for i in range(1, 13):
    queryString = "LOAD DATA INPATH 'wasb:///nyctaxitripraw2/trip_data_%d.csv' INTO TABLE nyctaxidb2.trip PARTITION (month=%d);" % (
        i, i)
    cursor.execute(queryString)
    queryString = "LOAD DATA INPATH 'wasb:///nyctaxifareraw2/trip_fare_%d.csv' INTO TABLE nyctaxidb2.fare PARTITION (month=%d);" % (
        i, i)
    cursor.execute(queryString)
```

* また、こちらの[チュートリアル](../team-data-science-process/hive-walkthrough.md)に従って NYC Taxi データを HDI クラスターにアップロードすることもできます。 主な手順は次のとおりです。
  
  * AzCopy: zip 圧縮された CSV をパブリック BLOB からローカル フォルダーにダウンロードする
  * AzCopy: 解凍した CSV をローカル フォルダーから HDI クラスターにアップロードする
  * Hadoop クラスターのヘッド ノードにログインして探索的データ分析の準備をする

HDI クラスターにデータを読み込んだら、Azure Storage エクスプローラーでデータを確認することができます。 加えて、HDI クラスターには、nyctaxidb というデータベースが作成されます。

**データデータ探索: Python での Hive クエリ**

Hadoop クラスターにデータが存在するため、pyodbc パッケージを使用して Hadoop クラスターに接続し、Hive を使ってデータベースを照会することによって探索や特徴エンジニアリングを実行できます。 前提条件の手順で作成した既存のテーブルを表示できます。

```python
queryString = """
    show tables in nyctaxidb2;
    """
pd.read_sql(queryString, connection)
```

![既存のテーブルを表示する](./media/vm-do-ten-things/Python_View_Existing_Tables_Hive_v3.PNG)

毎月のレコード件数とチップの頻度を trip テーブルで調べてみましょう。

```python
queryString = """
    select month, count(*) from nyctaxidb.trip group by month;
    """
results = pd.read_sql(queryString,connection)

%matplotlib inline

results.columns = ['month', 'trip_count']
df = results.copy()
df.index = df['month']
df['trip_count'].plot(kind='bar')
```

![毎月のレコード数をプロットする](./media/vm-do-ten-things/Exploration_Number_Records_by_Month_v3.PNG)

```python
queryString = """
    SELECT tipped, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;
    """
results = pd.read_sql(queryString, connection)

results.columns = ['tipped', 'trip_count']
df = results.copy()
df.index = df['tipped']
df['trip_count'].plot(kind='bar')
```

![チップの頻度をプロットする](./media/vm-do-ten-things/Exploration_Frequency_tip_or_not_v3.PNG)

乗車位置と降車位置との距離を計算し、トリップ距離と比較することもできます。

```python
queryString = """
                select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
                    3959*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
                    *radians(180)/180/2),2)-cos(pickup_latitude*radians(180)/180)
                    *cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2)))
                    /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*radians(180)/180/2),2)
                    +cos(pickup_latitude*radians(180)/180)*cos(dropoff_latitude*radians(180)/180)*
                    pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2))) as direct_distance
                    from nyctaxidb.trip
                    where month=1
                        and pickup_longitude between -90 and -30
                        and pickup_latitude between 30 and 90
                        and dropoff_longitude between -90 and -30
                        and dropoff_latitude between 30 and 90;
            """
results = pd.read_sql(queryString, connection)
results.head(5)
```

![乗車と降車テーブルの上位の行](./media/vm-do-ten-things/Exploration_compute_pickup_dropoff_distance_v2.PNG)

```python
results.columns = ['pickup_longitude', 'pickup_latitude', 'dropoff_longitude',
                   'dropoff_latitude', 'trip_distance', 'trip_time_in_secs', 'direct_distance']
df = results.loc[results['trip_distance'] <= 100]  # remove outliers
df = df.loc[df['direct_distance'] <= 100]  # remove outliers
plt.scatter(df['direct_distance'], df['trip_distance'])
```

![乗車と降車の距離からトリップ距離をプロットする](./media/vm-do-ten-things/Exploration_direct_distance_trip_distance_v2.PNG)

次に、モデリング用にダウンサンプル (1%) したデータ セットを用意します。 このデータを Machine Learning リーダー モジュールで使用できます。

```python
queryString = """
create  table if not exists nyctaxi_downsampled_dataset_testNEW (
medallion string,
hack_license string,
vendor_id string,
rate_code string,
store_and_fwd_flag string,
pickup_datetime string,
dropoff_datetime string,
pickup_hour string,
pickup_week string,
weekday string,
passenger_count int,
trip_time_in_secs double,
trip_distance double,
pickup_longitude double,
pickup_latitude double,
dropoff_longitude double,
dropoff_latitude double,
direct_distance double,
payment_type string,
fare_amount double,
surcharge double,
mta_tax double,
tip_amount double,
tolls_amount double,
total_amount double,
tipped string,
tip_class string
)
row format delimited fields terminated by ','
lines terminated by '\\n'
stored as textfile;
"""
cursor.execute(queryString)
```

join の内容を前述の内部テーブルに挿入します。

```python
queryString = """
insert overwrite table nyctaxi_downsampled_dataset_testNEW
select
t.medallion,
t.hack_license,
t.vendor_id,
t.rate_code,
t.store_and_fwd_flag,
t.pickup_datetime,
t.dropoff_datetime,
hour(t.pickup_datetime) as pickup_hour,
weekofyear(t.pickup_datetime) as pickup_week,
from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
t.passenger_count,
t.trip_time_in_secs,
t.trip_distance,
t.pickup_longitude,
t.pickup_latitude,
t.dropoff_longitude,
t.dropoff_latitude,
t.direct_distance,
f.payment_type,
f.fare_amount,
f.surcharge,
f.mta_tax,
f.tip_amount,
f.tolls_amount,
f.total_amount,
if(tip_amount>0,1,0) as tipped,
if(tip_amount=0,0,
if(tip_amount>0 and tip_amount<=5,1,
if(tip_amount>5 and tip_amount<=10,2,
if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class
from
(
select
medallion,
hack_license,
vendor_id,
rate_code,
store_and_fwd_flag,
pickup_datetime,
dropoff_datetime,
passenger_count,
trip_time_in_secs,
trip_distance,
pickup_longitude,
pickup_latitude,
dropoff_longitude,
dropoff_latitude,
3959*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
radians(180)/180/2),2)-cos(pickup_latitude*radians(180)/180)
*cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2)))
/sqrt(pow(sin((dropoff_latitude-pickup_latitude)*radians(180)/180/2),2)
+cos(pickup_latitude*radians(180)/180)*cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2))) as direct_distance,
rand() as sample_key

from trip
where pickup_latitude between 30 and 90
    and pickup_longitude between -90 and -30
    and dropoff_latitude between 30 and 90
    and dropoff_longitude between -90 and -30
)t
join
(
select
medallion,
hack_license,
vendor_id,
pickup_datetime,
payment_type,
fare_amount,
surcharge,
mta_tax,
tip_amount,
tolls_amount,
total_amount
from fare
)f
on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
where t.sample_key<=0.01
"""
cursor.execute(queryString)
```

しばらくすると、Hadoop クラスターにデータが読み込まれていることを確認できます。

```python
queryString = """
    select * from nyctaxi_downsampled_dataset limit 10;
    """
cursor.execute(queryString)
pd.read_sql(queryString, connection)
```

![テーブルのデータの上位の行](./media/vm-do-ten-things/DownSample_Data_For_Modeling_v2.PNG)

**Machine Learning のリーダー モジュール を使用して HDI からのデータを読み取る**

Machine Learning Studio で **リーダー** モジュールを使用して、Hadoop クラスター内のデータベースにアクセスすることもできます。 HDI クラスターと Azure ストレージ アカウントの資格情報を追加すると、HDI クラスター内のデータベースを使用して機械学習モデルを構築できるようになります。

![リーダー モジュールのプロパティ](./media/vm-do-ten-things/AML_Reader_Hive.PNG)

さらに、スコア付けされたデータセットを表示できます。

![スコア付けされたデータセットを表示する](./media/vm-do-ten-things/AML_Model_Results.PNG)

### <a name="azure-sql-data-warehouse--databases"></a>Azure SQL Data Warehouse と Azure SQL データベース
Azure SQL Data Warehouse は、エンタープライズ規模の SQL Server エクスペリエンスを備えた、サービスとしてのエラスティック データ ウェアハウスです。

Azure SQL Data Warehouse は、こちらの [記事](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)で説明されている手順に従ってプロビジョニングできます。 Azure SQL Data Warehouse をプロビジョニングしたら、こちらの[チュートリアル](../team-data-science-process/sqldw-walkthrough.md)を使って、SQL Data Warehouse 内のデータを使用したデータのアップロード、探索、モデリングを実行できます。

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB
Azure Cosmos DB は、クラウドにおける NoSQL データベースです。 これにより、JSON などのドキュメントを扱うことも、そうしたドキュメントを格納したり照会したりすることもできます。

以下に、DSVM から Azure Cosmos DB にアクセスするための前提条件となる手順を示します。

1. Azure Cosmos DB Python SDK が既に DSVM 上にインストールされています (コマンド プロンプトから ```pip install pydocumentdb --upgrade``` を実行して更新します)。
2. [Azure Portal](https://portal.azure.com) で Azure Cosmos DB アカウントとデータベースを作成します。
3. [こちら](https://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d) から "Azure Cosmos DB 移行ツール" をダウンロードし、任意のディレクトリに展開します
4. 移行ツール (Cosmos DB 移行ツールをインストールしたディレクトリの dtui.exe) に次のコマンド パラメーターを指定して、 [パブリック BLOB](https://cahandson.blob.core.windows.net/samples/volcano.json) に格納されている JSON データ (volcano データ) を Cosmos DB にインポートします。 これらのパラメーターでインポート元とインポート先の場所を入力します。
   
    `/s:JsonFile /s.Files:https://cahandson.blob.core.windows.net/samples/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1`

データのインポート後、Jupyter にアクセスして、*DocumentDBSample* というタイトルの Notebook を開くことができます。この Notebook には、Azure Cosmos DB にアクセスして基本的なクエリを実行する Python コードが含まれています。 Cosmos DB の詳細については、サービスの[ドキュメント ページ](https://docs.microsoft.com/azure/cosmos-db/)をご覧ください。

## <a name="8-build-reports-and-dashboard-using-the-power-bi-desktop"></a>8.Power BI Desktop を使ってレポートとダッシュボードを作成する
先ほどの例の Cosmos DB からの Volcano JSON ファイルを Power BI で視覚化して、データの本質を視覚的に把握することができます。 詳しい手順については、[Power BI の記事](../../cosmos-db/powerbi-visualize.md)を参照してください。 手順の概要は次のとおりです。

1. Power BI Desktop を開いて "データを取得" します。 URL に https://cahandson.blob.core.windows.net/samples/volcano.json を指定します。
2. リストとしてインポートされた JSON レコードが表示されます。
3. そのリストを Power BI が処理できるようテーブルに変換します。
4. 展開アイコン (列の右側にある "左矢印と右矢印" のアイコン) をクリックして列を展開します。
5. その位置が "Record" フィールドであることに注目します。 そのレコードを展開し、coordinates のみを選択してください。 coordinate はリスト列です。
6. 新しい列を追加し、 ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})```という式を使って、リストの coordinate 列を、coordinate リスト フィールドの 2 つの要素を連結したコンマ区切りの LatLong 列に変換します。
7. 最後に、```Elevation``` 列を Decimal に変換し、 **[閉じる]** と **[適用]** を選択します。

以下のコードは、データ変換をクエリ言語で記述できる Power BI の詳細エディターで使用した手順をスクリプト化したものです。前の手順を実行する代わりに、このコードをコピーして貼り付けてもかまいません。

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

これで Power BI データ モデルのデータができました。 Power BI Desktop は、次のようになります。

![Power BI Desktop](./media/vm-do-ten-things/PowerBIVolcanoData.png)

このデータ モデルを使ってレポートの作成と視覚化の作業を開始できます。 レポートを作成するには、こちらの [Power BI の記事](../../cosmos-db/powerbi-visualize.md#build-the-reports) にある手順に従います。 出力は次のようなレポートになります。

![Power BI Desktop のレポート ビュー- Power BI コネクタ](./media/vm-do-ten-things/power_bi_connector_pbireportview2.png)

## <a name="9-dynamically-scale-your-dsvm-to-meet-your-project-needs"></a>9.DSVM を動的に拡張してプロジェクトのニーズを満たす
プロジェクトのニーズを満たすために、DSVM はスケールアップしたりスケールダウンしたりすることができます。 夜間や週末に VM を使用する必要がない場合は、 [Azure ポータル](https://portal.azure.com)から VM をシャットダウンします。

> [!NOTE]
> VM でオペレーティング システムのシャットダウン ボタンを使用しただけであっても、コンピューティングの料金は発生します。  
> 
> 

大規模な分析で CPU の処理能力やメモリ量、ディスク容量が足りなくなった場合は、CPU コア数、ディープ ラーニングの GPU ベース インスタンス、メモリ容量、ディスクの種類 (ソリッド ステート ドライブなど) の観点から、処理能力や予算のニーズに応じて大きな VM サイズを選ぶことができます。 VM とその時間単位のコンピューティング料金をまとめた一覧については、「 [Azure Virtual Machines の価格](https://azure.microsoft.com/pricing/details/virtual-machines/) 」ページをご覧ください。

同様に、さほど大きな処理能力が VM に必要なくなった場合 (主なワークロードを Hadoop または Spark クラスターに移動したなど)、 [Azure ポータル](https://portal.azure.com) から、対象となる VM インスタンスの設定にアクセスして、クラスターをスケールダウンすることができます。 スクリーンショットを次に示します。

![VM インスタンスの設定](./media/vm-do-ten-things/VMScaling.PNG)

## <a name="10-install-additional-tools-on-your-virtual-machine"></a>10.仮想マシンに追加のツールをインストールする
多くの一般的なデータ分析のニーズに対応できる、DSVM に事前に組み込まれた複数のツールがあります。 これにより、1 つずつインストールして環境を構成しなくてもよいため、時間を節約できると共に、使用するリソースのみに対して支払いを行うことで、コストを節約できます。

この記事の中で取り上げた他の Azure データ サービスと Azure 分析サービスを利用して、分析環境を強化することができます。 場合によっては、サード パーティが独自に開発したツールなど、特別なツールが必要になることもあります。 新しいツールが必要となった場合、インストールには、仮想マシンに対するフル管理者アクセス権が必要です。 プレインストールされない、Python と R の追加パッケージをインストールすることもできます。 Python の場合、```conda``` または ```pip``` を使用できます。 R の場合は、R コンソールで ```install.packages()``` を使用するか、IDE で **[Packages (パッケージ)]**  ->  **[Install Packages (パッケージのインストール)]** を選択できます。

## <a name="summary"></a>まとめ
ここで紹介したのは、Microsoft データ サイエンス仮想マシンでできることの一例にすぎません。 他にもさまざまな手段で分析環境を強化することができます。

