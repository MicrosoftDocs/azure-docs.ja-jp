---
title: "Azure PIM リソース RBAC の概要 | Microsoft Docs"
description: "PIM の用語や通知を含めた RBAC 機能の概要について説明します"
services: active-directory
documentationcenter: 
author: barclayn
manager: mbaldwin
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2017
ms.author: barclayn
ms.openlocfilehash: abaf78428cb12235a659bad5c13d64e8e51f4ba6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="pim-for-azure-resources-preview"></a>Azure リソースの PIM (プレビュー)

組織内の Azure リソース (プレビュー) に対するアクセス権が、Azure Active Directory Privileged Identity Management (PIM) で管理、制御、監視できるようになりました。 対象には、サブスクリプション、リソース グループ、さらに仮想マシンも含まれます。 Azure ロールベースのアクセス制御 (RBAC) 機能を活用する Azure ポータル内のすべてのリソースが、Azure AD PIM から提供される優れたセキュリティとライフサイクル管理機能、そして間もなく Azure AD ロールに提供される強力な新機能の一部を活用できます。 

## <a name="pim-for-azure-resources-preview-helps-resource-administrators"></a>Azure リソースの PIM (プレビュー) はリソース管理者に便利

- 管理対象の Azure リソースのロールが割り当てられているユーザーとグループを確認する
- サブスクリプション、リソース グループなどのリソース管理のために、オンデマンドの "Just In Time な" アクセスを有効化する
- 新しい期限付きの割り当て設定により、割り当てられたユーザー/グループのリソース アクセスを自動的に期限切れにする
- クイック タスクまたはオンコール スケジュールのために、一時的なリソース アクセスを割り当てる
- 組み込みまたはカスタム ロールのリソース アクセスのために、多要素認証を適用する 
- ユーザーのアクティブなセッション中にリソース アクティビティと相関関係のあるリソース アクセスに関するレポートを取得する
- 新しいユーザーまたはグループにリソース アクセスを割り当てたときや、適格な割り当てをアクティブ化するときにアラートを取得する

Azure AD PIM は、組み込みの Azure リソース ロールと、以下を含むカスタム (RBAC) ロール (ただしこれらに限定されません) を管理できます。

- 所有者
- ユーザーアクセスの管理者
- 共同作成者
- セキュリティ管理者
- セキュリティ マネージャーなど

>[!NOTE]
Azure AD でサブスクリプション管理を有効にする、所有者またはユーザー アクセス管理者ロールに割り当てられたグループのユーザーまたはメンバー、および全体管理者がリソース管理者です。 これらの管理者は、ロールの割り当て、ロール設定の構成、Azure リソースの PIM を使用したアクセス レビューができます。 [Azure リソースの組み込みのロール](../role-based-access-built-in-roles.md)の一覧をご確認ください

## <a name="tasks"></a>タスク

PIM はロールのアクティブ化、保留中のアクティブ化/要求の表示、保留中の承認 ([Azure AD ディレクトリ ロール](azure-ad-pim-approval-workflow.md)の場合) の表示のためのアクセスを簡単にし、左側のナビゲーション メニューの [タスク] セクションの応答に対する保留をレビューします。
概要のエントリ ポイントから [タスク] メニュー項目のいずれかにアクセスして表示されるビューには、Azure AD ディレクトリ ロールと Azure リソース ロール (プレビュー) の両方の結果が含まれています。 

![](media/azure-pim-resource-rbac/role-settings-details.png)

[自分のロール] には、Azure AD ディレクトリ ロール、および Azure リソース ロール (プレビュー) のアクティブで適格なロール割り当ての一覧が含まれています。

## <a name="activate-roles"></a>ロールをアクティブ化する

Azure リソース (プレビュー) のロールをアクティブ化すると、適格なロール メンバーが将来の日付/時刻でアクティブ化をスケジュールしたり、特定のアクティブ化最大期間 (管理者により構成) を選択できるようになります。 Azure AD ロールのアクティブ化の詳細については、[こちら](../active-directory-privileged-identity-management-how-to-activate-role.md)を参照してください

![](media/azure-pim-resource-rbac/contributor.png)

[アクティブ化] メニューから、希望する開始日時を入力してロールをアクティブ化します。 または、アクティブ化期間 (ロールがアクティブな期間) を短くし、必要に応じて理由を入力して、[アクティブ化] をクリックします。

開始日時が変更されない場合、数秒以内にロールがアクティブ化されます。 [自分のロール] ページに、アクティブ化のキューに登録されているロールのバナー メッセージが表示されます。 このメッセージを消去するには、[更新] ボタンをクリックします。

![](media/azure-pim-resource-rbac/my-roles.png)

アクティブ化が将来の日時でスケジュールされている場合、保留中の要求が左側のナビゲーション メニューの [保留中の要求] タブに表示されます。 ロールのアクティブ化が必要でなくなった場合、ユーザーは、ページの右側にある [キャンセル] ボタンをクリックして、要求をキャンセルすることができます。

![](media/azure-pim-resource-rbac/pending-requests.png)

## <a name="discover-and-manage-azure-resources"></a>Azure リソースを検索および管理する

Azure リソースのロールを検索および管理するには、左側のナビゲーション メニューの [管理] タブで Azure リソース (プレビュー) を選択します。 リソースを検索するには、ページの上部にあるフィルターや検索バーを使用します。

![](media/azure-pim-resource-rbac/azure-resources.png)

## <a name="resource-dashboards"></a>リソースのダッシュボード

[管理者ビュー] のダッシュボードには、4 つの主要なコンポーネントが含まれます。 過去 7 日間のリソース ロールのアクティブ化がグラフィカルに表示されています。 このデータは、選択したリソースが対象となっていて、特に一般的なロール (所有者、共同作成者、ユーザー アクセス管理者) と、組み合わせられたすべてのロールのアクティブ化を表示しています。

アクティブ化のグラフの右側には 2 つのグラフがあり、ユーザーとグループの両方のロール割り当ての分布が割り当ての種類ごとに表示されています。 グラフのスライスを選択すると、値が割合 (またはその逆) に変更されます。

![](media/azure-pim-resource-rbac/admin-view.png)

グラフの下には、過去 30 日間の新しいロール割り当てが行われたユーザーとグループの数と、合計の割り当て数で並べ替えられたロールの一覧 (降順) が表示されます。

![](media/azure-pim-resource-rbac/role-settings.png)

## <a name="manage-role-assignments"></a>ロールの割り当てを管理する

管理者は、左側のナビゲーションからロールまたはメンバーのどちらかを選択してロールの割り当てを管理できます。 ロールの選択により、管理者が管理タスクを特定のロールに制限できるようになり、メンバーにはリソースに対するすべてのユーザーとグループのロール割り当てが表示されます。

![](media/azure-pim-resource-rbac/roles.png)

![](media/azure-pim-resource-rbac/members.png)

>[!NOTE]
アクティブ化が保留中のロールがある場合、メンバーシップを表示した際にページの上部に通知バナーが表示されます。

## <a name="assign-roles"></a>ロールを割り当てる

ロールにユーザーまたはグループを割り当てるには、ロールを選択する (ロールを表示している場合) か、操作バーで [追加] をクリックします ([メンバー] ビューを表示している場合)。

![](media/azure-pim-resource-rbac/members2.png)

>[!NOTE]
[メンバー] タブからユーザーまたはグループを追加する場合は、ユーザーまたはグループを選択する前に、[追加] メニューからロールを選択する必要があります。

![](media/azure-pim-resource-rbac/select-role.png)

ディレクトリからユーザーまたはグループを選択します。

![](media/azure-pim-resource-rbac/choose.png)

ドロップダウン メニューから適切な割り当ての種類を選択します。 

**Just In Time 割り当て:** 指定した期間または無期限 (ロール設定で構成されている場合) に、ロールに対して適格だが永続的ではないアクセス権を持つユーザーまたはグループ メンバーが提供されます。 

**直接割り当て:** ロール割り当てをアクティブ化するためにユーザーまたはグループ メンバーは必要ありません (永続的アクセスと呼ばれます)。 タスクの完了時にアクセスが必要とされない、単発のシフトや時間が限られたアクティビティなどの短期使用には直接割り当てを使用することをお勧めします。

![](media/azure-pim-resource-rbac/membership-settings.png)

[割り当ての種類] ドロップダウンの下にあるチェック ボックスで、割り当てを永続的にする (Just In Time 割り当てを永続的にアクティブ化できる/直接割り当てが永続的にアクティブになる) かどうかを指定できます。 特定の割り当て期間を指定するには、チェック ボックスの選択を解除して、開始または終了の日時フィールドを変更します。

>[!NOTE]
このチェック ボックスは、別の管理者がロール設定のそれぞれの割り当ての種類に対して最大の割り当て期間を指定している場合は変更できません。

![](media/azure-pim-resource-rbac/calendar.png)

## <a name="view-activation-and-azure-resource-activity"></a>アクティブ化と Azure リソースのアクティビティを表示する

各リソースに対して特定ユーザーが実行した操作を確認する必要がある場合は、指定したアクティブ化期間に関連付けられている Azure リソース アクティビティを表示することができます (適格なユーザーに限ります)。 最初に、[メンバー] ビューまたは特定のロールのメンバー一覧からユーザーを選択します。 結果には Azure リソースに対する日別のユーザーの操作と、同一期間の最近のロールのアクティブ化がグラフィカルに表示されます。

![](media/azure-pim-resource-rbac/user-details.png)

特定のロールのアクティブ化を選択すると、ロールのアクティブ化の詳細とそのユーザーがアクティブな間に発生した、対応する Azure リソース アクティビティが表示されます。

![](media/azure-pim-resource-rbac/audits.png)

## <a name="modify-existing-assignments"></a>既存の割り当てを変更する

ユーザー/グループの詳細ビューから既存の割り当てを変更するには、ページの上部にある操作バーから [設定の変更] を選択します。 [割り当ての種類] を Just In Time 割り当てまたは直接割り当てに変更します。

![](media/azure-pim-resource-rbac/change-settings.png)

## <a name="review-who-has-access-in-a-subscription"></a>サブスクリプションでアクセス権を持つユーザーをレビューする

サブスクリプション内のロールの割り当てをレビューするには、左側のナビゲーションから [メンバー] タブを選択するかロールを選択して、メンバーをレビューする特定のロールを選択します。 

既存のアクセス レビューを表示するには操作バーから [レビュー] を選択し、新しいレビューを作成するには [追加] を選択します。

![](media/azure-pim-resource-rbac/owner.png)

アクセス レビューの詳細については、[こちら](../active-directory-privileged-identity-management-how-to-perform-security-review.md)を参照してください

>[!NOTE]
現時点で、レビューはサブスクリプションのリソースでのみサポートされています。

## <a name="configure-role-settings"></a>ロール設定を構成する

ロール設定を構成すると、PIM 環境内の割り当てに適用される既定値が定義されます。 リソースに対してこれらを定義するには、左側のナビゲーションから [ロールの設定] タブを選択するか、任意のロールの操作バーから [ロールの設定] ボタンを選択して、現在のオプションを表示します。

ページの上部の操作バーから [編集] をクリックすると、各設定を変更できます。

![](media/azure-pim-resource-rbac/owner.png)

![](media/azure-pim-resource-rbac/owner02.png)

設定の変更により、最終変更日時、設定を変更した管理者を含めた情報が [ロールの設定] ページに記録されます。

![](media/azure-pim-resource-rbac/role-settings-02.png)

## <a name="resource-audit"></a>リソースの監査

リソースの監査では、リソースのすべてのロール アクティビティのビューが提供されます。 定義済みの日付またはカスタムの範囲を使用して情報をフィルターすることができます。
![](media/azure-pim-resource-rbac/last-day.png) リソースの監査では、ユーザーのアクティビティの詳細を表示するビューにもすばやくアクセスできます。 このビューでは、“ロールをアクティブ化する“ すべての操作が、特定の要求元のリソース アクティビティとリンクしています。
![](media/azure-pim-resource-rbac/resource-audit.png)

## <a name="just-enough-administration"></a>Just Enough Administration

Just Enough Administration (JEA) をリソースのロール割り当てで使用するベスト プラクティスは Azure リソースの PIM では簡単です。 Azure サブスクリプションまたはリソース グループで割り当てが行われているユーザーおよびグループ メンバーは、より限られた範囲で既存のロール割り当てをアクティブ化できます。 

検索ページで、管理する必要がある下位のリソースを検索します。

![](media/azure-pim-resource-rbac/azure-resources-02.png)

左側のナビゲーション メニューから [自分のロール] を選択し、アクティブ化する適切なロールを選択します。 以下のとおり、ロールがリソース グループではなくサブスクリプションで割り当てられたため、割り当ての種類が継承されていることがわかります。

![](media/azure-pim-resource-rbac/my-roles-02.png)

## <a name="next-steps"></a>次のステップ

- [Azure リソースの組み込みロール](../role-based-access-built-in-roles.md)
- Azure AD ロールのアクティブ化の詳細については、[こちら](../active-directory-privileged-identity-management-how-to-activate-role.md)を参照してください
- [PIM の承認ワークフロー](azure-ad-pim-approval-workflow.md)