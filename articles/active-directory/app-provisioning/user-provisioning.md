---
title: Azure AD での SaaS アプリ ユーザー プロビジョニングの自動化とは
description: Azure AD を使用して、複数のサードパーティ SaaS アプリケーション間でユーザー アカウントを自動的にプロビジョニング、プロビジョニング解除、継続的に更新する方法の紹介。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: overview
ms.workload: identity
ms.date: 02/08/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: f899c4d67321482dab62729632fe031bb23f71ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99830670"
---
# <a name="what-is-automated-saas-app-user-provisioning-in-azure-ad"></a>Azure AD での SaaS アプリ ユーザー プロビジョニングの自動化とは

Azure Active Directory (Azure AD) での **アプリ プロビジョニング** という用語は、ユーザーがアクセスする必要のあるクラウド ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) アプリケーションにおいてユーザーの ID とロールを自動的に作成することを意味します。 自動プロビジョニングには、ユーザー ID の作成に加えて、状態または役割が変化したときのユーザー ID のメンテナンスおよび削除が含まれます。 一般的なシナリオには、[Dropbox](../saas-apps/dropboxforbusiness-provisioning-tutorial.md)、[Salesforce](../saas-apps/salesforce-provisioning-tutorial.md)、[ServiceNow](../saas-apps/servicenow-provisioning-tutorial.md) などのアプリケーションへの Azure AD ユーザーのプロビジョニングが含まれます。

Azure AD におけるアプリケーション管理とシングル サインオン (SSO) が初めてである方は、 [一連のクイックスタート](../manage-apps/view-applications-portal.md)をご覧ください。

SCIM についての詳しい情報を入手し、Tech Community のスレッドに参加するには、[SCIM でのプロビジョニングに関するテクニカル コミュニティ](https://aka.ms/scimoverview)のページを参照してください。

![プロビジョニングの概要図](./media/user-provisioning/provisioning-overview.png)

この機能を使用すると次のことができます。

- **プロビジョニングを自動化する:** 新しいユーザーがチームまたは組織に加わるときに、適切なシステムで新しいアカウントを自動的に作成できます。
- **プロビジョニング解除を自動化する:** ユーザーがチームまたは組織を離れるときに、適切なシステムでアカウントを自動的に非アクティブ化できます。
- **システム間でデータを同期する:** アプリとシステムの ID は、ディレクトリ (人事システム) での変更に基づいて最新の状態に保たれることが保証されます。
- **グループをプロビジョニングする:** グループをサポートするアプリケーションにグループをプロビジョニングします。
- **アクセスを管理する:** アプリケーションにプロビジョニングされたユーザーの監視と監査を行います。
- **ブラウン フィールドのシナリオでシームレスにデプロイする:** ターゲット システムにユーザーが既に存在する場合でも、システム間で既存の ID を一致させ、簡単に統合できるようにします。
- **リッチなカスタマイズを使用する:** ソース システムからターゲット システムにフローする必要があるユーザー データが定義されている、カスタマイズ可能な属性マッピングを利用します。
- **重大なイベントのアラートを受け取る:** プロビジョニング サービスでは、重大なイベントに対するアラートが提供され、ビジネス ニーズに合わせてカスタム アラートを定義できる Log Analytics の統合が可能になります。

## <a name="benefits-of-automatic-provisioning"></a>自動プロビジョニングの利点

現代の組織では使用されるアプリケーションの数が増え続けており、IT 管理者は大規模なアクセス管理を強いられます。 Security Assertion Markup Language (SAML) や Open ID Connect (OIDC) などの標準を使用すると、管理者はシングル サインオン (SSO) をすばやく設定できますが、アクセスするにはユーザーをアプリにプロビジョニングする必要もあります。 多くの管理者にとって、プロビジョニングとは、すべてのユーザー アカウントを手動で作成すること、または毎週 CSV ファイルをアップロードすることを意味しますが、これらのプロセスは時間がかかり、コストがかかり、エラーが発生しやすくなります。 プロビジョニングを自動化するために SAML Just-In-Time (JIT) などのソリューションが採用されていますが、企業では、ユーザーが組織からいなくなる場合や、役割の変更のために特定のアプリにアクセスする必要がなくなる場合に、ユーザーのプロビジョニングを解除するためのソリューションも必要です。

自動プロビジョニングを使用する一般的な動機には、次のようなものがあります。

- プロビジョニング プロセスの効率と正確さを最大限に高める。
- 独自に開発したプロビジョニング ソリューションやプロビジョニング スクリプトのホスティングとメンテナンスに伴うコストを抑える。
- ユーザーが組織を離れるときに、主要な SaaS アプリからその ID をすぐに削除することで、組織のセキュリティを高める。
- 多くのユーザーを特定の SaaS アプリまたは SaaS システムに簡単にインポートする。
- 誰をプロビジョニングし、誰がアプリにサインインできるようにするかを、1 つのポリシー セットで決定する。

Azure AD のユーザー プロビジョニングは、これらの課題に対応するのに役立ちます。 お客様による Azure AD ユーザー プロビジョニングの使用方法について詳しくは、[ASOS のケース スタディ](https://aka.ms/asoscasestudy)をご覧ください。 次のビデオでは、Azure AD でのユーザー プロビジョニングの概要について説明します。

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Azure AD の自動ユーザー プロビジョニングで利用できるアプリケーションとシステム

Azure AD は、一般的な多くの SaaS アプリや人事管理システムとの連携にあらかじめ対応しているほか、[SCIM 2.0 標準](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)の特定の領域を実装するアプリにも広く対応しています。

* **事前統合されたアプリケーション (ギャラリー SaaS アプリ)** 。 Azure AD で事前統合プロビジョニング コネクタがサポートされているすべてのアプリケーションについては、[ユーザー プロビジョニングのためのアプリケーション チュートリアルの一覧](../saas-apps/tutorial-list.md)をご覧ください。 ギャラリーに一覧表示されている事前統合アプリケーションでは、通常、プロビジョニングに SCIM 2.0 ベースのユーザー管理 API が使用されています。 

   ![Salesforce のロゴ](./media/user-provisioning/gallery-app-logos.png)

   プロビジョニングのために新しいアプリケーションを要求したい場合は、[アプリケーションをアプリ ギャラリーと統合するよう要求する](../develop/v2-howto-app-gallery-listing.md)ことができます。 ユーザー プロビジョニング要求の場合、アプリケーションには SCIM 準拠のエンドポイントが必要です。 アプリをプラットフォームに迅速にオンボードできるよう、アプリケーションのベンダーに SCIM 標準に準拠するよう要求してください。

* **SCIM 2.0 をサポートするアプリケーション**。 SCIM 2.0 ベースのユーザー管理 API を実装するアプリケーションを汎用的に接続する方法については、「[SCIM エンドポイントの構築とユーザー プロビジョニングの構成](use-scim-to-provision-users-and-groups.md)」を参照してください。

## <a name="what-is-system-for-cross-domain-identity-management-scim"></a>System for Cross-domain Identity Management (SCIM) とは

プロビジョニングとプロビジョニング解除の自動化を助けるため、アプリでは独自のユーザー API とグループ API を公開します。 ただし、複数のアプリでユーザーを管理しようとすると、すべてのアプリで同じ単純な操作 (ユーザーの作成や更新、ユーザーのグループへの追加、ユーザーのプロビジョニング解除など) が実行されることがわかります。 ただし、これらの単純な操作のすべてが、異なるエンドポイント パス、異なるユーザー情報指定方法、および情報の各要素を表す異なるスキーマを使用して、少しずつ異なる方法で実装されています。

これらの課題に対処するため、SCIM 仕様では、ユーザーのアプリへの移動、アプリからの移動、アプリ内での移動に対し、共通のユーザー スキーマが提供されています。 SCIM は、プロビジョニングに対する事実上の標準になりつつあり、SAML や OpenID Connect などのフェデレーション標準と組み合わせて使用すると、エンドツーエンドの標準ベースのアクセス管理用ソリューションが管理者に提供されます。

アプリケーションに対するユーザーとグループのプロビジョニングおよびプロビジョニング解除を自動化するための SCIM エンドポイントの開発について詳しくは、「[SCIM エンドポイントの構築とユーザー プロビジョニングの構成](use-scim-to-provision-users-and-groups.md)」をご覧ください。 ギャラリー内の事前に統合されたアプリケーション (Slack、Azure Databricks、Snowflake など) については、開発者向けドキュメントをスキップし、[こちら](../saas-apps/tutorial-list.md)のチュートリアルを使用してください。

## <a name="manual-vs-automatic-provisioning"></a>手動プロビジョニングと自動プロビジョニングの比較

Azure AD ギャラリーのアプリケーションは、次の 2 つのプロビジョニング モードのいずれかをサポートしています。

* **手動** プロビジョニングとは、アプリの自動 Azure AD プロビジョニングコネクタがまだないことを意味します。 ユーザー アカウントは手動で作成する必要があります。たとえば、アプリの管理ポータルにユーザーを直接追加したり、ユーザー アカウントの詳細を含むスプレッドシートをアップロードしたりすることでこれを行います。 アプリから提供されるドキュメントを確認するか、アプリの開発者に問い合わせて、どのようなメカニズムが使用できるか判断してください。

* "**自動**" とは、このアプリケーション用の Azure AD プロビジョニング コネクタが開発済みであることを意味します。 そのアプリケーションのプロビジョニングの設定に固有の設定チュートリアルに従う必要があります。 アプリのチュートリアルについては、「[SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](../saas-apps/tutorial-list.md)」を参照してください。

Azure AD ギャラリーでは、自動プロビジョニングをサポートするアプリケーションは、**プロビジョニング** アイコンによって指定されます。 新しいギャラリー プレビュー エクスペリエンスに切り替えてこれらのアイコンを表示します ( **[アプリケーションの追加]** ページの上部にあるバナーで、 **[Click here to try out the new and improved app gallery] (改善された新しいアプリ ギャラリーを試すには、こちらをクリックしてください)** というリンクを選択します)。

![アプリケーション ギャラリーのプロビジョニング アイコン](./media/user-provisioning/browse-gallery.png)

アプリケーションでサポートされているプロビジョニング モードは、アプリケーションを **[エンタープライズ アプリ]** に追加した後の **[プロビジョニング]** タブにも表示されます。

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>アプリケーションへの自動プロビジョニングを設定する方法

ギャラリーに一覧表示される事前統合アプリケーションについては、自動プロビジョニングの設定すに関するステップバイステップのガイダンスが用意されています。 [統合ギャラリー アプリのチュートリアルの一覧](../saas-apps/tutorial-list.md)を参照してください。 次のビデオでは、SalesForce の自動ユーザー プロビジョニングの設定方法が説明されています。

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

SCIM 2.0 をサポートする他のアプリケーションについては、記事「[SCIM エンドポイントの構築とユーザー プロビジョニングの構成](use-scim-to-provision-users-and-groups.md)」の手順に従ってください。


## <a name="next-steps"></a>次の手順

- [SaaS アプリを統合する方法に関するチュートリアルの一覧](../saas-apps/tutorial-list.md)
- [ユーザー プロビジョニング用の属性マッピングのカスタマイズ](customize-application-attributes.md)
- [ユーザー プロビジョニング用のフィルターのスコープ](define-conditional-rules-for-provisioning-user-accounts.md)
