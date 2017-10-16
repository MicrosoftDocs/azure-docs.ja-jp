---
title: "Azure Active Directory ポータルのサインイン アクティビティ レポート | Microsoft Docs"
description: "Azure Active Directory ポータルのサインイン アクティビティ レポートの概要"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/19/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: b9e61950654ba427b09dd608d354589a0804aaa5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Azure Active Directory ポータルのサインイン アクティビティ レポート

環境の動作状況を判断するために必要な情報は、[Azure Portal](https://portal.azure.com) の Azure Active Directory (Azure AD) レポートで入手できます。

Azure Active Directory のレポート アーキテクチャは、次のコンポーネントで構成されます。

- **アクティビティ** 
    - **サインイン アクティビティ** – マネージ アプリケーションの使用状況とユーザー サインイン アクティビティに関する情報
    - **監査ログ** - ユーザーとグループの管理、マネージ アプリケーション、およびディレクトリのアクティビティに関するシステム アクティビティ情報です。
- **セキュリティ** 
    - **リスクの高いサインイン** - リスクの高いサインインは、ユーザー アカウントの正当な所有者ではない人によって行われた可能性があるサインイン試行の指標です。 詳細については、「Risky sign-ins (リスクの高いサインイン)」を参照してください。
    - **リスクのフラグ付きユーザー** - リスクの高いユーザーは、侵害された可能性があるユーザー アカウントの指標です。 詳細については、「Users flagged for risk (リスクのフラグ付きユーザー)」を参照してください。

このトピックでは、サインイン アクティビティの概要を説明します。

## <a name="pre-requisite"></a>前提条件

### <a name="who-can-access-the-data"></a>誰がデータにアクセスできますか。
* セキュリティ管理者またはセキュリティ リーダーの役割のユーザー
* グローバル管理者
* 任意のユーザー (非管理者) が自分のサインインにアクセス可能 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>サインイン アクティビティにアクセスするために必要な Azure AD ライセンスを教えてください。
* すべてのサインイン アクティビティ レポートを閲覧するためには、ご利用のテナントに、Azure AD Premium ライセンスが関連付けられている必要があります。


## <a name="signs-in-activities"></a>サインイン アクティビティ

ユーザー サインイン レポートによって提供される情報を使用すると、次のような疑問への答えを得ることができます。

* ユーザーのサインインにどのようなパターンがあるか。
* 1 週間で何人のユーザーがユーザー サインインを行ったか。
* これらのサインインはどのような状態か。

すべてのサインイン アクティビティ データへの最初のエントリ ポイントは、**[Azure Active Directory]** の [アクティビティ] セクションの **[サインイン]** です


![サインイン アクティビティ](./media/active-directory-reporting-activity-sign-ins/61.png "サインイン アクティビティ")


監査ログには、次のものを示す既定のリスト ビューがあります。

- 関連するユーザー
- ユーザーがサインインしたアプリケーション
- サインインの状態
- サインインの時間

![サインイン アクティビティ](./media/active-directory-reporting-activity-sign-ins/41.png "サインイン アクティビティ")

リスト ビューをカスタマイズするには、ツール バーの **[列]** をクリックします。

![サインイン アクティビティ](./media/active-directory-reporting-activity-sign-ins/19.png "サインイン アクティビティ")

これで、追加のフィールドの表示または既に表示されているフィールドの削除ができます。

![サインイン アクティビティ](./media/active-directory-reporting-activity-sign-ins/42.png "サインイン アクティビティ")

リスト ビュー内の項目をクリックすると、その項目に関する確認可能な詳細が表示されます。

![サインイン アクティビティ](./media/active-directory-reporting-activity-sign-ins/43.png "サインイン アクティビティ")


## <a name="filtering-sign-in-activities"></a>サインイン アクティビティのフィルター処理

報告されるデータを有用なものだけに絞り込むために、次のフィールドを使用してサインイン データをフィルター処理できます。

- 間隔
- User
- アプリケーション
- クライアント
- サインインの状態

![サインイン アクティビティ](./media/active-directory-reporting-activity-sign-ins/44.png "サインイン アクティビティ")


**[間隔]** フィルターでは、返されるデータの期間を定義できます。  
次のいずれかの値になります。

- 1 か月
- 7 日
- 24 時間
- カスタム

カスタムの期間を選択すると、開始時刻と終了時刻を構成できます。

**[ユーザー]** フィルターでは、確認したいユーザーの名前またはそのユーザー プリンシパル名 (UPN) を指定できます。

**[アプリケーション]** フィルターでは、確認したいアプリケーションの名前を指定できます。

**[クライアント]** フィルターでは、確認したいデバイスの情報を指定できます。

**[サインイン状態]** フィルターでは、次のフィルターのいずれかを選択できます。

- すべて
- 成功
- 失敗


## <a name="sign-in-activities-shortcuts"></a>サインイン アクティビティのショートカット

[Azure Active Directory] の他にも、Azure Portal にはサインイン アクティビティ データに対するエントリ ポイントが 2 つ用意されています。

- [概要]
- エンタープライズ アプリケーション


### <a name="users-and-groups-sign-ins-activities"></a>ユーザーとグループのサインイン アクティビティ

ユーザー サインイン レポートによって提供される情報を使用すると、次のような疑問への答えを得ることができます。

- ユーザーのサインインにどのようなパターンがあるか。
- 1 週間で何人のユーザーがユーザー サインインを行ったか。
- これらのサインインはどのような状態か。



このデータへのエントリ ポイントは、**[ユーザーとグループ]** の **[概要]** セクションにあるユーザー サインイン グラフです。

![サインイン アクティビティ](./media/active-directory-reporting-activity-sign-ins/45.png "サインイン アクティビティ")

ユーザー サインイン グラフは、特定期間内のすべてのユーザーのサインインについて、週単位の集計を示します。 期間の既定値は 30 日です。

![サインイン アクティビティ](./media/active-directory-reporting-activity-sign-ins/46.png "サインイン アクティビティ")

サインイン グラフ内の日付をクリックすると、その日のサインイン アクティビティの詳細な一覧が表示されます。

![サインイン アクティビティ](./media/active-directory-reporting-activity-sign-ins/41.png "サインイン アクティビティ")

サインイン アクティビティの一覧内の各行には、選択したサインインに関する次のような詳細情報が表示されます。

* サインインしたのはだれか。
* 関連する UPN は何だったか。
* サインインの対象となったのはどのアプリケーションか。
* サインインの IP アドレスは何か。
* サインインはどのような状態だったか。

**[サインイン]** オプションを使用すると、すべてのユーザー サインインの完全な概要を表示できます。

![サインイン アクティビティ](./media/active-directory-reporting-activity-sign-ins/51.png "サインイン アクティビティ")



## <a name="usage-of-managed-applications"></a>マネージ アプリケーションの使用状況

アプリケーションを中心にしてサインイン データを表示すると、次のような疑問に答えることができます。

* アプリケーションをだれが使用しているか。
* 組織内の上位 3 つのアプリケーションはどれか。
* 最近ロールアウトしたアプリケーションは、 どのような状況か。

このデータへのエントリ ポイントは、**[エンタープライズ アプリケーション]** の **[概要]** セクションにある過去 30 日間のレポートに示される、組織内の上位 3 つのアプリケーションです。

![サインイン アクティビティ](./media/active-directory-reporting-activity-sign-ins/64.png "サインイン アクティビティ")

アプリ使用状況グラフは、特定の期間の上位 3 つのアプリへのサインインを週ごとに集計します。 期間の既定値は 30 日です。

![サインイン アクティビティ](./media/active-directory-reporting-activity-sign-ins/47.png "サインイン アクティビティ")

必要に応じて、特定のアプリケーションにフォーカスを設定できます。


![レポート](./media/active-directory-reporting-activity-sign-ins/single_spp_usage_graph.png "レポート")

アプリ使用状況グラフ内の日付をクリックすると、サインイン アクティビティの詳細な一覧が表示されます。


![サインイン アクティビティ](./media/active-directory-reporting-activity-sign-ins/48.png "サインイン アクティビティ")


**[サインイン]** オプションを使用すると、アプリケーションへのすべてのサインイン イベントの完全な概要を表示できます。

![サインイン アクティビティ](./media/active-directory-reporting-activity-sign-ins/49.png "サインイン アクティビティ")



## <a name="next-steps"></a>次のステップ

サインイン アクティビティのエラー コードの詳細については、「[Azure Active Directory ポータルのサインイン アクティビティ レポートのエラー コード](active-directory-reporting-activity-sign-ins-errors.md)」を参照してください。

