---
title: 新しい Azure Application Insights リソースの作成 | Microsoft Docs
description: 新しいライブ アプリケーションを対象にした Application Insights 監視を手動でセットアップします。
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 878b007e-161c-4e36-8ab2-3d7047d8a92d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: mbullwin
ms.openlocfilehash: ae9c885b342664baf90f9c2b5702a092c9d838df
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2019
ms.locfileid: "69562847"
---
# <a name="create-an-application-insights-resource"></a>Application Insights リソースの作成

Azure Application Insights には、Microsoft Azure *リソース*のアプリケーションに関するデータが表示されます。 したがって、新しいリソースの作成は、[新しいアプリケーションを監視するための Application Insights の設定][start]の一部です。 新しいリソースを作成した後、そのインストルメンテーション キーを取得し、それを使用し Application Insights SDK を構成できます。 インストルメンテーション キーにより、リソースとテレメトリがリンクされます。

## <a name="sign-in-to-microsoft-azure"></a>Microsoft Azure にサインインする

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="create-an-application-insights-resource"></a>Application Insights リソースの作成

[Azure portal](https://portal.azure.com) にサインインし、Application Insights リソースを作成します。

![左上隅にある + 記号をクリックします。 開発者ツールを選択し、Application Insights を選択する](./media/create-new-resource/new-app-insights.png)

   | 設定        |  値           | 説明  |
   | ------------- |:-------------|:-----|
   | **Name**      | グローバルに一意の値 | 監視しているアプリを識別する名前。 |
   | **リソース グループ**     | myResourceGroup      | App Insights データをホストする新しいリソース グループまたは既存のリソース グループの名前。 |
   | **Location** | East US | お近くの場所か、アプリがホストされている場所の近くを選択します。 |

必須フィールドに適切な値を入力し、 **[確認と作成]** を選択します。

![必須フィールドに値を入力し、[確認と作成] を選択します。](./media/create-new-resource/review-create.png)

アプリが作成されると、新しいウィンドウが開きます。 そのウィンドウには、監視対象アプリケーションに関するパフォーマンスと使用状況データが表示されます。 

## <a name="copy-the-instrumentation-key"></a>インストルメンテーション キーのコピー

インストルメンテーション キーにより、利用統計情報と関連付けるリソースが識別されます。 インストルメンテーション キーをコピーしてアプリケーションのコードに追加する必要があります。

![インストルメンテーション キーをクリックしてコピーする](./media/create-new-resource/instrumentation-key.png)

## <a name="install-the-sdk-in-your-app"></a>アプリケーションでの SDK のインストール

アプリで Application Insights SDK をインストールします。 この手順は、アプリケーションの種類に大きく依存します。

インストルメンテーション キーを使用して、[アプリケーションにインストールする SDK][start] を構成します。

SDK には、追加コードを記述せずにテレメトリを送信する標準的なモジュールが含まれています。 ユーザーの操作を追跡したり、問題をより詳しく診断したりするには、[API を使用][api]して、独自のテレメトリを送信します。

## <a name="creating-a-resource-automatically"></a>リソースの自動作成

### <a name="powershell"></a>PowerShell

新しい Application Insights リソースを作成します。

```powershell
New-AzApplicationInsights [-ResourceGroupName] <String> [-Name] <String> [-Location] <String> [-Kind <String>]
 [-Tag <Hashtable>] [-DefaultProfile <IAzureContextContainer>] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="example"></a>例

```powershell
New-AzApplicationInsights -Kind java -ResourceGroupName testgroup -Name test1027 -location eastus
```
#### <a name="results"></a>結果

```powershell
Id                 : /subscriptions/{subid}/resourceGroups/testgroup/providers/microsoft.insights/components/test1027
ResourceGroupName  : testgroup
Name               : test1027
Kind               : web
Location           : eastus
Type               : microsoft.insights/components
AppId              : 8323fb13-32aa-46af-b467-8355cf4f8f98
ApplicationType    : web
Tags               : {}
CreationDate       : 10/27/2017 4:56:40 PM
FlowType           :
HockeyAppId        :
HockeyAppToken     :
InstrumentationKey : 00000000-aaaa-bbbb-cccc-dddddddddddd
ProvisioningState  : Succeeded
RequestSource      : AzurePowerShell
SamplingPercentage :
TenantId           : {subid}
```

このコマンドレットの詳細な PowerShell ドキュメントと、インストルメンテーション キーを取得する方法については、[Azure PowerShell のドキュメント](https://docs.microsoft.com/powershell/module/az.applicationinsights/new-azapplicationinsights?view=azps-2.5.0)を参照してください。

### <a name="azure-cli-preview"></a>Azure CLI (プレビュー)

プレビューの Application Insights Azure CLI コマンドにアクセスするには、まず次を実行する必要があります。

```azurecli
 az extension add -n application-insights
```

`az extension add` コマンドを実行しないと、`az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.` のようなエラー メッセージが表示されます。

これで、以下を実行して Application Insights リソースを作成できるようになりました。

```azurecli
az monitor app-insights component create --app
                                         --location
                                         --resource-group
                                         [--application-type]
                                         [--kind]
                                         [--tags]
```

#### <a name="example"></a>例

```azurecli
az monitor app-insights component create --app demoApp --location westus2 --kind web -g demoRg --application-type web
```

#### <a name="results"></a>結果

```azurecli
az monitor app-insights component create --app demoApp --location eastus --kind web -g demoApp  --application-type web
{
  "appId": "87ba512c-e8c9-48d7-b6eb-118d4aee2697",
  "applicationId": "demoApp",
  "applicationType": "web",
  "creationDate": "2019-08-16T18:15:59.740014+00:00",
  "etag": "\"0300edb9-0000-0100-0000-5d56f2e00000\"",
  "flowType": "Bluefield",
  "hockeyAppId": null,
  "hockeyAppToken": null,
  "id": "/subscriptions/{subid}/resourceGroups/demoApp/providers/microsoft.insights/components/demoApp",
  "instrumentationKey": "00000000-aaaa-bbbb-cccc-dddddddddddd",
  "kind": "web",
  "location": "eastus",
  "name": "demoApp",
  "provisioningState": "Succeeded",
  "requestSource": "rest",
  "resourceGroup": "demoApp",
  "samplingPercentage": null,
  "tags": {},
  "tenantId": {tenantID},
  "type": "microsoft.insights/components"
}
```

このコマンドの詳細な Azure CLI ドキュメントと、インストルメンテーション キーを取得する方法については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/ext/application-insights/monitor/app-insights/component?view=azure-cli-latest#ext-application-insights-az-monitor-app-insights-component-create)を参照してください。

## <a name="next-steps"></a>次の手順
* [診断検索](../../azure-monitor/app/diagnostic-search.md)
* [メトリックを探索する](../../azure-monitor/app/metrics-explorer.md)
* [Analytics クエリを作成する](../../azure-monitor/app/analytics.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[start]: ../../azure-monitor/app/app-insights-overview.md