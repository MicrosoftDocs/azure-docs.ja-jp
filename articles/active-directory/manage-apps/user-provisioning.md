---
title: Azure AD での SaaS アプリ ユーザー プロビジョニングの自動化 | Microsoft Docs
description: Azure AD を使用して、複数のサードパーティ SaaS アプリケーション間でユーザー アカウントを自動的にプロビジョニング、プロビジョニング解除、継続的に更新する方法の紹介。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/12/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 68c235f32d003adcddecb98c20f30c517c723617
ms.sourcegitcommit: 0ebc62257be0ab52f524235f8d8ef3353fdaf89e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723947"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-saas-applications-with-azure-active-directory"></a>Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化

Azure Active Directory (Azure AD) を使用すると、Dropbox、Salesforce、ServiceNow などのクラウド ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) アプリケーションで、ユーザー ID の作成、保守、削除を自動化できます。 これは、SaaS アプリの自動化されたユーザー プロビジョニングとして知られています。

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

この機能を使用すると次のことができます。

- 新しいユーザーがチームまたは組織に加わるときに、適切なシステムで新しいアカウントを自動的に作成できます。
- ユーザーがチームまたは組織を離れるときに、適切なシステムでアカウントを自動的に非アクティブ化できます。
- アプリとシステムの ID は、ディレクトリ (人事システム) の変更に基づいて最新の状態に保たれることが保証されます。
- ユーザー以外のオブジェクト (グループなど) をサポートするアプリケーションにそれらをプロビジョニングします。

自動化されたユーザー プロビジョニングには、次の機能も含まれています。

- ソース システムとターゲット システムとの間で既存の ID を一致させる機能。
- ソース システムからターゲット システムにフローするユーザー データを定義する、カスタマイズ可能な属性マッピング。
- プロビジョニング エラーのためのオプションの電子メール通知。
- 監視とトラブルシューティングに役立つレポートとアクティビティ ログ。

## <a name="why-use-automated-provisioning"></a>自動プロビジョニングを使用する理由

この機能を使用する一般的な動機は、次のとおりです。

- 手動によるプロビジョニング プロセスに関連するコスト、非効率性、および人的エラーを回避する。
- 独自に開発したプロビジョニング ソリューションやプロビジョニング スクリプトのホスティングとメンテナンスに伴うコストを回避する。
- ユーザーが組織を離れるときに、主要な SaaS アプリからその ID をすぐに削除することで、組織のセキュリティを高める。
- 多くのユーザーを特定の SaaS アプリまたは SaaS システムに簡単にインポートする。
- 誰をプロビジョニングし、誰がアプリにサインインできるようにするかを、1 つのポリシー セットで決定する。

## <a name="how-does-automatic-provisioning-work"></a>自動プロビジョニングのしくみ

**Azure AD プロビジョニング サービス**は、SaaS アプリや他のシステムに対してユーザーをプロビジョニングするために、各アプリケーション ベンダーから提供されるユーザー管理 API エンドポイントに接続します。 これらのユーザー管理 API エンドポイントを使用すると、Azure AD ではプログラムによってユーザーを作成、更新、削除できます。 また、一部の限られたアプリケーションについては、ID に関連したその他のオブジェクト (グループ、ロールなど) をプロビジョニング サービスで作成、更新、削除することができます。

![Azure AD プロビジョニング サービス](./media/user-provisioning/provisioning0.PNG)
*図 1: Azure AD プロビジョニング サービス*

![出力方向のユーザー プロビジョニング ワークフロー](./media/user-provisioning/provisioning1.PNG)
*図 2: Azure AD から主要 SaaS アプリケーションへの "出力方向" のユーザー プロビジョニング ワークフロー*

![入力方向のユーザー プロビジョニング ワークフロー](./media/user-provisioning/provisioning2.PNG)
*図 3: 主要な人材管理 (HCM) アプリケーションから Azure Active Directory および Windows Server Active Directory への "入力方向" のユーザー プロビジョニング ワークフロー*

## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Azure AD の自動ユーザー プロビジョニングで利用できるアプリケーションとシステム

Azure AD は、一般に普及している多くの SaaS アプリや人事管理システムとの連携にあらかじめ対応しているほか、SCIM 2.0 標準の特定の領域を実装するアプリにも広く対応しています。

### <a name="pre-integrated-applications"></a>事前統合されたアプリケーション

Azure AD が事前統合プロビジョニング コネクタをサポートするすべてのアプリケーションの一覧については、[ユーザー プロビジョニングのためのアプリケーション チュートリアルの一覧](../saas-apps/tutorial-list.md)に関するページを参照してください。

他のアプリのプロビジョニングのサポートを要求するために Azure AD エンジニア リング チームに問い合わせる場合は、[Azure Active Directory フィードバック フォーラム](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests/filters/new?category_id=172035)からメッセージを送信してください。

> [!NOTE]
> アプリで自動ユーザー プロビジョニングをサポートするには、まず、外部プログラム上でのユーザーの作成、保守、削除の自動化の実現に必要なユーザー管理 API を提供する必要があります。 そのため、すべての SaaS アプリがこの機能と互換性があるとは限りません。 ユーザー管理 API をサポートするアプリでは、Azure AD エンジニア リング チームがこれらのアプリケーションにプロビジョニング コネクタを作成できるようになります。この作業の優先順位は現在の顧客と見込み顧客のニーズによって決まります。

### <a name="connecting-applications-that-support-scim-20"></a>SCIM 2.0 をサポートするアプリケーションの接続

SCIM 2.0 ベースのユーザー管理 API を実装するアプリケーションを汎用的に接続する方法については、[SCIM を使用した Azure Active Directory からアプリケーションへのユーザーおよびグループの自動プロビジョニング](use-scim-to-provision-users-and-groups.md)に関するページをご覧ください。

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>アプリケーションへの自動プロビジョニングを設定する方法

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

Azure Active Directory ポータルを使用して、選択したアプリケーションに対する Azure AD プロビジョニング サービスを構成します。

1. **[Azure Active Directory ポータル](https://aad.portal.azure.com)** を開きます。
1. 左側のウィンドウで、 **[エンタープライズ アプリケーション]** を選択します。 構成済みのすべてのアプリの一覧が表示されます。
1. **[+ 新しいアプリケーション]** を選択して、アプリケーションを追加します。 シナリオに応じて、次のいずれかを追加します。

   - **[独自のアプリを追加する]** オプションでは、カスタム開発の SCIM の統合がサポートされます。
   - **[ギャラリーから追加する]**  >  **[注目のアプリケーション]** セクションに表示されるアプリケーションすべてが、自動プロビジョニングに対応しています。 追加のアプリケーションについては、[ユーザー プロビジョニングのためのアプリケーション チュートリアルの一覧](../saas-apps/tutorial-list.md)を参照してください。

1. 詳細を指定し、 **[追加]** を選択します。 新しいアプリがエンタープライズ アプリケーションの一覧に追加され、そのアプリケーションの管理画面が表示されます。
1. ユーザー アカウントでのアプリのプロビジョニング設定を管理するには、 **[プロビジョニング]** を選択します。

   ![プロビジョニング設定画面を表示します](./media/user-provisioning/provisioning_settings0.PNG)

1. **[プロビジョニング モード]** で [自動] オプションを選択し、管理者の資格情報、マッピング、開始と停止、および同期の設定を指定します。

   - **[管理者資格情報]** を展開し、Azure AD をアプリケーションのユーザー管理 API に接続するために必要な資格情報を入力します。 このセクションでは、資格情報でエラーが発生した場合、またはプロビジョニング ジョブが[検疫](#quarantine)に移行した場合の電子メール通知を有効にすることもできます。
   - **[マッピング]** を展開し、ユーザー アカウントをプロビジョニングまたは更新する場合に、Azure AD とターゲット アプリケーションの間でフローするユーザー属性を表示および編集します。 ターゲット アプリケーションが対応していれば、必要に応じてグループのプロビジョニングとユーザー アカウントをこのセクションで構成することができます。 テーブルでマッピングを選択すると右側にマッピング エディターが開き、ユーザー属性を表示してカスタマイズできます。

     **スコープ フィルター**により、ターゲット システムへのプロビジョニングまたはプロビジョニング解除の対象となるソース システム内のユーザーとグループをプロビジョニング サービスに指定します。 **[属性マッピング]** ウィンドウで **[ソース オブジェクト スコープ]** を選択し、特定の属性値でフィルター処理します。 たとえば、"Department" 属性が "Sales" であるユーザーのみをプロビジョニングの対象として指定することができます。 詳細については、[スコープ フィルターの使用](define-conditional-rules-for-provisioning-user-accounts.md)に関するページをご覧ください。

     詳細については、[属性マッピングのカスタマイズ](customize-application-attributes.md)に関するページを参照してください。

   - 現在実行中かどうかなど、アプリケーションのプロビジョニング サービスの動作は、 **[設定]** で制御します。 **[スコープ]** メニューでは、割り当て済みのユーザーとグループだけをプロビジョニングの対象とするか、Azure AD ディレクトリ内のすべてのユーザーをプロビジョニングの対象とするかを指定できます。 ユーザーとグループの "割り当て" の詳細については、「[Azure Active Directory でエンタープライズ アプリにユーザーまたはグループを割り当てる](assign-user-or-group-access-portal.md)」を参照してください。

アプリ管理画面で、 **[監査ログ]** を選択し、Azure AD プロビジョニング サービスによって実行されたすべての操作の記録を表示します。 詳細については、[プロビジョニング レポートに関するガイド](check-status-user-account-provisioning.md)を参照してください。

![例 - アプリの監査ログ画面](./media/user-provisioning/audit_logs.PNG)

> [!NOTE]
> Azure AD ユーザー プロビジョニング サービスは、[Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview) を使用して構成および管理することもできます。

## <a name="what-happens-during-provisioning"></a>プロビジョニング中の動作

Azure AD がソース システムである場合、プロビジョニング サービスは、[Azure AD Graph API の差分クエリ機能](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query)を使用してユーザーとグループを監視します。 プロビジョニング サービスは、ソース システムとターゲット システムに対して初期同期を実行した後、増分同期を定期的に行います。

### <a name="initial-sync"></a>初期同期

プロビジョニング サービスが開始されたときに行われる最初の同期では、次の処理が実行されます。

1. ソース システムのすべてのユーザーとグループにクエリを実行し、[属性マッピング](customize-application-attributes.md)で定義されているすべての属性を取得します。
1. 返されたユーザーおよびグループを、構成済み[割り当て](assign-user-or-group-access-portal.md)または[属性ベースのスコープ フィルター](define-conditional-rules-for-provisioning-user-accounts.md)を使用してフィルター処理します。
1. ユーザーが割り当て済み、またはプロビジョニング対象の場合、サービスはターゲット システムに対してクエリを実行し、指定された[照合属性](customize-application-attributes.md#understanding-attribute-mapping-properties)を使用して、一致するユーザーがいないかどうかを確認します。 例:ソース システムの userPrincipal 名が一致属性であり、ターゲット システムの userName にマップされる場合、プロビジョニング サービスは、ターゲット システムにクエリを実行し、ソース システムの userPrincipal 名の値と一致する userName がないかどうかを確認します。
1. 一致するユーザーがターゲット システムで見つからない場合、ソース システムから返された属性を使用してユーザーが作成されます。 ユーザー アカウントが作成された後、プロビジョニング サービスは新しいユーザー用のターゲット システムの ID を検出しキャッシュします。これはそのユーザーの将来の操作すべてを実行するときに使用されます。
1. 一致するユーザーが見つかった場合、そのユーザーは、ソース システムによって提供された属性を使用して更新されます。 ユーザー アカウントが照合された後、プロビジョニング サービスは新しいユーザー用のターゲット システムの ID を検出しキャッシュします。これはそのユーザーの将来の操作すべてを実行するときに使用されます。
1. 属性マッピングに "参照" 属性が含まれている場合、サービスは、ターゲット システムで追加の更新を実行して参照先オブジェクトを作成し、リンクします。 たとえば、あるユーザーがターゲット システムで "Manager" 属性を持ち、それがターゲット システムで作成された別のユーザーにリンクされている場合があります。
1. 初期同期の終了時に基準値を保持します。これは、以降の増分同期の始点になります。

ServiceNow、G Suite、Box など、アプリケーションの中には、ユーザーのプロビジョニングだけでなく、グループとそのメンバーのプロビジョニングをサポートしているものがあります。 このような場合、グループのプロビジョニングが[マッピング](customize-application-attributes.md)で有効になっていると、プロビジョニング サービスは、ユーザーとグループを同期したうえで、グループ メンバーシップを同期します。

### <a name="incremental-syncs"></a>増分同期

初期同期後に行われる他のすべての同期で、次の処理が実行されます。

1. ソース システムにクエリを実行し、前回の基準値が保存された後に更新されたユーザーおよびグループがないかどうかを確認します。
1. 返されたユーザーおよびグループを、構成済み[割り当て](assign-user-or-group-access-portal.md)または[属性ベースのスコープ フィルター](define-conditional-rules-for-provisioning-user-accounts.md)を使用してフィルター処理します。
1. ユーザーが割り当て済み、またはプロビジョニング対象の場合、サービスはターゲット システムに対してクエリを実行し、指定された[照合属性](customize-application-attributes.md#understanding-attribute-mapping-properties)を使用して、一致するユーザーがいないかどうかを確認します。
1. 一致するユーザーがターゲット システムで見つからない場合、ソース システムから返された属性を使用してユーザーが作成されます。 ユーザー アカウントが作成された後、プロビジョニング サービスは新しいユーザー用のターゲット システムの ID を検出しキャッシュします。これはそのユーザーの将来の操作すべてを実行するときに使用されます。
1. 一致するユーザーが見つかった場合、そのユーザーは、ソース システムによって提供された属性を使用して更新されます。 新しく割り当てられたアカウントが照合された場合、プロビジョニング サービスは新しいユーザー用のターゲット システムの ID を検出しキャッシュします。これはそのユーザーの将来の操作すべてを実行するときに使用されます。
1. 属性マッピングに "参照" 属性が含まれている場合、サービスは、ターゲット システムで追加の更新を実行して参照先オブジェクトを作成し、リンクします。 たとえば、あるユーザーがターゲット システムで "Manager" 属性を持ち、それがターゲット システムで作成された別のユーザーにリンクされている場合があります。
1. 以前プロビジョニングの対象だったユーザーが対象から外れた場合 (割り当てられていない場合を含む)、サービスは、更新を通じてターゲット システムのユーザーを無効にします。
1. 以前プロビジョニングの対象だったユーザーが、ソース システムで無効にされた場合、または論理削除された場合、サービスは、更新によってターゲット システムのユーザーを無効にします。
1. 以前プロビジョニングの対象だったユーザーが、ソース システムから物理的に削除された場合、サービスは、ターゲット システムのユーザーを削除します。 Azure AD では、論理削除されたユーザーは、削除されてから 30 日後に物理的に削除されます。
1. 増分同期の終了時に新しい基準値を保持します。これは、以降の増分同期の始点になります。

> [!NOTE]
> [[マッピング]](customize-application-attributes.md) セクションの **[対象オブジェクトのアクション]** チェック ボックスを使用して、 **[作成]** 、 **[更新]** 、または **[削除]** 操作を必要に応じて無効にできます。 更新中にユーザーを無効にするロジックは、"accountEnabled" などのフィールドの属性マッピングでも制御されます。

プロビジョニング サービスは、バックツーバック増分同期を、次のいずれかのイベントが発生するまで、[各アプリケーションに固有のチュートリアル](../saas-apps/tutorial-list.md)で定義された間隔で無期限に実行し続けます。

- Azure Portal または適切な Graph API コマンドを使用してサービスが手動で停止された 
- Azure Portal の **[Clear state and restart]\(状態をクリアして再起動\)** オプション、または適切な Graph API コマンドを使用して、新しい初期同期がトリガーされた。 この操作では、保存された基準値がクリアされ、すべてのソース オブジェクトが再評価されます。
- 属性マッピングまたはスコープ フィルターの変更によって、新しい初期同期がトリガーされた。 また、この操作では、保存された基準値がクリアされ、すべてのソース オブジェクトが再評価されます。
- 高いエラー率によりプロビジョニング プロセスが検疫に移行し (以下を参照)、そのまま 4 週間を超えて検疫にとどまっている。 この場合、サービスは自動的に無効になります。

### <a name="errors-and-retries"></a>エラーと再試行

ターゲット システムでのエラーが原因で個々のユーザーの追加、更新、または削除がターゲットシステム上で行えない場合、その操作は次の同期サイクルで再試行されます。 ユーザーの再試行が失敗し続けると、その頻度は減少し始め、1 日 1 回になるように段階的にスケール バックします。 エラーを解決するには、管理者が[監査ログ](check-status-user-account-provisioning.md)で "プロセス エスクロー" イベントがないかどうかを確認し、根本原因を特定して、適切な対応を取る必要があります。 たとえば、次のような一般的なエラーがあります。

- ターゲット システムで必要な属性がソース システムのユーザーに設定されていない
- ソース システムのユーザーの属性値に対して、ターゲット システムに一意の制約があり、同じ値が他のユーザー レコードに存在する

このようなエラーを解決するには、ソース システムで影響を受けるユーザーの属性値を調整するか、競合が発生しないように属性マッピングを調整します。

### <a name="quarantine"></a>検疫

ターゲット システムに対する呼び出しのほとんどまたはすべてが、常にエラー (管理者の資格情報が無効である場合など) が原因で失敗する場合、プロビジョニング ジョブは "検疫" 状態になります。 この状態は、[プロビジョニング概要レポート](check-status-user-account-provisioning.md)、または電子メール (電子メール通知が Azure portal で構成されている場合) で示されます。

検疫に移行すると、増分同期の頻度は徐々に減少し、最終的には 1 日 1 回になります。

問題を引き起こしているすべてのエラーが修正されたら、プロビジョニング ジョブは検疫から削除され、次の同期サイクルが開始します。 プロビジョニング ジョブが検疫にとどまっている期間が 4 週間を超えると、そのプロビジョニング ジョブは無効になります。

## <a name="how-long-will-it-take-to-provision-users"></a>ユーザーをプロビジョニングするにはどのくらいの時間がかかりますか。

プロビジョニング ジョブで実行されているのが初期プロビジョニング サイクルか増分サイクルかによって、パフォーマンスが異なります。 プロビジョニングにかかる時間、およびプロビジョニング サービスの状態を監視する方法について詳しくは、[ユーザー プロビジョニングの状態の確認](application-provisioning-when-will-provisioning-finish-specific-user.md)に関するページをご覧ください。

## <a name="how-can-i-tell-if-users-are-being-provisioned-properly"></a>ユーザーが正しくプロビジョニングされているかどうかを確認する方法

ユーザー プロビジョニング サービスによって実行された操作はすべて、Azure AD の監査ログに記録されます。 これには、ソース システムとターゲット システムに対して実行されたすべての読み取りおよび書き込み操作と、各操作の際に読み取られたり書き込まれたりしたユーザー データが含まれます。

Azure portal で監査ログを確認する方法については、[プロビジョニング レポートに関するガイド](check-status-user-account-provisioning.md)をご覧ください。

## <a name="how-do-i-troubleshoot-issues-with-user-provisioning"></a>ユーザー プロビジョニングに関する問題のトラブルシューティング方法

自動ユーザー プロビジョニングをトラブルシューティングする方法のシナリオ ベースのガイダンスについては、「[アプリケーションに対するユーザーの構成およびプロビジョニングに関する問題](application-provisioning-config-problem.md)」を参照してください。

## <a name="what-are-the-best-practices-for-rolling-out-automatic-user-provisioning"></a>自動ユーザー プロビジョニングを展開するためのベスト プラクティス

> [!VIDEO https://www.youtube.com/embed/MAy8s5WSe3A]

アプリケーションに対する出力方向のユーザー プロビジョニングを展開するための段階的なデプロイ計画の例については、[ユーザー プロビジョニングのための ID デプロイ ガイド](https://aka.ms/userprovisioningdeploymentplan)をご覧ください。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-b2b-users-in-azure-ad"></a>SaaS アプリへの自動ユーザー プロビジョニングは、Azure AD の B2B ユーザーに対応しますか。

はい、Azure AD ユーザー プロビジョニング サービスを使って、Azure AD の B2B (またはゲスト) ユーザーを SaaS アプリケーションにプロビジョニングすることは可能です。

ただし、B2B ユーザーが、Azure AD を使用して SaaS アプリケーションにサインインするには、SaaS アプリケーションで、SAML ベースのシングル サインオン機能が特定の方法で構成されている必要があります。 B2B ユーザーからのサインインをサポートするように SaaS アプリケーションを構成する方法の詳細については、「[B2B コラボレーション用の SaaS アプリの構成]( https://docs.microsoft.com/azure/active-directory/b2b/configure-saas-apps)」を参照してください。

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-dynamic-groups-in-azure-ad"></a>SaaS アプリへの自動ユーザー プロビジョニングは、Azure AD の動的グループに対応しますか。

はい。 "割り当てられているユーザーおよびグループのみを同期する" ように構成されている場合、Azure AD ユーザー プロビジョニング サービスは、SaaS アプリケーションのユーザーを、[動的グループ](../users-groups-roles/groups-create-rule.md)のメンバーであるかどうかに基づいて、プロビジョニングまたはプロビジョニング解除できます。 動的グループは、「すべてのユーザーとグループを同期する」オプションにも対処します。

ただし、動的グループの使用は、Azure AD から SaaS アプリケーションへのエンドツーエンドのユーザー プロビジョニングのパフォーマンス全体に影響することがあります。 動的グループを使用している場合は、これらの注意事項と推奨事項に留意してください。

- SaaS アプリケーションで動的グループのユーザーをプロビジョニングまたはプロビジョニング解除する速度は、動的グループがメンバーシップの変更を評価する速さによって決まります。 動的グループの処理状態を確認する方法の詳細については、「[メンバーシップ ルールの処理状態をチェックする](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule)」を参照してください。

- 動的グループを使用しているときに、メンバーシップの喪失はプロビジョニング解除イベントになるので、ユーザープロビジョニングおよびプロビジョニング解除に留意してルールを慎重に考慮する必要があります。

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-nested-groups-in-azure-ad"></a>SaaS アプリへの自動ユーザー プロビジョニングは、Azure AD の入れ子になった動的グループに対応しますか。

いいえ。 "割り当てられたユーザーとグループのみを同期する" ように構成されている場合、Azure AD ユーザー プロビジョニング サービスは、入れ子になったグループに含まれているユーザーを読み取ったりプロビジョニングしたりすることができません。 明示的に割り当てられたグループの直接のメンバーであるユーザーだけを読み取ってプロビジョニングできます。

これは、「アプリケーションへのグループベースの割り当て」の制限であり、シングル サインオンにも影響し、「[SaaS アプリケーションへのアクセスをグループで管理する](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-saasapps )」で説明しています。

対処法として、プロビジョニングする必要のあるユーザーを含んだグループを明示的に割り当てる (またはそれ以外の場合では[スコープ](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)する) 必要があります。

### <a name="is-provisioning-between-azure-ad-and-a-target-application-using-an-encrypted-channel"></a>Azure AD とターゲット アプリケーションの間のプロビジョニングでは、暗号化されたチャネルを使用していますか。

はい。 サーバー ターゲットに HTTPS SSL 暗号化を使用しています。

## <a name="related-articles"></a>関連記事

- [SaaS アプリを統合する方法に関するチュートリアルの一覧](../saas-apps/tutorial-list.md)
- [ユーザー プロビジョニング用の属性マッピングのカスタマイズ](customize-application-attributes.md)
- [属性マッピングの式の書き方](functions-for-customizing-application-data.md)
- [ユーザー プロビジョニング用のフィルターのスコープ](define-conditional-rules-for-provisioning-user-accounts.md)
- [SCIM を使用して、Azure AD からアプリケーションへのユーザーとグループの自動プロビジョニングを有効にする](use-scim-to-provision-users-and-groups.md)
- [Azure AD 同期 API の概要](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
