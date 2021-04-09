---
title: 既存のアプリをクラスターに迅速にデプロイする
description: Visual Studio を使用して、Azure Service Fabric クラスターで既存の Node.js アプリケーションをホストします。
ms.topic: conceptual
ms.date: 12/06/2017
ms.custom: devx-track-js
ms.openlocfilehash: dfd1fe9db54925bc17a53a7686fa34d5ea53cd5a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96013600"
---
# <a name="host-a-nodejs-application-on-azure-service-fabric"></a>Azure Service Fabric での Node.js アプリケーションのホスト

このクイックスタートを読むと、Azure で実行中の Service Fabric クラスターに既存のアプリケーション (この例では Node.js) をデプロイできるようになります。

## <a name="prerequisites"></a>前提条件

作業を開始する前に、 [開発環境がセットアップ](service-fabric-get-started.md)されていることを確認してください。 これには、Service Fabric SDK と Visual Studio 2019 または 2015 のインストールが含まれます。

また、デプロイ用の Node.js アプリケーションも用意しておく必要があります。 このクイックスタートでは、[こちら][download-sample]でダウンロードできる単純な Node.js Web サイトを使用します。 次の手順でプロジェクトを作成した後、このファイルを `<path-to-project>\ApplicationPackageRoot\<package-name>\Code\` フォルダーに抽出します。

Azure サブスクリプションをお持ちでない場合は、[無料アカウント][create-account]を作成してください。

## <a name="create-the-service"></a>サービスの作成

Visual Studio を **管理者** として起動します。

`CTRL`+`SHIFT`+`N` キーを押して、プロジェクトを作成します。

**[新しいプロジェクト]** ダイアログで、 **[クラウド]、[Service Fabric アプリケーション]** の順に選択します。

アプリケーションに **MyGuestApp** という名前を付けて、 **[作成]** をクリックします。

>[!IMPORTANT]
>Node.js では、Windows のパスの上限である 260 文字を超過しやすいため、 プロジェクト自体に短いパスを使用してください (例: **c:\code\svc1**)。 Windows 10 では、 **[こちらの手順](https://stackoverflow.com/a/41687101/1664231)** に従い、必要に応じて長いファイル パスを有効にすることができます。
   
![Visual Studio の [新しいプロジェクト] ダイアログ][new-project]

次のダイアログで、任意の種類の Service Fabric サービスを作成できます。 このクイックスタートでは、 **[ゲストの実行可能ファイル]** を選択します。

サービスに **MyGuestService** という名前を付けて、右側のオプションを次の値に設定します。

| 設定                   | 値 |
| ------------------------- | ------ |
| コード パッケージ フォルダー       | _&lt;Node.js アプリが含まれているフォルダー&gt;_ |
| コード パッケージの動作     | フォルダーの内容をプロジェクトにコピーする |
| プログラム                   | node.exe |
| 引数                 | server.js |
| 作業フォルダー            | CodePackage |

**[OK]** をクリックします。

![Visual Studio の [新しいサービス] ダイアログ][new-service]

Visual Studio によって、アプリケーション プロジェクトとアクター サービス プロジェクトが作成され、ソリューション エクスプローラーに表示されます。

アプリケーション プロジェクト (**MyGuestApp**) にはコードが直接には含まれていません。 このプロジェクトは、一連のサービス プロジェクトを参照します。 また、次の 3 つの他の種類のコンテンツも含まれています。

* **発行プロファイル**  
さまざまな環境に合わせてツールの基本設定を管理するために使用します。

* **スクリプト**  
アプリケーションをデプロイ/アップグレードするための PowerShell スクリプトです。

* **アプリケーション定義**  
*ApplicationPackageRoot* にアプリケーション マニフェストが含まれます。 関連するアプリケーション パラメーター ファイルは *ApplicationParameters* に格納され、ここでアプリケーションが定義されます。特定の環境向けに細かくアプリケーションを構成することができます。
    
サービス プロジェクトのコンテンツの概要については、「 [Service Fabric の Reliable Services の概要](service-fabric-reliable-services-quick-start.md)」を参照してください。

## <a name="set-up-networking"></a>ネットワークの設定

デプロイするサンプル Node.js アプリではポート **80** を使用するため、そのポートが公開されている必要があることを Service Fabric に指定する必要があります。

プロジェクトの **ServiceManifest.xml** ファイルを開きます。 マニフェストの下部に、エントリが既に定義された `<Resources> \ <Endpoints>` があります。 このエントリを変更して `Port`、`Protocol`、`Type` を追加します。 

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

**F5** を押してプロジェクトを実行すると、これはローカル クラスターにデプロイされます。 ただし、ここでは代わりに Azure にデプロイします。

プロジェクトを右クリックして **[発行]** を選択すると、Azure に発行するためのダイアログが開きます。

![Service Fabric サービスの Azure への発行ダイアログ][publish]

**PublishProfiles\Cloud.xml** ターゲット プロファイルを選択します。

前もって選択していない場合は、デプロイ先の Azure アカウントを選択します。 まだアカウントを持っていない場合は、[サインアップ][create-account]してください。

**[接続のエンドポイント]** で、デプロイ先の Service Fabric クラスターを選択します。 それがない場合は、 **&lt;[新しいクラスターの作成...]&gt;** を選択します。それにより、Azure Portal に Web ブラウザー ウィンドウが開かれます。 詳細については、[ポータルでのクラスターの作成](service-fabric-cluster-creation-via-portal.md#create-cluster-in-the-azure-portal)に関するページを参照してください。 

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

このクイック スタートで作成したリソースには課金されるため、それらのリソースをすべて忘れずに削除してください。

## <a name="next-steps"></a>次のステップ
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
