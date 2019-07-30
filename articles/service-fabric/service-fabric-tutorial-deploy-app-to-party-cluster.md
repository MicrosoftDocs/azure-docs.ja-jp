---
title: Azure で Service Fabric アプリをクラスターにデプロイする | Microsoft Docs
description: Visual Studio でアプリケーションをクラスターにデプロイする方法を説明します。
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/22/2019
ms.author: mikhegn
ms.custom: mvc
ms.openlocfilehash: d637b01eb9e4d1664def9eb537b810603eea18ea
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2019
ms.locfileid: "68598812"
---
# <a name="tutorial-deploy-a-service-fabric-application-to-a-cluster-in-azure"></a>チュートリアル:Azure で Service Fabric アプリケーションをクラスターにデプロイする

このチュートリアルは、シリーズの第 2 部です。 Azure の新しいクラスターに Azure Service Fabric アプリケーションをデプロイする方法を示します。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> * クラスターを作成する。
> * Visual Studio を使用してリモート クラスターにアプリケーションをデプロイする。

このチュートリアル シリーズで学習する内容は次のとおりです。
> [!div class="checklist"]
> * [.NET Service Fabric アプリケーションを構築する](service-fabric-tutorial-create-dotnet-app.md)。
> * アプリケーションをリモート クラスターにデプロイする。
> * [ASP.NET Core フロントエンド サービスに HTTPS エンドポイントを追加する](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)。
> * [Azure Pipelines を使用して CI/CD を構成する](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)。
> * [アプリケーションの監視と診断を設定する](service-fabric-tutorial-monitoring-aspnet.md)。

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に

* Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。
* [Visual Studio 2019 をインストール](https://www.visualstudio.com/)し、**Azure 開発**ワークロードと **ASP.NET および Web 開発**ワークロードをインストールします。
* [Service Fabric SDK をインストール](service-fabric-get-started.md)します。

## <a name="download-the-voting-sample-application"></a>投票サンプル アプリケーションをダウンロードする

[このチュートリアル シリーズの第 1 部](service-fabric-tutorial-create-dotnet-app.md)で投票サンプル アプリケーションをビルドしていない場合は、ダウンロードすることができます。 コマンド ウィンドウで次のコードを実行して、サンプル アプリケーションのリポジトリをお客様のローカル コンピューターに複製します。

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart 
```

管理者として実行している Visual Studio でアプリケーションを開き、アプリケーションをビルドします。

## <a name="create-a-cluster"></a>クラスターの作成

アプリケーションの準備ができたので、Service Fabric クラスターを作成して、アプリケーションをクラスターにデプロイします。 [Service Fabric クラスター](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-anywhere)は、ネットワークで接続された一連の仮想マシンまたは物理マシンで、マイクロサービスがデプロイおよび管理されます。

このチュートリアルでは、Visual Studio IDE で新しい 3 ノードのテスト クラスターを作成してから、そのクラスターにアプリケーションを発行します。 運用クラスターの作成については、[クラスターの作成と管理に関するチュートリアル](service-fabric-tutorial-create-vnet-and-windows-cluster.md)を参照してください。 [PowerShell](./scripts/service-fabric-powershell-create-secure-cluster-cert.md) スクリプトまたは [Azure CLI](./scripts/cli-create-cluster.md) スクリプトを使用するか、あるいは [Azure Resource Manager テンプレート](service-fabric-tutorial-create-vnet-and-windows-cluster.md)をベースにして、以前に [Azure portal](https://portal.azure.com) を使用して作成した既存のクラスターにアプリケーションをデプロイすることもできます。

> [!NOTE]
> 投票アプリケーション、およびその他の多くのアプリケーションでは、サービス間の通信に Service Fabric リバース プロキシが使用されます。 Visual Studio で作成されたクラスターでは、既定でリバース プロキシが有効になっています。 既存のクラスターにデプロイする場合、投票アプリケーションが動作するためには、[クラスターでリバース プロキシを有効にする](service-fabric-reverseproxy-setup.md)必要があります。


### <a name="find-the-votingweb-service-endpoint"></a>VotingWeb サービス エンドポイントを見つける

投票アプリケーションのフロントエンド Web サービスは、特定のポートでリッスンしています ([このチュートリアル シリーズの第 1 部](service-fabric-tutorial-create-dotnet-app.md)の手順に従っている場合は 8080)。 Azure でアプリケーションがクラスターにデプロイされると、クラスターとアプリケーションの両方が Azure ロード バランサーの背後で実行します。 Azure ロード バランサーで、規則を使用してアプリケーション ポートが開かれている必要があります。 規則に従って、インバウンド トラフィックがロード バランサー経由で Web サービスに送信されます。 ポートは、**VotingWeb/PackageRoot/ServiceManifest.xml** ファイルの **Endpoint** 要素にあります。 

```xml
<Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
```

サービス エンドポイントをメモしておきます。これは後の手順で必要になります。  既存のクラスターにデプロイする場合は、[PowerShell スクリプト](./scripts/service-fabric-powershell-open-port-in-load-balancer.md)を使用して Azure ロード バランサーに負荷分散規則とプローブを作成するか、または [Azure portal](https://portal.azure.com) でこのクラスターのロード バランサーを介して、このポートを開きます。

### <a name="create-a-test-cluster-in-azure"></a>Azure でテスト クラスターを作成する
ソリューション エクスプローラーで、 **[Voting]** を右クリックし、 **[発行]** を選択します。

**[接続のエンドポイント]** で、 **[新しいクラスターの作成]** を選択します。  既存のクラスターにデプロイする場合は、一覧でクラスターのエンドポイントを選択します。  [Service Fabric クラスターの作成] ダイアログ ボックスが開きます。

**[クラスター]** タブで、**クラスター名** (たとえば、"mytestcluster") を入力します。お客様のサブスクリプションを選択し、クラスターのリージョン (たとえば、"米国中南部") を選択します。クラスター ノードの数 (テスト クラスターには 3 つのノードをお勧めします) を入力し、リソース グループ (たとえば、"mytestclustergroup") を入力します。 **[次へ]** をクリックします。

![クラスターの作成](./media/service-fabric-tutorial-deploy-app-to-party-cluster/create-cluster.png)

**[証明書]** タブで、クラスター証明書のパスワードと出力パスを入力します。 自己署名証明書は PFX ファイルとして作成され、指定した出力パスに保存されます。  証明書は、ノード間のセキュリティと、クライアントとノード間のセキュリティの両方に使用されます。  運用クラスターでは、自己署名証明書を使用しないでください。  この証明書は、クラスターによる認証とアプリケーションのデプロイのために Visual Studio によって使用されます。 お客様のコンピューターの CurrentUser\My certificate store に PFX をインストールするために、 **[証明書のインポート]** を選択します。  **[次へ]** をクリックします。

![クラスターの作成](./media/service-fabric-tutorial-deploy-app-to-party-cluster/certificate.png)

**[VM の詳細]** タブで、クラスター管理者アカウントの**ユーザー名**と**パスワード**を入力します。  クラスター ノードの**仮想マシン イメージ**を選択し、各クラスター ノードの**仮想マシン サイズ**を選択します。  **[詳細]** タブをクリックします。

![クラスターの作成](./media/service-fabric-tutorial-deploy-app-to-party-cluster/vm-detail.png)

**[ポート]** に、前の手順の VotingWeb サービス エンドポイントを入力します (たとえば、8080)。  クラスターが作成されると、トラフィックをクラスターに転送するためにこれらのアプリケーション ポートが Azure ロード バランサーで開かれます。  **[作成]** をクリックしてクラスターを作成します。これには数分かかります。

![クラスターの作成](./media/service-fabric-tutorial-deploy-app-to-party-cluster/advanced.png)

## <a name="publish-the-application-to-the-cluster"></a>アプリケーションをクラスターに発行する

新しいクラスターの準備ができたら、Visual Studio から投票アプリケーションを直接デプロイできます。

ソリューション エクスプローラーで、 **[Voting]** を右クリックし、 **[発行]** を選択します。 **[発行]** ダイアログ ボックスが表示されます。

**[接続のエンドポイント]** で、お客様が前の手順で作成したクラスターのエンドポイントを選択します  (たとえば、"mytestcluster.southcentral.cloudapp.azure.com:19000")。 **[詳細な接続パラメーター]** を選択した場合、証明書情報は自動的に入力されます。  
![Service Fabric アプリケーションを発行する](./media/service-fabric-tutorial-deploy-app-to-party-cluster/publish-app.png)

**[発行]** を選択します。

アプリケーションがデプロイされたら、ブラウザーを開き、クラスター アドレスとそれに続けて「 **:8080**」を入力します。 他のポートが構成されている場合は、そのポートを入力します。 例: `http://mytestcluster.southcentral.cloudapp.azure.com:8080`。 Azure のクラスターでアプリケーションが実行されていることがわかります。 投票 Web ページで、投票オプションの追加や削除を試します。さらに、これらのオプションの 1 つ以上に投票してみます。

![Service Fabric の投票サンプル](./media/service-fabric-tutorial-deploy-app-to-party-cluster/application-screenshot-new-azure.png)


## <a name="next-steps"></a>次の手順
チュートリアルのこの部分で学習した内容は次のとおりです。

> [!div class="checklist"]
> * クラスターを作成する。
> * Visual Studio を使用してリモート クラスターにアプリケーションをデプロイする。

次のチュートリアルに進みます。
> [!div class="nextstepaction"]
> [HTTPS を有効にする](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
