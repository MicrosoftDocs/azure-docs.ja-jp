---
title: Azure Spring Cloud でステージング環境を設定する | Microsoft Docs
description: Azure Spring Cloud でブルーグリーン デプロイを使用する方法について説明します
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: v-vasuke
ms.openlocfilehash: 454eeaa2568891ec35fe698cdb20c5448e10887e
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038301"
---
# <a name="how-to-set-up-a-staging-environment"></a>ステージング環境を設定する方法

この記事では、Azure Spring Cloud でブルーグリーン デプロイ パターンを使ってステージング環境のデプロイを利用する方法について説明します。 また、運用環境デプロイに直接変更を加えることなく、ステージング環境のデプロイを運用環境に移す方法についても説明します。

## <a name="prerequisites"></a>前提条件

この記事では、[アプリケーションの起動に関するチュートリアル](spring-cloud-quickstart-launch-app-portal.md)の PiggyMetrics アプリケーションを既にデプロイしてあることを前提としています。 PiggyMetrics は、"gateway"、"account-service"、"auth-service" という 3 つのアプリケーションで構成されます。  

この例に別のアプリケーションを使用したい場合は、そのアプリケーションの公開部分に簡単な変更を加える必要があります。  その変更により、ステージング環境のデプロイが運用環境と区別されます。

>[!NOTE]
> このクイックスタートを開始する前に、自分の Azure サブスクリプションで Azure Spring Cloud にアクセスできることを確認してください。  プレビュー サービスとして、Microsoft がお客様のサブスクリプションを許可リストに追加できるよう、ご連絡をお願いしています。  Azure Spring Cloud の機能をお試しになりたい場合は、メールで azure-spring-cloud@service.microsoft.com までお問い合わせください。

>[!TIP]
> Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。  最新バージョンの Git、JDK、Maven、Azure CLI など、一般的な Azure ツールがプレインストールされています。 Azure サブスクリプションにログインしている場合は、shell.azure.com から [Azure Cloud Shell](https://shell.azure.com) を起動します。  Azure Cloud Shell の詳細については、[ドキュメントを参照](../cloud-shell/overview.md)してください

この記事を完了するには:

1. [Git をインストールする](https://git-scm.com/)
1. [JDK 8 をインストールする](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
1. [Maven 3.0 以上をインストールする](https://maven.apache.org/download.cgi)
1. [Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
1. [Azure サブスクリプションにサインアップする](https://azure.microsoft.com/free/)

## <a name="install-the-azure-cli-extension"></a>Azure CLI 拡張機能をインストールする

次のコマンドを使用して、Azure CLI 用の Azure Spring Cloud 拡張機能をインストールします

```azurecli
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```
    
## <a name="view-all-deployments"></a>すべてのデプロイを表示する

Azure portal で自分のサービス インスタンスに移動し、 **[Deployment management]\(デプロイ管理\)** を選択してすべてのデプロイを表示します。 各デプロイを選択すると、詳細を表示できます。

## <a name="create-a-staging-deployment"></a>ステージング環境のデプロイの作成

1. ローカル開発環境で、PiggyMetrics のゲートウェイ アプリケーションに小さな変更を加えます。 たとえば、`gateway/src/main/resources/static/css/launch.css` で色を変更します。 これにより、2 つのデプロイを簡単に区別できるようになります。 次のコマンドを実行して、jar パッケージをビルドします。 

    ```azurecli
    mvn clean package
    ```

1. Azure CLI を使用して新しいデプロイを作成し、ステージング環境のデプロイの名前を "グリーン" にします。

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app gateway -n green --jar-path gateway/target/gateway.jar
    ```

1. デプロイが正常に完了したら、**アプリケーション ダッシュボード**からゲートウェイのページにアクセスし、左側の **[App Instances]\(アプリ インスタンス\)** タブで自分のインスタンスをすべて表示します。
  
> [!NOTE]
> 検出状態が "OUT_OF_SERVICE" になっているので、確認が完了するまで、このデプロイにはトラフィックがルーティングされません。

## <a name="verify-the-staging-deployment"></a>ステージング環境のデプロイを確認する

1. **[Deployment management]\(デプロイ管理\)** に戻って、新しいデプロイを選択します。 デプロイの状態は **[実行中]** と表示されます。 ステージング環境であるため、[Assign/Unassign domain]\(ドメインの割り当て/割り当て解除\) ボタンは無効になります。

1. **[概要]** ページには**テスト エンドポイント**が表示されます。 それを新しいブラウザーのページにコピーして貼り付けると、新しい PiggyMetrics ページが表示されます。

>[!TIP]
> * テスト エンドポイントの末尾が "/" であり、CSS が正常に読み込まれることを確認してください。  
> * ページを表示するためにブラウザーでログイン資格情報の入力を求められる場合は、[URL デコード](https://www.urldecoder.org/)を使用して自分のテスト エンドポイントをデコードします。 URL デコードは、"https://\<ユーザー名>:\<パスワード>@\<クラスター名>.test.azureapps.io/gateway/green" の形式で URL を返します。  これを使用してエンドポイントにアクセスします。

>[!NOTE]    
> 構成サーバーの設定はステージング環境のほか、運用環境にも適用されます。 たとえば、構成サーバーでアプリ ゲートウェイのコンテキスト パス (`server.servlet.context-path`) を *somepath* に設定すると、自分のグリーン デプロイへのパスは、"https://\<ユーザー名>:\<パスワード>@\<クラスター名>.test.azureapps.io/gateway/green/somepath/..." になります。
 
 このポイントで自分の公開アプリ ゲートウェイにアクセスすると、新しい変更が反映されていない古いページが表示されます。
    
## <a name="set-the-green-as-production-deployment"></a>グリーンを運用環境デプロイとして設定する

1. ステージング環境で変更を確認したら、それを運用環境にプッシュすることができます。 **[Deployment management]\(デプロイ管理\)** に戻って、"gateway" アプリケーションの前のチェック ボックスをオンにします。
2. [Set deployment]\(デプロイの設定\) を選択します。
3. [運用環境デプロイ] メニューで "グリーン" を選択し、 **[適用]** を選択します
4. 自分のゲートウェイ アプリケーションの **[概要]** ページに移動します。 ゲートウェイ アプリケーションにドメインを既に割り当ててある場合、 **[概要]** ページに URL が表示されます。 URL にアクセスすると、変更された PiggyMetrics ページが表示されます。

>[!NOTE]
> グリーン デプロイを運用環境に設定したら、前のデプロイがステージング環境のデプロイになります。

## <a name="modify-the-staging-deployment"></a>ステージング環境のデプロイを変更する

変更に満足していない場合は、Azure CLI を使用することで、アプリケーション コードを変更し、新しい jar パッケージをビルドして、それをグリーン デプロイにアップロードできます。

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-a-staging-deployment"></a>ステージング環境のデプロイを削除する

ステージング環境のデプロイのページに移動して **[削除]** ボタンを選択し、Azure ポートから自分のステージング環境のデプロイを削除します。

または、次のコマンドを使用して Azure CLI でステージング環境のデプロイを削除します。

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```
