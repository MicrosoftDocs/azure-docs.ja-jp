---
title: クイックスタート - ソース コードから Spring Cloud アプリケーションを起動する
description: このクイックスタートでは、ソース コードから Azure Spring Cloud アプリケーションを直接起動する方法について説明します
author: jpconnock
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/30/2019
ms.author: jeconnoc
ms.openlocfilehash: ca78a9d7eeeeccb71fea75f6ab429e76f2344bbe
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895455"
---
# <a name="quickstart-launch-your-spring-cloud-application-from-source-code"></a>クイック スタート:ソース コードから Spring Cloud アプリケーションを起動する

Azure Spring Cloud では、Spring Cloud ベースのマイクロサービス アプリケーションを Azure で簡単に実行できます。

Azure Spring Cloud を使用すると、Java ソース コードから、またはビルド済みの JAR から直接アプリケーションを起動できます。 この記事では、必要な手順について説明します。

このクイックスタートでは、次の方法について説明します。

> [!div class="checklist"]
> * サービス インスタンスをプロビジョニングする
> * インスタンスに構成サーバーを設定する
> * マイクロサービス アプリケーションをローカルにビルドする
> * 各マイクロサービスをデプロイする
> * アプリケーションのパブリック エンドポイントを割り当てる

## <a name="prerequisites"></a>前提条件

>[!Note]
> Azure Spring Cloud は現時点ではパブリック プレビューとして提供されています。 パブリック プレビュー オファリングにより、お客様は公式リリースの前に新機能を試すことができます。  パブリック プレビューの機能とサービスは、運用環境での使用を目的としたものではありません。  プレビュー期間中のサポートの詳細については、[FAQ](https://azure.microsoft.com/support/faq/) のページを参照するか、詳細について[サポート リクエスト](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)を提出してください。

開始する前に、ご利用の Azure サブスクリプションで、以下の必要な依存関係があることを確認します。

1. [Git をインストールする](https://git-scm.com/)
2. [JDK 8 をインストールする](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
3. [Maven 3.0 以上をインストールする](https://maven.apache.org/download.cgi)
4. [Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Azure サブスクリプションにサインアップする](https://azure.microsoft.com/free/)

> [!TIP]
> Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。  最新バージョンの Git、JDK、Maven、Azure CLI など、一般的な Azure ツールがプレインストールされています。 Azure サブスクリプションにログインしている場合は、shell.azure.com から [Azure Cloud Shell](https://shell.azure.com) を起動します。  Azure Cloud Shell の詳細については、[ドキュメントを参照](../cloud-shell/overview.md)してください

## <a name="install-the-azure-cli-extension"></a>Azure CLI 拡張機能のインストール

次のコマンドを使用して、Azure CLI 用の Azure Spring Cloud 拡張機能をインストールします

```Azure CLI
az extension add --name spring-cloud
```

## <a name="provision-a-service-instance-using-the-azure-cli"></a>Azure CLI を使用してサービス インスタンスをプロビジョニングする

Azure CLI にログインし、アクティブなサブスクリプションを選択します。 Azure Spring Cloud のホワイトリストに登録されているアクティブなサブスクリプションを選択してください

```Azure CLI
az login
az account list -o table
az account set --subscription
```

Azure CLI ウィンドウを開き、次のコマンドを実行して、Azure Spring Cloud のインスタンスをプロビジョニングします。 ここで Azure Spring Cloud にパブリック ドメインの割り当てを指示していることにも留意します。

```azurecli
    az spring-cloud create -n <resource name> -g <resource group name> --is-public true
```

サービス インスタンスのデプロイには約 5 分かかります。

次のコマンドを使用して、既定のリソース グループ名とクラスター名を設定します。

```azurecli
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```

## <a name="create-the-spring-cloud-application"></a>Spring Cloud アプリケーションを作成する

次のコマンドで、サブスクリプションに Spring Cloud アプリケーションを作成します。  アプリケーションをアップロードできる空の Spring Cloud サービスが作成されます。

```azurecli
az spring-cloud app create -n <app-name>
```

## <a name="deploy-your-spring-cloud-application"></a>Spring Cloud アプリケーションをデプロイする

ビルド済みの JAR から、あるいは Gradle または Maven リポジトリからアプリケーションをデプロイできます。  ケースごとに次の手順を行います。

### <a name="deploy-a-built-jar"></a>ビルドされた JAR をデプロイする

ローカル コンピューター上にビルドされた JAR からデプロイするには、ビルドによって [fat-JAR](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-build.html#howto-create-an-executable-jar-with-maven) が生成されることを確認します。

アクティブなデプロイに fat-JAR をデプロイするには

```azurecli
az spring-cloud app deploy -n <app-name> --jar-path <path-to-fat-JAR>
```

特定のデプロイに fat-JAR をデプロイするには

```azurecli
az spring-cloud app deployment create --app <app-name> -n <deployment-name> --jar-path <path-to-built-jar>
```

### <a name="deploy-from-source-code"></a>ソース コードからのデプロイ

Azure Spring Cloud では、[kpack](https://github.com/pivotal/kpack) を使用してプロジェクトをビルドします。  Azure CLI を使用してソース コードをアップロードし、kpack を使用してプロジェクトをビルドして、ターゲット アプリケーションにデプロイすることができます。

> [!WARNING]
> プロジェクトは、Maven デプロイの場合は、`target` の `MANIFEST.MF` に `main-class` エントリを持つ JAR ファイルを 1 つだけ生成する必要があります (Gradle デプロイの場合は、`build/libs`)。  `main-class` のエントリを持つ複数の JAR ファイルを使用すると、デプロイが失敗します。

単一モジュールの Maven/Gradle プロジェクトの場合:

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name>
```

複数のモジュールを含む Maven/Gradle プロジェクトの場合は、モジュールごとに以下を繰り返します。

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name> --target-module <relative-path-to-module>
```

### <a name="show-deployment-logs"></a>デプロイ ログの表示

次のコマンドを使用して、kpack ビルド ログを確認します。

```azurecli
az spring-cloud app show-deploy-log -n <app-name> [-d <deployment-name>]
```

> [!NOTE]
> kpack ログには、そのデプロイが kpack を使用してソースからビルドされた場合にのみ、最新のデプロイが表示されます。

## <a name="assign-a-public-endpoint-to-gateway"></a>ゲートウェイにパブリック エンドポイントを割り当てる

1. **[アプリケーション ダッシュボード]** ページを開きます。
2. `gateway` アプリケーションを選択して、 **[アプリケーションの詳細]** ページを表示します。
3. **[Assign Domain]\(ドメインの割り当て\)** を選択して、ゲートウェイにパブリック エンドポイントを割り当てます。 これには数分かかることがあります。 
4. 実行中のアプリケーションを表示するには、割り当てられたパブリック IP をブラウザーに入力します。

## <a name="next-steps"></a>次の手順

このクイック スタートでは、次の方法について説明しました。

> [!div class="checklist"]
> * サービス インスタンスをプロビジョニングする
> * インスタンスに構成サーバーを設定する
> * マイクロサービス アプリケーションをローカルにビルドする
> * 各マイクロサービスをデプロイする
> * アプリケーションの環境変数を編集する
> * アプリケーション ゲートウェイにパブリック IP を割り当てる

> [!div class="nextstepaction"]
> [Azure Spring Cloud アプリケーションをデプロイ用に準備する](spring-cloud-tutorial-prepare-app-deployment.md)

その他のサンプルを GitHub で入手できます ([Azure Spring Cloud のサンプル](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql))。
