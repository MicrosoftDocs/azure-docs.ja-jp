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
ms.date: 02/09/2016
ms.author: asmalser
translationtype: Human Translation
ms.sourcegitcommit: 6b77e338e1c7f0f79ea3c25b0b073296f7de0dcf
ms.openlocfilehash: 1bee9573e23e1c814626d19a3df1eb7ab12486ab


---
# <a name="automate-user-provisioning-and-deprovisioning-to-saas-applications-with-azure-active-directory"></a>Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化
## <a name="what-is-automated-user-provisioning-for-saas-apps"></a>SaaS アプリへのユーザー プロビジョニングの自動化とは
Azure Active Directory (Azure AD) を使用すると、Dropbox、Salesforce、ServiceNow などのクラウド ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) アプリケーションで、ユーザー ID の作成、保守、削除を自動化できます。

**以下に、この機能を使用して行うことができる例をいくつか示します。**

* 新しいユーザーがチームに参加するときに、適切な SaaS アプリで新しいアカウントを自動的に作成できます。
* ユーザーがチームを離れなければならないときに、SaaS アプリのアカウントを自動で非アクティブ化できます。
* SaaS アプリの ID は、ディレクトリの変更に基づいて最新の状態に保たれることが保証されます。
* ユーザー以外のオブジェクト (グループなど) をサポートする SaaS アプリにプロビジョニングします。

**自動化されたユーザー プロビジョニングには、次の機能も含まれています。**

* Azure AD と SaaS アプリ間で既存の ID を一致させる機能。
* 組織が使用している SaaS アプリの現在の構成を Azure AD で調整するのに役立つカスタマイズ オプション。
* プロビジョニング エラーのためのオプションの電子メール通知。
* 監視とトラブルシューティングに役立つレポートとアクティビティ ログ。

## <a name="why-use-automated-provisioning"></a>自動プロビジョニングを使用する理由
この機能を使用する一般的な動機は、次のとおりです。

* 手動によるプロビジョニング プロセスに関連するコスト、非効率性、人的エラーを回避するため。
* ユーザーが組織を離れるときに、主要な SaaS アプリからその ID をすぐに削除することで、組織のセキュリティを高めるため。
* 大量のユーザーを特定の SaaS アプリに簡単にインポートするため。
* Azure AD シングル サインオン用に定義された同じアプリケーションのアクセス ポリシーからプロビジョニング ソリューションを実行させる利便性を享受するため。

## <a name="frequently-asked-questions"></a>よく寄せられる質問
**Azure AD はどのくらいの頻度で SaaS アプリにディレクトリの変更を書き込みますか。**

Azure AD は 5 ～ 10 分ごとに変更を確認します。 SaaS アプリが複数のエラーを返す場合 (管理者資格情報が無効な場合など)、Azure AD は、エラーが修正されるまでその頻度を 1 日 1 回まで徐々に遅らせます。

**自分のユーザーをプロビジョニングするにはどのくらいの時間がかかりますか。**

増分の変更は瞬時に行われますが、ディレクトリの大部分をプロビジョニングする場合は、設定するユーザーとグループの数によって異なります。 小規模のディレクトリでわずか 2 ～ 3 分、中規模のディレクトリで数分、非常に大規模なディレクトリでは数時間かかる場合があります。

**現在のプロビジョニング ジョブの進行状況を追跡する方法はありますか。**

ディレクトリのレポート セクションにあるアカウント プロビジョニング レポートで確認できます。 これとは別に、プロビジョニングしている SaaS アプリの [ダッシュボード] タブにアクセスし、ページの下の方にある [統合の状態] セクションを確認する方法もあります。

**ユーザーがプロビジョニングに失敗した場合はどのようにしてわかりますか。**

プロビジョニング構成ウィザードの最後に、プロビジョニング障害用の電子メール通知をサブスクライブするオプションがあります。 さらに、プロビジョニング エラー レポートを調べて、プロビジョニングに失敗したユーザーとその理由を確認できます。

**Azure AD は SaaS アプリからディレクトリに変更を書き込むことができますか。**

ほとんどの SaaS アプリのプロビジョニングは送信のみです。つまり、ユーザーはディレクトリからアプリに書き込まれ、アプリの変更をディレクトリに書き込むことはできません。 ただし、[Workday](https://msdn.microsoft.com/library/azure/dn762434.aspx) の場合、プロビジョニングは受信のみであるため、ユーザーは Workday からディレクトリにインポートされ、同様にディレクトリの変更を Workday に書き込むことはできません。

**エンジニアリング チームにフィードバックを送信する方法はありますか。**

[Azure Active Directory フィードバック フォーラム](https://feedback.azure.com/forums/169401-azure-active-directory/)からお問い合わせください。

## <a name="how-does-automated-provisioning-work"></a>自動プロビジョニングのしくみ
Azure AD は、各アプリケーション ベンダーによって提供されるプロビジョニング エンドポイントに接続することで、SaaS アプリにユーザーをプロビジョニングします。 これらのエンドポイントを使用して、Azure AD はプログラムによってユーザーを作成、更新、削除します。 Azure AD がプロビジョニングを自動化するために行うさまざまな手順の概要を以下に示します。

1. 初めてアプリのプロビジョニングを有効にする場合は、次の操作が実行されます。
   * Azure AD は、SaaS アプリ内の既存のユーザーとディレクトリ内の対応する ID を一致させようとします。 ユーザーが一致したとき、シングル サインオンは自動的に有効に *なりません* 。 ユーザーがアプリケーションにアクセスできるようにするには、直接またはグループのメンバーシップを介して、ユーザーを Azure AD のアプリに明示的に割り当てる必要があります。
   * アプリに割り当てられるユーザーを既に指定している場合や、Azure AD がこれらのユーザーに対する既存のアカウントを見つけることができない場合は、Azure AD はそのアプリに新しいアカウントをプロビジョニングします。
2. 初期同期が完了すると、上記で説明したように Azure AD は 10 分ごとに次の変更を確認します。
   * 新しいユーザーがアプリに割り当てられている場合 (直接またはグループ メンバーシップを通じて) は、SaaS アプリに新しいアカウントがプロビジョニングされます。
   * ユーザーのアクセスが削除されている場合は、SaaS アプリ内のそのユーザーのアカウントは無効として設定されます (構成が正しくない場合のデータ損失を回避するためユーザーは完全には削除されません)。
   * ユーザーが最近アプリに割り当てられ、既に SaaS アプリにアカウントを持っている場合、そのアカウントは有効として設定され、特定のユーザー プロパティがディレクトリと比較して最新ではない場合は、更新されることがあります。
   * ユーザーの情報 (電話番号、勤務先所在地など) がディレクトリで変更された場合は、SaaS アプリでもその情報が更新されます。

Azure AD とSaaS アプリ間で属性がマップされる方法の詳細については、「 [属性マッピングのカスタマイズ](active-directory-saas-customizing-attribute-mappings.md)」の記事をご覧ください。

## <a name="list-of-apps-that-support-automated-user-provisioning"></a>自動ユーザー プロビジョニングをサポートするアプリの一覧
アプリをクリックして、自動プロビジョニングの構成方法に関するチュートリアルをご覧ください。

* [Box](http://go.microsoft.com/fwlink/?LinkId=286016)
* [Citrix GoToMeeting](http://go.microsoft.com/fwlink/?LinkId=309580)
* [Concur](http://go.microsoft.com/fwlink/?LinkId=309575)
* [Docusign](http://go.microsoft.com/fwlink/?LinkId=403254)
* [Dropbox for Business](http://go.microsoft.com/fwlink/?LinkId=309581)
* [Google Apps](http://go.microsoft.com/fwlink/?LinkId=309577)
* [Jive](http://go.microsoft.com/fwlink/?LinkId=309591)
* [Salesforce](http://go.microsoft.com/fwlink/?LinkId=286017)
* [Salesforce Sandbox](http://go.microsoft.com/fwlink/?LinkId=327869)
* [ServiceNow](http://go.microsoft.com/fwlink/?LinkId=309587)
* [Workday](http://go.microsoft.com/fwlink/?LinkId=690250) (受信のプロビジョニング)

自動ユーザー プロビジョニングをサポートするアプリに対して、まず、外部のプログラムでユーザーの作成、保守、削除を自動化するのに必要なエンドポイントを提供する必要があります。 そのため、すべての SaaS アプリがこの機能と互換性があるとは限りません。 これをサポートするアプリでは、Azure AD エンジニア リング チームがこれらのアプリケーションにプロビジョニング コネクタを作成でき、この作業の優先順位は現在と将来の顧客のニーズによって決まります。

他のアプリのプロビジョニングに関するサポートを要求するために Azure AD エンジニア リング チームに問い合わせる場合は、 [Azure Active Directory フィードバック フォーラム](https://feedback.azure.com/forums/169401-azure-active-directory/)からメッセージを送信してください。

## <a name="related-articles"></a>関連記事
* [Article Index for Application Management in Azure Active Directory](active-directory-apps-index.md)
* [ユーザーのプロビジョニング用の属性マッピングのカスタマイズ](active-directory-saas-customizing-attribute-mappings.md)
* [属性マッピングの式の書き方](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [ユーザーのプロビジョニング用のフィルターのスコープ](active-directory-saas-scoping-filters.md)
* [SCIM を使用して、Azure Active Directory からアプリケーションへのユーザーとグループの自動プロビジョニングを有効にする](active-directory-scim-provisioning.md)
* [アカウント プロビジョニング通知](active-directory-saas-account-provisioning-notifications.md)
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)




<!--HONumber=Dec16_HO5-->


