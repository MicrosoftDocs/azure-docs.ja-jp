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
ms.date: 09/17/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5aa8f675e3fd36fbebfecf42db0f02b0f0f00115
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95995983"
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Azure Active Directory ポータルの監査アクティビティ レポート 

環境の動作状況を判断するために必要な情報は、Azure Active Directory (Azure AD) レポートで入手できます。



このレポート アーキテクチャは、次のコンポーネントで構成されます。

- **アクティビティ** 
    - **サインイン** - [サインイン レポート](concept-sign-ins.md)は、マネージド アプリケーションの使用状況とユーザー サインイン アクティビティに関する情報を提供します。
    - **監査ログ** - Azure AD 内のさまざまな機能によって行われたすべての変更についてログによる追跡可能性を提供します。 監査ログの例として、ユーザー、アプリ、グループ、ロール、ポリシーの追加や削除など、Azure AD 内のあらゆるリソースに加えられた変更があります。
    - **プロビジョニング ログ** - [プロビジョニング ログ](./concept-provisioning-logs.md)によって、プロビジョニング サービスによるアクティビティの監視 (ServiceNow のグループや Workday からインポートされたユーザーの作成など) が可能になります。 
- **Security** 
    - **リスクの高いサインイン** - [リスクの高いサインイン](../identity-protection/overview-identity-protection.md)は、ユーザー アカウントの正当な所有者ではない人によって行われた可能性があるサインイン試行の指標です。 
    - **リスクのフラグ付きユーザー** - [リスクの高いユーザー](../identity-protection/overview-identity-protection.md)は、侵害された可能性があるユーザー アカウントの指標です。

この記事では、監査レポートの概要について説明します。
 
## <a name="who-can-access-the-data"></a>誰がデータにアクセスできますか。

* **セキュリティ管理者**、**セキュリティ閲覧者**、**レポート閲覧者**、**グローバル閲覧者**、または **全体管理者** のロールのユーザー

## <a name="audit-logs"></a>監査ログ

Azure AD の監査ログは、コンプライアンスのためにシステム アクティビティのレコードを提供します。 監査レポートにアクセスするには、**Azure Active Directory** の **[監視]** セクションで **[監査ログ]** を選択します。 



監査ログには、次のものを示す既定のリスト ビューがあります。

- 発生の日付と時刻
- 発生をログに記録したサービス
- アクティビティの名前とカテゴリ (*何か* を指す) 
- アクティビティの状況 (成功または失敗)
- ターゲット
- アクティビティのイニシエーターまたはアクター ("だれが" を指す)

![監査ログ](./media/concept-audit-logs/listview.png "監査ログ")

リスト ビューをカスタマイズするには、ツール バーの **[列]** をクリックします。

![列の監査](./media/concept-audit-logs/columns.png "列の監査")

これで、追加のフィールドの表示または既に表示されているフィールドの削除ができます。

![フィールドの削除](./media/concept-audit-logs/columnselect.png "フィールドの削除")

詳細な情報を取得するには、リスト ビューで項目を選択します。

![項目の選択](./media/concept-audit-logs/details.png "項目の選択")


## <a name="filtering-audit-logs"></a>監査ログのフィルター処理

次のフィールドで監査データをフィルター処理できます。

- サービス
- カテゴリ
- アクティビティ
- Status
- 移行先
- 開始者 (アクター)
- 期間

![オブジェクトのフィルタリング](./media/concept-audit-logs/filter.png "Filter オブジェクト")

**[サービス]** フィルターでは、次のサービスのドロップダウン リストから選択できます。

- All
- AAD 管理 UX
- アクセス レビュー
- アカウント プロビジョニング
- アプリケーション プロキシ
- 認証方法
- B2C
- 条件付きアクセス
- Core Directory (コア ディレクトリ)
- エンタイトルメント管理
- ハイブリッド認証
- Identity Protection
- 招待されたユーザー
- MIM サービス
- MyApps
- PIM
- セルフサービスによるグループ管理
- Self-service Password Management \(セルフサービスによるパスワード管理)
- 使用条件

**[カテゴリ]** フィルターでは、次のフィルターのいずれかを選択できます。

- All
- AdministrativeUnit
- ApplicationManagement
- 認証
- 承認
- Contact
- Device
- DeviceConfiguration
- DirectoryManagement
- EntitlementManagement
- GroupManagement
- KerberosDomain
- KeyManagement
- Label
- その他
- PermissionGrantPolicy
- ポリシー
- ResourceManagement
- RoleManagement
- UserManagement

**[アクティビティ]** フィルターは、カテゴリとアクティビティ リソースの種類の選択に基づいたものです。 参照する特定のアクティビティを選択することも、すべてを選択することもできます。 

Graph API を使用して、すべての監査アクティビティの一覧を取得できます。`https://graph.windows.net/<tenantdomain>/activities/auditActivityTypesV2?api-version=beta`

**[状態]** フィルターでは、監査操作の状態に基づいてフィルターに掛けることができます。 状態は、次のいずれかになります。

- All
- Success
- 障害

**[ターゲット]** フィルターでは、名前またはユーザー プリンシパル名 (UPN) の先頭で特定のターゲットを検索できます。 ターゲット名と UPN では大文字小文字を区別します。 

**[開始者]** フィルターでは、開始者の名前またはユニバーサル プリンシパル名 (UPN) の先頭を定義できます。 名前と UPN では大文字小文字を区別します。

**[期間]** フィルターでは、返されるデータの期間を定義できます。  
次のいずれかの値になります。

- 7 日
- 24 時間
- Custom

カスタムの期間を選択すると、開始時刻と終了時刻を構成できます。

また、 **[ダウンロード]** ボタンを選択して、フィルターされたデータ (最大 250,000 個のレコード) をダウンロードすることもできます。 CSV 形式または JSON 形式でログをダウンロードできます。 ダウンロードできるレコードの数は、[Azure Active Directory レポートの保持ポリシー](reference-reports-data-retention.md)によって制限されます。

![データをダウンロードする](./media/concept-audit-logs/download.png "データをダウンロードする")

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

ユーザーに関連する監査データだけを確認する場合は、 **[ユーザー]** タブの **[監視]** セクションの **[監査ログ]** に、フィルター処理されたビューがあります。このエントリ ポイントには、事前選択カテゴリとして **UserManagement** があります。

![User](./media/concept-audit-logs/users.png "User")

グループに関連する監査データだけを確認する場合は、 **[グループ]** タブの **[監視]** セクションの **[監査ログ]** に、フィルター処理されたビューがあります。このエントリ ポイントには、事前選択カテゴリとして **GroupManagement** があります。

![グループのフィルタリング](./media/concept-audit-logs/groups.png "グループのフィルタリング")

### <a name="enterprise-applications-audit-logs"></a>エンタープライズ アプリケーションの監査ログ

アプリケーション ベースの監査レポートを使用すると、次のような疑問に対する答えを得ることができます。

* どのアプリケーションが追加または更新されたか。
* どのアプリケーションが削除されたか。
* アプリケーションのサービス プリンシパルは変更されたか。
* アプリケーションの名前は変更されたか。
* アプリケーションに同意したのはだれか。

お使いのアプリケーションに関連する監査データを確認する場合は、 **[エンタープライズ アプリケーション]** ブレードの **[アクティビティ]** セクションの **[監査ログ]** に、フィルター処理されたビューがあります。 このエントリ ポイントには、 **[アクティビティの種類]** として **[エンタープライズ アプリケーション]** があらかじめ選択されています。

![エンタープライズ アプリケーション](./media/concept-audit-logs/enterpriseapplications.png "エンタープライズ アプリケーション")

## <a name="microsoft-365-activity-logs"></a>Microsoft 365 のアクティビティ ログ

Microsoft 365 のアクティビティ ログは、[Microsoft 365 管理センター](/office365/admin/admin-overview/about-the-admin-center)から確認できます。 Microsoft 365 のアクティビティ ログと Azure AD のアクティビティ ログでは多くのディレクトリ リソースが共有されていますが、Microsoft 365 のアクティビティ ログがすべて表示されるのは、Microsoft 365 管理センターのみです。 

また、[Office 365 Management API](/office/office-365-management-api/office-365-management-apis-overview) を使用すると、Microsoft 365 のアクティビティ ログにプログラムでアクセスすることもできます。

## <a name="next-steps"></a>次のステップ

- [Azure AD 監査アクティビティのリファレンス](reference-audit-activities.md)
- [Azure AD レポートの保持のリファレンス](reference-reports-data-retention.md)
- [Azure AD ログの待機時間のリファレンス](reference-reports-latencies.md)