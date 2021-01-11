---
title: クイックスタート - Azure Spring Cloud サービスをプロビジョニングする
description: アプリのデプロイのための Azure Spring Cloud サービス インスタンスの作成について説明します。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/03/2020
ms.custom: devx-track-java
ms.openlocfilehash: c91237e3a14c60e477f58be0bf62f634b462960b
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "88951674"
---
# <a name="quickstart-provision-azure-spring-cloud-service"></a>クイック スタート:Azure Spring Cloud サービスのプロビジョニング

Azure Spring Cloud のインスタンス化は、Azure portal または Azure CLI を使用して行うことができます。  以下の手順では、両方の方法を説明します。
## <a name="prerequisites"></a>前提条件

* [JDK 8 をインストールする](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
* [Azure サブスクリプションにサインアップする](https://azure.microsoft.com/free/)
* (オプション) [Azure CLI バージョン 2.0.67 以降をインストール](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)し、`az extension add --name spring-cloud` コマンドを使用して Azure Spring Cloud 拡張機能をインストールする
* (オプション) [Azure Toolkit for IntelliJ をインストール](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/)し、[サインイン](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)する

## <a name="provision-an-instance-of-azure-spring-cloud"></a>Azure Spring Cloud のインスタンスをプロビジョニングする

#### <a name="portal"></a>[ポータル](#tab/Azure-portal)

次の手順では、Azure portal を使用して、Azure Spring Cloud のインスタンスを作成します。

1. 新しいタブで、[Azure portal](https://ms.portal.azure.com/) を開きます。 

2. 上部の検索ボックスから **Azure Spring Cloud** を探します。

3. その結果から **[Azure Spring Cloud]** を選択します。

    ![ASC アイコンでの開始](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

4. Azure Spring Cloud ページで **[+ 追加]** をクリックします。

    ![ASC アイコンでの追加](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

5. Azure Spring Cloud の **[作成]** ページで、フォームに入力します。  次のガイドラインを考慮してください。
    - **サブスクリプション**:このリソースに対する課金用のサブスクリプションを選択します。
    - **[リソース グループ]** :新しいリソース用に新しいリソース グループを作成することをお勧めします。 これが後の手順で **\<resource group name\>** として使用されることに注意してください。
    - **[サービスの詳細/名前]** : **\<service instance name\>** を指定します。  名前の長さは 4 文字から 32 文字で、小文字、数字、およびハイフンのみを使用できます。  サービス名の最初の文字は英字でなければならず、最後の文字は英字または数字でなければなりません。
    - **[場所]** :自分のサービス インスタンスの場所を選択します。

    ![ASC ポータルの起動](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. **[確認と作成]** をクリックします。

> [!div class="nextstepaction"]
> [問題が発生しました](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=public-endpoint)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

次の手順では、Azure CLI 拡張機能を使用して、Azure Spring Cloud のインスタンスをプロビジョニングします。

1. Azure CLI にログインし、アクティブなサブスクリプションを選択します。 Azure Spring Cloud のホワイトリストに登録されているアクティブなサブスクリプションを選択してください

    ```azurecli
    az login
    az account list -o table
    az account set --subscription <Name or ID of subscription, skip if you only have 1 subscription>
    ```

1. Azure Spring Cloud サービスの名前を準備します。  名前の長さは 4 文字から 32 文字で、小文字、数字、およびハイフンのみを使用できます。  サービス名の最初の文字は英字でなければならず、最後の文字は英字または数字でなければなりません。

1. Azure Spring Cloud サービスが含まれるリソース グループを作成します。

    ```azurecli
    az group create --location eastus --name <resource group name>
    ```

    Azure リソース グループの詳細については[こちら](../azure-resource-manager/management/overview.md)をご覧ください。

1. Azure CLI ウィンドウを開き、次のコマンドを実行して、Azure Spring Cloud のインスタンスをプロビジョニングします。

    ```azurecli
    az spring-cloud create -n <service instance name> -g <resource group name>
    ```

    サービス インスタンスのデプロイには約 5 分かかります。
---

## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [構成サーバーを設定する](spring-cloud-quickstart-setup-config-server.md)


