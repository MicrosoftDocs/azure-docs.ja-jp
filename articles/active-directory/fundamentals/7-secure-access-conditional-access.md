---
title: Azure Active Directory 条件付きアクセスを使用して外部アクセスを管理する
description: Azure Active Directory 条件付きアクセス ポリシーを使用して、リソースへの外部アクセスをセキュリティで保護する方法について説明します。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64209a4d9ca200c69783a4ae317b38beef8ded89
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98222311"
---
# <a name="manage-external-access-with-conditional-access-policies"></a>条件付きアクセス ポリシーを使用して外部アクセスを管理する 

[条件付きアクセス](../conditional-access/overview.md)は、信号を集約し、ポリシーを適用して、ユーザーにリソースへのアクセスを許可する必要があるかどうかを判断するために Azure AD で使用されるツールです。 条件付きアクセス ポリシー (条件付きアクセス ポリシー) を作成して使用する方法の詳細については、「[条件付きアクセスのデプロイを計画する](../conditional-access/plan-conditional-access.md)」を参照してください。 

![条件付きアクセスの信号と決定の図](media/secure-external-access//7-conditional-access-signals.png)



この記事では、条件付きアクセス ポリシーの外部ユーザーへの適用について説明します。ここでは、ユーザーが[エンタイトルメント管理](../governance/entitlement-management-overview.md)機能にアクセスできないことを前提としています。 条件付きアクセス ポリシーはエンタイトルメント管理と共に使用でき、通常はそのように使用されます。

このドキュメント セットの前半では、次のような[セキュリティ計画を作成](3-secure-access-plan.md)しました。

* アプリケーションとリソースのセキュリティ要件が同じで、アクセスのためにグループ化できる。

* 外部ユーザーのサインイン要件。

その計画を使用して、外部アクセスのための条件付きアクセス ポリシーを作成します。 

> [!IMPORTANT]
> 作成したポリシーをすべての外部ユーザーに適用する前にテストできるように、いくつかの外部ユーザーのテスト アカウントを作成します。

## <a name="conditional-access-policies-for-external-access"></a>外部アクセスの条件付きアクセス ポリシー

条件付きアクセス ポリシーを使用した外部アクセスの管理に関連したベスト プラクティスを次に示します。

* 接続されている組織をエンタイトルメント管理で使用できない場合は、共同作業するパートナー組織ごとに Azure AD セキュリティ グループまたは Microsoft 365 グループを作成します。 そのパートナーのすべてのユーザーをグループに割り当てます。 その後、これらのグループを条件付きアクセス ポリシーで使用できます。

* できるだけ少数の条件付きアクセス ポリシーを作成します。 同じアクセス ニーズを持つアプリケーションの場合は、それらすべてを同じポリシーに追加します。  
‎ 
   > [!NOTE]
   > 条件付きアクセス ポリシーは、最大 250 のアプリケーションに適用できます。 250 以上のアプリが同じアクセス ニーズを持つ場合は、重複するポリシーを作成します。 ポリシー A はアプリ 1-250 に適用され、ポリシー B はアプリ 251-500 に適用されます。

* 名前付け規則を使用して、ポリシーに対して外部アクセスに固有の明確な名前を付けます。 1 つの名前付け規則は *ExternalAccess_actiontaken_AppGroup* です (例: ExternalAccess_Block_FinanceApps)。

## <a name="block-all-external-users-from-resources"></a>すべての外部ユーザーをリソースからブロックする

条件付きアクセス ポリシーを使用して、外部ユーザーからの特定のリソース セットへのアクセスをブロックできます。 アクセスをブロックするリソースのセットを決定したら、ポリシーを作成します。

アプリケーションのセットへの外部ユーザーのアクセスをブロックするポリシーを作成するには、次の手順に従います。

1. **Azure portal** にアクセスし、 **[Azure Active Directory]** を選択して、 **[セキュリティ]** を選択してから、 **[条件付きアクセス]** を選択します。

2. **[新しいポリシー]** を選択し、**名前** を入力します (例: ExternalAccess_Block_FinanceApps)。

3. **[ユーザーとグループ]** を選択します。 [含める] タブで **[ユーザーとグループの選択]** を選択し、 **[すべてのゲストおよび外部ユーザー]** を選択します。 

4. **[除外]** を選択し、管理者グループと緊急時アクセス (非常用) アカウントを入力します。

5. **[クラウド アプリまたは操作]** を選択し、 **[アプリを選択]** を選択して、外部アクセスをブロックするすべてのアプリを選択してから、 **[選択]** を選択します。

6. **[条件]** を選択し、 **[場所]** を選択して、[構成] で **[はい]** を選択し、 **[すべての場所]** を選択します。

7. [アクセス制御] で **[許可]** を選択し、切り替えを **[ブロック]** に変更して、 **[選択]** を選択します。

8. [ポリシーの有効化] 設定が **[レポート専用]** に設定されていることを確認し、 **[作成]** を選択します。

## <a name="block-external-access-to-all-except-specific-external-users"></a>特定の外部ユーザーを除くすべてのユーザーによる外部アクセスをブロックする

特定のグループを除く外部ユーザーをブロックすることが必要になる場合があります。 たとえば、財務チームのために作業しているユーザーを除くすべての外部ユーザーを、財務アプリケーションからブロックできます。 これを行うには、次の手順を実行します。

1. 財務グループにアクセスする必要がある外部ユーザーを保持するセキュリティ グループを作成します。

2. 上に示す、外部アクセスをリソースからブロックする手順 1-3 に従います。

3. 手順 4 で、財務アプリからのブロックから除外するセキュリティ グループを追加します。

4. 残りの手順を実行します。

## <a name="implement-conditional-access"></a>条件付きアクセスを実装する

さまざまな一般的な条件付きアクセス ポリシーが文書化されています。 外部ユーザーに適用できる次の文書を参照してください。

* [すべてのユーザーの多要素認証を必須にする](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)

* [ユーザー リスクベースの条件付きアクセス](../conditional-access/howto-conditional-access-policy-risk-user.md)

* [信頼されていないネットワークからのアクセスに対して多要素認証を必須にする](../conditional-access/untrusted-networks.md) 

* [利用規約を必須にする](../conditional-access/terms-of-use.md)

## <a name="next-steps"></a>次のステップ

リソースへの外部アクセスをセキュリティで保護する方法については、次の記事を参照してください。 アクションは、表示されている順序で実行することをお勧めします。

1. [外部アクセスに必要なセキュリティ体制を決定する](1-secure-access-posture.md)

2. [現在の状態を理解する](2-secure-access-current-state.md)

3. [ガバナンス プランを作成する](3-secure-access-plan.md)

4. [セキュリティにグループを使用する](4-secure-access-groups.md)

5. [Azure AD B2B に移行する](5-secure-access-b2b.md)

6. [エンタイトルメント管理を使用してアクセスをセキュリティで保護する](6-secure-access-entitlement-managment.md)

7. [条件付きアクセス ポリシーを使用してアクセスをセキュリティで保護する](7-secure-access-conditional-access.md) (この記事)

8. [秘密度ラベルを使用してアクセスをセキュリティで保護する](8-secure-access-sensitivity-labels.md)

9. [Microsoft Teams、OneDrive、SharePoint へのアクセスをセキュリティで保護する](9-secure-access-teams-sharepoint.md)
