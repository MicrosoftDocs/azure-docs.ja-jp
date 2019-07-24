---
title: メトリックおよび自動スケーリング用クラシック デプロイ モデル API の Azure Monitor での提供終了
description: メトリックおよび自動スケーリングのクラシック API (Azure Service Management (ASM) や RDFE デプロイ モデルともいう) の提供終了
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/19/2018
ms.author: robb
ms.openlocfilehash: ce54b63aa7831ed40a8592d536c43fc83fdc5567
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/15/2018
ms.locfileid: "53433093"
---
# <a name="azure-monitor-retirement-of-classic-deployment-model-apis-for-metrics-and-autoscale"></a>メトリックおよび自動スケーリング用クラシック デプロイ モデル API の Azure Monitor での提供終了

Azure Monitor (最初のリリース時には Azure Insights という名前だった) では、現在、クラシック VM およびクラシック Cloud Services 用の自動スケーリング設定を作成して管理し、クラシック VM およびクラシック Cloud Services からのメトリックを使用する機能が提供されています。 すべてのリージョンのすべての Azure パブリックおよびプライベート クラウドでは、クラシック デプロイ モデルベースの API の元のセットの**提供が、2019 年 6 月 30 日に終了**します。   

1 年以上、Azure Resource Manager ベースの API セットを介して同じ操作がサポートされてきました。 Azure ポータルでは、自動スケーリングとメトリックの両方で新しい REST API が使用されます。 これらの Resource Manager API に基づく新しい SDK、PowerShell、および CLI も使用できます。 Microsoft の監視パートナー サービスでは、Azure Monitor で新しい Azure Resource Manager ベースの REST API が使用されます。  

## <a name="who-is-not-affected"></a>影響を受けないユーザー

Azure ポータル、[新しい Azure Monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/)、PowerShell、CLI、または Resource Manager テンプレートを介して自動スケーリングを管理する場合、操作は必要ありません。  

Azure ポータルまたはさまざまな[監視パートナー サービス](../../azure-monitor/platform/partners.md)を介してメトリックを使用する場合、操作は必要ありません。 Microsoft では監視パートナーと協力して、新しい API への移行を行っています。

## <a name="who-is-affected"></a>影響を受けるユーザー

この記事は、次のコンポーネントを使用しているユーザーに適用されます。

- **クラシック Azure Insights SDK** - [クラシック Azure Insights SDK](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/) を使用している場合は、[.NET](https://github.com/azure/azure-libraries-for-net#download) または [Java](https://github.com/azure/azure-libraries-for-java#download) 用の新しい Azure Monitor SDK の使用に切り替えます。 [Azure Monitor SDK NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/)をダウンロードします。

- **クラシック自動スケーリング** - カスタム ビルド ツールから[クラシック自動スケーリング設定 API](https://msdn.microsoft.com/library/azure/mt348562.aspx) を呼び出している場合や、[クラシック Azure Insights SDK](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/) を使用している場合は、[Resource Manager Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/autoscalesettings) の使用に切り替える必要があります。

- **クラシック メトリック** - カスタム ビルド ツールの[クラシック REST API](https://msdn.microsoft.com/library/azure/dn510374.aspx) または[クラシック Azure Insights SDK](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/) を使ってメトリックを使用している場合は、[Resource Manager Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/autoscalesettings) の使用に切り替える必要があります。 

コードまたはカスタム ツールでクラシック API を呼び出しているかどうかがわからない場合は、以下を参照してください。

- コードまたはツールで参照されている URI を確認します。 クラシック API では https://management.core.windows.net という URI が使用されます。 使用する必要がある、Resource Manager ベースの API の新しい URI は https://management.azure.com/ で始まります。

- ご利用のコンピューター上のアセンブリ名を比較します。 古いクラシック アセンブリは https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/ にあります。

- メトリックまたは自動スケーリング API にアクセスするために証明書認証を使用している場合は、クラシック エンドポイントおよびライブラリを使っています。 新しい Resource Manager API では、サービス プリンシパルまたはユーザー プリンシパルを使用した Azure Active Directory 認証が必要です。

- 次のいずれかのリンクのドキュメントで参照されている呼び出しを使用している場合は、古いクラシック API を使っています。

  - [Windows.Azure.Management.Monitoring クラス ライブラリ](https://docs.microsoft.com/previous-versions/azure/dn510414(v=azure.100))

  - [(クラシック) .NET の監視](https://docs.microsoft.com/previous-versions/azure/reference/mt348562(v%3dazure.100))

  - [IMetricOperations インターフェイス](https://docs.microsoft.com/previous-versions/azure/reference/dn802395(v%3dazure.100))

## <a name="why-you-should-switch"></a>切り替える必要がある理由

自動スケールとメトリックの既存のすべての機能は、新しい API を介して引き続き動作します。  

新しい API に移行することで、すべての監視サービス全体での一貫性のあるロールベースのアクセス制御 (RBAC) のためのサポートなど、Resource Manager ベースの機能が得られます。 また、以下のメトリックの追加機能が得られます。 

- ディメンションのサポート
- すべてのサービス全体での一貫性のある 1 分のメトリック粒度 
- より優れたクエリ
- より長いデータ保有期間 (93 日間のメトリック。これまでは 30 日間) 

全体的に、Azure の他のすべてのサービスと同じように、Resource Manager ベースの Azure Monitor API では、より優れたパフォーマンス、スケーラビリティ、信頼性が得られます。 

## <a name="what-happens-if-you-do-not-migrate"></a>移行しないとどうなるのか

### <a name="before-retirement"></a>提供終了前

Azure サービスやそのワークロードに直接影響はありません。  

### <a name="after-retirement"></a>提供終了後

上にリストされているクラシック API のすべての呼び出しが失敗し、次のようなエラー メッセージが返されます。

自動スケールの場合: *この API は推奨されていません。Azure ポータル、Azure Monitor SDK、PowerShell、CLI、または Resource Manager テンプレートを使用して、自動スケーリング設定を管理してください*。  

メトリックの場合: *この API は推奨されていません。Azure ポータル、Azure Monitor SDK、PowerShell、CLI を使用して、メトリックのクエリを実行してください*。

## <a name="email-notifications"></a>電子メール通知

提供終了に関する通知が、次のアカウント ロールのメール アドレスで送信されました。 

- アカウントおよびサービス管理者
- 共同管理者  

ご質問がある場合は、MonitorClassicAPIhelp@microsoft.com からお問い合わせください。  

## <a name="references"></a>参照

- [Azure Monitor の新しい REST API](https://docs.microsoft.com/rest/api/monitor/) 
- [新しい Azure Monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/)
