---
title: Azure Active Directory アクティビティ ログ コンテンツ パックのエラーのトラブルシューティングを行う | Microsoft Docs
description: Azure Active Directory アクティビティ ログ コンテンツ パックのエラー メッセージの一覧とエラーを修正するための手順を示します。
services: active-directory
documentationcenter: ''
author: markusvi
manager: daveba
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 06/07/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ee49ae56122fe596a4490914677d91d2f0348f6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66807528"
---
# <a name="troubleshooting-azure-active-directory-activity-logs-content-pack-errors"></a>Azure Active Directory アクティビティ ログ コンテンツ パックのエラーのトラブルシューティングを行う 

|  |
|--|
|現在、Azure AD Power BI コンテンツ パックでは Azure AD Graph API を使用して Azure AD テナントからデータを取得します。 そのため、コンテンツ パック内のデータと[レポート用の Microsoft Graph API](concept-reporting-api.md) を使用して取得したデータに差異が発生する可能性があります。 |
|  |

Azure Active Directory (Azure AD) の Power BI コンテンツ パックを操作しているときに、次のエラーが発生する可能性があります。 

- [更新が失敗しました](troubleshoot-content-pack.md#refresh-failed) 
- [データ ソースの資格情報を更新できませんでした](troubleshoot-content-pack.md#failed-to-update-data-source-credentials) 
- [データのインポートに時間がかかっています](#data-import-is-too-slow) 

この記事では、考えられる原因とこれらのエラーを修正する方法について説明します。
 
## <a name="refresh-failed"></a>更新が失敗しました 
 
**このエラーがどのように表面化するか**:Power BI からの電子メールまたは更新履歴の失敗状態。 


| 原因 | 修正方法 |
| ---   | ---        |
| 更新失敗エラーは、コンテンツ パックに接続するユーザーの資格情報がリセットされたが、コンテンツ パックの接続設定が更新されていないときに発生することがあります。 | Power BI で、Azure Active Directory アクティビティ ログ ダッシュボード (**Azure Active Directory のアクティビティ ログ**) に対応するデータセットを検索し、[更新のスケジュール設定] を選択し、Azure AD 資格情報を入力します。 |
| 大規模なデータセットが原因で更新が失敗します。 | 現時点において、Power BI を使用した Azure AD コンテンツ パックでは、Power BI サービスのタイムアウトに関するいくつかの制限のため、小さなデータ セットのみ (500,00 行未満) のみサポートできます。 調整エラーが発生した場合、またはタイムアウトの問題で更新に失敗した場合は、大規模なデータセットをフェッチしようとしていることが原因の可能性があります。 クエリの時間を短縮し、もう一度やり直してください。|
 
 
## <a name="failed-to-update-data-source-credentials"></a>データ ソースの資格情報を更新できませんでした 
 
**このエラーがどのように表面化するか**:Power BI で、Azure AD アクティビティ ログ コンテンツ パックに接続するとき。 

| 原因 | 修正方法 |
| ---   | ---        |
| 接続するユーザーが、グローバル管理者、セキュリティ閲覧者、またはセキュリティ管理者のいずれでもありません。 | コンテンツ パックにアクセスするには、グローバル管理者、セキュリティ閲覧者、またはセキュリティ管理者のいずれかのアカウントを使用します。 |
| テナントが Premium テナントでないか、Premium ライセンス ファイルに少なくとも 1 人のユーザーが登録されていません。 | [サポート チケットを提出](../fundamentals/active-directory-troubleshooting-support-howto.md)します。|
 


## <a name="data-import-is-too-slow"></a>データのインポートが遅すぎます 
 
**このエラーがどのように表面化するか**:Power BI でコンテンツ パックに接続した後、Azure AD アクティビティ ログ用のダッシュボードを準備するためのデータ インポート処理が開始されます。 次のメッセージが表示されます。 **[データをインポートしています...]** 。これ以上は進みません。  

| 原因 | 修正方法 |
| ---   | ---        |
| テナントのサイズに応じて、この手順は数分から 30 分かかることがあります。 | 1 時間以内にメッセージがダッシュボードの表示に変わらない場合は、[サポート チケットを提出](../fundamentals/active-directory-troubleshooting-support-howto.md)してください。|

## <a name="next-steps"></a>次の手順

* [Azure AD レポート用の Power BI コンテンツ パックをインストールする](quickstart-install-power-bi-content-pack.md)。
* [Azure AD レポート用の Power BI コンテンツ パックを使用してデータを視覚化する](howto-power-bi-content-pack.md)
* [Azure Active Directory のサポートを得る方法](../fundamentals/active-directory-troubleshooting-support-howto.md)
