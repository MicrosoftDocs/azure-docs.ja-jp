---
title: ローカルで実行されている Azure Service Fabric Mesh Web アプリをデバッグする
description: このチュートリアルでは、ローカル クラスター上で実行している Azure Service Fabric Mesh アプリケーションをデバッグします。
author: georgewallace
ms.topic: tutorial
ms.date: 10/31/2018
ms.author: gwallace
ms.custom: mvc, devcenter
ms.openlocfilehash: 9e3dc16481340c0266cd398d0970e2147648e17f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99625451"
---
# <a name="tutorial-debug-a-service-fabric-mesh-application-running-in-your-local-development-cluster"></a>チュートリアル: ローカル開発クラスター内で実行している Service Fabric Mesh アプリケーションをデバッグする

> [!IMPORTANT]
> Azure Service Fabric Mesh のプレビューは廃止されました。 Service Fabric Mesh API を介した新しいデプロイは許可されなくなります。 既存のデプロイのサポートは、2021 年 4 月 28 日まで継続されます。
> 
> 詳細については、「[Azure Service Fabric Mesh のプレビューの廃止](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)」を参照してください。

このチュートリアルはシリーズの第 2 部です。ローカル開発クラスター上の Azure Service Fabric Mesh アプリをビルドしてデバッグする方法について説明します。

このチュートリアルでは、次のことについて説明します。

> [!div class="checklist"]
> * Azure Service Fabric Mesh アプリケーションをビルドする際の動作
> * サービス間の呼び出しを確認するためのブレークポイントを設定する方法

このチュートリアル シリーズで学習する内容は次のとおりです。
> [!div class="checklist"]
> * [Visual Studio で Service Fabric Mesh アプリを作成する](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * ローカル開発クラスター内で実行されている Service Fabric Mesh アプリをデバッグする
> * [Service Fabric Mesh アプリをデプロイする](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * [Service Fabric Mesh アプリをアップグレードする](service-fabric-mesh-tutorial-upgrade.md)
> * [Service Fabric Mesh リソースをクリーンアップする](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に

* Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)できます。

* Service Fabric ランタイム、SDK、Docker、Visual Studio 2017 がインストールされた[開発環境の設定](service-fabric-mesh-howto-setup-developer-environment-sdk.md)が済んでいることを確認します。

## <a name="download-the-to-do-sample-application"></a>To Do サンプル アプリケーションをダウンロードする

[このチュートリアル シリーズの第 1 部](service-fabric-mesh-tutorial-create-dotnetcore.md)で To Do サンプル アプリケーションを作成しなかった場合は、ダウンロードできます。 コマンド ウィンドウで、次のコマンドを実行して、サンプル アプリのリポジトリをローカル コンピューターに複製します。

```
git clone https://github.com/azure-samples/service-fabric-mesh
```

アプリケーションは `src\todolistapp` ディレクトリにあります。

## <a name="build-and-debug-on-your-local-cluster"></a>ローカル クラスターでビルドおよびデバッグする

プロジェクトが読み込まれると、すぐに自動で Docker イメージがビルドされ、ローカル クラスターにデプロイされます。 このプロセスにはしばらく時間がかかることがあります。 Visual Studio の **[出力]** ウィンドウ内で進行状況を確認するには、[出力] ウィンドウの **[出力元:]** ボックスの一覧を **[Service Fabric Tools]** に設定します。

**F5** キーを押して、サービスをローカルでコンパイルおよび実行します。 プロジェクトがローカルで実行およびデバッグされている場合、Visual Studio は次のように動作します。

* Docker for Windows が実行中で、コンテナーのオペレーティング システムとして Windows を使用するように設定されていることを確認してください。
* 不足している Docker ベースイメージをダウンロードします。 これには時間がかかる場合があります。
* コード プロジェクトをホストするために使用する Docker イメージをビルド (またはリビルド) します。
* ローカルの Service Fabric 開発クラスター上のコンテナーをデプロイおよび実行します。
* サービスを実行し、設定したブレークポイントに到達します。

ローカルのデプロイが完了し、Visual Studio がアプリを実行している状態になると、ブラウザー ウィンドウで既定のサンプル Web ページが開きます。

## <a name="debugging-tips"></a>デバッグのヒント

最初のデバッグ実行 (F5) は、「[Visual Studio のパフォーマンスの最適化](service-fabric-mesh-howto-optimize-vs.md)」の手順に従うと大幅に時間が短縮されます。

現在、ある問題が原因で、`using (HttpResponseMessage response = client.GetAsync("").GetAwaiter().GetResult())` の呼び出しがサービスへの接続に失敗します。 この問題は、ホストの IP アドレスが変わるたびに発生します。 それを解決するには、次のようにします。

1. ローカル クラスターからアプリを削除します (Visual Studio で **[ビルド]**  >  **[ソリューションのクリーン]** を選択します)。
2. Service Fabric ローカル クラスター マネージャーから **[Stop Local CLuster]\(ローカル クラスターの停止\)** を選択し、**[Start Local Cluster]\(ローカル クラスターを起動する\)** を選択します。
3. アプリを再デプロイします (Visual Studio で **F5** キーを押します)。

"**No Service Fabric local cluster is running**" (Service Fabric のローカル クラスターが実行されていません) というエラーが表示された場合、Service Fabric のローカル クラスター マネージャー (LCM) が実行中になっていることを確認し、タスク バーの LCM アイコンを右クリックして、**[Start Local Cluster]\(ローカル クラスターを起動する\)** をクリックします。 起動したら、Visual Studio に戻り、**F5** キーを押します。

アプリの起動時に **404** エラーが表示された場合には、**service.yaml** 内の環境変数が正しくない可能性があります。 `ApiHostPort` と `ToDoServiceName` が「[環境変数を作成する](./service-fabric-mesh-tutorial-create-dotnetcore.md#create-environment-variables)」の手順に従って正しく設定されていることを確認します。

**service.yaml** でビルド エラーが表示された場合、行のインデントにタブではなくスペースを使用していることを確認してください。 また、現在、アプリをビルドするときは、英語のロケールを使用する必要があります。

### <a name="debug-in-visual-studio"></a>Visual Studio でのデバッグ

Visual Studio で Service Fabric Mesh アプリケーションをデバッグするときは、ローカルの Service Fabric 開発クラスターを使用します。 バックエンド サービスから To Do 項目が取得される方法を表示するには、OnGet() メソッドをデバッグします。
1. **WebFrontEnd** プロジェクト内で **[Pages]** > **[Index.cshtml]** > **[Index.cshtml.cs]** の順に開き、**OnGet** メソッド (17 行目) 内にブレークポイントを設定します。
2. **ToDoService** プロジェクト内で **[TodoController.cs]** を開き、**Get** メソッド (15 行目) 内にブレークポイントを設定します。
3. ブラウザーに戻り、ページを更新します。 Web フロントエンドの `OnGet()` メソッドでブレークポイントに到達します。 `backendUrl` 変数を検査すると、**service.yaml** ファイル内で定義した環境変数が、バックエンド サービスに接続するために使用する URL にどのように組み込まれているかを確認できます。
4. `client.GetAsync(backendUrl).GetAwaiter().GetResult())` の呼び出しをステップ オーバー (F10) し、コントローラーの `Get()` ブレークポイントに到達します。 このメソッドでは、メモリ内のリストから To Do 項目のリストがどのように取得されているかを確認できます。
5. 完了したら、Visual Studio で **Shift + F5** キーを押して、プロジェクトのデバッグを停止します。

## <a name="next-steps"></a>次のステップ

チュートリアルのこの部分で学習した内容は次のとおりです。

> [!div class="checklist"]
> * Azure Service Fabric Mesh アプリケーションをビルドする際の動作
> * サービス間の呼び出しを確認するためのブレークポイントを設定する方法

次のチュートリアルに進みます。
> [!div class="nextstepaction"]
> [Service Fabric Mesh アプリをデプロイする](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
