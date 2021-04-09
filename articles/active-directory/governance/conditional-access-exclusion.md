---
title: 条件付きアクセス ポリシーから除外されたユーザーの管理 - Azure AD
description: Azure Active Directory (Azure AD) アクセス レビューを使用して、条件付きアクセス ポリシーから除外されているユーザーを管理する方法を説明します
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 426e28048ae370919529ea710717a3a3867d999d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97746254"
---
# <a name="use-azure-ad-access-reviews-to-manage-users-excluded-from-conditional-access-policies"></a>Azure AD アクセス レビューを使用して、条件付きアクセス ポリシーから除外されているユーザーを管理する

理想的な世界では、すべてのユーザーが、組織のリソースへのアクセスをセキュリティで保護するアクセス ポリシーに従います。 ただし、ビジネス ケースには、例外を認める必要がある場合もあります。 この記事では、場合によっては除外が必要になる状況の例について説明します。 IT 管理者としてこのタスクを管理する場合には、Azure Active Directory (Azure AD) アクセス レビューを使用して、ポリシーの例外の見過ごしを防止すると共に、これらの例外が定期的にレビューされていることの証明を監査者に提示します。

>[!NOTE]
> Azure AD アクセス レビューを使用するには、有効な Azure AD Premium P2、有料の Enterprise Mobility + Security E5、または評価版ライセンスが必要です。 詳細については、「 [Azure Active Directory のエディション](../fundamentals/active-directory-whatis.md)」をご覧ください。

## <a name="why-would-you-exclude-users-from-policies"></a>ポリシーからユーザーを除外する理由

たとえば、管理者として [Azure AD Conditional Access](../conditional-access/concept-conditional-access-policy-common.md) を使用して多要素認証 (MFA) を要求し、特定のネットワークまたはデバイスに対する認証要求を制限することを決定したとします。 展開の計画中に、すべてのユーザーがこれらの要件を満たせるわけではないことに気が付きます。 たとえば、社内ネットワークに含まれていない、リモート オフィスから作業しているユーザーがいる場合があります。 また、デバイスの交換を待機している間、サポートされていないデバイスを使用して接続するユーザーに対応する必要が生じる場合もあります。 つまり、その企業では、これらのユーザーがサインインしてジョブを実行する必要があるため、条件付きアクセス ポリシーから除外します。

もう 1 つの例として、条件付きアクセス内で[ネームド ロケーション](../conditional-access/location-condition.md)を使用して、ユーザーによるテナントへのアクセスを許可したくない一連の国や地域を指定することもできます。

![条件付きアクセスでのネームド ロケーション](./media/conditional-access-exclusion/named-locations.png)

残念ながら、ユーザーによっては、これらのブロックされた国または地域からサインインするための正当な理由を、引き続き保持している場合があります。 たとえば、ユーザーが出張していたり、会社のリソースにアクセスする必要が生じる可能性があります。 この場合、これらの国や地域をブロックする条件付きアクセス ポリシーでは、ポリシーから除外されたユーザーのためにクラウド セキュリティ グループを使用できます。 旅行中にアクセスする必要があるユーザーは、[Azure AD のセルフサービスによるグループ管理](../enterprise-users/groups-self-service-management.md)を使用して、グループに自分自身を追加することができます。

もう 1 つの例は、[大半のユーザーのレガシ認証をブロックする](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/07/azure-ad-conditional-access-support-for-blocking-legacy-auth-is-in-public-preview/)条件付きアクセス ポリシーを使用する場合です。 ただし、Office 2010 または IMAP/SMTP/POP ベースのクライアント経由でリソースにアクセスするために、レガシ認証の方法を使用することが必要なユーザーがいる場合は、レガシ認証の方法をブロックするポリシーからこれらのユーザーを除外することができます。

>[!NOTE]
>Microsoft は、セキュリティ状態を改善するために、テナントでの従来のプロトコルの使用をブロックすることを強くお勧めしています。

## <a name="why-are-exclusions-challenging"></a>除外が困難な理由

Azure AD では、ユーザーのグループに条件付きアクセス ポリシーの範囲を指定できます。 また、Azure AD ロール、個々のユーザー、またはゲストを選択することで、除外を構成することもできます。 除外を構成する場合は、除されたユーザーにはポリシーの目的を適用できない点に注意してください。 ユーザーの一覧を使用するか、または従来のオンプレミス セキュリティ グループを使用して、除外が構成されている場合は、除外に対する可視性が制限されます。 その結果、次のような影響が出ています。

- ユーザーは、除外されていることを知らない可能性があります。

- ユーザーはセキュリティ グループに参加して、ポリシーをバイパスすることができます。

- 除外されたユーザーは、以前に除外の候補として評価された可能性がありますが、今後はその条件には該当しなくなります。

多くの場合、最初に除外を構成するときに、ポリシーをバイパスするユーザーの簡単なリストが示されます。 時間が経つにつれて、除外に追加されるユーザーが増えていき、リストが長くなっていきます。 ある時点でリストを見直して、これらの各ユーザーが引き続き除外の対象になることを確認する必要があります。 技術的な観点からは、除外リストを管理することは比較的簡単にできますが、誰がビジネス上の意思決定を行い、すべて監査可能であることをどのように確認するのでしょうか。 しかし、Azure AD グループを使用して除外を構成すれば、アクセス レビューを補完的な制御として使用して、可視性を高め、除外されるユーザーの数を減らすことができます。

## <a name="how-to-create-an-exclusion-group-in-a-conditional-access-policy"></a>条件付きアクセス ポリシーの除外グループを作成する方法

次の手順に従って、新しい Azure AD グループと、そのグループに適用されない条件付きアクセス ポリシーを作成します。

### <a name="create-an-exclusion-group"></a>除外グループの作成

1. Azure portal にサインインします。

2. 左側のナビゲーションで、**[Azure Active Directory]** をクリックして、**[グループ]** をクリックします。

3. 上部のメニューで、**[新しいグループ]** をクリックしてグループ ウィンドウを開きます。

4. **[グループの種類]** リストで **[セキュリティ]** を選択します。 名前と説明を指定します。

5. **[メンバーシップ]** の種類は必ず **[割り当て済み]** に設定してください。

6. この除外グループに含める必要があるユーザーを選択し、**[作成]** をクリックします。

![Azure Active Directory の [新しいグループ] ウィンドウ](./media/conditional-access-exclusion/new-group.png)

### <a name="create-a-conditional-access-policy-that-excludes-the-group"></a>グループを除外する条件付きアクセス ポリシーの作成

これで、この除外グループを使用する条件付きアクセス ポリシーを作成できるようになりました。

1. 左側のナビゲーションで **[Azure Active Directory]** をクリックしてから、 **[条件付きアクセス]** をクリックして **[ポリシー]** ブレードを開きます。

2. **[新しいポリシー]** をクリックして **[新規]** ウィンドウを開きます。

3. 名前を指定します。

4. [割り当て] の下の **[ユーザーとグループ]** をクリックします。

5. **[含める]** タブで **[すべてのユーザー]** を選択します。

6. **[除外]** タブで、 **[ユーザーとグループ]** にチェックマークを付けて、 **[対象外とするユーザーの選択]** をクリックします。

7. 作成した除外グループを選択します。

   > [!NOTE] 
   > ベスト プラクティスとして、テナントから確実にロックアウトされていないことをテストする際は、少なくとも 1 つの管理者アカウントをポリシーから除外することをお勧めします。

8. 引き続き、組織の要件に基づいて条件付きアクセス ポリシーを設定します。

![条件付きアクセスでの [対象外とするユーザーの選択] ウィンドウ](./media/conditional-access-exclusion/select-excluded-users.png)
  
アクセス レビューを使用して条件付きアクセス ポリシーの除外を管理する 2 つの例について説明します。

## <a name="example-1-access-review-for-users-accessing-from-blocked-countriesregions"></a>例 1: ブロックされている国や地域からアクセスするユーザーのアクセス レビュー

特定の国や地域からのアクセスをブロックする条件付きアクセス ポリシーがあるとします。 これには、ポリシーから除外されているグループが含まれます。 次に示す推奨されるアクセス レビューでは、グループのメンバーがレビューされます。

> [!NOTE] 
> アクセス レビューを作成するには、グローバル管理者またはユーザー管理者のロールが必要です。

1. レビューは、毎週行われます。

2. また、この除外グループを常に最新の状態に保つために、終わりはありません。

3. このグループのすべてのメンバーがレビューの対象範囲に入ります。

4. 各ユーザーは、自分がこれらのブロックされている国や地域から引き続きアクセスする必要があり、そのために引き続きグループのメンバーでなければならないことを、自分で証明する必要があります。

5. ユーザーがレビュー要求に応答しない場合、そのユーザーはグループから自動的に削除されるため、これらの国や地域に出張中はテナントにアクセスできなくなります。

6. メール通知を有効にして、ユーザーにアクセス レビューの開始と完了について通知するようにします。

    ![例 1 の [アクセス レビューを作成する] ウィンドウ](./media/conditional-access-exclusion/create-access-review-1.png)

## <a name="example-2-access-review-for-users-accessing-with-legacy-authentication"></a>例 2: レガシ認証を使用してアクセスしているユーザーのアクセス レビュー

レガシ認証と古いバージョンのクライアントを使用しているユーザーのアクセスをブロックする条件付きアクセス ポリシーがあり、ポリシーから除外されているグループが含まれているとします。 次に示す推奨されるアクセス レビューでは、グループのメンバーがレビューされます。

1. このレビューは定期的なレビューである必要があります。

2. グループの全員がレビューされる必要があります。

3. 選択したレビュー担当者として部署の所有者をリストするように構成できます。

4. 結果を自動適用し、従来の認証方法を引き続き使用することが承認されなかったユーザーを削除します。

5. 大規模なグループのレビュー担当者が簡単に意思決定できるような推奨事項を有効にすることをお勧めします。

6. メール通知を有効にして、ユーザーにアクセス レビューの開始と完了について通知されるようにします。

    ![例 2 の [アクセス レビューを作成する] ウィンドウ](./media/conditional-access-exclusion/create-access-review-2.png)

>[!IMPORTANT] 
>多数の除外グループがあるために、複数のアクセス レビューを作成する必要がある場合、Microsoft Graph のベータ版エンドポイントの API では、それらをプログラムによって作成および管理できるようになりました。 最初に、[Azure AD アクセス レビューの API リファレンス](/graph/api/resources/accessreviews-root?view=graph-rest-beta)に関するページと、[Microsoft Graph を使用した Azure AD アクセスの取得の例](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/td-p/236096)に関するページを参照してください。

## <a name="access-review-results-and-audit-logs"></a>アクセス レビューの結果と監査ログ

これで、グループ、条件付きアクセス ポリシー、およびアクセス レビューの準備がすべて整ったので、今度はこれらのレビューの結果を監視および追跡してみましょう。

1. Azure portal 上で **[アクセス レビュー]** ブレードを開きます。

2. 除外グループを管理するために作成したコントロールとプログラムを開きます。

3. **[結果]** をクリックして、リストに残すことが承認されたユーザーと削除されたユーザーを確認します。

    ![承認されたユーザーを示すアクセス レビューの結果](./media/conditional-access-exclusion/access-reviews-results.png)

4. 次に、**[監査ログ]** をクリックして、このレビュー中に実行されたアクションを確認します。

    ![アクションの一覧を示すアクセス レビューの監査ログ](./media/conditional-access-exclusion/access-reviews-audit-logs.png)

IT 管理者は、ポリシーに照らした除外グループの管理が、時には不可避であることがわかっています。 ただし、これらのグループの維持、ビジネス オーナーやユーザー自身による定期的なレビュー、これらの変更の監査は、Azure AD アクセス レビューを使えば簡単に行うことができます。

## <a name="next-steps"></a>次のステップ

- [グループまたはアプリケーションのアクセス レビューを作成する](create-access-review.md)
- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)