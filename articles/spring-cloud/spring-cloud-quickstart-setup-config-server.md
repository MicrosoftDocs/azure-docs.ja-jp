---
title: クイックスタート - Azure Spring Cloud 構成サーバーを設定する
description: アプリのデプロイのための Azure Spring Cloud 構成サーバーの設定について説明します。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/03/2020
ms.custom: devx-track-java
ms.openlocfilehash: 47f74b551919177b13f5a72d6eedeae3c77b9f14
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "88951663"
---
# <a name="quickstart-set-up-azure-spring-cloud-configuration-server"></a>クイック スタート:Azure Spring Cloud の構成サーバーを設定する

Azure Spring Cloud 構成サーバーは、分散システムのための一元化された構成サービスです。 現時点でローカル ストレージ、Git、および Subversion をサポートしている、プラグ可能なリポジトリ レイヤーを使用します。  Azure Spring Cloud にマイクロサービス アプリをデプロイするように、構成サーバーを設定します。

## <a name="prerequisites"></a>前提条件

* [JDK 8 をインストールする](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
* [Azure サブスクリプションにサインアップする](https://azure.microsoft.com/free/)
* (オプション) [Azure CLI バージョン 2.0.67 以降をインストール](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)し、`az extension add --name spring-cloud` コマンドを使用して Azure Spring Cloud 拡張機能をインストールする
* (オプション) [Azure Toolkit for IntelliJ をインストール](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/)し、[サインイン](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)する

## <a name="azure-spring-cloud-config-server-procedures"></a>Azure Spring Cloud 構成サーバーの手順

#### <a name="portal"></a>[ポータル](#tab/Azure-portal)

次の手順では、Azure portal を使用して、[Piggymetrics サンプル](spring-cloud-quickstart-sample-app-introduction.md)をデプロイするように構成サーバーを設定します。

1. サービスの **[概要]** ページに移動し、 **[Config Server]\(構成サーバー\)** を選択します。

2. **[既定のリポジトリ]** セクションで **[URI]** を "https://github.com/Azure-Samples/piggymetrics-config" に設定します。

3. **[適用]** を選択して変更を保存します。

    ![ASC ポータルのスクリーンショット](media/spring-cloud-quickstart-launch-app-portal/portal-config.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

次の手順では、Azure CLI を使用して、[Piggymetrics サンプル](spring-cloud-quickstart-sample-app-introduction.md)をデプロイするように構成サーバーを設定します。

次のように、プロジェクトの git リポジトリの場所を使用して、構成サーバーを設定します。

```azurecli
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/Azure-Samples/piggymetrics-config
```

---

## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [アプリをビルドして配置する](spring-cloud-quickstart-deploy-apps.md)
