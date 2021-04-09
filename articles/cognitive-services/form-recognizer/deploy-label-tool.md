---
title: Form Recognizer のサンプル ラベル付けツールのデプロイ方法
titleSuffix: Azure Cognitive Services
description: 監視された学習に役立てるため、Form Recognizer のサンプル ラベル付けツールをデプロイするさまざまな方法について説明します。
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 02/11/2021
ms.author: lajanuar
ms.openlocfilehash: 0f5f0714235ee23624b3a199eac744155d2bbdd1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101093389"
---
# <a name="deploy-the-sample-labeling-tool"></a>サンプル ラベル付けツールのデプロイ

Form Recognizer のサンプル ラベル付けツールは、教師あり学習のためにフォーム (ドキュメント) に手動でラベルを付けることができる、簡単なユーザー インターフェイス (UI) のアプリケーションです。 この記事では、次の方法についてのリンクと説明を提供します。

* [サンプル ラベル付けツールをローカル環境で実行する](#run-the-sample-labeling-tool-locally)
* [サンプル ラベル付けツールを Azure コンテナー インスタンス (ACI) にデプロイする](#deploy-with-azure-container-instances-aci)
* [オープンソースの OCR Form Labeling Tool を使用して、それに貢献する](#open-source-on-github)

## <a name="run-the-sample-labeling-tool-locally"></a>サンプル ラベル付けツールをローカル環境で実行する

データのラベル付けを最も簡単に始める方法は、サンプル ラベル付けツールをローカル環境で実行することです。 以下のクイックスタートでは、Form Recognizer REST API とサンプル ラベル付けツールを使用し、手動でラベル付けされたデータでカスタム モデルをトレーニングします。 

* [クイック スタート: サンプル ラベル付けツールを使用したフォームのラベル付け、モデルのトレーニング、フォームの分析](./quickstarts/label-tool.md)。

## <a name="deploy-with-azure-container-instances-aci"></a>Azure Container Instances (ACI) を使用してデプロイする

始める前に、Azure コンテナー インスタンス (ACI) にサンプル ラベル付けツールをデプロイするには 2 つの方法があることに注意することが重要です。 どちらのオプションも、ACI でサンプル ラベル付けツールを実行するために使用されます。

* [Azure Portal の使用](#azure-portal)
* [Azure CLI の使用](#azure-cli)

### <a name="azure-portal"></a>Azure portal

Azure portal を使用して新しいリソースを作成するには、次の手順のようにします。 

1. [Azure portal](https://portal.azure.com/signin/index/) にサインインします。
2. **[リソースの作成]** を選択します。
3. 次に、 **[Web アプリ]** を選択します。

   > [!div class="mx-imgBorder"]
   > ![[Web アプリ] を選択する](./media/quickstarts/create-web-app.png)

4. 最初に、 **[基本]** タブが選択されていることを確認します。 次に、いくつかの情報を指定する必要があります。

   > [!div class="mx-imgBorder"]
   > ![[基本] を選択する](./media/quickstarts/select-basics.png)
   * [サブスクリプション] - 既存の Azure サブスクリプションを選択します
   * [リソース グループ] - 既存のリソース グループを再利用するか、このプロジェクト用に新しいリソース グループを作成することができます。 新しいリソース グループを作成することをお勧めします。
   * [名前] - Web アプリの名前を指定します。 
   * [発行] - **[Docker コンテナー]** を選択します
   * [オペレーティング システム] - **[Linux]** を選択します
   * [リージョン] - 適切なリージョンを選択します。
   * [Linux プラン] - App Service の価格レベルとプランを選択します。 

   > [!div class="mx-imgBorder"]
   > ![Web アプリ を構成する](./media/quickstarts/select-docker.png)

5. 次に、 **[Docker]** タブを選択します。

   > [!div class="mx-imgBorder"]
   > ![[Docker] を選択する](./media/quickstarts/select-docker.png)

6. ここでは、Docker コンテナーを構成します。 特に注意がない限り、すべてのフィールドが必須です。
<!-- markdownlint-disable MD025 -->
# <a name="v21-preview"></a>[v2.1 プレビュー](#tab/v2-1)

* [オプション] - **[単一コンテナー]** を選択します
* [イメージ ソース] - **[プライベート レジストリ]** を選択します 
* [サーバー URL] - これは `https://mcr.microsoft.com` に設定します
* [ユーザー名] (省略可能) - ユーザー名を作成します。 
* [パスワード] (省略可能) - 憶えやすい安全なパスワードを作成します。
* [イメージとタグ] - これは `mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest-preview` に設定します
* 継続的なデプロイ - 開発チームがサンプル ラベル付けツールを変更したときに自動更新を受信する場合は、これを **オン** に設定します。
* [スタートアップ コマンド] - これは `./run.sh eula=accept` に設定します

# <a name="v20"></a>[v2.0](#tab/v2-0)  

* [オプション] - **[単一コンテナー]** を選択します
* [イメージ ソース] - **[プライベート レジストリ]** を選択します 
* [サーバー URL] - これは `https://mcr.microsoft.com` に設定します
* [ユーザー名] (省略可能) - ユーザー名を作成します。 
* [パスワード] (省略可能) - 憶えやすい安全なパスワードを作成します。
* [イメージとタグ] - これは `mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest` に設定します
* 継続的なデプロイ - 開発チームがサンプル ラベル付けツールを変更したときに自動更新を受信する場合は、これを **オン** に設定します。
* [スタートアップ コマンド] - これは `./run.sh eula=accept` に設定します

 ---

   > [!div class="mx-imgBorder"]
   > ![Docker を構成する](./media/quickstarts/configure-docker.png)

7. これで終了です。 次に、 **[確認と作成]** を選択し、 **[作成]** を選択して Web アプリをデプロイします。 完了すると、リソースの **[概要]** で指定した URL で Web アプリにアクセスできるようになります。

> [!NOTE]
> Web アプリを作成するときに、承認と認証を構成することもできます。 これは、開始するために必要なことではありません。

> [!IMPORTANT]
> Web アプリが `https` アドレスで表示されるようにするために、TLS を有効にしなければならない場合があります。 [TLS エンドポイントを有効にする](../../container-instances/container-instances-container-group-ssl.md)ための手順に従って、Web アプリの TLS/SSL を有効にするサイドカー コンテナーを設定してください。
<!-- markdownlint-disable MD001 -->
### <a name="azure-cli"></a>Azure CLI

Azure portal を使用する代わりに、Azure CLI を使用してリソースを作成することもできます。 続ける前に、[Azure CLI](/cli/azure/install-azure-cli) をインストールする必要があります。 既に Azure CLI を使用している場合は、このステップを省略できます。 

このコマンドについて知っておくべきことがいくつかあります。

* `DNS_NAME_LABEL=aci-demo-$RANDOM` ではランダムな DNS 名が生成されます。 
* このサンプルでは、リソースの作成に使用できるリソース グループがあることを前提としています。 `<resource_group_name>` は、サブスクリプションに関連付けられている有効なリソース グループに置き換えます。 
* リソースを作成する場所を指定する必要があります。 `<region name>` は、Web アプリの目的のリージョンに置き換えます。
* このコマンドでは、EULA が自動的に受け入れられます。

Azure CLI から次のコマンドを実行して、サンプル ラベル付けツール用の Web アプリ リソースを作成します。

<!-- markdownlint-disable MD024 -->
# <a name="v21-preview"></a>[v2.1 プレビュー](#tab/v2-1)

```azurecli
DNS_NAME_LABEL=aci-demo-$RANDOM

az container create \
  --resource-group <resource_group_name> \
  --name <name> \
  --image mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest-preview \
  --ports 3000 \
  --dns-name-label $DNS_NAME_LABEL \
  --location <region name> \
  --cpu 2 \
  --memory 8 \
  --command-line "./run.sh eula=accept"

```

# <a name="v20"></a>[v2.0](#tab/v2-0)


```azurecli
DNS_NAME_LABEL=aci-demo-$RANDOM

az container create \
  --resource-group <resource_group_name> \
  --name <name> \
  --image mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool \
  --ports 3000 \
  --dns-name-label $DNS_NAME_LABEL \
  --location <region name> \
  --cpu 2 \
  --memory 8 \
  --command-line "./run.sh eula=accept"
``` 


---

### <a name="connect-to-azure-ad-for-authorization"></a>承認のために Azure AD に接続する

Web アプリを Azure Active Directory に接続することをお勧めします。 これにより、有効な資格情報を持つユーザーのみがサインインして Web アプリを使用できるようになります。 [App Service アプリの構成](../../app-service/configure-authentication-provider-aad.md)に関する記事の手順に従って、Azure Active Directory に接続します。

## <a name="open-source-on-github"></a>GitHub でのオープンソース

GitHub のオープンソース プロジェクトの OCR Form Labeling Tool を使用することもできます。 このツールは、React + Redux を使用してビルドされ、TypeScript で記述された Web アプリケーションです。 詳細については、または投稿に関しては、[OCR Form Labeling Tool](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md) に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

[ラベルを使用したトレーニング](./quickstarts/label-tool.md)のクイックスタートを使用し、ツールを使用してトレーニング データに手動でラベルを付ける方法、および教師あり学習を行う方法について学習します。