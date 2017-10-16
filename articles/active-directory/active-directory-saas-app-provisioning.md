---
title: "Azure AD での SaaS アプリ ユーザー プロビジョニングの自動化 | Microsoft Docs"
description: "Azure AD を使用して、複数のサードパーティ SaaS アプリケーション間でユーザー アカウントを自動的にプロビジョニング、プロビジョニング解除、継続的に更新する方法の紹介。"
services: active-directory
documentationcenter: 
author: asmalser-msft
manager: femila
editor: 
ms.assetid: 58c5fa2d-bb33-4fba-8742-4441adf2cb62
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2017
ms.author: asmalser
ms.openlocfilehash: 3fe57e9c22d04a3557978093ce3fe86613c5c1d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="automate-user-provisioning-and-deprovisioning-to-saas-applications-with-azure-active-directory"></a>Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化
## <a name="what-is-automated-user-provisioning-for-saas-apps"></a>SaaS アプリへのユーザー プロビジョニングの自動化とは
Azure Active Directory (Azure AD) を使用すると、Dropbox、Salesforce、ServiceNow などのクラウド ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) アプリケーションで、ユーザー ID の作成、保守、削除を自動化できます。

**以下に、この機能を使用して行うことができる例をいくつか示します。**

* 新しいユーザーがチームまたは組織に加わるときに、適切なシステムで新しいアカウントを自動的に作成できます。
* ユーザーがチームまたは組織を離れるときに、適切なシステムでアカウントを自動的に非アクティブ化できます。
* アプリとシステムの ID は、ディレクトリ (人事システム) の変更に基づいて最新の状態に保たれることが保証されます。
* ユーザー以外のオブジェクト (グループなど) をサポートするアプリケーションにそれらをプロビジョニングします。

**自動化されたユーザー プロビジョニングには、次の機能も含まれています。**

* ソース システムとターゲット システムとの間で既存の ID を一致させる機能。
* 組織が使用しているアプリとシステムの現在の構成を Azure AD で調整するのに役立つカスタマイズ オプション。
* プロビジョニング エラーのためのオプションの電子メール通知。
* 監視とトラブルシューティングに役立つレポートとアクティビティ ログ。

## <a name="why-use-automated-provisioning"></a>自動プロビジョニングを使用する理由
この機能を使用する一般的な動機は、次のとおりです。

* 手動によるプロビジョニング プロセスに関連するコスト、非効率性、人的エラーを回避するため。
* 独自開発のプロビジョニング ソリューションやプロビジョニング スクリプトのホスティングとメンテナンスに伴うコストを避けるため。
* ユーザーが組織を離れるときに、主要な SaaS アプリからその ID をすぐに削除することで、組織のセキュリティを高めるため。
* 大量のユーザーを特定の SaaS アプリまたは SaaS システムに簡単にインポートするため。
* Azure AD シングル サインオン用に定義された同じアプリケーションのアクセス ポリシーからプロビジョニング ソリューションを実行させる利便性を享受するため。


## <a name="how-does-automatic-provisioning-work"></a>自動プロビジョニングのしくみ
    
**Azure AD プロビジョニング サービス**は、SaaS アプリや他のシステムに対してユーザーをプロビジョニングするために、各アプリケーション ベンダーから提供されるユーザー管理 API エンドポイントに接続します。 これらのユーザー管理 API エンドポイントを使用すると、Azure AD ではプログラムによってユーザーを作成、更新、削除できます。 また、一部の限られたアプリケーションについては、ID に関連したその他のオブジェクト (グループ、ロールなど) をプロビジョニング サービスで作成、更新、削除することができます。 

![プロビジョニング](./media/active-directory-saas-app-provisioning/provisioning0.PNG)
*図 1: Azure AD プロビジョニング サービス*

![出力方向のプロビジョニング](./media/active-directory-saas-app-provisioning/provisioning1.PNG)
*図 2: Azure AD から主要 SaaS アプリケーションへの "出力方向" のユーザー プロビジョニング ワークフロー*

![入力方向のプロビジョニング](./media/active-directory-saas-app-provisioning/provisioning2.PNG)
*図 3: 主要な人材管理 (HCM) アプリケーションから Azure Active Directory および Windows Server Active Directory への "入力方向" のユーザー プロビジョニング ワークフロー*


## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Azure AD の自動ユーザー プロビジョニングで利用できるアプリケーションとシステム

Azure AD は、一般に普及しているさまざまな SaaS アプリや人事管理システムとの連携にあらかじめ対応しているほか、[SCIM 2.0 標準](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-scim-provisioning)の特定の領域を実装するアプリにも広く対応しています。

Azure AD アプリケーション ギャラリーの "おすすめ" アプリのすべてで、自動的なユーザー プロビジョニングがサポートされています。 おすすめアプリの一覧を表示するには、[こちら](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1&subcategories=featured)をクリックしてください。

アプリで自動ユーザー プロビジョニングをサポートするには、まず、外部のプログラムでユーザーの作成、保守、削除を自動化するのに必要なユーザー管理エンドポイントを提供する必要があります。 そのため、すべての SaaS アプリがこの機能と互換性があるとは限りません。 ユーザー管理 API をサポートするアプリでは、Azure AD エンジニア リング チームがこれらのアプリケーションにプロビジョニング コネクタを作成できるようになります。この作業の優先順位は現在の顧客と見込み顧客のニーズによって決まります。 

他のアプリのプロビジョニングのサポートを要求するために Azure AD エンジニア リング チームに問い合わせる場合は、[Azure Active Directory フィードバック フォーラム](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests/category/172035-user-provisioning)からメッセージを送信してください。 
    
    
## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>アプリケーションへの自動プロビジョニングを設定する方法

選択したアプリケーション用の Azure AD プロビジョニング サービスの構成は、**[Azure Portal](https://potal.azure.com)** から開始します。 **[Azure Active Directory] > [エンタープライズ アプリケーション]** セクションで **[追加]** を選択し、**[すべて]** を選択した後、実際のシナリオに応じて次のいずれかを追加します。

* **[注目のアプリケーション]** セクションに表示されるすべてのアプリケーションは自動プロビジョニングに対応しています。

* 独自開発の SCIM 統合には、[ギャラリー以外のアプリケーション] オプションを使用します。

![[ギャラリー]](./media/active-directory-saas-app-provisioning/gallery.png)

プロビジョニングの構成は、アプリケーション管理画面の **[プロビジョニング]** タブで行います。

![設定](./media/active-directory-saas-app-provisioning/provisioning_settings0.PNG)


* Azure AD プロビジョニング サービスには、アプリケーションが提供するユーザー管理 API に接続できるように**管理者資格情報**を指定する必要があります。

* ソース システム (例: Azure AD) のどのフィールドの内容をターゲット システム (例: ServiceNow) のどのフィールドと同期させるかを指定する**属性マッピング**を構成できます。 ターゲット アプリケーションが対応していれば、ユーザー アカウントだけでなく、必要に応じてグループのプロビジョニングもこのセクションで構成することができます。 システム間のアカウントの照合に使用するフィールドは、[Matching properties]\(一致するプロパティ\) で選択できます。 "[式](active-directory-saas-writing-expressions-for-attribute-mappings.md)" を使用すると、ソース システムから取得した値をターゲット システムに書き込む前に変更または変換できます。 詳細については、[属性マッピングのカスタマイズ](active-directory-saas-customizing-attribute-mappings.md)に関するページを参照してください。

![設定](./media/active-directory-saas-app-provisioning/provisioning_settings1.PNG)

* **スコープ フィルター**により、ターゲット システムへのプロビジョニング/プロビジョニング解除の対象となるソース システム内のユーザーとグループをプロビジョニング サービスに指定します。 スコープ フィルターでは、次の 2 つの側面を総合的に評価してプロビジョニング対象ユーザーが決定されます。

* **属性の値に基づくフィルター** - 属性マッピングの [ソース オブジェクト スコープ] メニューで、特定の属性値に基づいてフィルター処理できます。 たとえば、"Department" 属性が "Sales" であるユーザーのみをプロビジョニングの対象として指定することができます。

* **割り当てに基づくフィルター** - "割り当て済み" のユーザーとグループだけをプロビジョニングの対象とするか、Azure AD ディレクトリ内のすべてのユーザーをプロビジョニングの対象とするかを、ポータルの [プロビジョニング] の [設定] セクションにある [スコープ] メニューで指定できます。 ユーザーとグループの "割り当て" の詳細については、「[Azure Active Directory でエンタープライズ アプリにユーザーまたはグループを割り当てる](active-directory-coreapps-assign-user-azure-portal.md)」を参照してください。
    
* 実行中であるかどうかを含め、アプリケーションのプロビジョニング サービスの動作は、**[設定]** で制御します。

* **[監査ログ]** には、Azure AD プロビジョニング サービスによって実行された各操作の記録が表示されます。 詳細については、[プロビジョニング レポートに関するガイド](active-directory-saas-provisioning-reporting.md)を参照してください。

![設定](./media/active-directory-saas-app-provisioning/audit_logs.PNG)

## <a name="what-happens-during-provisioning"></a>プロビジョニング中の動作

1. 初めてアプリのプロビジョニングを有効にする場合は、次の操作が実行されます。
   * Azure AD は、SaaS アプリ内の既存のユーザーとディレクトリ内の対応する ID を一致させようとします。 ユーザーが一致したとき、シングル サインオンは自動的に有効に *なりません* 。 ユーザーがアプリケーションにアクセスできるようにするには、直接またはグループのメンバーシップを介して、ユーザーを Azure AD のアプリに明示的に割り当てる必要があります。
   * アプリに割り当てられるユーザーを既に指定している場合や、Azure AD がこれらのユーザーに対する既存のアカウントを見つけることができない場合は、Azure AD はそのアプリに新しいアカウントをプロビジョニングします。
2. 初期同期が完了すると、上記で説明したように Azure AD は 20 分ごとに次の変更を確認します。
   * 新しいユーザーがアプリに (直接またはグループ メンバーシップを通じて) 割り当てられている場合は、SaaS アプリに新しいアカウントでプロビジョニングされます。
   * ユーザーのアクセスが削除されている場合は、SaaS アプリ内のそのユーザーのアカウントは無効として設定されます (構成が正しくない場合のデータ損失を回避するためユーザーは完全には削除されません)。
   * ユーザーが最近アプリに割り当てられ、既に SaaS アプリにアカウントを持っている場合、そのアカウントは有効として設定され、特定のユーザー プロパティがディレクトリと比較して最新ではない場合は、更新されることがあります。
   * ユーザーの情報 (電話番号、勤務先所在地など) がディレクトリで変更された場合は、SaaS アプリでもその情報が更新されます。


## <a name="frequently-asked-questions"></a>よく寄せられる質問
**Azure AD はどのくらいの頻度で SaaS アプリにディレクトリの変更を書き込みますか。**

初回完全同期が完了した後は、通常、Azure AD プロビジョニング サービスによって変更の有無が 20 分おきにチェックされます。 

SaaS アプリが複数のエラーを返す場合 (管理者資格情報が無効な場合など)、Azure AD は、エラーが修正されるまでその頻度を 1 日 1 回まで徐々に遅らせます。 このような場合、Azure AD プロビジョニング ジョブが "検疫" の状態に移行し、[プロビジョニング サマリー レポート](active-directory-saas-provisioning-reporting.md)にその旨が記録されます。

**自分のユーザーをプロビジョニングするにはどのくらいの時間がかかりますか。**

初回完全同期が完了した後、通常、20 ～ 40 分以内に増分の変更が適用されます。 ディレクトリの大部分をプロビジョニングする場合は、設定したユーザーとグループの数によって異なります。 パフォーマンスは、プロビジョニング サービスがソース システムからのデータの読み取りとターゲット システムへのデータの書き込みに使用するユーザー管理 API のパフォーマンスに左右されます。 

また、多くのエラーが発生し ([監査ログ](active-directory-saas-provisioning-reporting.md)に記録されています)、プロビジョニング サービスが "検疫" 状態に移行した場合、パフォーマンスは低下します。

**初回完全同期とは何でしょうか。また、それ以降の同期と比べて時間がかかるのはなぜですか。**

特定のアプリを対象に Azure AD プロビジョニング サービスが初めて実行されるとき、ソース ディレクトリのユーザー (と必要に応じてグループ) の "スナップショット" が作成されます。 このスナップショットにより、プロビジョニング サービスとソース/ターゲット API との間で発生するラウンド トリップの回数を抑え、それ以降の "差分" 同期を効率よく行うことができます。 

ユーザーの初回完全同期は、ディレクトリがごく小さければ数分で完了することも珍しくありませんが、ディレクトリの規模が大きいと数時間かかる場合があります。 数十万のユーザーを擁する企業のディレクトリになると、初回同期に何時間もかかると考えられます。 ただし初回同期が完了すれば、それ以降の "差分" 同期は、初回に比べてはるかに短い時間で完了します。

> [!NOTE]
> グループのプロビジョニングとグループ メンバーシップのプロビジョニングをサポートするアプリケーションで、それを有効にすると、完全同期の所要時間がかなり長くなります。 グループ名やグループ メンバーシップをアプリケーションにプロビジョニングする必要がない場合は、プロビジョニング構成の[属性マッピング](active-directory-saas-customizing-attribute-mappings.md)でその機能を無効にすることができます。

**現在のプロビジョニング ジョブの進行状況を追跡する方法はありますか。**

[プロビジョニング レポートに関するガイド](active-directory-saas-provisioning-reporting.md)を参照してください。

**ユーザーがプロビジョニングに失敗した場合はどのようにしてわかりますか。**

エラーはすべて Azure AD の監査ログに記録されます。 詳細については、[プロビジョニング レポートに関するガイド](active-directory-saas-provisioning-reporting.md)を参照してください。

**エンジニアリング チームにフィードバックを送信する方法はありますか。**

[Azure Active Directory フィードバック フォーラム](https://feedback.azure.com/forums/169401-azure-active-directory/)からご連絡ください。


## <a name="related-articles"></a>関連記事
* [Article Index for Application Management in Azure Active Directory](active-directory-apps-index.md)
* [ユーザーのプロビジョニング用の属性マッピングのカスタマイズ](active-directory-saas-customizing-attribute-mappings.md)
* [属性マッピングの式の書き方](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [ユーザーのプロビジョニング用のフィルターのスコープ](active-directory-saas-scoping-filters.md)
* [SCIM を使用して、Azure Active Directory からアプリケーションへのユーザーとグループの自動プロビジョニングを有効にする](active-directory-scim-provisioning.md)
* [アカウント プロビジョニング通知](active-directory-saas-account-provisioning-notifications.md)
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)

