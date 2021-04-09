---
title: Azure プラットフォーム ログの概要 | Microsoft Docs
description: Azure リソースの操作に関する高頻度の豊富なデータを提供する、Azure Monitor のログの概要。
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: bwren
ms.openlocfilehash: 3300b0587cbb6c2193b56c9152af0cb11ea51936
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102033301"
---
# <a name="overview-of-azure-platform-logs"></a>Azure プラットフォーム ログの概要
プラットフォーム ログでは、Azure リソースとそれらが依存している Azure プラットフォームの詳細な診断情報と監査情報が提供されます。 これらは自動的に生成されますが、特定のプラットフォーム ログを保持するための 1 つ以上の送信先に転送するように構成する必要があります。 この記事では、提供される情報の内容や収集と分析のための構成方法など、プラットフォーム ログの概要について説明します。

## <a name="types-of-platform-logs"></a>プラットフォーム ログの種類
次の表に、Azure のさまざまなレイヤーで使用できる特定のプラットフォーム ログを示します。

| ログ | レイヤー | 説明 |
|:---|:---|:---|
| [リソース ログ](./resource-logs.md) | Azure リソース | Azure リソース ("*データ プレーン*") 内で実行された操作に関する分析情報を提供します。たとえば、Key Vault からのシークレットの取得や、データベースに対する要求などです。 リソース ログの内容は、Azure サービスとリソースの種類によって異なります。<br><br>*リソース ログは、以前は診断ログと呼ばれていました。*  |
| [アクティビティ ログ](../essentials/activity-log.md) | Azure サブスクリプション | Service Health イベントの更新に加えて、外部 ("*管理プレーン*") からサブスクリプションの各 Azure リソースに対する操作についての分析情報を提供します。 アクティビティ ログを使用して、サブスクリプションのリソースに対して行われるすべての書き込み操作 (PUT、POST、DELETE) について、"_何を_"、"_誰が_"、"_いつ_" 行ったのかを確認できます。 Azure サブスクリプションごとに 1 つのアクティビティ ログがあります。 |
| [Azure Active Directory ログ](../../active-directory/reports-monitoring/overview-reports.md) | Azure テナント |  サインイン アクティビティの履歴と、特定のテナントに対して Azure Active Directory で行われた変更の監査証跡が含まれます。   |

> [!NOTE]
> Azure アクティビティ ログは、主に Azure Resource Manager で発生したアクティビティを記録します。 クラシック/RDFE モデルを使用しているリソースは追跡されません。 一部のクラシック リソース タイプでは、Azure Resource Manager にプロキシ リソース プロバイダー (例: Microsoft.ClassicCompute) が存在します。 これらのプロキシ リソース プロバイダーを使用して Azure Resource Manager 経由でクラシック リソース タイプを操作すると、その操作がアクティビティ ログに表示されます。 Azure Resource Manager プロキシの外部でクラシック リソース タイプを操作すると、そのアクションは操作ログにのみ記録されます。 操作ログは、ポータルの別のセクションで参照できます。

![プラットフォーム ログの概要](media/platform-logs-overview/logs-overview.png)




## <a name="viewing-platform-logs"></a>プラットフォーム ログの表示
さまざまな Azure プラットフォーム ログを表示および分析するための各種オプションがあります。

- Azure portal のアクティビティ ログを表示し、PowerShell と CLI からイベントにアクセスします。 詳細については、「[アクティビティ ログを表示する](../essentials/activity-log.md#view-the-activity-log)」を参照してください。 
- Azure Portal で Azure Active Directory のセキュリティ レポートとアクティビティ レポートを表示します。 詳細については、「[Azure Active Directory レポートの概要](../../active-directory/reports-monitoring/overview-reports.md)」  を参照してください。
- リソース ログは、サポートされている Azure リソースによって自動的に生成されますが、[送信先](#destinations)に送信しない限り表示できません。 

## <a name="destinations"></a>変換先
監視要件に応じて、次の表に示す 1 つ以上の送信先にプラットフォーム ログを送信できます。 [診断設定を作成する](../essentials/diagnostic-settings.md)ことによって、プラットフォーム ログの送信先を構成します。

| 到着地 | 説明 |
|:---|:---|
| Log Analytics ワークスペース | すべての Azure リソースのログをまとめて分析し、[ログ クエリ](../logs/log-query-overview.md)と[ログ アラート](../alerts/alerts-log.md)を含めて、[Azure Monitor ログ](../logs/data-platform-logs.md)に使用できるすべての機能を活用します。 ログ クエリの結果を Azure ダッシュボードにピン留めするか、対話型のレポートの一部としてブックに含めます。 |  |
| イベント ハブ | たとえば、サードパーティ製の SIEM やカスタム テレメトリ プラットフォームなど、Azure の外部のプラットフォーム ログ データを送信します。
| Azure Storage | 監査やバックアップのためにログをアーカイブします。 |

- アクティビティ ログまたはリソース ログの診断設定の作成の詳細については、「[プラットフォーム ログとメトリックを異なる宛先に送信するための診断設定を作成する](../essentials/diagnostic-settings.md)」を参照してください。 
- Azure Active Directory ログの診断設定を作成する方法の詳細については、次の記事を参照してください。
  - [Azure AD ログを Azure Monitor ログと統合する](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)
  - [チュートリアル:Azure Active Directory ログを Azure イベント ハブにストリーム配信する](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)
  - [チュートリアル:Azure AD のログを Azure ストレージ アカウントにアーカイブする](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md)



## <a name="next-steps"></a>次のステップ

* [詳細については、アクティビティ ログに関するページをご覧ください](../essentials/activity-log.md)
* [詳細については、リソース ログに関するページをご覧ください](./resource-logs.md)