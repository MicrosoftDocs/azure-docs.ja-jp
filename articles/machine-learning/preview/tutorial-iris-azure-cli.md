---
title: "Azure Machine Learning プレビュー機能のチュートリアル記事 - コマンド ライン インターフェイス | Microsoft Docs"
description: "このチュートリアルでは、コマンド ライン インターフェイスを使って Iris 分類を始めから終わりまで実行するために必要なすべての手順について説明します。"
services: machine-learning
author: ahgyger
ms.author: ahgyger, ritbhat
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: c2a3b9702afd99c29b64133a05515a1b5f395130
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-classifying-iris-using-the-command-line-interface"></a>チュートリアル: コマンド ライン インターフェイスを使用して Iris を分類する
Azure Machine Learning サービス (プレビュー) は、データ サイエンスと高度な分析をエンド ツー エンドで支援する統合ソリューションです。データの準備、実験の開発、モデルのデプロイをクラウド スケールで行うプロフェッショナルなデータ サイエンティストを対象としています。

このチュートリアルでは、Azure Machine Learning プレビュー機能でコマンド ライン インターフェイス (CLI) ツールを使って次のことを行う方法を説明します。 
> [!div class="checklist"]
> * 実験アカウントを設定してワークスペースを作成する
> * プロジェクトの作成
> * 複数のコンピューティング ターゲットに実験を送信する
> * トレーニング済みのモデルを昇格させて登録する
> * 新しいデータをスコア付けする Web サービスをデプロイする

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件
- Azure サブスクリプションへのアクセス権と、そのサブスクリプションにリソースを作成するアクセス許可が必要です。 
- [インストールと作成のクイックスタート](quickstart-installation.md)に関する記事に従って、Azure Machine Learing Workbench アプリケーションをインストールする必要があります。 

  >[!NOTE]
  >Azure Machine Learning Workbench のローカルなインストールだけが必要です。 行う必要があるのは、Azure Machine Learning Workbench のインストールに関するセクションの手順だけです。アカウントの作成と新しいプロジェクトの作成に関する手順は、この記事でコマンド ラインを使って行います。
 
## <a name="getting-started"></a>使用の開始
Azure Machine Learning コマンド ライン インターフェイス (CLI) では、データ サイエンス ワークフローの開始から終了までに必要なすべてのタスクを実行することができます。 CLI ツールには次の方法でアクセスできます。

### <a name="option-1-launch-azure-ml-cli-from-azure-ml-workbench-log-in-dialog-box"></a>方法 1. Azure ML Workbench ログイン ダイアログ ボックスから Azure ML CLI を起動する
Azure ML Workbench を初めて起動してログインするとき、実験アカウントへのアクセス権がまだない場合は、次の画面が表示されます。

![アカウントが見つかりませんでした](media/tutorial-iris-azure-cli/no_account_found.png)

ダイアログ ボックスの **[Command Line Window]\(コマンド ライン ウィンドウ\)** リンクをクリックして、コマンド ライン ウィンドウを起動します。

### <a name="option-2-launch-azure-ml-cli-from-azure-ml-workbench-app"></a>方法 2. Azure ML Workbench アプリから Azure ML CLI を起動する
実験アカウントへのアクセス権が既にある場合は、正常にログインすることができます。 その後、**[ファイル]** --> **[Open Commmand Prompt]\(コマンド プロンプトを開く\)** メニューをクリックして、コマンド ライン ウィンドウを開くことができます。

### <a name="option-3-enable-azure-ml-cli-in-an-arbitrary-command-line-window"></a>方法 3. 任意のコマンド ライン ウィンドウで Azure ML CLI を有効にする
任意のコマンド ライン ウィンドウで Azure ML CLI を有効にすることもできます。 普通にコマンド ウィンドウを開き、次のコマンドを入力します。

```sh
# Windows Command Prompt
set PATH=%LOCALAPPDATA%\amlworkbench\Python;%LOCALAPPDATA%\amlworkbench\Python\Scripts;%PATH%

# Windows PowerShell
$env:Path = $env:LOCALAPPDATA+"\amlworkbench\Python;"+$env:LOCALAPPDATA+"\amlworkbench\Python\Scripts;"+$env:Path

# macOS Bash Shell
PATH=$HOME/Library/Caches/AmlWorkbench/Python/bin:$PATH
```
変更を永続的にするには、Windows では `SETX` を使うことができます。 macOS の場合は、`setenv` を使うことができます。

>[!TIP]
>上の環境変数を設定することにより、好みのターミナル ウィンドウで Azure CLI を有効にできます。

## <a name="step-1-log-in-to-azure"></a>手順 1. Azure へのログイン
最初に、AMLWorkbench アプリから CLI を開きます ([ファイル] > [Open Command Prompt]\(コマンド プロンプトを開く\))。 これにより、正しい Python 環境が使われて、ML CLI コマンドを使うことができるようになります。 

次に、Azure リソースにアクセスして管理するための適切なコンテキストを CLI で設定する必要があります。
 
```bash
az login
az account set -s d128f140-94e6-1206-80a7-954b9d27d007
```

> [!TIP]
> すべてのサブスクリプションの一覧を取得するには、次のコマンドを実行します。 
>```
>az account list -o table
>```

## <a name="step-2-create-a-new-azure-machine-learning-experimentation-account-and-workspace"></a>手順 2. 新しい Azure Machine Learning 実験アカウントとワークスペースを作成する
最初に、新しい実験アカウントと新しいワークスペースを作成します。 実験アカウントとワークスペースについて詳しくは、「[Azure Machine Learning の概念](overview-general-concepts.md)」をご覧ください。 

> [!NOTE]
> 実験アカウントにはストレージ アカウントが必要です。ストレージ アカウントは、実験の実行の出力を格納するために使われます。 ストレージ アカウント名は、関連付けられる URL があるため、Azure 内でグローバルに一意である必要があります。 実験アカウント名を使って、新しいストレージ アカウントが自動的に作成されます。 必ず一意名を使ってください。一意でないと、"_'amlsampleexp' という名前のストレージ アカウントは既に取得されています_" のようなエラーが発生します。 または、`--storage` 引数を使って既存のストレージ アカウントを使うこともできます。

```bash
az group create --name amlsample --location eastus2
az ml account experimentation create --name amlsampleexp --resource-group amlsample
az ml account experimentation create --name amlsampleexp --resource-group amlsample --storage /subscriptions/6d48cffb-b787-47bd-8d20-1696afa33b67/resourceGroups/existing/providers/Microsoft.Storage/storageAccounts/mystorageacct
az ml workspace create --name amlsamplew --account amlsampleexp --resource-group amlsample
```

## <a name="step-2a-optional-share-a-workspace-with-co-worker"></a>手順 2.a (省略可能) ワークスペースを同僚と共有する
ここでは、ワークスペースへのアクセスを同僚と共有する方法を示します。 実験アカウントでもプロジェクトでも、アクセスを共有する手順は同じです。 Azure リソース ID を取得する方法だけを更新する必要があります。

```bash
az ml workspace show --name amlsamplew --account amlsampleexp --resource-group amlsample 
az role assignment create --assignee ahgyger@microsoft.com --role owner --scope "/subscriptions/d128f140-94e6-4175-87a7-954b9d27db16/resourceGroups/amlsample/providers/Microsoft.MachineLearningExperimentation/accounts/amlsampleexp/workspaces/amlsamplew"
```

> [!TIP]
> エイリアスではなく、同僚の実際のメール アドレスを使う必要があります。 

## <a name="step-3-create-a-new-project"></a>手順 3. 新しいプロジェクトを作成する
次に、新しいプロジェクトを作成します。 新しいプロジェクトを始めるにはいくつかの方法があります。

### <a name="create-a-new-blank-project"></a>空のプロジェクトを新しく作成する

```bash
az ml project create --name 9_25_1 --workspace amlsamplew --account amlsampleexp --resource-group amlsample --path c:\Users\ahgyger\Documents\AMLworkbench_Demo\9_25\
```

### <a name="create-a-new-project-with-template-files"></a>テンプレート ファイルで新しいプロジェクトを作成する
テンプレート ファイルはサンプルではありませんが、新しいプロジェクトにフレームを提供します。 プロジェクトは、2 つのファイル `train.py` と `score.py` で事前に設定されます。

```bash
az ml project create --name 9_25_1 --workspace amlsamplew --account amlsampleexp --resource-group amlsample --path c:\Users\ahgyger\Documents\AMLworkbench_Demo\ --template
```

### <a name="create-a-new-project-associated-with-a-cloud-git-repository"></a>クラウド Git リポジトリに関連付けられた新しいプロジェクトを作成する
クラウド Git リポジトリに関連付けられた新しいプロジェクトを作成できます。 実験が送信されるたびに、Git は実行履歴の分岐でコミットするので、プロジェクトのコンテンツがスナップショットになります。 詳しくは、「[Azure Machine Learning Workbench プロジェクトでの Git リポジトリの使用](using-git-ml-project.md)」をご覧ください。

> [!NOTE]
> Azure Machine Learning では、バージョン管理として Git を使う空の Team Project (VSTS) のみがサポートされます。

> [!TIP]
> "Repository url might be invalid or user might not have access" (リポジトリの URL が正しくないか、ユーザーにアクセス権がない可能性があります) というエラーが発生する場合は、VSTS でセキュリティ トークンを作成し ([セキュリティ]、個人用アクセス トークンを追加)、プロジェクトを作成するときに __vststoken__ 引数を使うことができます。 

```bash
az ml project create --name 9_25_2 --workspace amlsamplew --account amlsampleexp --resource-group amlsample --path c:\Users\ahgyger\Documents\AMLworkbench_Demo\ --repo https://ahgyger.visualstudio.com/AMLWorkbench/_git/9_25 --vststoken m2fholwwrcn7u4nrdqfxx007u5rztjfhgofnemvuvtue6pbwo3sa
```

### <a name="sample_create"></a>オンライン サンプルから新しいプロジェクトを作成する
この例では、新しいプロジェクトを作成するときに GitHub プロジェクトのテンプレートを使います。 

```bash
# List the project samples
az ml project sample list

# Create a new project from a sample
az ml project create --name 9_25_3 --workspace amlsamplew --account amlsampleexp --resource-group amlsample --path c:\Users\ahgyger\Documents\AMLworkbench_Demo\ --repo https://ahgyger.visualstudio.com/AMLWorkbench/_git/9_25 --template-url https://github.com/MicrosoftDocs/MachineLearningSamples-Iris
```

プロジェクトを作成した後、ディレクトリをそこに変更してから、次の手順に進みます。

```bash
cd c:\Users\ahgyger\Documents\AMLworkbench_Demo\9_25\9_25_1
```

## <a name="step-4-run-the-training-experiment"></a>手順 4. トレーニング実験を実行する 
次の手順では、Iris サンプルのプロジェクトがあるものとします (「[オンライン サンプルから新しいプロジェクトを作成する](#sample_create)」を参照)。

### <a name="prepare-your-environment"></a>環境を準備する 
Iris サンプルでは、matplotlib をインストールする必要があります。

```bash
pip install matplotlib
```

###  <a name="submit-the-experiment"></a>実験を送信する

```bash
# Execute the file
az ml experiment submit --run-configuration local iris_sklearn.py
```

### <a name="iterate-on-your-experiment-with-descending-regularization-rates"></a>正則化項を降順にして実験を繰り返す
少し工夫すれば、異なる正則化項で実験を送信する Python スクリプトと組み合わせることは簡単です  (ファイルを編集して、適切なプロジェクト パスをポイントすることが必要になる場合があります)。

```bash
python run.py
```

## <a name="step-5-view-run-history"></a>手順 5. 実行履歴を表示する
次のコマンドは、以前のすべての実行を一覧表示します。 

```bash
az ml history list -o table
```
上のコマンドを実行すると、このプロジェクトに属しているすべての実行の一覧が表示されます。 精度と正則化項のメトリックも表示されます。 これにより、一覧から最善の実行を簡単に識別できます。

## <a name="step-5a-view-attachment-created-by-a-given-run"></a>手順 5.a 特定の実行によって作成された添付ファイルを表示する 
特定の実行に関連付けられている添付ファイルを表示するには、実行履歴の info コマンドを使います。

```bash
az ml history info --run 9_16_4_1505589545267 --artifact driver_log
```

実行からアーティファクトをダウンロードするには、次のコマンドを使います。

```bash
# Stream a given attachment 
az ml history info --run <run id> --artifact <artifact location>
```

## <a name="step-6-promote-artifacts-of-a-run"></a>手順 6. 実行のアーティファクトを昇格させる 
行った実行の 1 つの AUC が優れているので、それを使って運用環境にデプロイするスコア付け Web サービスを作ります。 そのためには最初に、アーティファクトを資産に昇格させる必要があります。

```bash
az ml history promote --run 9_25_1505346632975 --artifact-path outputs/model.pkl --name model.pkl
```

このコマンドは、プロジェクト ディレクトリに pickle.link を含む __assets__ フォルダーを作成します。 このリンク ファイルは、昇格されたファイルのバージョンを追跡するために使われます。

## <a name="step-7-download-the-files-to-be-operationalized"></a>手順 7. 運用可能にするファイルをダウンロードする
次に、昇格させたファイルをダウンロードし、それを使って予測 Web サービスを作成できるようにします。 

```bash
az ml asset download --link-file assets\pickle.link -d asset_download
```

## <a name="step-8-setup-your-model-management-environment"></a>手順 8. モデル管理環境を設定する 
Web サービスをデプロイするための環境を作成します。 Docker を使ってローカル コンピューターで Web サービスを実行できます。 または、高スケールの運用には ACS クラスターにデプロイします。 

```bash
# Create new environment
az ml env setup -l eastus2 -n amlsamplesenv
# Once setup is complete, set your environment for current context
az ml env set -g amlsamplesenvrg -n amlsamplesenv
```

## <a name="step-9-create-a-model-management-account"></a>手順 9. モデル管理アカウントを作成する 
運用環境にモデルをデプロイして追跡するには、モデル管理アカウントが必要です。 

```bash
az ml account modelmanagement create -n amlsamplesacct -g amlsamplesenvrg -l eastus2
```

## <a name="step-10-create-a-web-service"></a>手順 10. Web サービスの作成
デプロイしたモデルを使って予測を返す Web サービスを作成します。 

```bash
az ml service create realtime -m modelfilename -f score.py -r python –n amlsamplews
```

## <a name="step-10-run-the-web-service"></a>手順 10. Web サービスを実行する
前の手順で出力された Web サービス ID を使うことで、Web サービスを呼び出してテストできます。 

```
# Get web service usage infomration
az ml service usage realtime -i <web service id>

# Call the web service with the run command:
az ml service run realtime -i <web service id> -d <input data>
```

## <a name="deleting-all-the-resources"></a>すべてのリソースを削除する 
学習を続ける場合を除き、最後に、作成したすべてのリソースを削除します。 

それには、単に、すべてのリソースを保持しているリソース グループを削除します。 

```bash
az group delete --name amlsample
az group delete --name amlsamplesenvrg
```

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、Azure Machine Learning プレビュー機能を使って以下のことを行う方法を学習しました。 
> [!div class="checklist"]
> * 実験アカウントを設定してワークスペースを作成する
> * プロジェクトを作成する
> * 複数のコンピューティング ターゲットに実験を送信する
> * トレーニング済みのモデルを昇格させて登録する
> * モデル管理用のモデル管理アカウントを作成する
> * Web サービスをデプロイするための環境を作成する
> * Web サービスをデプロイして新しいデータでスコア付けする

