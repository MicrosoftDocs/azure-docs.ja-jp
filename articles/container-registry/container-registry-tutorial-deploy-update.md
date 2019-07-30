---
title: チュートリアル - 更新されたコンテナー イメージをリージョンの Azure アプリのデプロイにプッシュする
description: 変更された Docker イメージを Geo レプリケートされた Azure コンテナー レジストリにプッシュした後、それらの変更が複数のリージョンで実行されている Web アプリに自動的にデプロイされたことを確認します。 3 部構成のシリーズの第 3 部。
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: tutorial
ms.date: 04/30/2018
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: e01fdc41d0cc2e65951bd92378eb59f0fd35816a
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310430"
---
# <a name="tutorial-push-an-updated-container-image-to-a-geo-replicated-container-registry-for-regional-web-app-deployments"></a>チュートリアル:更新されたコンテナー イメージをリージョンの Web アプリのデプロイ用に geo レプリケーション コンテナー レジストリにプッシュする

これは 3 部構成のチュートリアル シリーズの第 3 部です。 [前のチュートリアル](container-registry-tutorial-deploy-app.md)では、Geo レプリケーションは 2 つの異なるリージョン Web アプリ デプロイに対して構成されました。 このチュートリアルでは、まずアプリケーションを変更してから、新しいコンテナー イメージを構築し、それを Geo レプリケートされたレジストリにプッシュします。 最後に、両方の Web アプリ インスタンスで、Azure Container Registry Webhook によって自動的にデプロイされた変更を表示します。

このチュートリアルは、シリーズの最後の部分です。

> [!div class="checklist"]
> * Web アプリケーション HTML を変更する
> * Docker イメージを構築してタグ付けする
> * 変更を Azure Container Registry にプッシュする
> * 2 つの異なるリージョン内の更新されたアプリを表示する

2 つの *Web App for Containers* リージョン デプロイをまだ構成していない場合は、シリーズ内の前のチュートリアルに戻り、[Azure Container Registry から Web アプリをデプロイします](container-registry-tutorial-deploy-app.md)。

## <a name="modify-the-web-application"></a>Web アプリケーションを変更する

この手順では、更新されたコンテナー イメージを Azure Container Registry にプッシュしたら明確にわかる変更を Web アプリケーションに加えます。

前のチュートリアルで [GitHub から複製した](container-registry-tutorial-prepare-registry.md#get-application-code)アプリケーション ソース内の `AcrHelloworld/Views/Home/Index.cshtml` ファイルを見つけ、それをお気に入りのテキスト エディターで開きます。 次の行を既存の `<h1>` 行の下に追加します。

```html
<h1>MODIFIED</h1>
```

変更された `Index.cshtml` は次のようになります。

```html
@{
    ViewData["Title"] = "Azure Container Registry :: Geo-replication";
}
<style>
    body {
        background-image: url('images/azure-regions.png');
        background-size: cover;
    }
    .footer {
        position: fixed;
        bottom: 0px;
        width: 100%;
    }
</style>

<h1 style="text-align:center;color:blue">Hello World from:  @ViewData["REGION"]</h1>
<h1>MODIFIED</h1>
<div class="footer">
    <ul>
        <li>Registry URL: @ViewData["REGISTRYURL"]</li>
        <li>Registry IP: @ViewData["REGISTRYIP"]</li>
        <li>Registry Region: @ViewData["REGION"]</li>
    </ul>
</div>
```

## <a name="rebuild-the-image"></a>イメージを再構築する

これで Web アプリケーションが更新されたので、そのコンテナー イメージを再構築します。 以前と同様に、タグにはログイン サーバー URL を含む完全修飾イメージ名（FQDN）を使用します:

```bash
docker build . -f ./AcrHelloworld/Dockerfile -t <acrName>.azurecr.io/acr-helloworld:v1
```

## <a name="push-image-to-azure-container-registry"></a>Azure Container Registry へのイメージのプッシュ

次に、更新された *acr-helloworld* コンテナー イメージを Geo レプリケートされたレジストリにプッシュします。 ここでは、更新されたイメージを*米国西部*と*米国東部*の両方のリージョンにあるレジストリ レプリカにデプロイするための 1 つの `docker push` コマンドを実行します。

```bash
docker push <acrName>.azurecr.io/acr-helloworld:v1
```

`docker push`出力は次のようになります:

```console
$ docker push uniqueregistryname.azurecr.io/acr-helloworld:v1
The push refers to a repository [uniqueregistryname.azurecr.io/acr-helloworld]
5b9454e91555: Pushed
d6803756744a: Layer already exists
b7b1f3a15779: Layer already exists
a89567dff12d: Layer already exists
59c7b561ff56: Layer already exists
9a2f9413d9e4: Layer already exists
a75caa09eb1f: Layer already exists
v1: digest: sha256:4c3f2211569346fbe2d1006c18cbea2a4a9dcc1eb3a078608cef70d3a186ec7a size: 1792
```

## <a name="view-the-webhook-logs"></a>Webhook ログを表示する

イメージがレプリケートされている間、Azure Container Registry Webhook がトリガーされていることを確認できます。

前のチュートリアルでコンテナーを *Web Apps for Containers* にデプロイしたときに作成されたリージョン Webhook を表示するには、Azure Portal のコンテナー レジストリに移動し、 **[SERVICES] \(サービス)** の **[Webhook]** を選択します。

![Azure Portal のコンテナー レジストリ Webhook][tutorial-portal-01]

各 Webhook を選択すると、その呼び出しと応答の履歴が表示されます。 両方の Webhook のログに**プッシュ** アクションの行が表示されます。 ここで、*米国西部*リージョンにある Webhook のログは、前の手順で `docker push` によってトリガーされた**プッシュ** アクションを示しています。

![Azure Portal のコンテナー レジストリ Webhook のログ (米国西部)][tutorial-portal-02]

## <a name="view-the-updated-web-app"></a>更新された Web アプリを表示する

Webhook は、Web アプリに新しいイメージがレジストリにプッシュされたことを通知します。これにより、更新されたコンテナーが 2 つのリージョン Web アプリに自動的にデプロイされます。

Web ブラウザーで両方のリージョン Web アプリ デプロイに移動することによって、アプリケーションが両方のデプロイで更新されたことを確認します。 各 [App Service の概要] タブの右上で、デプロイされた Web アプリの URL を見つけることができます。

![Azure Portal の [App Service の概要]][tutorial-portal-03]

更新されたアプリケーションを表示するには、[App Service の概要] のリンクを選択します。 *米国西部*で実行されているアプリのビューの例を次に示します。

![米国西部リージョンで実行されている変更された Web アプリのブラウザー ビュー][deployed-app-westus-modified]

ブラウザーで*米国東部*デプロイを表示することによって、更新されたコンテナー イメージがここにもデプロイされたことを確認します。

![米国東部リージョンで実行されている変更された Web アプリのブラウザー ビュー][deployed-app-eastus-modified]

1 つの`docker push`で、両方地域で Web アプリの展開で実行されている web アプリケーションが自動的に更新されました。 Azure Container Registry は、各デプロイの一番近くに位置するデポジトリからコンテナー イメージを提供します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Web アプリケーション コンテナーの新しいバージョンを更新し、それを Geo レプリケートされたレジストリにプッシュしました。 Azure Container Registry の Webhook が Web Apps for Containers に更新を通知し、それが一番近いレジストリ レプリカからのローカル プルをトリガーしました。

### <a name="acr-build-automated-image-build-and-patch"></a>ACR のビルド:自動イメージ ビルドとパッチ

Geo レプリケーションに加えて、ACR ビルドはコンテナー展開パイプラインを最適化するための別のAzure Container Registry です。 ACRビルド概要から始めて、機能を理解するアイデアを得ましょう:

[ACRビルドを使用してOSとフレームワークの修正プログラムの適用を自動化する](container-registry-tasks-overview.md)

<!-- IMAGES -->
[deployed-app-eastus-modified]: ./media/container-registry-tutorial-deploy-update/deployed-app-eastus-modified.png
[deployed-app-westus-modified]: ./media/container-registry-tutorial-deploy-update/deployed-app-westus-modified.png
[local-container-01]: ./media/container-registry-tutorial-deploy-update/local-container-01.png
[tutorial-portal-01]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-01.png
[tutorial-portal-02]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-02.png
[tutorial-portal-03]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-03.png