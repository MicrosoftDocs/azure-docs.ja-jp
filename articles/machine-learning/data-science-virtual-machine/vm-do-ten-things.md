---
title: Windows でのデータとモデルの探索
titleSuffix: Azure Data Science Virtual Machine
description: Windows Data Science Virtual Machine 上でデータ探索およびモデリングのタスクを実行します。
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 6af9f2c80915745f995144c24ae1649fb02a31ae
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2019
ms.locfileid: "70192280"
---
# <a name="ten-things-you-can-do-on-the-windows-data-science-virtual-machine"></a>Windows データ サイエンス仮想マシンでできる 10 のこと

Windows Data Science Virtual Machine (DSVM) は強力なデータ サイエンス開発環境であり、ここではデータ探索およびモデリング タスクを実行することができます。 この環境は既に構築されていて、いくつかの一般的なデータ分析ツールがバンドルされているため、オンプレミス、クラウド、ハイブリッドの各デプロイの分析を簡単に開始することができます。 

DSVM は Azure サービスと密接に連携します。 Azure 上の Azure SQL Data Warehouse、Azure Data Lake、Azure Storage、または Azure Cosmos DB に既に保存されているデータを読み取って処理することができます。 Azure Machine Learning や Azure Data Factory など、その他の分析ツールを利用することもできます。

この記事では、DSVM を使用してデータ サイエンス タスクを実行したり、他の Azure サービスとやりとりしたりする方法について説明します。 DSVM では次のことを実行できます。

- DSVM 上で Microsoft Machine Learning Server および Python を使用してデータの探索およびモデルの開発をローカルで行います。
- Jupyter ノートブックを使用して、Python 2、Python 3、Microsoft R を使用して、ご自分のデータをブラウザーで実験します (Microsoft R は、パフォーマンスを考慮して設計された、エンタープライズ対応の R バージョンです)。
- R や Python を通して構築されたモデルを Azure Machine Learning 上にデプロイすると、クライアント アプリケーションからシンプルな Web サービス インターフェイスを使用してモデルにアクセスできるようになります。
- Azure portal または PowerShell を使用して Azure リソースを管理します。
- ご利用の DSVM にマウント可能なドライブとして Azure Files 共有を作成することによってストレージ スペースを拡張し、大規模なデータセット/コードをチーム全体で共有します。
- GitHub を使用してチームとコードを共有します。 インストール済みの次の Git クライアントを使用してリポジトリにアクセスします: Git Bash と Git GUI。
- Azure Blob Storage、Azure Data Lake、Azure HDInsight (Hadoop)、Azure Cosmos DB、Azure SQL Data Warehouse、Azure SQL Database など、Azure のデータ サービスと分析サービスにアクセスします。
- DSVM にプレインストールされた Power BI Desktop インスタンスを使用してレポートとダッシュボードを作成し、それらをクラウドにデプロイします。
- プロジェクトのニーズを満たすように、ご利用の DSVM を動的にスケーリングします。
- ご利用の仮想マシン上に追加のツールをインストールします。   

> [!NOTE]
> この記事に一覧したデータ ストレージ サービスおよび分析サービスの多くには、追加の使用料金が適用されます。 詳細については、「[Azure の価格](https://azure.microsoft.com/pricing/)」を参照してください。
> 
> 

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションが必要です。 [無料試用版にサインアップ](https://azure.microsoft.com/free/)できます。
* Azure portal で Data Science Virtual Machine をプロビジョニングする手順については、[仮想マシンの作成](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016)に関するページをご覧ください。


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="explore-data-and-develop-models-with-microsoft-machine-learning-server"></a>Microsoft Machine Learning Server を使用してデータを探索し、モデルを開発する
R や Python などの言語を使用して、DSVM 上で直接データ分析を実行できます。

R の場合は、スタート メニューまたはデスクトップにある RStudio などの IDE を使用できます。 または、R Tools for Visual Studio を使用することもできます。 Microsoft は、オープン ソース CRAN R の上に追加のライブラリを提供することで、スケーラブルな分析を可能にし、並列チャンク分析で許容されたメモリ サイズを超える大きなデータを分析する機能を有効にしました。 

Python に関しては、Python Tools for Visual Studio (PTVS) 拡張機能がプレインストールされる Visual Studio Community Edition などの IDE を使用できます。 既定では、ルート Conda 環境である Python 3.6 のみが PTVS で構成されます。 Anaconda Python 2.7 を有効にするには、次の手順を行います。

1. Visual Studio Community Edition 内で **[ツール]**  >  **[Python ツール]**  >  **[Python 環境]** の順に移動し、 **[+ カスタム]** を選択して、各バージョンのカスタム環境を作成します。
1. 説明を入力し、環境プレフィックス パス (Anaconda Python 2.7 の場合は **c:\anaconda\envs\python2**) を設定します。
1. **[自動検出]**  >  **[適用]** の順に選択して環境を保存します。

Python 環境の作成方法の詳細については、[PTVS のドキュメント](https://aka.ms/ptvsdocs)を参照してください。

これで、新しい Python プロジェクトを作成する準備が整いました。 **[ファイル]**  >  **[新規作成]**  >  **[プロジェクト]**  >  **[Python]** の順に移動し、作成する Python アプリケーションの種類を選択します。 現在のプロジェクトの Python 環境を目的のバージョン (Python 2.7 または 3.6) に設定できます。これには、 **[Python 環境]** を右クリックして、 **[Python 環境の追加/削除]** を選択します。 PTVS の操作方法の詳細については、製品の[製品のドキュメント](https://aka.ms/ptvsdocs)を参照してください。

## <a name="use-jupyter-notebooks"></a>Jupyter Notebook の使用
Jupyter Notebook には、データ探索とモデリング用のブラウザーベースの IDE が用意されています。 Jupyter Notebook では Python 2、Python 3、R (オープン ソースおよび Microsoft R Server の両方) のいずれを使用することもできます。

Jupyter Notebook を開始するには、 **[スタート]** メニューまたはデスクトップで **[Jupyter Notebook]** アイコンを選択します。 また、DSVM コマンド プロンプトで既存の Notebook が配置されているディレクトリ、または新しいノートブックを作成するディレクトリから、コマンド ```jupyter notebook``` を実行することもできます。  

Jupyter を開始すると、DSVM にあらかじめパッケージ化されているいくつかのノートブックの例を含んだディレクトリが表示されます。 ここでは、次の操作を実行できます。

* ノートブックを選択してコードを表示します。
* Shift + Enter キーを選択して、各セルを実行します。
* **[セル]**  >  **[実行]** の順にクリックしてノートブック全体を実行します。
* 新しいノートブックを作成します。それには、Jupyter アイコン (左上隅) を選択し、右側の **[新規]** ボタンを選択して、ノートブックの言語 (カーネルとも呼ばれます) を選択します。   

> [!NOTE]
> 現在は、Jupyter の Python 2.7、Python 3.6、R、Julia、および PySpark カーネルがサポートされています。 R カーネルでは、オープンソース R および Microsoft R の両方でのプログラミングがサポートされています。   
> 
> 

ノートブックを開いているときは、ご自分で選んだライブラリを使用して、ご利用のデータの探索、モデルの構築、モデルのテストを行うことができます。

## <a name="train-and-deploy-models-by-using-azure-machine-learning"></a>Azure Machine Learning を使用してモデルをトレーニングおよびデプロイする
モデルを構築して検証したら、通常、次の手順として、それを運用環境にデプロイします。 この手順では、クライアント アプリケーションを使用して、リアルタイムまたはバッチ モードでモデルの予測を呼び出すことができます。 R または Python で構築されたモデルを運用可能な状態にするためのメカニズムは、Azure Machine Learning によって提供されます。

Azure Machine Learning でモデルを運用化すると、Web サービスが公開されます。 これにより、クライアントは、入力パラメーターを渡して、出力としてモデルから予測を受け取る REST 呼び出しを実行することができます。   

> [!NOTE]
> Azure Machine Learning にまだサインアップしていない場合は、[Azure Machine Learning Studio](https://studio.azureml.net/) のホーム ページにアクセスして **[作業の開始]** を選択すると、無料のワークスペースまたは Standard ワークスペースを利用できます。   
> 
> 

### <a name="build-and-operationalize-python-models"></a>Python モデルを構築して運用可能な状態にする
Python Jupyter ノートブックで開発された、Scikit-learn ライブラリを使用してシンプルなモデルを作成するコード スニペットを次に示します。

```python
# IRIS classification
from sklearn import datasets
from sklearn import svm
clf = svm.SVC()
iris = datasets.load_iris()
X, y = iris.data, iris.target
clf.fit(X, y)
```

Python のモデルを Azure Machine Learning にデプロイするために使用するメソッドでは、モデルの予測が関数にラップされ、プレインストールされている Azure Machine Learning Python ライブラリに用意された属性でそれが修飾されます。 この属性では、Azure Machine Learning のワークスペース ID、API キー、入力パラメーター、戻り値パラメーターが指定されます。  

```python
from azureml import services
@services.publish(workspaceid, auth_token)
@services.types(sep_l=float, sep_w=float, pet_l=float, pet_w=float)
@services.returns(int)  # 0, or 1, or 2
def predictIris(sep_l, sep_w, pet_l, pet_w):
    inputArray = [sep_l, sep_w, pet_l, pet_w]


return clf.predict(inputArray)
```

これでクライアントから Web サービスを呼び出すことができます。 便利なラッパーによって、REST API 要求が構築されます。 以下に、Web サービスを利用するためのサンプル コードを示します。

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
> 現在、Azure Machine Learning ライブラリは Python 2.7 のみでサポートされています。   
> 
> 

### <a name="build-and-operationalize-r-models"></a>R モデルを構築して運用可能な状態にする
Data Science Virtual Machine やその他の環境で構築された R のモデルは、Python の場合と同様の方法で Azure Machine Learning にデプロイすることができます。 手順は次のようになります。

1. ワークスペース ID と認証トークンを提供する settings.json ファイルを作成します。 
2. モデルの予測関数のラッパーを作成します。
3. Azure Machine Learning ライブラリで ```publishWebService``` を呼び出し、関数ラッパーを渡します。  

次の手順とコード スニペットを使用して、Azure Machine Learning でモデルを Web サービスとして設定、ビルド、公開、および利用します。

#### <a name="set-up"></a>セットアップ

ホーム ディレクトリの下にある ```.azureml``` という名前のディレクトリに settings.json ファイルを作成します。 ご利用の Azure Machine Learning ワークスペースからのパラメーターを入力します。

settings.json ファイルの構造を次に示します。

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
クライアント アプリケーションからモデルを利用するには、Azure Machine Learning ライブラリを使用して、公開されている Web サービスを名前で検索します。 `services` API 呼び出しを使用して、エンドポイントを特定します。 後は、`consume` 関数を呼び出して、予測の対象となるデータ フレームを渡すだけです。

次のコードを使用して、Azure Machine Learning Web サービスとして発行されたモデルを利用します。

```r
library(AzureML)
library(lme4)
ws <- workspace(config="~/.azureml/settings.json")

s <-  services(ws, name = "sleepy lm")
s <- tail(s, 1) # use the last published function, in case of duplicate function names

ep <- endpoints(ws, s)

# Try this out, and compare with raw data
ans = consume(ep, sleepstudy)$ans
```

Machine Learning Studio の R パッケージの詳細については、[こちら](/azure/machine-learning/studio-module-reference/r-packages-supported-by-azure-machine-learning)を参照してください。

## <a name="manage-azure-resources"></a>Azure のリソースを管理する
DSVM では、仮想マシン上でローカルに分析ソリューションを構築することはできません。 また、Azure クラウド プラットフォーム上のサービスには、アクセスすることができます。 Azure には、コンピューティング サービス、ストレージ サービス、データ分析など、ご利用の DSVM から管理してアクセスできるさまざまなサービスが用意されています。

Azure サブスクリプションとクラウド リソースを管理するには、次の 2 つの方法があります。
+ Web ブラウザーを使用して、[Azure portal](https://portal.azure.com) に移動します。

+ PowerShell スクリプトを使用します。 Azure PowerShell は、デスクトップ上のショートカットから、または **[スタート]** メニューから実行します。 詳細については、[Microsoft Azure PowerShell のドキュメント](../../powershell-azure-resource-manager.md)を参照してください。 

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

    git clone https://github.com/Azure/DataScienceVM.git

Visual Studio から同じクローン操作を行うこともできます。 下のスクリーンショットでは、Visual Studio で Git と GitHub ツールにアクセスする方法を示しています。

![GitHub 接続が表示される Visual Studio のスクリーンショット](./media/vm-do-ten-things/VSGit.PNG)

Git を使った GitHub リポジトリの操作の詳細については、github.com で公開されているリソースをご覧ください。 [チート シート](https://services.github.com/on-demand/downloads/github-git-cheat-sheet.pdf) を参照すると便利です。

## <a name="access-azure-data-and-analytics-services"></a>Azure のデータ サービスと分析サービスにアクセスする
### <a name="azure-blob-storage"></a>Azure BLOB ストレージ
Azure Blob Storage は、大規模データにも小規模データにも対応する信頼性と経済性に優れたクラウド ストレージ サービスです。 このセクションでは、データを BLOB ストレージに移動し、Azure BLOB の格納データにアクセスする方法について説明します。

#### <a name="prerequisites"></a>前提条件

* [Azure portal](https://portal.azure.com) から Azure Blob Storage アカウントを作成します。

   ![Azure portal でのストレージ アカウント作成プロセスのスクリーンショット](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* コマンドライン AzCopy ツールが ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe``` にプレインストールされていることを確認します。 azcopy.exe を含むディレクトリが既に PATH 環境変数上にあるので、このツールの実行時に完全なコマンド パスを入力せずに済みます。 AzCopy ツールの詳細については、[AzCopy のドキュメント](../../storage/common/storage-use-azcopy.md)を参照してください。
* Azure ストレージ エクスプローラー ツールを起動します。 これは [Storage Explorer の Web ページ](https://storageexplorer.com/)からダウンロードできます。 

   ![ストレージ アカウントにアクセスする Azure Storage Explorer のスクリーンショット](./media/vm-do-ten-things/AzureStorageExplorer_v4.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azcopy"></a>VM から Azure BLOB にデータを移動する: AzCopy

ローカル ファイルと BLOB ストレージとの間のデータ移動には、次のように、コマンド ラインまたは PowerShell から AzCopy を使用します。

    AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt

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

#### <a name="move-data-from-a-vm-to-an-azure-blob-azure-storage-explorer"></a>VM から Azure BLOB にデータを移動する: Azure ストレージ エクスプローラー

Azure Storage Explorer を使用して、ご利用の VM 内のローカル ファイルからデータをアップロードすることもできます。

* コンテナーにデータをアップロードするには、ターゲット コンテナーを選択し、 **[アップロード]** ボタンを選択します。![Azure Storage Explorer の [アップロード] ボタンのスクリーンショット](./media/vm-do-ten-things/storage-accounts.png)
* **[ファイル]** ボックスの右側の省略記号 **[...]** を選択し、アップロードする 1 つまたは複数のファイルをファイル システムから選択します。さらに、 **[アップロード]** を選択して、ファイルのアップロードを開始します。![ファイルのアップロード ダイアログ ボックスのスクリーンショット](./media/vm-do-ten-things/upload-files-to-blob.png)

#### <a name="read-data-from-an-azure-blob-machine-learning-reader-module"></a>Azure BLOB からデータを読み取る: Machine Learning リーダー モジュール

Azure Machine Learning Studio では、データのインポート モジュールを使用してご利用の BLOB からデータを読み取ることができます。

![Machine Learning Studio でのデータのインポート モジュールのスクリーンショット](./media/vm-do-ten-things/AML_ReaderBlob_Module_v3.png)

#### <a name="read-data-from-an-azure-blob-python-odbc"></a>Azure BLOB からデータを読み取る: Python ODBC

BlobService ライブラリを使用すると、Jupyter ノートブックまたは Python プログラムで BLOB から直接データを読み取ることができます。

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

![データの最初の 10 行のスクリーンショット](./media/vm-do-ten-things/IPNB_data_readin.PNG)

### <a name="azure-data-lake"></a>Azure Data Lake
Azure Data Lake Storage は、ビッグ データ分析ワークロード用のハイパースケール リポジトリであり、Hadoop 分散ファイル システム (HDFS) と互換性があります。 Hadoop、Spark、および Azure Data Lake Analytics で動作します。 このセクションでは、データを Azure Data Lake Storage に移動し、Azure Data Lake Analytics を使用して分析を実行する方法について説明します。

#### <a name="prerequisites"></a>前提条件

* [Azure portal](https://portal.azure.com) で Azure Data Lake Analytics インスタンスを作成します。

   ![Azure portal から Data Lake Analytics インスタンスを作成しているスクリーンショット](./media/vm-do-ten-things/Azure_Data_Lake_Create_v2.png)

* [Azure Data Lake and Stream Analytics Tools for Visual Studio プラグイン](https://www.microsoft.com/download/details.aspx?id=49504)は、仮想マシン上の Visual Studio Community Edition に既にインストールされています。 Visual Studio を起動して Azure サブスクリプションにサインインすると、Visual Studio の左パネルに Azure Data Analytics のアカウントとストレージが表示されます。

   ![Visual Studio での Data Lake Tools のプラグインのスクリーンショット](./media/vm-do-ten-things/Azure_Data_Lake_PlugIn_v2.PNG)

#### <a name="move-data-from-a-vm-to-data-lake-azure-data-lake-explorer"></a>VM から Data Lake にデータを移動する: Azure Data Lake エクスプローラー

Azure Data Lake Explorer を使用して、[ご利用の仮想マシン内のローカル ファイルから Data Lake Storage にデータをアップロードできます](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)。

また、[Azure Data Factory](https://azure.microsoft.com/services/data-factory/) を使用して、Azure Data Lake との間のデータ移動を運用可能にするデータ パイプラインを作成することもできます。 データ パイプラインを作成する手順については、[こちらの記事](https://azure.microsoft.com/blog/creating-big-data-pipelines-using-azure-data-lake-and-azure-data-factory/)をご覧ください。

#### <a name="read-data-from-an-azure-blob-to-data-lake-u-sql"></a>Azure BLOB から Data Lake にデータを読み取る: U-SQL

ご利用のデータが Azure Blob Storage に存在する場合、U-SQL クエリで Azure BLOB から直接データを読み取ることができます。 U-SQL クエリを作成する前に、ご利用の BLOB ストレージ アカウントがご利用の Azure Data Lake インスタンスにリンクされていることを確認します。 Azure portal に移動して [Azure Data Lake Analytics] ダッシュボードを探し、 **[データ ソースの追加]** を選択します。ストレージの種類に **[Azure Storage]** を選択し、Azure ストレージ アカウントの名前とキーでプラグ インします。 これで、ストレージ アカウントの格納データを参照できます。

![[データ ソースの追加] ダイアログ ボックスのスクリーンショット](./media/vm-do-ten-things/Link_Blob_to_ADLA_v2.PNG)

Visual Studio では、BLOB ストレージからのデータ読み取り、データ操作、機能の設計を実行できるほか、結果として得られたデータを Azure Data Lake または Azure Blob Storage に出力することができます。 BLOB ストレージ内のデータを参照する場合は、**wasb://** を使用します。 Azure Data Lake でデータを参照する場合は、**swbhdfs://** を使用します。

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

ご利用のクエリがサーバーに送信されると、ご自分のジョブの状態がダイアグラムに表示されます。

![ジョブの状態ダイアグラムのスクリーンショット](./media/vm-do-ten-things/USQL_Job_Status.PNG)

#### <a name="query-data-in-data-lake-u-sql"></a>Data Lake 内のデータを照会する: U-SQL

Azure Data Lake にデータセットを取り込んだ後、[U-SQL 言語](../../data-lake-analytics/data-lake-analytics-u-sql-get-started.md)を使用してデータを照会、探索することができます。 U-SQL 言語は T-SQL と似ていますが、C# の機能が一部組み合わされているため、ユーザーは独自のモジュールやユーザー定義関数を作成することが可能です。 先ほどの手順で紹介したスクリプトを使用することができます。

クエリがサーバーに送信されると、Azure Data Lake Explorer に tripdata_summary.CSV が表示されます。 そのファイルを右クリックすると、データがプレビューされます。

![Data Lake Explorer での CSV ファイルのスクリーンショット](./media/vm-do-ten-things/USQL_create_summary.png)

ファイルの情報が表示されます。

![ファイル概要情報のスクリーンショット](./media/vm-do-ten-things/USQL_tripdata_summary.png)

### <a name="hdinsight-hadoop-clusters"></a>HDInsight Hadoop クラスター
Azure HDInsight は、クラウド内の Apache Hadoop、Spark、HBase、および Storm マネージド サービスです。 Azure HDInsight クラスターは、Data Science Virtual Machine から簡単に操作できます。

#### <a name="prerequisites"></a>前提条件

* [Azure portal](https://portal.azure.com) から Azure Blob Storage アカウントを作成します。 HDInsight クラスターのデータは、このストレージ アカウントを使用して格納されます。

   ![Azure portal からストレージ アカウントを作成しているスクリーンショット](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* [Azure portal](../team-data-science-process/customize-hadoop-cluster.md) から Azure HDInsight Hadoop クラスターをカスタマイズします。
  
   作成したストレージ アカウントは、この HDInsight クラスターに対して作成時にリンクします。 このストレージ アカウントは、クラスター内で処理可能なデータにアクセスするために使用されます。

   ![作成したストレージ アカウントを HDInsight クラスターにリンクするための選択](./media/vm-do-ten-things/Create_HDI_v4.PNG)

* 作成したら、クラスターのヘッド ノードへのリモート デスクトップ アクセスを有効にします。 ここで指定するリモート アクセスの資格情報は、以降の手順で必要になるため、記憶しておいてください。

   ![HDInsight クラスターへのリモート アクセスを有効にするための [リモートデスクトップ] ボタン](./media/vm-do-ten-things/Create_HDI_dashboard_v3.PNG)

* Azure Machine Learning ワークスペースを作成します。 実施した Machine Learning の実験は、この Machine Learning ワークスペースに保存されます。 下のスクリーンショットに示したように、ポータル内で強調表示されているオプションを選択します。

   ![Azure Machine Learning ワークスペースの作成](./media/vm-do-ten-things/Create_ML_Space.PNG)

* ご利用のワークスペースのパラメーターを入力します。

   ![Machine Learning ワークスペース パラメーターの入力](./media/vm-do-ten-things/Create_ML_Space_step2_v2.PNG)

* IPython Notebook を使用してデータをアップロードします。 必要なパッケージをインポートし、資格情報をプラグインし、ご利用のストレージ アカウントでデータベースを作成してから、データを HDI クラスターに読み込みます。

```python
# Import required packages
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


# Create the connection to Hive by using ODBC
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


# Create the Hive database and tables
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


# Upload data from Blob storage to an HDI cluster
for i in range(1, 13):
    queryString = "LOAD DATA INPATH 'wasb:///nyctaxitripraw2/trip_data_%d.csv' INTO TABLE nyctaxidb2.trip PARTITION (month=%d);" % (
        i, i)
    cursor.execute(queryString)
    queryString = "LOAD DATA INPATH 'wasb:///nyctaxifareraw2/trip_fare_%d.csv' INTO TABLE nyctaxidb2.fare PARTITION (month=%d);" % (
        i, i)
    cursor.execute(queryString)
```

あるいは、こちらの[チュートリアル](../team-data-science-process/hive-walkthrough.md)に従って NYC Taxi データを HDI クラスターにアップロードすることもできます。 主な手順は次のとおりです。
  
* AzCopy を使用して、zip 形式の CSV をパブリック BLOB からご利用のローカル フォルダーにダウンロードします。
* AzCopy を使用して、解凍された CSV をローカル フォルダーから HDI クラスターにアップロードします。
* Hadoop クラスターのヘッド ノードにログインして探索的データ分析の準備をします。

HDI クラスターにデータが読み込まれたら、Azure Storage Explorer でご自分のデータを確認することができます。 さらに、nyctaxidb データベースが HDI クラスターに作成されています。

#### <a name="data-exploration-hive-queries-in-python"></a>データの探索: Python での Hive クエリ

データは Hadoop クラスターに存在しているため、探索や機能設計を実行するには、pyodbc パッケージを使用して Hadoop クラスターに接続してから、Hive を使用してデータベースに対してクエリを実行します。 前提条件の手順で作成した既存のテーブルを表示できます。

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

乗車位置と降車位置との距離を計算して、トリップ距離と比較することもできます。

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

次に、モデリング用にダウンサンプル (1%) したデータ セットを用意します。 このデータは Machine Learning リーダー モジュールで使用できます。

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

次に、join の内容を前述の内部テーブルに挿入します。

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

しばらくすると、Hadoop クラスターにデータが読み込まれてあることを確認できます。

```python
queryString = """
    select * from nyctaxi_downsampled_dataset limit 10;
    """
cursor.execute(queryString)
pd.read_sql(queryString, connection)
```

![テーブルのデータの上位の行](./media/vm-do-ten-things/DownSample_Data_For_Modeling_v2.PNG)

#### <a name="read-data-from-hdi-by-using-machine-learning-reader-module"></a>Machine Learning のリーダー モジュールを使用して HDI からのデータを読み取る

Machine Learning Studio のリーダー モジュールを使用して、Hadoop クラスター内のデータベースにアクセスすることもできます。 ご利用の HDI クラスターと Azure ストレージ アカウントの資格情報を追加すると、HDI クラスター内のデータベースを使用して機械学習モデルを構築できるようになります。

![リーダー モジュールのプロパティ](./media/vm-do-ten-things/AML_Reader_Hive.PNG)

さらに、スコア付けされたデータセットを表示できます。

![スコア付けされたデータセットを表示する](./media/vm-do-ten-things/AML_Model_Results.PNG)

### <a name="azure-sql-data-warehouse-and-databases"></a>Azure SQL Data Warehouse とデータベース
Azure SQL Data Warehouse は、エンタープライズ規模の SQL Server エクスペリエンスを備えた、サービスとしてのエラスティック データ ウェアハウスです。

使用する Azure SQL データ ウェアハウスは、こちらの[記事](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)に記載されている手順に従ってプロビジョニングできます。 SQL データ ウェアハウスをプロビジョニングしたら、[こちらのチュートリアル](../team-data-science-process/sqldw-walkthrough.md)を使用して、SQL データ ウェアハウス内のデータを使用したデータのアップロード、探索、モデリングを実行できます。

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB
Azure Cosmos DB は、クラウドにおける NoSQL データベースです。 それを使用すれば、JSON などのドキュメントを操作することや、そうしたドキュメントを格納してクエリを実行することができます。

次の前提条件となる手順を使用して、DSVM から Azure Cosmos DB にアクセスする:

1. Azure Cosmos DB Python SDK は DSVM に既にインストールされています。 これを更新するには、コマンド プロンプトから ```pip install pydocumentdb --upgrade``` を実行します。
2. [Azure portal](https://portal.azure.com) で Azure Cosmos DB アカウントとデータベースを作成します。
3. [Microsoft ダウンロード センター](https://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d)から Azure Cosmos DB データ移行ツールをダウンロードして、任意のディレクトリに展開します。
4. 移行ツールへの次のコマンド パラメーターを使用して、[パブリック BLOB](https://cahandson.blob.core.windows.net/samples/volcano.json) に格納されている JSON データ (火山に関するデータ) を Azure Cosmos DB にインポートします (Azure Cosmos DB データ移行ツールをインストールしたディレクトリから dtui.exe を使用します)。これらのパラメーターでインポート元とインポート先の場所を入力します。
   
    `/s:JsonFile /s.Files:https://cahandson.blob.core.windows.net/samples/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1`

データをインポートしたら、Jupyter にアクセスして、*DocumentDBSample* というタイトルのノートブックを開くことができます。 それには、Azure Cosmos DB にアクセスして何らかの基本的なクエリを実行するための Python コードが含まれています。 Azure Cosmos DB の詳細については、サービスの[ドキュメント ページ](https://docs.microsoft.com/azure/cosmos-db/)をご覧ください。

## <a name="use-power-bi-reports-and-dashboards"></a>Power BI レポートとダッシュボードを使用する 
先ほど示した Azure Cosmos DB の例での Volcano JSON ファイルを Power BI Desktop で視覚化することで、データの本質を視覚的に把握することができます。 詳しい手順については、[Power BI の記事](../../cosmos-db/powerbi-visualize.md)を参照してください。 手順の概要は次のとおりです。

1. Power BI Desktop を開いて、 **[データの取得]** を選びます。 URL に https://cahandson.blob.core.windows.net/samples/volcano.json を指定します。
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
> VM 上でオペレーティング システムのシャットダウン ボタンを使用しただけであっても、コンピューティングの料金は発生します。  
> 
> 

大規模な分析を処理する必要が生じると、より多くの CPU、メモリ、またはディスク容量を必要とする場合があります。 その場合は、CPU コア数、ディープ ラーニング用の GPU ベース インスタンス、メモリ容量、ディスクの種類 (ソリッド ステート ドライブなど) の観点から、処理能力や予算のニーズに応じて VM のサイズを選ぶことができます。 VM とその時間単位のコンピューティング料金をまとめた一覧については、「[Azure Virtual Machines の価格](https://azure.microsoft.com/pricing/details/virtual-machines/)」ページをご覧ください。

同様に、VM 処理容量のニーズが減少する可能性もあります (例: Hadoop または Spark クラスターに主なワークロードを移動した場合)。その後、[Azure portal](https://portal.azure.com) からクラスターをスケール ダウンし、ご利用の VM インスタンスの設定に移動できます。 

## <a name="add-more-tools"></a>ツールをさらに追加する
DSVM に事前に組み込まれているツールを使用すると、多くの一般的なデータ分析ニーズに対応できます。 これにより、ご利用の環境を 1 つずつインストールして構成する必要がなくなるため、時間の節約になります。 また、使用するリソースについてのみ支払いを行うので、コストも節約できます。

この記事の中で取り上げた他の Azure データ サービスと Azure 分析サービスを使用して、分析環境を強化することができます。 場合によっては、独自のパートナー ツールなど、追加のツールが必要になることがあります。 必要となった新しいツールをインストールするには、仮想マシンに対するフル管理者アクセス権が必要です。 プレインストールされない、Python と R の追加パッケージをインストールすることもできます。 Python の場合、```conda``` または ```pip``` を使用できます。 R の場合は、R コンソールで ```install.packages()``` を使用することも、IDE を使用して **[パッケージ]**  >  **[パッケージのインストール]** の順に選択することもできます。

## <a name="deep-learning"></a>ディープ ラーニング

フレームワーク ベースのサンプルに加えて、DSVM で検証済みの包括的なチュートリアルのセットもご利用いただけます。 これらのチュートリアルは、イメージやテキスト/言語の理解などの分野でディープ ラーニング アプリケーションの開発をすぐに開始するのに役立ちます。   


- [さまざまなフレームワークでのニューラル ネットワークの実行](https://github.com/ilkarman/DeepLearningFrameworks): このチュートリアルは、あるフレームワークから別のフレームワークにコードを移行する方法を示します。 フレームワーク間でモデルと実行時のパフォーマンスを比較する方法も示します。 

- [イメージ内で製品を検出するエンド ツー エンド ソリューションを構築するための攻略ガイド](https://github.com/Azure/cortana-intelligence-product-detection-from-images): イメージ検出は、イメージ内のオブジェクトを特定して分類する手法です。 このテクノロジには、多くの実際のビジネス ドメインに大きなメリットをもたらす可能性があります。 たとえば、小売り業者は、この手法を使用して、顧客が棚から選ぶ製品を判断できます。 この情報は、店舗が製品在庫を管理するのに役立ちます。 

- [音声のディープ ラーニング](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/): このチュートリアルでは、[都市音声データセット](https://serv.cusp.nyu.edu/projects/urbansounddataset/urbansound8k.html)で発生する音声イベントを検出するためのディープ ラーニング モデルをトレーニングする方法を説明します。 また、音声データを操作する方法の概要についても説明します。

- [テキスト ドキュメントの分類](https://github.com/anargyri/lstm_han): このチュートリアルでは、2 つのニューラル ネットワーク アーキテクチャを構築してトレーニングする方法を示します。Hierarchical Attention Network と Long Short Term Memory (LSTM) ネットワークです。 これらのニューラル ネットワークでは、ディープ ラーニング用の Keras API を使用して、テキスト ドキュメントを分類します。 Keras は、最も普及している 3 つのディープ ラーニング フレームワーク (Microsoft Cognitive Toolkit、TensorFlow、および Theano) のフロントエンドです。

## <a name="summary"></a>まとめ
この記事では、Microsoft Data Science Virtual Machine 上でできることの一部について説明しました。 DSVM を効果的な分析環境にするためにできることは他にも多数あります。

