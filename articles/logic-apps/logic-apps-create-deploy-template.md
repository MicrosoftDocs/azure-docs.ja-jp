---
title: "Azure Logic Apps のデプロイメント テンプレートの作成 | Microsoft Docs"
description: "ロジック アプリのデプロイとリリース管理に使用する Azure Resource Manager テンプレートの作成"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 85928ec6-d7cb-488e-926e-2e5db89508ee
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.custom: H1Hack27Feb2017
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: 18c66b0ade7f96fa377926474f8f278d110b91e4
ms.lasthandoff: 03/04/2017


---
# <a name="create-templates-for-logic-apps-deployment-and-release-management"></a>ロジック アプリのデプロイとリリース管理に使用するテンプレートの作成

ロジック アプリが作成された後は、このロジック アプリを Azure Resource Manager のテンプレートとして作成することができます。 これにより、任意の環境またはリソース グループにロジック アプリを簡単にデプロイできます。 Resource Manager テンプレートの詳細については、「[Azure Resource Manager テンプレートの作成](../azure-resource-manager/resource-group-authoring-templates.md)」と「[Resource Manager テンプレートと Azure CLI を使用したリソースのデプロイ](../azure-resource-manager/resource-group-template-deploy.md)」をご覧ください。

## <a name="logic-app-deployment-template"></a>ロジック アプリ デプロイ テンプレート

ロジック アプリには、次の&3; つの基本的なコンポーネントがあります。

* **ロジック アプリ リソース**: 価格設定計画、場所、ワークフロー定義などの情報が含まれています。
* **ワークフロー定義**: ロジック アプリのワークフローの手順と、Logic Apps エンジンでのワークフローの実行方法を説明しています。 この定義はロジック アプリの **[コード ビュー]** ウィンドウで確認できます。 ロジック アプリ リソースでは、`definition` プロパティでこの定義を確認できます。
* **接続**: 接続文字列やアクセス トークンなど、コネクタ接続関連のメタデータを安全に保存するための個別のリソースです。 ロジック アプリ リソースでは、`parameters` セクションでこれらのリソースが参照されます。

以上のコンポーネントを既存のロジック アプリで参照する場合、[Azure リソース エクスプローラー](http://resources.azure.com)などのツールを利用できます。

リソース グループ デプロイで使用するロジック アプリのテンプレートを作成するには、リソースを定義し、必要に応じてパラメーター化する必要があります。 たとえば、開発環境、テスト環境、運用環境にデプロイする場合、環境ごとに異なる接続文字列を SQL データベースに使用すると効果的です。 または、異なるサブスクリプションやリソース グループ内でデプロイすることもできます。  

## <a name="create-a-logic-app-deployment-template"></a>ロジック アプリのデプロイ テンプレートの作成

有効なロジック アプリ デプロイ テンプレートを作成するもっとも簡単な方法は、[Visual Studio Tools for Logic Apps](logic-apps-deploy-from-vs.md) を使用することです。 Visual Studio Tools は、任意のサブスクリプションまたは場所で使用できる有効なデプロイ テンプレートを生成します。

他にも、ロジック アプリ デプロイ テンプレートの作成に役立ついくつかのツールがあります。 テンプレートは手書きで作成できます。つまり、上述のリソースを使用して、必要に応じてパラメータを作成できます。 また、[Logic App Template Creator](https://github.com/jeffhollan/LogicAppTemplateCreator) という PowerShell モジュールを利用することもできます。 このオープン ソース モジュールは、最初にロジック アプリとそれが利用している接続を評価し、デプロイに必要なパラメーターと共にテンプレート リソースを生成します。 たとえば、ロジック アプリが Azure Service Bus キューからメッセージを受信し、Azure SQL データベースにデータを追加した場合、このツールはすべてのオーケストレーション ロジックを保存し、デプロイ時に設定できるように、SQL と Service Bus 接続文字列をパラメーター化します。

> [!NOTE]
> 接続はロジック アプリと同じリソース グループ内に置く必要があります。
>
>

### <a name="install-the-logic-app-template-powershell-module"></a>ロジック アプリ テンプレートの PowerShell モジュールのインストール
最も簡単なモジュールのインストール方法は、[PowerShell ギャラリー](https://www.powershellgallery.com/packages/LogicAppTemplate/0.1)と `Install-Module -Name LogicAppTemplate`コマンドを使用することです。  

PowerShell モジュールを手動でインストールすることもできます。

1. [ロジック アプリのテンプレート作成機能](https://github.com/jeffhollan/LogicAppTemplateCreator/releases)の最新リリースをダウンロードします。  
2. PowerShell モジュール フォルダー (通常は `%UserProfile%\Documents\WindowsPowerShell\Modules`) にフォルダーを解凍します。

モジュールがあらゆるテナントやサブスクリプション アクセス トークンで機能するように、[ARMClient](https://github.com/projectkudu/ARMClient) コマンド ライン ツールの使用をお勧めします。  この[ブログの投稿](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html)では、ARMClient についてさらに詳しく説明しています。

### <a name="generate-a-logic-app-template-by-using-powershell"></a>PowerShell を使用したロジック アプリ テンプレートの生成
PowerShell をインストールした後は、次のコマンドを使用してテンプレートを生成できます。

`armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp MyApp -ResourceGroup MyRG -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json`

`$SubscriptionId` は Azure サブスクリプション ID です。 この行はまず、ARMClient を使用してアクセス トークンを取得し、このアクセス トークンを PowerShell スクリプトにパイプして、JSON ファイルでテンプレートを作成します。

## <a name="add-parameters-to-a-logic-app-template"></a>ロジック アプリ テンプレートにパラメーターを追加する
ロジック アプリ テンプレートを作成したら、引き続き必要なパラメータを追加または変更できます。 たとえば、定義により、1 回のデプロイでデプロイを予定している Azure 機能または入れ子になったワークフローにリソース ID が追加される場合、テンプレートにリソースを追加し、必要に応じて ID をパラメーター化すると便利です。 これは、カスタム API や Swagger エンドポイントの参照をリソース グループごとにデプロイする予定の場合にも該当します。

## <a name="deploy-a-logic-app-template"></a>ロジック アプリ テンプレートをデプロイする

PowerShell、REST API、[Visual Studio Team Services Release Management](#team-services)、Azure Portal を介したテンプレート デプロイなど、さまざまなツールを使用して、テンプレートをデプロイできます。 パラメーターの値を保存するため、[パラメーター ファイル](../azure-resource-manager/resource-group-template-deploy.md#parameters)の作成も推奨されます。
詳しくは、「[Azure Resource Manager テンプレートと PowerShell を使用したリソースのデプロイ](../azure-resource-manager/resource-group-template-deploy.md)」または「[Azure Resource Manager テンプレートと Azure Portal を使用したリソースのデプロイ](../azure-resource-manager/resource-group-template-deploy-portal.md)」をご覧ください。

### <a name="authorize-oauth-connections"></a>OAuth 接続を作成する

デプロイ後、ロジック アプリは有効なパラメーターを使用してエンド ツー エンドで動作します。 ただし、有効なアクセス トークンを生成するには、OAuth 接続を承認する必要があります。 OAuth 接続を承認するには、Logic Apps デザイナーでロジック アプリを開いてこれらの接続を承認します。 または、デプロイを自動化する場合は、各 OAuth 接続に対して同意するスクリプトを使用することができます。 例として、 [LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth) プロジェクトの GitHub のスクリプトがあります。

<a name="team-services"></a>
## <a name="visual-studio-team-services-release-management"></a>Visual Studio Team Services Release Management

環境をデプロイし、管理する一般的なシナリオは、Visual Studio Team Services の Release Management のようなツールをロジック アプリ デプロイ テンプレートと共に使用することです。 Visual Studio Team Services に含まれる [Deploy Azure Resource Group (Azure リソース グループのデプロイ)](https://github.com/Microsoft/vsts-tasks/tree/master/Tasks/DeployAzureResourceGroup) タスクは、あらゆるビルドまたはリリース パイプラインに追加できます。 認証には[サービス プリンシパル](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/)をデプロイする必要があります。その後、リリース定義を生成できます。

1. Release Management で、**[空]** を選択して空の定義を作成します。

    ![空の定義の作成][1]

2. 必要なリソースをすべて選択します。これには、多くの場合、手動またはビルド プロセスで生成したロジック アプリ テンプレートが含まれます。
3. **Azure Resource Group Deployment (Azure リソース グループ デプロイ)** タスクを追加します。
4. [サービス プリンシパル](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/)で構成し、テンプレート ファイルとテンプレート パラメーター ファイルを参照します。
5. 必要に応じて、その他の環境、自動化されたテスト、承認者のために、リリース プロセスの手順の構築を続行します。

<!-- Image References -->
[1]: ./media/logic-apps-create-deploy-template/emptyreleasedefinition.png

