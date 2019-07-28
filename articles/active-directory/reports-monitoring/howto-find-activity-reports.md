---
title: Azure Portal で Azure Active Directory のユーザー アクティビティ レポートを見つける | Microsoft Docs
description: Azure Portal で Azure Active Directory のアクティビティ レポートがある場所について説明します。
services: active-directory
documentationcenter: ''
author: markusvi
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: d47072713c57576abe780134792c3a5cbc27127c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60438328"
---
# <a name="find-activity-reports-in-the-azure-portal"></a>Azure Portal でアクティビティ レポートを見つける

この記事では、Azure portal で Azure Active Directory (Azure AD) のユーザー アクティビティ レポートを見つける方法について説明します。

## <a name="audit-logs-report"></a>監査ログ レポート

監査ログ レポートは、アプリケーション アクティビティに関する複数のレポートをコンテキストベースのレポートのために単一のビューに結合したものです。 監査ログ レポートにアクセスするには:

1. [Azure Portal](https://portal.azure.com) に移動します。
2. 右上からディレクトリを選択し、左側のナビゲーション ウィンドウで **[Azure Active Directory]** ブレードを選択します。
3. Azure Active Directory ブレードの **[アクティビティ]** セクションから **[監査ログ]** を選択します。 

    ![監査ログ](./media/howto-find-activity-reports/482.png "監査ログ")

監査ログ レポートは、次のレポートを統合しています。

* 監査レポート
* パスワード リセット アクティビティ
* パスワード リセット登録アクティビティ
* セルフ サービス グループ アクティビティ
* Office 365 グループ名の変更
* アカウント プロビジョニングのアクティビティ
* パスワード ロールオーバーの状態
* アカウント プロビジョニング エラー

### <a name="filtering-on-audit-logs"></a>監査ログのフィルター処理

監査レポートで高度なフィルター処理を使用すると、 **[カテゴリ]** フィルターで指定することで、特定のカテゴリの監査データにアクセスできます。 たとえば、ユーザーに関連するすべてのアクティビティを表示するには、**UserManagement** カテゴリを選択します。 

次のようなカテゴリがあります。

- All
- AdministrativeUnit
- ApplicationManagement
- Authentication
- Authorization
- Contact
- Device
- DeviceConfiguration
- DirectoryManagement
- EntitlementManagement
- GroupManagement
- その他
- ポリシー
- ResourceManagement
- RoleManagement
- UserManagement

**[サービス]** ドロップダウン フィルターを使用して、特定のサービスでフィルター処理することもできます。 たとえば、セルフサービスによるパスワード管理に関連するすべての監査イベントを取得するには、 **[Self-service Password Management]\(セルフサービスによるパスワード管理\)** フィルターを選択します。

次のようなサービスがあります。

- All
- アクセス レビュー
- アカウント プロビジョニング 
- アプリケーション SSO
- 認証方法
- B2C
- 条件付きアクセス
- Core Directory (コア ディレクトリ)
- エンタイトルメント管理
- Identity Protection
- 招待されたユーザー
- PIM
- セルフサービスによるグループ管理
- Self-service Password Management \(セルフサービスによるパスワード管理)
- 使用条件

## <a name="sign-ins-report"></a>サインイン レポート 

**[サインイン]** ビューには、すべてのユーザー サインインと、**アプリケーションの使用状況**レポートが表示されます。 アプリケーションの使用状況に関する情報は、 **[エンタープライズ アプリケーション]** の概要の **[管理]** セクションで確認することもできます。

サインイン レポートにアクセスするには:

1. [Azure Portal](https://portal.azure.com) に移動します。
2. 右上からディレクトリを選択し、左側のナビゲーション ウィンドウで **[Azure Active Directory]** ブレードを選択します。
3. Azure Active Directory ブレードの **[アクティビティ]** セクションから **[サインイン]** を選択します。 

    ![[サインイン] ビュー](./media/howto-find-activity-reports/483.png "[サインイン] ビュー")


### <a name="filtering-on-application-name"></a>アプリケーション名のフィルター処理

サインイン レポートを使用すると、ユーザー名またはアプリケーション名をフィルター処理して、アプリケーションの使用状況の詳細を表示できます。

![[サインイン イベントのフィルター処理] ページ](./media/howto-find-activity-reports/07.png "[サインイン イベントのフィルター処理] ページ")

## <a name="security-reports"></a>セキュリティ レポート

### <a name="anomalous-activity-reports"></a>異常アクティビティ レポート

異常アクティビティ レポートは、Azure AD で検出および報告されるセキュリティ関連のリスク イベントに関する情報を提供します。

次の表は、Azure AD 異常アクティビティ セキュリティ レポートと、各レポートに対応する Azure Portal のリスク イベントの種類を示しています。 詳細については、「[Azure Active Directory リスク イベント](concept-risk-events.md)」を参照してください。  


| Azure AD 異常アクティビティ レポート |  Identity Protection のリスク イベントの種類|
| :--- | :--- |
| 資格情報が漏洩したユーザー | 漏洩した資格情報 |
| 不規則なサインイン アクティビティ | 特殊な場所へのあり得ない移動 |
| 感染している可能性があるデバイスからのサインイン | 感染しているデバイスからのサインイン|
| 不明なソースからのサインイン | 匿名の IP アドレスからのサインイン |
| 不審なアクティビティのある IP アドレスからのサインイン | 不審なアクティビティのある IP アドレスからのサインイン |
| - | 未知の場所からのサインイン |

次の Azure AD 異常アクティビティ セキュリティ レポートは、Azure Portal ではリスク イベントに含まれません。

* 複数のエラー後のサインイン
* 複数の地域からのサインイン


### <a name="detected-risk-events"></a>検出されたリスク イベント

[Azure portal](https://portal.azure.com) では、 **[Azure Active Directory]** ブレードの **[セキュリティ]** セクションで、検出されたリスク イベントに関するレポートにアクセスできます。 検出されたリスク イベントは、次のレポートで追跡されます。   

- [危険な状態のユーザー](concept-user-at-risk.md)
- [リスクの高いサインイン](concept-risky-sign-ins.md)

    ![セキュリティ レポート](./media/howto-find-activity-reports/04.png "セキュリティ レポート")

## <a name="troubleshoot-issues-with-activity-reports"></a>アクティビティ レポートに関する問題のトラブルシューティング

### <a name="missing-data-in-the-downloaded-activity-logs"></a>ダウンロードしたアクティビティ ログにデータが見つからない

#### <a name="symptoms"></a>現象 

アクティビティ ログ (監査またはサインイン) をダウンロードしましたが、選択した期間のレコードがまったく表示されません。 なぜですか? 

 ![レポート](./media/troubleshoot-missing-data-download/01.png)
 
#### <a name="cause"></a>原因

Azure portal でアクティビティ ログをダウンロードする場合は、新しい順に並べ替えられた最新の 250000 件のレコードに制限されます。 

#### <a name="resolution"></a>解決策

[Azure AD Reporting API](concept-reporting-api.md) を利用すると、任意の時点のレコードを最大 100 万件取得できます。

### <a name="missing-audit-data-for-recent-actions-in-the-azure-portal"></a>Azure portal で最近の操作の監査データが見つからない

#### <a name="symptoms"></a>現象

Azure Portal でいくつかの操作を実行したので、`Activity logs > Audit Logs` ブレードでこれらの操作の監査ログが表示されるはずですが、見つかりません。

 ![レポート](./media/troubleshoot-missing-audit-data/01.png)
 
#### <a name="cause"></a>原因

操作はアクティビティ ログにすぐには表示されません。 アクティビティ ログの待ち時間を次の表に示します。 

| レポート | &nbsp; | 待ち時間 (P95) | 待ち時間 (P99) |
|--------|--------|---------------|---------------|
| ディレクトリ監査 | &nbsp; | 2 分 | 5 分 |
| サインイン アクティビティ | &nbsp; | 2 分 | 5 分 | 

#### <a name="resolution"></a>解決策

15 分から 2 時間待ってから、操作がログに表示されるかどうかを確認します。 2 時間が経過した後もログが表示されない場合は、[サポート チケットを発行](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)してください。Microsoft が調査します。

### <a name="missing-logs-for-recent-user-sign-ins-in-the-azure-ad-sign-ins-activity-log"></a>Azure AD のサインイン アクティビティ ログで最近のユーザー サインインのログが見つからない

#### <a name="symptoms"></a>現象

最近 Azure portal にサインインしたので `Activity logs > Sign-ins` ブレードにこれらの操作のサインイン ログが表示されるはずですが、見つかりません。

 ![レポート](./media/troubleshoot-missing-audit-data/02.png)
 
#### <a name="cause"></a>原因

操作はアクティビティ ログにすぐには表示されません。 アクティビティ ログの待ち時間を次の表に示します。 

| レポート | &nbsp; | 待ち時間 (P95) | 待ち時間 (P99) |
|--------|--------|---------------|---------------|
| ディレクトリ監査 | &nbsp; | 2 分 | 5 分 |
| サインイン アクティビティ | &nbsp; | 2 分 | 5 分 | 

#### <a name="resolution"></a>解決策

15 分から 2 時間待ってから、操作がログに表示されるかどうかを確認します。 2 時間が経過した後もログが表示されない場合は、[サポート チケットを発行](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)してください。Microsoft が調査します。

### <a name="i-cant-view-more-than-30-days-of-report-data-in-the-azure-portal"></a>Azure portal で 30 日より前のレポート データを表示できません

#### <a name="symptoms"></a>現象

Azure portal で 30 日より前のサインイン データおよび監査データを表示できません。 なぜですか? 

 ![レポート](./media/troubleshoot-missing-audit-data/03.png)

#### <a name="cause"></a>原因

Azure Active Directory Actions には、ライセンスに応じて次の期間のアクティビティ レポートが保存されます。

| レポート           | &nbsp; |  Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| ---              | ----   |  ---           | ---                 | ---                 |
| 監査ログ  | &nbsp; |   7 日     | 30 日             | 30 日             |
| サインイン | &nbsp; | 使用できません。 7 日間分のサインインについては、個人のユーザー プロファイル ブレードからアクセスできます | 30 日 | 30 日             |

詳細については、「[Azure Active Directory レポートのアイテム保持ポリシー](reference-reports-data-retention.md)」を参照してください。  

#### <a name="resolution"></a>解決策

30 日を超えてデータを保持するには、2 つの方法があります。 [Azure AD レポート API](concept-reporting-api.md) を使用してデータをプログラムで取得し、データベースに保存することができます。 また、監査ログを Splunk や SumoLogic などのサード パーティの SIEM システムに統合することもできます。

## <a name="next-steps"></a>次の手順

* [監査ログの概要](concept-audit-logs.md)
* [サインインの概要](concept-sign-ins.md)
* [危険なイベントの概要](concept-risk-events.md)
