---
title: Node.js ファースト ステップ ガイド
description: 簡単な Node.js Web アプリケーションを作成して、Azure のクラウド サービスにデプロイする方法について説明します。
services: cloud-services
documentationcenter: nodejs
author: georgewallace
ms.service: cloud-services
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 08/17/2017
ms.author: gwallace
ms.openlocfilehash: 81ad3bce6c7e331fd67fba60b35de6b8a37c5da9
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359535"
---
# <a name="build-and-deploy-a-nodejs-application-to-an-azure-cloud-service"></a>Node.js アプリケーションの構築と Azure クラウド サービスへのデプロイ

このガイドでは、Azure のクラウド サービスで実行される簡単な Node.js アプリケーションを作成します。 Cloud Services は、Azure のスケーラブルなクラウド アプリケーションの構成要素です。 クラウド サービスによって、アプリケーションのフロントエンド コンポーネントとバックエンド コンポーネントの分離および独立した管理とスケールアウトを実現できます。  Cloud Services は、各ロールを信頼性の高い方法でホストするための堅牢な専用仮想マシンを提供します。

Cloud Services の詳細と Azure の Web サイトおよび仮想マシンとの違いについては、 [Azure Websites、Cloud Services、および Virtual Machines の比較]に関するページを参照してください。

> [!TIP]
> 単純な Web サイトを構築する場合 シナリオが、単純な Web サイトのフロントエンドにのみ関係している場合は、[軽量の Web アプリを使用]することを検討してください。 Web アプリの規模が増大し、要件が変化したときには、容易にクラウド サービスにアップグレードできます。

このチュートリアルでは、Web ロールでホストされる単純な Web アプリケーションを作成します。 コンピューティング エミュレーターを使用してアプリケーションをローケルでテストし、PowerShell コマンド ライン ツールを使用してアプリケーションをデプロイします。

このアプリケーションは簡単な "hello world" アプリケーションです。

![Hello World Web ページを表示している Web ブラウザー][A web browser displaying the Hello World web page]

## <a name="prerequisites"></a>前提条件
> [!NOTE]
> このチュートリアルは、Windows 上の Azure PowerShell を使用します。

* [Azure Powershell]のインストールおよび構成。
* [Azure SDK for .NET 2.7]のダウンロードとインストール。 インストールのセットアップ時に、次のいずれかを選択します。
  * MicrosoftAzureAuthoringTools
  * MicrosoftAzureComputeEmulator

## <a name="create-an-azure-cloud-service-project"></a>Azure クラウド サービス プロジェクトの作成
新しい Azure クラウド サービス プロジェクトおよび基本的な Node.js スキャフォールディングを作成するには、次のタスクを実行します。

1. **Windows PowerShell** を管理者として実行します。 **[スタート] メニュー**または**スタート画面**で、「**Windows PowerShell**」を検索します。
2. [PowerShell を接続します] 。
3. 次の PowerShell コマンドレットを入力してプロジェクトを作成します。

        New-AzureServiceProject helloworld

    ![New-AzureService helloworld コマンドの結果][The result of the New-AzureService helloworld command]

    **New-AzureServiceProject** コマンドレットは、クラウド サービスに Node.js アプリケーションを発行するための基本的な構造を生成します。 これには、Azure への発行に必要な構成ファイルが含まれています。 また、このコマンドレットにより、作業ディレクトリがこのサービス用のディレクトリに変更されます。

    このコマンドレットによって作成されるファイルは、次のとおりです。

   * **ServiceConfiguration.Cloud.cscfg**、**ServiceConfiguration.Local.cscfg**、および **ServiceDefinition.csdef**:アプリケーションの発行に必要な、Azure 固有のファイルです。 詳細については、「 [Overview of Creating a Hosted Service for Azure (Azure 対応のホステッド サービスの作成の概要)]」を参照してください。
   * **deploymentSettings.json**:Azure PowerShell デプロイ コマンドレットによって使用されるローカル設定が格納されます。
4. 次のコマンドを入力して、新しい Web ロールを追加します。

       Add-AzureNodeWebRole

   ![The output of the Add-AzureNodeWebRole command][The output of the Add-AzureNodeWebRole command]

   **Add-azurenodewebrole** コマンドレットは、基本的な Node.js アプリケーションを作成します。 **.csfg** ファイルおよび **.csdef** ファイルも変更され、新しいロールの構成エントリが追加されます。

   > [!NOTE]
   > ロール名を指定しない場合は、既定の名前が使用されます。 名前は、 `Add-AzureNodeWebRole MyRole`

Node.js アプリは **server.js** ファイルで定義されます。このファイルは Web ロール用のディレクトリ (既定では **WebRole1**) に配置されます。 次にコードを示します。

    var http = require('http');
    var port = process.env.port || 1337;
    http.createServer(function (req, res) {
        res.writeHead(200, { 'Content-Type': 'text/plain' });
        res.end('Hello World\n');
    }).listen(port);

このコードは、クラウド環境に割り当てられたポート番号を使用する点を除いて、基本的に [nodejs.org] Web サイトの "Hello World" サンプルと同じです。

## <a name="deploy-the-application-to-azure"></a>Azure にアプリケーションを展開する

> [!NOTE]
> このチュートリアルを完了するには、Azure アカウントが必要です。 [MSDN サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF)か、[無料アカウントにサインアップ](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A85619ABF)してください。

### <a name="download-the-azure-publishing-settings"></a>Azure 発行設定のダウンロード
アプリケーションを Azure に展開するには、まず Azure サブスクリプションの発行設定をダウンロードする必要があります。

1. 次の Azure PowerShell コマンドレットを実行します。

       Get-AzurePublishSettingsFile

   ブラウザーで、発行設定のダウンロード ページが表示されます。 Microsoft アカウントによるログインを求められる場合があります。 その場合は、Azure サブスクリプションに関連付けられたアカウントを使用します。

   ダウンロードしたプロファイルを、簡単にアクセスできるファイルの保存場所に保存します。
2. 次のコマンドレットを実行して、ダウンロードした発行プロファイルをインポートします。

       Import-AzurePublishSettingsFile [path to file]

    > [!NOTE]
    > 発行設定をインポートしたら、ダウンロードした .publishSettings ファイルには他のユーザーがアカウントにアクセスできる情報が含まれているので、削除することを検討してください。

### <a name="publish-the-application"></a>アプリケーションの発行
発行するには、次のコマンドを実行します。

      $ServiceName = "NodeHelloWorld" + $(Get-Date -Format ('ddhhmm'))
    Publish-AzureServiceProject -ServiceName $ServiceName  -Location "East US" -Launch

* **- ServiceName** は、このデプロイに使用する名前を指定します。 必ず一意の名前を使用してください。一意でない場合は発行が失敗します。 **Get-Date** コマンドは、名前を一意にする日付/時刻文字列を追加します。
* **-Location** は、アプリケーションがホストされるデータ センターを指定します。 使用可能なデータ センターの一覧を表示するには、 **Get-azurelocation** コマンドレットを使用してください。
* **-Launch** は、デプロイの完了後に、ブラウザー ウィンドウの起動とホストされるサービスへの移動を行います。

発行が成功すると、次のような応答が表示されます。

![Publish-AzureService コマンドの出力][The output of the Publish-AzureService command]

> [!NOTE]
> 初めてアプリケーションを発行する場合、アプリケーションがデプロイされ、利用可能になるまでに数分かかることがあります。

デプロイが完了すると、ブラウザー ウィンドウが開き、クラウド サービスに移動します。

![A browser window displaying the hello world page; the URL indicates the page is hosted on Azure.][A browser window displaying the hello world page; the URL indicates the page is hosted on Azure.]

これで、アプリケーションが Azure 上で実行されました。

**Publish-AzureServiceProject** コマンドレットによって行われる処理は次のとおりです。

1. デプロイするためのパッケージを作成します。 このパッケージには、アプリケーション フォルダー内のすべてのファイルが含まれます。
2. **ストレージ アカウント** が存在しない場合は、新たに作成します。 Azure ストレージ アカウントは、デプロイ中にアプリケーション パッケージを格納するために使用されます。 デプロイが完了したら、ストレージ アカウントを削除してかまいません。
3. **クラウド サービス** が存在しない場合は、新たに作成します。 **クラウド サービス** は、Azure に展開したアプリケーションがホストされるコンテナーです。 詳細については、「 [Overview of Creating a Hosted Service for Azure (Azure 対応のホステッド サービスの作成の概要)]」を参照してください。
4. デプロイ パッケージを Azure に発行します。

## <a name="stopping-and-deleting-your-application"></a>アプリケーションの停止と削除
アプリケーションをデプロイした後、余分なコストを回避するためにアプリケーションを無効にすることができます。 Azure では、消費されたサーバー時間の 1 時間単位の料金が Web ロール インスタンスに課金されます。 インスタンスが実行されていない場合や停止状態の場合でも、アプリケーションを展開した直後からサーバー時間が消費されます。

1. Windows PowerShell ウィンドウで次のコマンドレットを実行し、前のセクションで作成したサービスのデプロイを停止します。

       Stop-AzureService

   サービスの停止には、数分間かかる場合があります。 サービスが停止すると、停止したことを知らせるメッセージが表示されます。

   ![Stop-AzureService コマンドの状態][The status of the Stop-AzureService command]
2. サービスを削除するには、次のコマンドレットを呼び出します。

       Remove-AzureService

   確認を求めるメッセージが表示されたら、「 **Y** 」と入力して、サービスを削除します。

   サービスの削除には、数分間かかる場合があります。 サービスが削除されると、削除されたことを知らせるメッセージが表示されます。

   ![Remove-AzureService コマンドの状態][The status of the Remove-AzureService command]

   > [!NOTE]
   > サービスを削除しても、サービスが最初に発行されたときに作成されたストレージ アカウントは削除されず、使用したストレージに対して引き続き課金されます。 他の用途に使用しているのでなければ、ストレージを削除してください。

## <a name="next-steps"></a>次の手順
詳細については、 [Node.js デベロッパー センター]を参照してください。

<!-- URL List -->

[Azure Websites、Cloud Services、および Virtual Machines の比較]: /azure/architecture/guide/technology-choices/compute-decision-tree
[軽量の Web アプリを使用]: ../app-service/app-service-web-get-started-nodejs.md
[Azure Powershell]: /powershell/azureps-cmdlets-docs
[Azure SDK for .NET 2.7]: https://www.microsoft.com/en-us/download/details.aspx?id=48178
[PowerShell を接続します]: /powershell/azureps-cmdlets-docs
[nodejs.org]: https://nodejs.org/
[Overview of Creating a Hosted Service for Azure (Azure 対応のホステッド サービスの作成の概要)]: https://azure.microsoft.com/documentation/services/cloud-services/
[Node.js デベロッパー センター]: https://azure.microsoft.com/develop/nodejs/

<!-- IMG List -->

[The result of the New-AzureService helloworld command]: ./media/cloud-services-nodejs-develop-deploy-app/node9.png
[The output of the Add-AzureNodeWebRole command]: ./media/cloud-services-nodejs-develop-deploy-app/node11.png
[A web browser displaying the Hello World web page]: ./media/cloud-services-nodejs-develop-deploy-app/node14.png
[The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node19.png
[A browser window displaying the hello world page; the URL indicates the page is hosted on Azure.]: ./media/cloud-services-nodejs-develop-deploy-app/node21.png
[The status of the Stop-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node48.png
[The status of the Remove-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node49.png
