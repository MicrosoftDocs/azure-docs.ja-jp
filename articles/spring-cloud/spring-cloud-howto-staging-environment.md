---
title: Azure Spring Cloud でステージング環境を設定する | Microsoft Docs
description: Azure Spring Cloud でブルーグリーン デプロイを使用する方法について説明します
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 5333dd45cee58821d89cd49c44d3c5bba4cd9115
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87500404"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>Azure Spring Cloud でステージング環境を設定する

この記事では、Azure Spring Cloud でブルーグリーン デプロイ パターンを使ってステージング環境のデプロイを設定する方法について説明します。 ブルー/グリーン デプロイとは、新しい (グリーン) バージョンのデプロイ中に、既存 (ブルー) のバージョンを実行状態のまま保持する、Azure DevOps の継続的デリバリーのパターンです。 この記事では、運用環境デプロイに直接変更を加えることなく、ステージング環境のデプロイを運用環境に移す方法について説明します。

## <a name="prerequisites"></a>前提条件

この記事では、[Azure Spring Cloud アプリケーションの起動に関するチュートリアル](spring-cloud-quickstart-launch-app-portal.md)の PiggyMetrics アプリケーションを既にデプロイしてあることを前提としています。 PiggyMetrics は、"gateway"、"account-service"、"auth-service" という 3 つのアプリケーションで構成されます。  

この例に別のアプリケーションを使用したい場合は、そのアプリケーションの公開部分に簡単な変更を加える必要があります。  その変更により、ステージング環境のデプロイが運用環境と区別されます。

>[!TIP]
> Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。  最新バージョンの Git、JDK、Maven、Azure CLI など、一般的な Azure ツールがプレインストールされています。 Azure サブスクリプションにサインインしている場合は、[Azure Cloud Shell](https://shell.azure.com) を開始します。  詳細については、[Cloud Shell の概要](../cloud-shell/overview.md)に関するページをご覧ください。

Azure Spring Cloud でステージング環境を設定するには、次のセクションの手順に従います。

## <a name="install-the-azure-cli-extension"></a>Azure CLI 拡張機能をインストールする

次のコマンドを使用して、Azure CLI 用の Azure Spring Cloud 拡張機能をインストールします。

```azurecli
az extension add --name spring-cloud
```
    
## <a name="view-all-deployments"></a>すべてのデプロイを表示する

Azure portal で自分のサービス インスタンスに移動し、 **[Deployment management]\(デプロイ管理\)** を選択してすべてのデプロイを表示します。 詳細を表示するには、各デプロイを選択します。

## <a name="create-a-staging-deployment"></a>ステージング環境のデプロイの作成

1. ローカル開発環境で、PiggyMetrics のゲートウェイ アプリケーションに小さな変更を加えます。 たとえば、*gateway/src/main/resources/static/css/launch.css* ファイルの色を変更します。 こうすることで、2 つのデプロイを簡単に区別できるようになります。 jar パッケージをビルドするには、次のコマンドを実行します。 

    ```console
    mvn clean package
    ```

1. Azure CLI で新しいデプロイを作成し、ステージング環境のデプロイの名前を "グリーン" にします。

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app gateway -n green --jar-path gateway/target/gateway.jar
    ```

1. デプロイが正常に完了したら、 **[アプリケーション ダッシュボード]** からゲートウェイのページにアクセスし、左側の **[App Instances]\(アプリ インスタンス\)** タブで自分のインスタンスをすべて表示します。
  
> [!NOTE]
> 検出状態が *OUT_OF_SERVICE* になっているので、確認が完了するまで、このデプロイにはトラフィックがルーティングされません。

## <a name="verify-the-staging-deployment"></a>ステージング環境のデプロイを確認する

1. **[Deployment management]\(デプロイ管理\)** に戻って、新しいデプロイを選択します。 デプロイの状態は *[実行中]* と表示されます。 環境がステージング環境であるため、 **[Assign/Unassign domain]\(ドメインの割り当て/割り当て解除\)** ボタンは淡色表示になります。

1. **[概要]** ペインには**テスト エンドポイント**が表示されます。 新しいブラウザー ウィンドウにコピーして貼り付けると、新しい PiggyMetrics ページが表示されます。

>[!TIP]
> * テスト エンドポイントの末尾がスラッシュ (/) であり、CSS ファイルが正しく読み込まれていることを確認します。  
> * ページを表示するためにブラウザーでログイン資格情報の入力を求められる場合は、[URL デコード](https://www.urldecoder.org/)を使用して自分のテスト エンドポイントをデコードします。 URL デコードでは、"https://\<username>:\<password>@\<cluster-name>.test.azureapps.io/gateway/green" の形式の URL が返されます。  このフォームを使用してエンドポイントにアクセスします。

>[!NOTE]    
> 構成サーバーの設定はステージング環境と運用環境の両方に適用されます。 たとえば、構成サーバー内のアプリ ゲートウェイのコンテキスト パス (`server.servlet.context-path`) を *somepath* として設定すると、グリーン デプロイへのパスは "https://\<username>:\<password>@\<cluster-name>.test.azureapps.io/gateway/green/somepath/..." に変更されます。
 
 このポイントで自分の公開アプリ ゲートウェイにアクセスすると、新しい変更が反映されていない古いページが表示されます。
    
## <a name="set-the-green-deployment-as-the-production-environment"></a>実稼働環境としてグリーン デプロイを設定する

1. ステージング環境で変更を確認したら、それを運用環境にプッシュすることができます。 **[Deployment management]\(デプロイ管理\)** に戻って、**ゲートウェイ** アプリケーションのチェック ボックスをオンにします。

2. **[Set deployment]\(デプロイの設定\)** を選択します。
3. **[運用環境デプロイ]** の一覧で **[グリーン]** を選択し、 **[適用]** を選択します。
4. 自分のゲートウェイ アプリケーションの **[概要]** ページに移動します。 ゲートウェイ アプリケーションにドメインを既に割り当ててある場合、 **[概要]** ペインに URL が表示されます。 変更された PiggyMetrics ページを表示するには、URL を選択してサイトにアクセスします。

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
