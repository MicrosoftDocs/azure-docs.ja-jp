---
title: 既存のアプリを Azure Service Fabric クラスターに迅速にデプロイする
description: Visual Studio を使用して、Azure Service Fabric クラスターで既存の Node.js アプリケーションをホストします。
services: service-fabric
documentationcenter: nodejs
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: aljo
ms.openlocfilehash: 63630f5c4799e9272601f431671abc78ec86a238
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58669012"
---
# <a name="host-a-nodejs-application-on-azure-service-fabric"></a>Azure Service Fabric での Node.js アプリケーションのホスト

このクイックスタートを読むと、Azure で実行中の Service Fabric クラスターに既存のアプリケーション (この例では Node.js) をデプロイできるようになります。

## <a name="prerequisites"></a>前提条件

作業を開始する前に、 [開発環境がセットアップ](service-fabric-get-started.md)されていることを確認してください。 これには、Service Fabric SDK と Visual Studio 2017 または 2015 のインストールが必要です。

また、デプロイ用の Node.js アプリケーションも用意しておく必要があります。 このクイックスタートでは、[こちら][download-sample]でダウンロードできる単純な Node.js Web サイトを使用します。 次の手順でプロジェクトを作成した後、このファイルを `<path-to-project>\ApplicationPackageRoot\<package-name>\Code\` フォルダーに抽出します。

Azure サブスクリプションをお持ちでない場合は、[無料アカウント][create-account]を作成してください。

## <a name="create-the-service"></a>サービスの作成

Visual Studio を**管理者**として起動します。

`CTRL` + `SHIFT` + `N` キーを押して、プロジェクトを作成します。

**[新しいプロジェクト]** ダイアログで、**[クラウド]、[Service Fabric アプリケーション]** の順に選択します。

アプリケーションに **MyGuestApp** という名前を付けて、**[OK]** をクリックします。

>[!IMPORTANT]
>Node.js では、Windows のパスの上限である 260 文字を超過しやすいため、 プロジェクト自体に短いパスを使用してください (例: **c:\code\svc1**)。 Windows 10 では、**[こちらの手順](https://stackoverflow.com/a/41687101/1664231)** に従い、必要に応じて長いファイル パスを有効にすることができます。
   
![Visual Studio の [新しいプロジェクト] ダイアログ][new-project]

次のダイアログで、任意の種類の Service Fabric サービスを作成できます。 このクイックスタートでは、**[ゲストの実行可能ファイル]** を選択します。

サービスに **MyGuestService** という名前を付けて、右側のオプションを次の値に設定します。

| Setting                   | 値 |
| ------------------------- | ------ |
| コード パッケージ フォルダー       | _&lt;Node.js アプリが含まれているフォルダー&gt;_ |
| コード パッケージの動作     | フォルダーの内容をプロジェクトにコピーする |
| プログラム                   | node.exe |
| 引数                 | server.js |
| 作業フォルダー            | CodePackage |

**[OK]** をクリックします。

![Visual Studio の [新しいサービス] ダイアログ][new-service]

Visual Studio によって、アプリケーション プロジェクトとアクター サービス プロジェクトが作成され、ソリューション エクスプローラーに表示されます。

アプリケーション プロジェクト (**MyGuestApp**) には直接コードが含まれません。 代わりに、一連のサービス プロジェクトが参照されます。 さらに、アプリケーション プロジェクトには、他に次の 3 つの種類のコンテンツが含まれます。

* **発行プロファイル**  
さまざまな環境に合わせてツールの基本設定を管理するために使用します。

* **スクリプト**  
アプリケーションをデプロイ/アップグレードするための PowerShell スクリプトです。

* **アプリケーション定義**  
*ApplicationPackageRoot* にアプリケーション マニフェストが含まれます。 関連するアプリケーション パラメーター ファイルは *ApplicationParameters* に格納され、ここでアプリケーションが定義されます。特定の環境向けに細かくアプリケーションを構成することができます。
    
サービス プロジェクトのコンテンツの概要については、「 [Service Fabric の Reliable Services の概要](service-fabric-reliable-services-quick-start.md)」を参照してください。

## <a name="set-up-networking"></a>ネットワークの設定

デプロイするサンプル Node.js アプリではポート **80** を使用するため、そのポートが公開されている必要があることを Service Fabric に指定する必要があります。

プロジェクトの **ServiceManifest.xml** ファイルを開きます。 マニフェストの下部に、エントリが既に定義されている `<Resources> \ <Endpoints>` があります。 このエントリを変更して `Port`、`Protocol`、`Type` を追加します。 

```xml
  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="MyGuestAppServiceTypeEndpoint" Port="80" Protocol="http" Type="Input" />
    </Endpoints>
  </Resources>
```

## <a name="deploy-to-azure"></a>Deploy to Azure (Azure へのデプロイ)

**F5** キーを押してプロジェクトを実行すると、プロジェクトはローカル クラスターにデプロイされます。 ただし、ここでは代わりに Azure にデプロイします。

プロジェクトを右クリックして **[発行]** を選択すると、Azure に発行するためのダイアログが開きます。

![Service Fabric サービスの Azure への発行ダイアログ][publish]

**PublishProfiles\Cloud.xml** ターゲット プロファイルを選択します。

前もって選択していない場合は、デプロイ先の Azure アカウントを選択します。 まだアカウントを持っていない場合は、[サインアップ][create-account]してください。

**[接続のエンドポイント]** で、デプロイ先の Service Fabric クラスターを選択します。 クラスターがない場合は、**[&lt;新しいクラスターの作成&gt;]** を選択します。これで、Web ブラウザーのウィンドウで Azure Portal が開きます。 詳細については、[ポータルでのクラスターの作成](service-fabric-cluster-creation-via-portal.md#create-cluster-in-the-azure-portal)に関するページを参照してください。 

Service Fabric クラスターを作成する際に、必ず **[カスタム エンドポイント]** を **80** に設定するようにしてください。

![カスタム エンドポイントによる Service Fabric のノード タイプの構成][custom-endpoint]

新しい Service Fabric クラスターの作成の完了には、しばらく時間がかかります。 作成が完了したら、発行ダイアログに戻って **[&lt;更新&gt;]** を選択します。 新しいクラスターがドロップダウン ボックスに表示されるので、選択します。

**[発行]** をクリックして、デプロイが完了するまで待ちます。

これには数分かかることがあります。 完了した後、アプリケーションが完全に使用できるようになるまで、さらに数分かかる場合があります。

## <a name="test-the-website"></a>Web サイトのテスト

サービスを発行したら、Web ブラウザーでテストを行います。 

最初に、Azure Portal を開き、目的の Service Fabric サービスを探します。

サービス アドレスの概要ブレードを確認します。 _[Client connection endpoint]\(クライアントの接続エンドポイント\)_ プロパティのドメイン名を使用します。 たとえば、「 `http://mysvcfab1.westus2.cloudapp.azure.com` 」のように入力します。

![Azure Portal の Service Fabric 概要ブレード][overview]

このアドレスに移動すると、`HELLO WORLD` という応答が表示されます。

## <a name="delete-the-cluster"></a>クラスターを削除する

このクイックスタートで作成したリソースについて料金が発生するため、すべてのリソースを忘れずに削除してください。

## <a name="next-steps"></a>次の手順
[ゲスト実行可能ファイル](service-fabric-guest-executables-introduction.md)の詳細を確認します。

<!-- Image References -->

[new-project]: ./media/quickstart-guest-app/new-project.png
[new-service]: ./media/quickstart-guest-app/template.png
[solution-exp]: ./media/quickstart-guest-app/solution-explorer.png
[publish]: ./media/quickstart-guest-app/publish.png
[overview]: ./media/quickstart-guest-app/overview.png
[custom-endpoint]: ./media/quickstart-guest-app/custom-endpoint.png

[download-sample]: https://github.com/MicrosoftDocs/azure-cloud-services-files/raw/temp/service-fabric-node-website.zip
[create-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
