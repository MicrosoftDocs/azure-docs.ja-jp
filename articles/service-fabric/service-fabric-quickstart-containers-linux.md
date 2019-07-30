---
title: Azure で Service Fabric 上に Linux コンテナー アプリを作成する | Microsoft Docs
description: このクイック スタートでは、アプリケーションの Docker イメージをビルドし、そのイメージをコンテナー レジストリにプッシュした後、Service Fabric クラスターにコンテナーをデプロイします。
services: service-fabric
documentationcenter: linux
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: python
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/22/2019
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: 979c94e0c577c2a7dbcb11434ac5544e4dd1df64
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385149"
---
# <a name="quickstart-deploy-linux-containers-to-service-fabric"></a>クイック スタート:Service Fabric への Linux コンテナーのデプロイ

Azure Service Fabric は、スケーラブルで信頼性に優れたマイクロサービスとコンテナーのデプロイと管理を行うための分散システム プラットフォームです。

このクイック スタートでは、Azure の Service Fabric クラスターに Linux コンテナーをデプロイする方法を説明します。 完了すると、Service Fabric クラスターで実行される Python Web フロントエンドと Redis バックエンドで構成される投票アプリケーションが作成されます。 さらに、アプリケーションのフェールオーバー方法と、クラスター内のアプリケーションのスケーリング方法も説明します。

![投票アプリの Web ページ][quickstartpic]

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、以下が必要です。

1. サブスクリプションを所有していない場合は、始める前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成します。

2. [Azure CLI](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) をインストールします。

3. [Service Fabric SDK と CLI](service-fabric-get-started-linux.md#installation-methods) をインストールします。

4. [Git](https://git-scm.com/)


## <a name="get-the-application-package"></a>アプリケーション パッケージの取得

コンテナーを Service Fabric にデプロイするには、個別のコンテナーとアプリケーションを説明する一連のマニフェスト ファイル (アプリケーション定義) が必要です。

コンソールで git を使用して、アプリケーション定義のコピーを複製します。次に、ディレクトリを複製の `Voting` ディレクトリに変更します。

```bash
git clone https://github.com/Azure-Samples/service-fabric-containers.git

cd service-fabric-containers/Linux/container-tutorial/Voting
```

## <a name="create-a-service-fabric-cluster"></a>Service Fabric クラスターの作成

Azure にアプリケーションをデプロイするには、アプリケーションを実行する Service Fabric クラスターが必要です。 次のコマンドは、5 ノードのクラスターを Azure に作成します。  また、自己署名証明書を作成してキー コンテナーに追加し、証明書をローカルにダウンロードします。 新しい証明書は、デプロイ時にクラスターをセキュリティで保護す目的で使用されるほか、クライアントの認証にも使用されます。

```azurecli
#!/bin/bash

# Variables
ResourceGroupName="containertestcluster" 
ClusterName="containertestcluster" 
Location="eastus" 
Password="q6D7nN%6ck@6" 
Subject="containertestcluster.eastus.cloudapp.azure.com" 
VaultName="containertestvault" 
VmPassword="Mypa$$word!321"
VmUserName="sfadminuser"

# Login to Azure and set the subscription
az login

az account set --subscription <mySubscriptionID>

# Create resource group
az group create --name $ResourceGroupName --location $Location 

# Create secure five node Linux cluster. Creates a key vault in a resource group
# and creates a certficate in the key vault. The certificate's subject name must match 
# the domain that you use to access the Service Fabric cluster.  The certificate is downloaded locally.
az sf cluster create --resource-group $ResourceGroupName --location $Location --certificate-output-folder . --certificate-password $Password --certificate-subject-name $Subject --cluster-name $ClusterName --cluster-size 5 --os UbuntuServer1604 --vault-name $VaultName --vault-resource-group $ResourceGroupName --vm-password $VmPassword --vm-user-name $VmUserName
```

> [!Note]
> Web フロントエンド サービスは、ポート 80 で受信トラフィックをリッスンする構成になっています。 クラスターの VM および Azure Load Balancer では、ポート 80 が既定で開放されています。
>

## <a name="configure-your-environment"></a>環境の構成

Service Fabric には、クラスターとそのアプリケーションを管理するために使用できるツールがいくつか用意されています。

- ブラウザーベースのツールである Service Fabric Explorer。
- Azure CLI 上で実行される Service Fabric コマンド ライン インターフェイス (CLI)。 
- PowerShell コマンド。

このクイック スタートでは、Service Fabric CLI と Service Fabric Explorer (Web ベースのツール) を使用します。 Service Fabric Explorer を使用するには、証明書の PFX ファイルをブラウザーにインポートする必要があります。 既定では、PFX ファイルにパスワードはありません。

Mozilla Firefox は、Ubuntu 16.04 の既定のブラウザーです。 証明書を Firefox にインポートするには、ブラウザーの右上隅にあるメニュー ボタンをクリックし、 **[オプション]** をクリックします。 **環境設定**ページで、検索ボックスを使用して "証明書" を検索します。 **[証明書を表示]** をクリックし、 **[あなたの証明書]** タブを選択します。次に、 **[インポート]** をクリックし、プロンプトに従って証明書をインポートします。

   ![Firefox での証明書のインストール](./media/service-fabric-quickstart-containers-linux/install-cert-firefox.png)

## <a name="deploy-the-service-fabric-application"></a>Service Fabric アプリケーションをデプロイする

1. CLI を使用して Azure の Service Fabric クラスターに接続します。 エンドポイントは、クラスターの管理エンドポイントです。 前のセクションで PEM ファイルを作成しました 

    ```bash
    sfctl cluster select --endpoint https://containertestcluster.eastus.cloudapp.azure.com:19080 --pem containertestcluster22019013100.pem --no-verify
    ```

2. インストール スクリプトを使用して投票アプリケーション定義をクラスターにコピーし、アプリケーションの種類を登録して、アプリケーションのインスタンスを作成します。  PEM 証明書ファイルは、*install.sh* ファイルと同じディレクトリに置く必要があります。

    ```bash
    ./install.sh
    ```

3. Web ブラウザーを開き、クラスターの Service Fabric Explorer エンドポイントに移動します。 エンドポイントの形式は、**https://\<my-azure-service-fabric-cluster-url>:19080/Explorer** です (例: `https://containertestcluster.eastus.cloudapp.azure.com:19080/Explorer`)。 </br>

4. **Applications** ノードを展開し、投票アプリケーションの種類と作成したインスタンスのエントリがあることを確認します。

    ![Service Fabric Explorer][sfx]

5. 実行中のコンテナーに接続するには、Web ブラウザーを開いてクラスターの URL に移動します (例: `http://containertestcluster.eastus.cloudapp.azure.com:80`)。 ブラウザーに Voting アプリケーションが表示されます。

    ![投票アプリの Web ページ][quickstartpic]

> [!NOTE]
> Service Fabric アプリケーションは、Docker Compose でデプロイすることもできます。 たとえば、次のコマンドを使用すれば、Docker Compose を使ってクラスターにアプリケーションをデプロイしてインストールできます。
>  ```bash
> sfctl compose create --deployment-name TestApp --file-path ../docker-compose.yml
> ```

## <a name="fail-over-a-container-in-a-cluster"></a>クラスター内のコンテナーをフェールオーバーする

障害が発生すると、Service Fabric は自動的にコンテナー インスタンスをクラスター内の他のノードに移動します。 また、コンテナーのノードを手動でドレインして、クラスター内の他のノードに適切に移動することもできます。 Service Fabric では、いくつかの方法でサービスをスケーリングできます。 次の手順では、Service Fabric Explorer を使用します。

フロントエンド コンテナーのフェールオーバーは、次の手順で行います。

1. クラスターで Service Fabric Explorer を開きます (例: `https://containertestcluster.eastus.cloudapp.azure.com:19080/Explorer`)。
2. ツリー ビューの **fabric:/Voting/azurevotefront** ノードをクリックし、パーティション ノード (GUID で表されます) を展開します。 ツリー ビューのノード名に注意してください。この名前は、コンテナーが現在実行しているノードを示します (例: `_nodetype_1`)。
3. ツリー ビューの **Nodes** ノードを展開します。 コンテナーを実行しているノードの横にある省略記号 (...) をクリックします。
4. **[再起動]** を選んでそのノードを再起動し、再起動操作を確認します。 再起動により、コンテナーはクラスター内の別のノードにフェールオーバーします。

    ![Service Fabric Explorer のノード ビュー][sfxquickstartshownodetype]

## <a name="scale-applications-and-services-in-a-cluster"></a>クラスター内のアプリケーションとサービスをスケールする

Service Fabric サービスは、その負荷に対応するように、クラスターで簡単にスケールすることができます。 サービスをスケールするには、クラスターで実行されるインスタンスの数を変更します。

Web フロントエンド サービスをスケールするには、次の手順に従います。

1. クラスターで Service Fabric Explorer を開きます (例: `https://containertestcluster.eastus.cloudapp.azure.com:19080`)。
2. ツリー ビューで **fabric:/Voting/azurevotefront** ノードの横にある省略記号 (3 つの点) をクリックし、 **[Scale Service]\(サービスのスケーリング\)** を選択します。

    ![Service Fabric Explorer スケーリング サービスの開始][containersquickstartscale]

    これで Web フロントエンド サービスのインスタンス数をスケールできる状態になりました。

3. この数値を **2** に変更し、 **[Scale Service]\(サービスのスケール\)** をクリックします。
4. ツリー ビューの **fabric:/Voting/azurevotefront** ノードをクリックし、パーティション ノード (GUID で表されます) を展開します。

    ![Service Fabric Explorer スケーリング サービスの完了][containersquickstartscaledone]

    サービスに 2 つのインスタンスがあることがわかります。 ツリー ビューでは、インスタンスが実行されるノードを確認できます。

この簡単な管理タスクを通じて、フロントエンド サービスでユーザー負荷を処理するためのリソースが 2 倍になりました。 実行するサービスの信頼性を高めるために、サービスのインスタンスを複数用意する必要はないことに注目してください。 サービスで障害が発生した場合、Service Fabric によって新しいサービス インスタンスがクラスターで実行されます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

クラスターからアプリケーション インスタンスを削除し、アプリケーションの種類の登録を解除するには、テンプレートに指定されているアンインストール スクリプト (uninstall.sh) を使用します。 このスクリプトによってインスタンスがクリーンアップされるまで、しばらく時間がかかります。そのため、このスクリプトの直後にインストール スクリプトを実行しないようにしてください。 Service Fabric Explorer を使用して、インスタンスが削除され、アプリケーションの種類が登録解除された時間を確認できます。

```bash
./uninstall.sh
```

クラスターと、そのクラスターによって使用されるすべてのリソースを削除するための最も簡単な方法は、リソース グループを削除することです。

Azure にサインインして、クラスターを削除するサブスクリプション ID を選択します。 サブスクリプション ID は、Azure portal にログインして確認できます。 リソース グループとクラスター リソースすべてを削除するには、[az group delete コマンド](/cli/azure/group?view=azure-cli-latest)を使用します。

```azurecli
az login
az account set --subscription <guid>
ResourceGroupName="containertestcluster"
az group delete --name $ResourceGroupName
```

クラスターの操作が完了したら、証明書ストアから証明書を削除できます。 例:
- Windows の場合:[証明書 MMC スナップイン](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in)を使用します。 スナップインの追加時に **[ユーザー アカウント]** を選択してください。 `Certificates - Current User\Personal\Certificates` に移動して、証明書を削除します。
- Mac の場合:キーチェーン アプリを使用します。
- Ubuntu の場合:証明書の表示に使用される手順に従ってから、証明書を削除します。

## <a name="next-steps"></a>次の手順

このクイック スタートでは、Linux コンテナー アプリケーションを Azure の Service Fabric クラスターにデプロイし、アプリケーションのフェールオーバーを実行して、クラスター内のアプリケーションをスケーリングしました。 Service Fabric で Linux コンテナーを操作する方法の詳細については、Linux コンテナー アプリのチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Linux コンテナー アプリの作成](./service-fabric-tutorial-create-container-images.md)

[sfx]: ./media/service-fabric-quickstart-containers-linux/containersquickstartappinstance.png
[quickstartpic]: ./media/service-fabric-quickstart-containers-linux/votingapp.png
[sfxquickstartshownodetype]:  ./media/service-fabric-quickstart-containers-linux/containersquickstartrestart.png
[containersquickstartscale]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscale.png
[containersquickstartscaledone]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscaledone.png
