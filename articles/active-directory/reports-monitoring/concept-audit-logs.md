---
title: Azure Active Directory ポータルの監査アクティビティ レポート | Microsoft Docs
description: Azure Active Directory ポータルの監査アクティビティ レポートの概要
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3da96c09026baff3965e0a90d1f461fd948a3a50
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58438397"
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Azure Active Directory ポータルの監査アクティビティ レポート 

環境の動作状況を判断するために必要な情報は、Azure Active Directory (Azure AD) レポートで入手できます。

このレポート アーキテクチャは、次のコンポーネントで構成されます。

- **アクティビティ** 
    - **サインイン** - [サインイン レポート](concept-sign-ins.md)は、マネージド アプリケーションの使用状況とユーザー サインイン アクティビティに関する情報を提供します。
    - **監査ログ** - Azure AD 内のさまざまな機能によって行われたすべての変更についてログによる追跡可能性を提供します。 監査ログの例として、ユーザー、アプリ、グループ、ロール、ポリシーの追加や削除など、Azure AD 内のあらゆるリソースに加えられた変更があります。
- **セキュリティ** 
    - **リスクの高いサインイン** - [リスクの高いサインイン](concept-risky-sign-ins.md)は、ユーザー アカウントの正当な所有者ではない人によって行われた可能性があるサインイン試行の指標です。 
    - **リスクのフラグ付きユーザー** - [リスクの高いユーザー](concept-user-at-risk.md)は、侵害された可能性があるユーザー アカウントの指標です。

この記事では、監査レポートの概要について説明します。
 
## <a name="who-can-access-the-data"></a>誰がデータにアクセスできますか。

* **セキュリティ管理者**、**セキュリティ閲覧者**、**レポート閲覧者**、**グローバル管理者**のいずれかのロールであるユーザー
* さらに、すべてのユーザー (管理者以外) は、独自の監査アクティビティを表示できます

## <a name="audit-logs"></a>監査ログ

Azure AD の監査ログは、コンプライアンスのためにシステム アクティビティのレコードを提供します。 監査レポートにアクセスするには、**Azure Active Directory** の **[アクティビティ]** セクションで **[監査ログ]** を選択します。 監査ログでは最大 1 時間の待ち時間が生じる可能性があることに注意してください。そのため、タスクが完了した後、監査アクティビティ データがポータルに表示されるまでにそれだけ長い時間がかかる場合があります。

![監査ログ](./media/concept-audit-logs/61.png "監査ログ")

監査ログには、次のものを示す既定のリスト ビューがあります。

- 発生の日付と時刻
- 発生をログに記録したサービス
- アクティビティの名前とカテゴリ (*何か*を指す) 
- アクティビティの状況 (成功または失敗)
- ターゲット
- アクティビティのイニシエーターまたはアクター ("だれが" を指す)

![監査ログ](./media/concept-audit-logs/listview.png "監査ログ")

リスト ビューをカスタマイズするには、ツール バーの **[列]** をクリックします。

![監査ログ](./media/concept-audit-logs/columns.png "監査ログ")

これで、追加のフィールドの表示または既に表示されているフィールドの削除ができます。

![監査ログ](./media/concept-audit-logs/columnselect.png "監査ログ")

詳細な情報を取得するには、リスト ビューで項目を選択します。

![監査ログ](./media/concept-audit-logs/details.png "監査ログ")


## <a name="filtering-audit-logs"></a>監査ログのフィルター処理

次のフィールドで監査データをフィルター処理できます。

- Service
- Category
- アクティビティ
- Status
- ターゲット
- 開始者 (アクター)
- 期間

![監査ログ](./media/concept-audit-logs/filter.png "監査ログ")

**[サービス]** フィルターでは、次のサービスのドロップダウンから選択することができます。

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
- セルフサービスによるパスワード管理
- 使用条件

**[カテゴリ]** フィルターでは、次のフィルターのいずれかを選択できます。

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
- GroupManagement
- GroupManagement

**[アクティビティ]** フィルターは、カテゴリとアクティビティ リソースの種類の選択に基づいたものです。 参照する特定のアクティビティを選択することも、すべてを選択することもできます。 

Graph API https://graph.windows.net/$tenantdomain/activities/auditActivityTypes?api-version=beta を使用して全監査アクティビティのリストを取得できます ($tenantdomain は実際のドメイン名)。または、[監査レポートのイベント](reference-audit-activities.md)に関する記事を参照してください。

**[状態]** フィルターでは、監査操作の状態に基づいてフィルターに掛けることができます。 状態は、次のいずれかになります。

- All
- Success
- 失敗

**[ターゲット]** フィルターでは、名前またはユーザー プリンシパル名 (UPN) で特定のターゲットを検索することができます。 ターゲット名と UPN では大文字小文字を区別します。 

**[開始者]** フィルターでは、開始者の名前またはユニバーサル プリンシパル名 (UPN) を定義できます。 名前と UPN では大文字小文字を区別します。

**[期間]** フィルターでは、返されるデータの期間を定義できます。  
次のいずれかの値になります。

- 1 か月
- 7 日
- 24 時間
- カスタム

カスタムの期間を選択すると、開始時刻と終了時刻を構成できます。

また、**[ダウンロード]** ボタンを選択して、フィルターされたデータ (最大 250,000 個のレコード) をダウンロードすることもできます。 CSV 形式または JSON 形式でログをダウンロードすることができます。 ダウンロードできるレコードの数は、[Azure Active Directory レポートの保持ポリシー](reference-reports-data-retention.md)によって制限されます。

![監査ログ](./media/concept-audit-logs/download.png "監査ログ")

## <a name="audit-logs-shortcuts"></a>監査ログのショートカット

**[Azure Active Directory]** の他にも、Azure Portal には監査データに対するエントリ ポイントが 2 つ用意されています。

- ユーザーとグループ
- エンタープライズ アプリケーション

### <a name="users-and-groups-audit-logs"></a>ユーザーとグループの監査ログ

ユーザーとグループ ベースの監査レポートを使用すると、次のような疑問に対する答えを得ることができます。

- どの種類の更新プログラムがユーザーによって適用されているか。

- 何人のユーザーが変更されたか。

- 何個のパスワードが変更されたか。

- 管理者がディレクトリで何を行ったか。

- 追加されたのはどのグループか。

- メンバーシップが変更されたグループはあるか。

- グループの所有者は変更されたか。

- グループまたはユーザーにどのライセンスが割り当てられているか。

ユーザーに関連する監査データだけを確認する場合は、**[ユーザー]** タブの **[アクティビティ]** セクションの **[監査ログ]** に、フィルター処理されたビューがあります。このエントリ ポイントには、事前選択カテゴリとして **UserManagement** があります。

![監査ログ](./media/concept-audit-logs/users.png "監査ログ")

グループに関連する監査データだけを確認する場合は、**[グループ]** タブの **[アクティビティ]** セクションの **[監査ログ]** に、フィルター処理されたビューがあります。このエントリ ポイントには、事前選択カテゴリとして **GroupManagement** があります。

![監査ログ](./media/concept-audit-logs/groups.png "監査ログ")

### <a name="enterprise-applications-audit-logs"></a>エンタープライズ アプリケーションの監査ログ

アプリケーション ベースの監査レポートを使用すると、次のような疑問に対する答えを得ることができます。

* どのアプリケーションが追加または更新されたか。
* どのアプリケーションが削除されたか。
* アプリケーションのサービス プリンシパルは変更されたか。
* アプリケーションの名前は変更されたか。
* アプリケーションに同意したのはだれか。

お使いのアプリケーションに関連する監査データを確認する場合は、**[エンタープライズ アプリケーション]** ブレードの **[アクティビティ]** セクションの **[監査ログ]** に、フィルター処理されたビューがあります。 このエントリ ポイントには、**[アクティビティの種類]** として **[エンタープライズ アプリケーション]** があらかじめ選択されています。

![監査ログ](./media/concept-audit-logs/enterpriseapplications.png "監査ログ")

## <a name="office-365-activity-logs"></a>Office 365 のアクティビティ ログ

Office 365 のアクティビティ ログは、[Microsoft 365 管理センター](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center)から確認できます。 Office 365 のアクティビティ ログと Azure AD のアクティビティ ログでは多くのディレクトリ リソースが共有されていますが、Office 365 のアクティビティ ログがすべて表示されるのは、Microsoft 365 管理センターのみです。 

[Office 365 Management API シリーズ](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview)を使用すると、Office 365 のアクティビティ ログにプログラムでアクセスすることもできます。

## <a name="next-steps"></a>次の手順

- [Azure AD 監査アクティビティのリファレンス](reference-audit-activities.md)
- [Azure AD レポートの保持のリファレンス](reference-reports-data-retention.md)
- [Azure AD ログの待機時間のリファレンス](reference-reports-latencies.md)
