---
title: 'トラブルシューティング: アクティビティ ログにデータが見つからない | Microsoft Docs'
description: Azure Active Directory アクティビティ ログにデータが見つからない問題の解決策を提供します。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 7cbe4337-bb77-4ee0-b254-3e368be06db7
ms.service: active-directory
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f48c26a65314e2f23513fba155f07db3805a516
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93123066"
---
# <a name="troubleshoot-missing-data-in-the-azure-active-directory-activity-logs"></a>トラブルシューティング:Azure Active Directory アクティビティ ログにデータが見つからない 

## <a name="i-cant-find-audit-logs-for-recent-actions-in-the-azure-portal"></a>Azure portal での最近の操作の監査ログが見つかりません

### <a name="symptoms"></a>現象

Azure Portal でいくつかの操作を実行したので、`Activity logs > Audit Logs` ブレードでこれらの操作の監査ログが表示されるはずですが、見つかりません。

 ![スクリーンショットには、監査ログエントリが示されています。](./media/troubleshoot-missing-audit-data/01.png)
 
### <a name="cause"></a>原因

操作はアクティビティ ログにすぐには表示されません。 アクティビティ ログの待ち時間を次の表に示します。 

| レポート | 待ち時間 (P95) | 待ち時間 (P99) |
|--------|---------------|---------------|
| ディレクトリ監査 | 2 分 | 5 分 |
| サインイン アクティビティ | 2 分 | 5 分 |

### <a name="resolution"></a>解決方法

15 分から 2 時間待ってから、操作がログに表示されるかどうかを確認します。 2 時間が経過した後もログが表示されない場合は、[サポート チケットを発行](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)してください。Microsoft が調査します。

## <a name="i-cant-find-recent-user-sign-ins-in-the-azure-active-directory-sign-ins-activity-log"></a>最近のユーザー サインインが Azure Active Directory のサインイン アクティビティ ログにありません

### <a name="symptoms"></a>現象

最近 Azure portal にサインインしたので `Activity logs > Sign-ins` ブレードにこれらの操作のサインイン ログが表示されるはずですが、見つかりません。

 ![スクリーンショットには、アクティビティ ログのサインインが示されています。](./media/troubleshoot-missing-audit-data/02.png)
 
### <a name="cause"></a>原因

操作はアクティビティ ログにすぐには表示されません。 アクティビティ ログの待ち時間を次の表に示します。 

| レポート | 待ち時間 (P95) | 待ち時間 (P99) |
|--------|---------------|---------------|
| ディレクトリ監査 | 2 分 | 5 分 |
| サインイン アクティビティ  2 分 | 5 分 |

### <a name="resolution"></a>解決方法

15 分から 2 時間待ってから、操作がログに表示されるかどうかを確認します。 2 時間が経過した後もログが表示されない場合は、[サポート チケットを発行](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)してください。Microsoft が調査します。

## <a name="i-cant-view-more-than-30-days-of-report-data-in-the-azure-portal"></a>Azure portal で 30 日より前のレポート データを表示できません

### <a name="symptoms"></a>現象

Azure portal で 30 日より前のサインイン データおよび監査データを表示できません。 なぜですか? 

 ![スクリーンショットには、[日付] メニューが示されています。](./media/troubleshoot-missing-audit-data/03.png)

### <a name="cause"></a>原因

Azure Active Directory Actions には、ライセンスに応じて次の期間のアクティビティ レポートが保存されます。

| レポート           | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| ---              | ---           | ---                 | ---                 |
| 監査ログ  |  7 日       | 30 日             | 30 日             |
| サインイン | 使用できません。 7 日間分のサインインについては、個人のユーザー プロファイル ブレードからアクセスできます | 30 日 | 30 日             |

詳細については、「[Azure Active Directory レポートのアイテム保持ポリシー](reference-reports-data-retention.md)」を参照してください。  

### <a name="resolution"></a>解決方法

30 日を超えてデータを保持するには、2 つの方法があります。 [Azure AD レポート API](concept-reporting-api.md) を使用してデータをプログラムで取得し、データベースに保存することができます。 また、監査ログを Splunk や SumoLogic などのサード パーティの SIEM システムに統合することもできます。

## <a name="next-steps"></a>次のステップ

* [Azure AD のレポートの保持](reference-reports-data-retention.md)。
* [Azure Active Directory レポートの待ち時間](reference-reports-latencies.md)。
* [Azure Active Directory レポートに関する FAQ](reports-faq.md)。

