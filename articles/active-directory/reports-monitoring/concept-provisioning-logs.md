---
title: Azure Active Directory ポータルのプロビジョニング ログ (プレビュー) | Microsoft Docs
description: Azure Active Directory ポータルのプロビジョニング アクティビティ レポートの概要
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 07/29/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d48aa3ead28ab0b0a22478a0c4183995483058a
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2019
ms.locfileid: "70983494"
---
# <a name="provisioning-reports-in-the-azure-active-directory-portal-preview"></a>Azure Active Directory ポータルのプロビジョニング レポート (プレビュー)

Azure Active Directory (Azure AD) のレポート アーキテクチャは、次のコンポーネントで構成されます。

- **アクティビティ** 
    - **サインイン** – マネージド アプリケーションの使用状況とユーザー サインイン アクティビティに関する情報。
    - **監査ログ** - [監査ログ](concept-audit-logs.md)は、ユーザーとグループの管理や、マネージド アプリケーションとディレクトリのアクティビティに関するシステム アクティビティ情報を提供します。
    - **プロビジョニング ログ** - Azure AD プロビジョニング サービスによってプロビジョニングされたユーザー、グループ、およびロールに関するシステム アクティビティを提供します。 

- **セキュリティ** 
    - **リスクの高いサインイン** - [リスクの高いサインイン](concept-risky-sign-ins.md)は、ユーザー アカウントの正当な所有者ではない人によって行われた可能性があるサインイン試行の指標です。
    - **リスクのフラグ付きユーザー** - [リスクの高いユーザー](concept-user-at-risk.md)は、侵害された可能性があるユーザー アカウントの指標です。

このトピックでは、プロビジョニング レポートの概要を説明します。

## <a name="prerequisites"></a>前提条件

### <a name="who-can-access-the-data"></a>誰がデータにアクセスできますか。
* セキュリティ管理者、セキュリティ閲覧者、レポート閲覧者、アプリケーション管理者、クラウド アプリケーション管理者のいずれかのロールであるユーザー
* グローバル管理者


### <a name="what-azure-ad-license-do-you-need-to-access-provisioning-activities"></a>プロビジョニング アクティビティにアクセスするために必要な Azure AD ライセンス

すべてのプロビジョニング アクティビティ レポートを閲覧するためには、ご利用のテナントに、Azure AD Premium ライセンスが関連付けられている必要があります。 Azure Active Directory エディションにアップグレードするには、「[Azure Active Directory Premium の概要](../fundamentals/active-directory-get-started-premium.md)」を参照してください。 

## <a name="provisioning-logs"></a>プロビジョニング ログ

プロビジョニング ログから、次の情報を得ることができます。

* ServiceNow で正常に作成されたグループ
* アマゾン ウェブ サービスからロールがインポートされた方法
* DropBox で作成に失敗したユーザー

[Azure portal](https://portal.azure.com) の **[Azure Active Directory]** ブレードの **[監視]** セクションで **[プロビジョニング ログ]** を選択して、プロビジョニング レポートにアクセスできます。 プロビジョニング レコードによっては、ポータルに表示されるまでに最大 2 時間かかるものもあります。

![プロビジョニング ログ](./media/concept-provisioning-logs/access-provisioning-logs.png "プロビジョニング ログ")


プロビジョニング ログには、次のものを示す既定のリスト ビューがあります。

- ID
- アクション
- ソース システム
- ターゲット システム
- 状態
- 日付


![既定の列](./media/concept-provisioning-logs/default-columns.png "既定の列")

リスト ビューをカスタマイズするには、ツール バーの **[列]** をクリックします。

![列の選択](./media/concept-provisioning-logs/column-chooser.png "列の選択")

これで、追加のフィールドの表示または既に表示されているフィールドの削除ができます。

![使用可能な列](./media/concept-provisioning-logs/available-columns.png "使用可能な列")

詳細な情報を取得するには、リスト ビューで項目を選択します。

![詳細情報](./media/concept-provisioning-logs/steps.png "フィルター")


## <a name="filter-provisioning-activities"></a>プロビジョニング アクティビティのフィルター処理

報告されるデータを有用なものだけに絞り込むために、次の既定のフィールドを使用してプロビジョニング データをフィルター処理できます。 フィルターの値は、テナントに基づいて動的に設定されることに注意してください。 たとえば、テナントに作成イベントがない場合は、作成のフィルター オプションはありません。

- ID
- Action
- ソース システム
- ターゲット システム
- Status
- Date


![フィルター](./media/concept-provisioning-logs/filter.png "フィルター")

**ID** フィルターを使用すると、関心のある名前または ID を指定できます。 この ID には、ユーザー、グループ、ロール、またはその他のオブジェクトを指定できます。 オブジェクトの名前または ID で検索できます。 ID はシナリオによって異なります。 たとえば、Azure AD から SalesForce にオブジェクトをプロビジョニングする場合、ソース ID は Azure AD 内のユーザーのオブジェクト ID であり、ターゲット ID は Salesforce のユーザーの ID です。 Workday から Active Directory にプロビジョニングする場合、ソース ID は Workday ワーカーの従業員 ID です。 ユーザーの名前が必ずしも ID 列に存在するとは限らないことに注意してください。 常に 1 つの ID が存在します。 

**[ソース システム]** フィルターでは、ID のプロビジョニング元となる場所を指定できます。 たとえば、Azure AD から ServiceNow にオブジェクトをプロビジョニングする場合、ソース システムは Azure AD です。 

**[ターゲット システム]** フィルターでは、ID のプロビジョニング先となる場所を指定できます。 たとえば、Azure AD から ServiceNow にオブジェクトをプロビジョニングする場合、ターゲット システムは ServiceNow です。 

**[状態]** フィルターでは、次のいずれかを選択できます。

- All
- Success
- 失敗
- Skipped

**[アクション]** フィルターでは、次のものをフィルター処理できます。

- 作成 
- アップデート
- 削除
- Disable
- その他

**[日付]** フィルターでは、返されるデータの期間を定義できます。  
次のいずれかの値になります。

- 1 か月
- 7 日
- 30 日
- 24 時間
- カスタム期間

カスタムの期間を選択すると、開始日と終了日を構成できます。


既定のフィールドに加えて、フィルターに次のフィールドを含めることもできます (選択した場合)。

- **[ジョブ ID]** - プロビジョニングを有効にした各アプリケーションに、一意のジョブ ID が関連付けられます。   

- **[サイクル ID]** - プロビジョニング サイクルを一意に識別します。 この ID をサポートと共有して、このイベントが発生したサイクルを調べることができます。

- **[変更 ID]** - プロビジョニング イベントの一意の識別子。 この ID をサポートと共有して、プロビジョニング イベントを検索できます。   



  

## <a name="provisioning-details"></a>プロビジョニングの詳細 

プロビジョニング リスト ビューで項目を選択すると、この項目の詳細が表示されます。
詳細は、次のカテゴリに基づいてグループ化されます。

- 手順

- トラブルシューティングと推奨事項

- 変更されたプロパティ

- まとめ


![フィルター](./media/concept-provisioning-logs/provisioning-tabs.png "タブ")



### <a name="steps"></a>手順

**[ステップ]** タブには、オブジェクトをプロビジョニングするために実行される手順の概要が示されます。 オブジェクトのプロビジョニングは次の 4 つの手順で構成されます。 

- オブジェクトをインポートする
- オブジェクトがスコープ内にあるかどうかを判断する
- ソースとターゲットの間でオブジェクトを一致させる
- オブジェクトをプロビジョニングする (アクションの実行 - 作成、更新、削除、または無効化)



![フィルター](./media/concept-provisioning-logs/steps.png "フィルター")


### <a name="troubleshoot-and-recommendations"></a>トラブルシューティングと推奨事項


**[トラブルシューティングと推奨事項]** タブには、エラー コードと理由が表示されます。 エラー情報は、エラーが発生した場合にのみ提供されます。 


### <a name="modified-properties"></a>変更されたプロパティ

**[変更されたプロパティ]** には、古い値と新しい値が表示されます。 古い値が存在しない場合、古い値の列は空白になります。 


### <a name="summary"></a>まとめ

**[概要]** タブには、ソース システムとターゲット システムのオブジェクトの発生内容の概要と識別子が表示されます。 

## <a name="what-you-should-know"></a>知っておくべきこと

- 報告されたプロビジョニング データは、Premium Edition の場合は 30 日間、Free Edition の場合は 7 日間、Azure portal に保存されます。

- [変更 ID] 属性は、一意の識別子として使用できます。 これは、たとえば製品サポートとやり取りするときに便利です。

- 現在、プロビジョニング データをダウンロードするオプションはありません。

- ログの分析は、現時点ではサポートされていません。

- アプリのコンテキストからプロビジョニング ログにアクセスしても、監査ログのように、イベントが特定のアプリに自動的にフィルター処理されることはありません。

## <a name="next-steps"></a>次の手順

* [ユーザー プロビジョニングの状態を確認する](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)
* [Azure AD ギャラリー アプリケーションへのユーザー プロビジョニングの構成に関する問題](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem)


