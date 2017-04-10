---
title: "Azure Active Directory レポート: 使用の開始 | Microsoft Docs"
description: "Azure Active Directory レポートで使用可能なさまざまなレポートが一覧表示します。"
services: active-directory
documentationcenter: 
author: dhanyahk
manager: femila
editor: 
ms.assetid: 7ac99919-8df5-4424-9298-fc7c025ba949
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/07/2016
ms.author: dhanyahk;markvi
translationtype: Human Translation
ms.sourcegitcommit: eec9b73cbaccfa50eec6f237e4d1d810c6efa1d9
ms.openlocfilehash: e5b8ac91914203156bd395d7f462385e9f6dbcb4
ms.lasthandoff: 02/24/2017


---
# <a name="getting-started-with-azure-active-directory-reporting"></a>Azure Active Directory レポートの使用の開始
## <a name="what-it-is"></a>説明
Azure Active Directory (Azure AD) には、ディレクトリに関するセキュリティ レポート、アクティビティ レポート、および監査レポートが含まれています。 含まれているレポートの一覧を次に示します。

### <a name="security-reports"></a>セキュリティ レポート
* 不明なソースからのサインイン
* 複数のエラー後のサインイン
* 複数の地域からのサインイン
* 不審なアクティビティのある IP アドレスからのサインイン
* 不規則なサインイン アクティビティ
* 感染している可能性があるデバイスからのサインイン
* 異常なサインイン アクティビティがあるユーザー

### <a name="activity-reports"></a>アクティビティ レポート
* アプリケーションの使用状況: 概要
* アプリケーションの使用状況: 詳細
* アプリケーション ダッシュボード
* アカウント プロビジョニング エラー
* 個々のユーザー デバイス
* 個々のユーザー アクティビティ
* グループのアクティビティ レポート
* パスワード リセット登録アクティビティ レポート
* パスワード リセット アクティビティ

### <a name="audit-reports"></a>監査レポート
* ディレクトリ監査レポート

> [!TIP]
> Azure AD レポートの詳細については、「 [アクセスおよび使用状況レポートの表示](active-directory-view-access-usage-reports.md)」を参照してください。
> 
> 

## <a name="how-it-works"></a>動作のしくみ
### <a name="reporting-pipeline"></a>レポート パイプライン
レポート パイプラインは、次の&3; つの主要な手順で構成されます。 ユーザーがサインインするたびに、または認証が行われるたびに、次の処理が実行されます。

* 最初に、ユーザーの認証が行われ、その結果 (成功または失敗) が Azure Active Directory サービスのデータベースに格納されます。
* 定期的に、最新のすべてのサインインが処理されます。 この時点で、Microsoft のセキュリティおよび異常アクティビティ アルゴリズムにより、最新のすべてのサインインについて疑わしいアクティビティが検索されます。
* 処理が終わると、レポートが生成され、キャッシュに格納され、Azure クラシック ポータルで提供されます。

### <a name="report-generation-times"></a>レポートの生成時間
Azure AD プラットフォームによって処理される認証およびサインインは膨大な量になるため、平均で&1; 時間前のサインインが最新の処理済みサインインとして示されます。 まれに、最新のサインインを処理するのに最大で 8 時間かかる場合があります。

各レポートの最上部のヘルプ テキストを調べることで、最新の処理済みサインインを確認できます。

![各レポートの上部のヘルプ テキスト](./media/active-directory-reporting-getting-started/reportingWatermark.PNG)

> [!TIP]
> Azure AD レポートの詳細については、「 [アクセスおよび使用状況レポートの表示](active-directory-view-access-usage-reports.md)」を参照してください。
> 
> 

## <a name="getting-started"></a>使用の開始
### <a name="sign-into-the-azure-classic-portal"></a>Azure クラシック ポータルにサインインする
最初に、グローバル管理者またはコンプライアンス管理者として [Azure クラシック ポータル](https://manage.windowsazure.com) にサインインする必要があります。 このとき、Azure サブスクリプションのサービス管理者または共同管理者であるか、"Azure AD へのアクセス" Azure サブスクリプションを使用している必要があります。

### <a name="navigate-to-reports"></a>レポートに移動する
レポートを表示するには、ディレクトリの上部の [レポート] タブに移動します。

今回初めてレポートを表示する場合、レポートを表示する前に、ダイアログ ボックスに示される条件に同意する必要があります。 これは、一部の国において個人情報と見なされる可能性のあるこのデータを組織の管理者が表示することが許容されることを確認するためです。

![ダイアログ ボックス](./media/active-directory-reporting-getting-started/dialogBox.png)

### <a name="explore-each-report"></a>各レポートを表示する
各レポートに移動して、収集されているデータおよび処理済みのサインインを表示します。 [すべてのレポートの一覧をここで](active-directory-reporting-guide.md)確認できます。

![すべてのレポート](./media/active-directory-reporting-getting-started/reportsMain.png)

### <a name="download-the-reports-as-csv"></a>レポートを CSV としてダウンロードする
各レポートは、CSV (コンマ区切り値) ファイルとしてダウンロードすることができます。 これらのファイルを Excel、PowerBI、またはサードパーティの分析プログラムで使用して、さらにデータを分析することができます。

任意のレポートを CSV としてダウンロードするには、レポートに移動し、下部にある [ダウンロード] をクリックします。

![[ダウンロード] ボタン](./media/active-directory-reporting-getting-started/downloadButton.png)

> [!TIP]
> Azure AD レポートの詳細については、「 [アクセスおよび使用状況レポートの表示](active-directory-view-access-usage-reports.md)」を参照してください。
> 
> 

## <a name="next-steps"></a>次のステップ
### <a name="customize-alerts-for-anomalous-sign-in-activity"></a>異常なサインイン アクティビティのアラートをカスタマイズする
ディレクトリの [構成] タブに移動します。

[通知] セクションまでスクロールします。

[異常なサインインの電子メール通知] を有効または無効にします。

![[通知] セクション](./media/active-directory-reporting-getting-started/notificationsSection.png)

### <a name="integrate-with-the-azure-ad-reporting-api"></a>Azure AD Reporting API と統合する
[Reporting API の概要](active-directory-reporting-api-getting-started.md)に関するページを参照してください。

### <a name="engage-multi-factor-authentication-on-users"></a>ユーザーに Multi-Factor Authentication を適用する
レポート内のユーザーを選択します。

画面の下部の [MFA を有効にする] をクリックします。

![画面下部の[多要素認証] ボタン](./media/active-directory-reporting-getting-started/mfaButton.png)

> [!TIP]
> Azure AD レポートの詳細については、「 [アクセスおよび使用状況レポートの表示](active-directory-view-access-usage-reports.md)」を参照してください。
> 
> 

## <a name="learn-more"></a>詳細情報
### <a name="audit-events"></a>イベントを監査する
ディレクトリで監査対象となるイベントについては、 [Azure Active Directory レポートの監査イベント](active-directory-reporting-audit-events.md)に関するページを参照してください。

### <a name="api-integration"></a>API の統合
[Reporting API の概要](active-directory-reporting-api-getting-started.md)に関する記事および [API リファレンス ドキュメント](https://msdn.microsoft.com/library/azure/mt126081.aspx)をご覧ください。

### <a name="get-in-touch"></a>お問い合わせ
ご意見ご感想、サポート、またはご質問については、 [aadreportinghelp@microsoft.com](mailto:aadreportinghelp@microsoft.com) までメールをお送りください。

> [!TIP]
> Azure AD レポートの詳細については、「 [アクセスおよび使用状況レポートの表示](active-directory-view-access-usage-reports.md)」を参照してください。
> 
> 


