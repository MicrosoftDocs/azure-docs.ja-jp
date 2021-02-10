---
title: Azure Spring Cloud でステージング環境を設定する | Microsoft Docs
description: Azure Spring Cloud でブルーグリーン デプロイを使用する方法について説明します
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 991a335207fc29cef7b243d7e520dd5f62ff691f
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2021
ms.locfileid: "99226112"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>Azure Spring Cloud でステージング環境を設定する

**この記事の適用対象:** ✔️ Java

この記事では、Azure Spring Cloud でブルーグリーン デプロイ パターンを使ってステージング環境のデプロイを設定する方法について説明します。 ブルー/グリーン デプロイとは、新しい (グリーン) バージョンのデプロイ中に、既存 (ブルー) のバージョンを実行状態のまま保持する、Azure DevOps の継続的デリバリーのパターンです。 この記事では、運用環境デプロイに直接変更を加えることなく、ステージング環境のデプロイを運用環境に移す方法について説明します。

## <a name="prerequisites"></a>前提条件

* *Standard* **価格レベル** が適用される Azure Spring Cloud インスタンス。
* 実行中のアプリケーション  「[クイック スタート: 初めての Azure Spring Cloud アプリケーションをデプロイする](spring-cloud-quickstart.md)」を参照してください。
* Azure CLI [asc extension](https://docs.microsoft.com/cli/azure/azure-cli-extensions-overview)

この例に別のアプリケーションを使用したい場合は、そのアプリケーションの公開部分に簡単な変更を加える必要があります。  その変更により、ステージング環境のデプロイが運用環境と区別されます。

>[!TIP]
> Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。  最新バージョンの Git、JDK、Maven、Azure CLI など、一般的な Azure ツールがプレインストールされています。 Azure サブスクリプションにサインインしている場合は、[Azure Cloud Shell](https://shell.azure.com) を開始します。  詳細については、[Cloud Shell の概要](../cloud-shell/overview.md)に関するページをご覧ください。

Azure Spring Cloud でステージング環境を設定するには、次のセクションの手順に従います。

## <a name="install-the-azure-cli-extension"></a>Azure CLI 拡張機能をインストールする

次のコマンドを使用して、Azure CLI 用の Azure Spring Cloud 拡張機能をインストールします。

```azurecli
az extension add --name spring-cloud
```
    
## <a name="view-apps-and-deployments"></a>アプリとデプロイを表示する

デプロイされたアプリを表示するには、次の手順を行います。

1. Azure portal で Azure Spring Cloud インスタンスに移動します。

1. 左側のナビゲーション ウィンドウで、 **[デプロイ]** を開きます。

    [ ![デプロイ - 廃止](media/spring-cloud-blue-green-staging/deployments.png)](media/spring-cloud-blue-green-staging/deployments.png)

1. [アプリ] ブレードを開いて、ご利用のサービス インスタンス用のアプリを表示します。

    [ ![アプリ - ダッシュボード](media/spring-cloud-blue-green-staging/app-dashboard.png)](media/spring-cloud-blue-green-staging/app-dashboard.png)

1. アプリをクリックして詳細を表示できます。

    [ ![アプリ - 概要](media/spring-cloud-blue-green-staging/app-overview.png)](media/spring-cloud-blue-green-staging/app-overview.png)

1. **[デプロイ]** ブレードを開いて、アプリのすべてのデプロイを表示します。 デプロイ グリッドに、デプロイが運用環境またはステージング環境のどちらであるかが表示されます。

    [ ![[デプロイ] ダッシュボード](media/spring-cloud-blue-green-staging/deployments-dashboard.png)](media/spring-cloud-blue-green-staging/deployments-dashboard.png)

1. デプロイの名前をクリックすると、デプロイの概要が表示されます。 この場合、唯一のデプロイには *Default* という名前が付けられます。

    [ ![デプロイの概要](media/spring-cloud-blue-green-staging/deployments-overview.png)](media/spring-cloud-blue-green-staging/deployments-overview.png)
    

## <a name="create-a-staging-deployment"></a>ステージング環境のデプロイの作成

1. ご利用のローカル開発環境で、アプリケーションに小さな変更を加えます。 こうすることで、2 つのデプロイを簡単に区別できるようになります。 jar パッケージをビルドするには、次のコマンドを実行します。 

    ```console
    mvn clean package -DskipTests
    ```

1. Azure CLI で新しいデプロイを作成し、ステージング環境のデプロイの名前を "グリーン" にします。

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app <appName> -n green --jar-path gateway/target/gateway.jar
    ```

1. CLI デプロイが正常に完了したら、 **[アプリケーション ダッシュボード]** からアプリのページにアクセスし、左側の **[デプロイ]** タブで自分のインスタンスをすべて表示します。

   [ ![グリーンのデプロイ後の [デプロイ] ダッシュボード](media/spring-cloud-blue-green-staging/deployments-dashboard-2.png)](media/spring-cloud-blue-green-staging/deployments-dashboard-2.png)

  
> [!NOTE]
> 検出状態が *OUT_OF_SERVICE* になっているので、確認が完了するまで、このデプロイにはトラフィックがルーティングされません。

## <a name="verify-the-staging-deployment"></a>ステージング環境のデプロイを確認する

グリーン ステージング環境の開発が機能していることを確認するには、次のようにします。
1. **[デプロイ]** にアクセスし、`green` の **[ステージング環境のデプロイ]** をクリックします。
1. **[概要]** ページで、 **[テストエンドポイント]** をクリックします。
1. これにより、ステージング ビルドが開き、加えた変更が示されます。

>[!TIP]
> * テスト エンドポイントの末尾がスラッシュ (/) であり、CSS ファイルが正しく読み込まれていることを確認します。  
> * ページを表示するためにブラウザーでログイン資格情報の入力を求められる場合は、[URL デコード](https://www.urldecoder.org/)を使用して自分のテスト エンドポイントをデコードします。 URL デコードでは、"https://\<username>:\<password>@\<cluster-name>.test.azureapps.io/gateway/green" の形式の URL が返されます。  このフォームを使用してエンドポイントにアクセスします。

>[!NOTE]    
> 構成サーバーの設定はステージング環境と運用環境の両方に適用されます。 たとえば、構成サーバー内のアプリ ゲートウェイのコンテキスト パス (`server.servlet.context-path`) を *somepath* として設定すると、グリーン デプロイへのパスは "https://\<username>:\<password>@\<cluster-name>.test.azureapps.io/gateway/green/somepath/..." に変更されます。
 
 このポイントで自分の公開アプリ ゲートウェイにアクセスすると、新しい変更が反映されていない古いページが表示されます。
    
## <a name="set-the-green-deployment-as-the-production-environment"></a>実稼働環境としてグリーン デプロイを設定する

1. ステージング環境で変更を確認したら、それを運用環境にプッシュすることができます。 **[デプロイの管理]** に戻って、現在 `Production` であるアプリケーションを選択します。

1. **[登録状態]** の後にある省略記号をクリックし、運用ビルドを `staging` に設定します。

   [ ![[デプロイ] で設定された [ステージング環境のデプロイ]](media/spring-cloud-blue-green-staging/set-staging-deployment.png)](media/spring-cloud-blue-green-staging/set-staging-deployment.png)

1. **[デプロイの管理]** ページに戻ります。 `green` デプロイを `production` に設定します。 設定が完了すると、`green` デプロイの状態の表示が *[稼働中]* になります。 これは稼働中の運用ビルドになりました。

   [ ![[デプロイ] で設定されたステージング環境のデプロイの結果](media/spring-cloud-blue-green-staging/set-staging-deployment-result.png)](media/spring-cloud-blue-green-staging/set-staging-deployment-result.png)

1. アプリの URL に加えた変更が表示されます。

>[!NOTE]
> グリーン デプロイを運用環境に設定したら、前のデプロイがステージング環境のデプロイになります。

## <a name="modify-the-staging-deployment"></a>ステージング環境のデプロイを変更する

変更に満足していない場合は、Azure CLI を使用することで、アプリケーション コードを変更し、新しい jar パッケージをビルドして、それをグリーン デプロイにアップロードできます。

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-the-staging-deployment"></a>ステージング環境のデプロイを削除する

Azure ポートから自分のステージング環境のデプロイを削除するには、ステージング環境のデプロイのページに移動して **[削除]** ボタンを選択します。

または、次のコマンドを実行して Azure CLI でステージング環境のデプロイを削除します。

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```

## <a name="next-steps"></a>次の手順

* [Azure Spring Cloud の CI/CD](https://review.docs.microsoft.com/azure/spring-cloud/spring-cloud-howto-cicd?branch=pr-en-us-142929&pivots=programming-language-java)
