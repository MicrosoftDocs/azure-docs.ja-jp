---
title: 'Kubernetes 開発空間を作成する: Visual Studio と .NET Core'
services: azure-dev-spaces
ms.custom: vs-azure, devx-track-azurecli, devx-track-csharp
ms.workload: azure-vs
ms.date: 07/09/2018
ms.topic: tutorial
description: このチュートリアルでは、Azure Dev Spaces と Visual Studio を使用して、Azure Kubernetes Service 上で .NET Core アプリケーションのデバッグと迅速な反復型開発を行う方法を示します
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー, Helm, サービス メッシュ, サービス メッシュのルーティング, kubectl, k8s
ms.openlocfilehash: 78ad3c643b839d2a0901ac2c1d930d73b718cd8d
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "88999259"
---
# <a name="create-a-kubernetes-dev-space-visual-studio-and-net-core-with-azure-dev-spaces"></a>Kubernetes 開発空間を作成する: Azure Dev Spaces での Visual Studio と .NET Core

このガイドでは、以下の方法について説明します。

- Azure でマネージド Kubernetes クラスターを使用して Azure Dev Spaces をセットアップする。
- Visual Studio を使用して、コンテナー内のコードを繰り返し開発する。
- 2 つのサービスを個別に開発し、Kubernetes の DNS サービス検索を使用して別のサービスを呼び出す。
- チーム環境でコードを生産的に開発してテストする。

> [!NOTE]
> いつでも**問題が発生した場合**は「[トラブルシューティング](troubleshooting.md)」セクションを参照してください。

## <a name="install-the-azure-cli"></a>Azure CLI のインストール
Azure Dev Spaces には、ローカル マシンの最小限のセットアップが必要です。 開発空間の構成の大半はクラウドに保存され、他のユーザーと共有できます。 まず、[Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) をダウンロードして実行します。

### <a name="sign-in-to-azure-cli"></a>Azure CLI へのサインイン
Azure にサインインします。 ターミナル ウィンドウで次のコマンドを入力します。

```azurecli
az login
```

> [!NOTE]
> Azure サブスクリプションをお持ちでない場合は、[無料のアカウント](https://azure.microsoft.com/free)を作成できます。

#### <a name="if-you-have-multiple-azure-subscriptions"></a>複数の Azure サブスクリプションがある場合:
以下を実行して、サブスクリプションを表示できます。 

```azurecli
az account list --output table
```

*IsDefault* が *True* のサブスクリプションを探します。
それが使用したいサブスクリプションでない場合は、既定のサブスクリプションを変更できます。

```azurecli
az account set --subscription <subscription ID>
```

## <a name="create-a-kubernetes-cluster-enabled-for-azure-dev-spaces"></a>Azure Dev Spaces 対応の Kubernetes クラスターを作成する

[Azure Dev Spaces をサポートするリージョン][supported-regions]に、コマンド プロンプトでリソース グループを作成します。

```azurecli
az group create --name MyResourceGroup --location <region>
```

以下のコマンドを使用して Kubernetes クラスターを作成します。

```azurecli
az aks create -g MyResourceGroup -n MyAKS --location <region> --generate-ssh-keys
```

クラスターの作成には数分かかります。

### <a name="configure-your-aks-cluster-to-use-azure-dev-spaces"></a>Azure Dev Spaces を使用するように AKS クラスターを構成する

次の Azure CLI コマンドを入力します。このとき、AKS クラスターを含む含むリソース グループと、AKS クラスター名を使用します。 このコマンドでは、Azure Dev Spaces のサポートを使用してクラスターが構成されます。

   ```azurecli
   az aks use-dev-spaces -g MyResourceGroup -n MyAKS
   ```
   
> [!IMPORTANT]
> Azure Dev Spaces 構成プロセスでは、クラスター内に `azds` 名前空間が存在する場合はこれを削除します。

## <a name="get-the-visual-studio-tools"></a>Visual Studio ツールを入手する
Azure 開発ワークロードを使用して、[Visual Studio 2019](https://www.visualstudio.com/vs/) の最新バージョンを Windows にインストールします。

## <a name="create-a-web-app-running-in-a-container"></a>コンテナーで実行される Web アプリを作成する

このセクションでは、ASP.NET Core Web アプリを作成し、Kubernetes のコンテナーで実行します。

### <a name="create-an-aspnet-web-app"></a>ASP.NET Web アプリを作成する

Visual Studio 内から、新しいプロジェクトを作成します。 現時点では、このプロジェクトは **ASP.NET Core Web アプリケーション**である必要があります。 プロジェクトに "**webfrontend**" という名前を付けます。

![[新しいプロジェクト] ダイアログ ボックスでは、"webfrontend" という名前の C# Web アプリケーションが C:\Source\Repos の場所に作成されることが示されています。 [ソリューション] ドロップダウン リストに [新しいソリューションの作成] が表示され、[ソリューションのディレクトリを作成] チェック ボックスがオンになっています。](media/get-started-netcore-visualstudio/NewProjectDialog1.png)

**[Web アプリケーション (モデル ビュー コントローラー)]** テンプレートを選択し、ダイアログの上部にある 2 つのドロップダウンで **.NET Core** と **ASP.NET Core 2.0** が対象になっていることを確認します。 **[OK]** をクリックしてプロジェクトを作成します。

![[新しい A S P ドット NET Core Web アプリケーション] ダイアログ ボックスの 2 つのドロップダウン リスト ボックスには、[ドット NET Core] と [A S P ドット NET Core 2 てん 0] が表示されています。 リスト ボックスの下にあるプロジェクト テンプレート ボタンの配列では、[Web アプリケーション (モデル ビュー コントローラー)] テンプレートが選択されています。 [Docker サポートを有効にする] チェック ボックスはオフになっています。](media/get-started-netcore-visualstudio/NewProjectDialog2.png)

### <a name="enable-dev-spaces-for-an-aks-cluster"></a>AKS クラスターの開発空間を有効にする

先ほど作成したプロジェクトに対し、次の図のように、起動設定のドロップダウンから **[Azure Dev Spaces]** を選択します。

![Microsoft Visual Studio Int プレビューというラベルが付いたウィンドウの上部に、ドロップダウン リストボックスがあります。 [Azure Dev Spaces] が選択されています。](media/get-started-netcore-visualstudio/LaunchSettings.png)

次に表示されるダイアログで、適切なアカウントでサインインしていることを確認し、既存の Kubernetes クラスターを選択します。

![[Azure Dev Spaces] ダイアログ ボックスには、次のボックスがあります: [サブスクリプション]、[Azure Kubernetes Service クラスター]、[スペース]。](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.PNG)

現時点では、 **[スペース]** ドロップダウンは既定の `default` のままにしておきます。 このオプションについては、後で詳しく説明します。 パブリック エンドポイント経由で Web アプリにアクセスできるように、 **[Publicly Accessible]\(パブリックにアクセス可能\)** チェック ボックスをオンにします。 この設定は必須ではありませんが、このチュートリアルの後の方でいくつかの概念を示す際に役立ちます。 いずれにせよ、Visual Studio を使用して Web サイトをデバッグできるので心配はありません。

![[パブリックにアクセス可能] チェック ボックスがオンになっています。](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog2.png)

**[OK]** をクリックして、クラスターを選択または作成します。

Azure Dev Spaces を使用できないクラスターを選択すると、クラスターを構成するかどうかをたずねるメッセージが表示されます。

![メッセージの内容は次のとおりです。"Azure Dev Spaces リソースの追加 Azure Dev Spaces を使用するには、使用するように選択した A K S クラスターを構成する必要があります。 そうしますか?" [O K] ボタンと [キャンセル] ボタンがあります。](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

**[OK]** を選択します。

> [!IMPORTANT]
> Azure Dev Spaces 構成プロセスでは、クラスター内に `azds` 名前空間が存在する場合はこれを削除します。

 処理を完了するために、バックグラウンド タスクが開始されます。 完了するまでに、数分かかります。 まだ作成中かどうかを確認するには、次の画像に示すように、ステータス バーの左下隅にある **[バックグラウンド タスク]** アイコンをポイントします。

![ポイントすると表示されるポップアップ ウィンドウには、"Creating "My A K S" in resource group (リソース グループに "My A K S" を作成しています)" と表示されています。](media/get-started-netcore-visualstudio/BackgroundTasks.PNG)

> [!NOTE]
> 開発空間が正常に作成されるまで、アプリケーションをデバッグすることはできません。

### <a name="look-at-the-files-added-to-project"></a>プロジェクトに追加されたファイルを確認する
開発空間が作成されるのを待っている間に、開発空間を使用することを選択したときにプロジェクトに追加されたファイルを確認しておきましょう。

まず、`charts` という名前のフォルダーが追加されており、このフォルダー内にアプリケーションの [Helm チャート](https://docs.helm.sh)がスキャフォールディングされていることがわかります。 これらのファイルは、アプリケーションを開発空間に展開するために使用されます。

`Dockerfile` という名前のファイルが追加されています。 このファイルには、標準の Docker 形式でアプリケーションをパッケージ化するために必要な情報が含まれています。

最後に、`azds.yaml` という名前のファイルがあります。このファイルには、開発空間で必要となる開発時の構成が含まれています。

![[ソリューション エクスプローラー] ウィンドウの "webfrontend" ソリューションには、"a z d s ドット yaml" ファイルが表示されています。](media/get-started-netcore-visualstudio/ProjectFiles.png)

## <a name="debug-a-container-in-kubernetes"></a>Kubernetes でコンテナーをデバッグする
開発空間が正常に作成されたら、アプリケーションをデバッグすることができます。 コードにブレークポイントを設定します。たとえば、`HomeController.cs` ファイルの 20 行目 (`Message` 変数が設定されている行) に設定します。 **F5** キーを押してデバッグを開始します。 

Visual Studio は開発空間と通信してアプリケーションをビルドして展開した後、ブラウザーを開いて Web アプリを実行します。 コンテナーはローカルで実行されているように見えますが、実際には Azure の開発空間で実行されています。 localhost アドレスであるのは、Azure Dev Spaces では AKS で実行されているコンテナーへの一時的な SSH トンネルが作成されるためです。

ページの上部にある **[About]\(バージョン情報\)** リンクをクリックすると、ブレークポイントがトリガーされます。 コードがローカルで実行されている場合と同様に、デバッグ情報 (呼び出し履歴、ローカル変数、例外情報など) にフル アクセスできます。

## <a name="iteratively-develop-code"></a>反復的にコードを開発する

Azure Dev Spaces は、Kubernetes でコードを実行するだけのものではありません。Azure Dev Spaces を使用すると、クラウドの Kubernetes 環境でコードの変更が有効になっていることをすぐに繰り返し確認できるようになります。

### <a name="update-a-content-file"></a>コンテンツ ファイルを更新する


1. `./Views/Home/Index.cshtml` ファイルを見つけ、HTML を編集します。 たとえば、[73 行目の `<h2>Application uses</h2>`](https://github.com/Azure/dev-spaces/blob/master/samples/dotnetcore/getting-started/webfrontend/Views/Home/Index.cshtml#L73) を次のように変更します。 
  
    ```html
    <h2>Hello k8s in Azure!</h2>`
    ```

2. ファイルを保存します。
3. ブラウザーに移動し、ページを更新します。 Web ページに更新された HTML が表示されます。

なぜでしょうか? HTML や CSS などのコンテンツ ファイルを編集しても、.NET Core Web アプリで再コンパイルする必要はありません。アクティブな F5 セッションによって、変更されたコンテンツ ファイルが AKS で実行中のコンテナーに自動的に同期されるので、編集後のコンテンツをすぐに確認できます。

### <a name="update-a-code-file"></a>コード ファイルを更新する
コード ファイルを更新するには、.NET Core アプリを再ビルドし、更新されたアプリケーション バイナリを生成する必要があるため、もう少し作業が必要です。

1. Visual Studio のデバッガーを停止します。
1. `Controllers/HomeController.cs` という名前のコード ファイルを開き、About ページに表示されるメッセージ (`ViewData["Message"] = "Your application description page.";`) を編集します。
1. ファイルを保存します。
1. **F5** キーを押して再度デバッグを開始します。 

コードを編集するたびに新しいコンテナー イメージを再構築して再展開すると、多くの場合、かなりの時間がかかります。Azure Dev Spaces では、代わりに既存のコンテナー内でコードの増分再コンパイルを実行することで、編集/デバッグ ループを高速化します。

ブラウザーで Web アプリを更新し、About ページに移動します。 UI にカスタム メッセージが表示されます。

## <a name="next-steps"></a>次のステップ

Azure Dev Spaces のしくみの詳細について確認します。

> [!div class="nextstepaction"]
> [Azure Dev Spaces のしくみ](how-dev-spaces-works.md)


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
