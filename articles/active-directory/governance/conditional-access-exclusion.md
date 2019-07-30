---
title: アクセス レビューを使用して、条件付きアクセス ポリシーから除外されているユーザーを管理する - Azure Active Directory | Microsoft Docs
description: Azure Active Directory (Azure AD) アクセス レビューを使用して、条件付きアクセス ポリシーから除外されているユーザーを管理する方法を説明します
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 09/25/2018
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 246503fec6436bf49dcd5fb89c2dc0ed345a43ca
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/25/2019
ms.locfileid: "68499914"
---
# <a name="use-azure-ad-access-reviews-to-manage-users-excluded-from-conditional-access-policies"></a>Azure AD アクセス レビューを使用して、条件付きアクセス ポリシーから除外されているユーザーを管理する

理想的な世界では、すべてのユーザーが、組織のリソースへのアクセスをセキュリティで保護するアクセス ポリシーに従っているでしょう。 ただし、ビジネス ケースには、例外を認める必要がある場合もあります。 この記事では、どのような状況で除外が必要となる可能性があるかと、IT 管理者が Active Directory (Azure AD) アクセス レビューを使用してどのようにこのタスクを管理し、ポリシーの例外の監視を回避し、これらの例外が定期的にレビューされることの証明を監査者に提供できるかを示す、いくつかの例について説明します。

> [!NOTE]
> Azure AD アクセス レビューを使用するには、有効な Azure AD Premium P2、有料の Enterprise Mobility + Security E5、または評価版ライセンスが必要です。 詳細については、「 [Azure Active Directory のエディション](../fundamentals/active-directory-whatis.md)」をご覧ください。

## <a name="why-would-you-exclude-users-from-policies"></a>ポリシーからユーザーを除外する理由

IT 管理者は、[Azure AD の条件付きアクセス](../conditional-access/overview.md)を使用して、多要素認証 (MFA) を使用した認証または信頼できるネットワークやデバイスからのサインインをユーザーに要求します。 デプロイの計画時には、これらの要件の一部を満たすことができないユーザーもいることがわかります。 たとえば、社内ネットワークに含まれていないリモート オフィスから作業しているユーザーがいたり、サポートされていない古い電話を使用している役員がいたりします。 企業はこのようなユーザーがサインインして仕事をすることを許可する必要があるため、これらのユーザーは条件付きアクセス ポリシーから除外されます。

もう 1 つの例として、条件付きアクセスで[ネームド ロケーション](../conditional-access/location-condition.md)を使用して、ユーザーにテナントへのアクセスを許可したくない国や地域のセットを構成することもできます。

![条件付きアクセスでのネームド ロケーション](./media/conditional-access-exclusion/named-locations.png)

ただし、ユーザーには、これらのブロックされた国や地域からサインインする正当な理由がある場合があります。 たとえば、ユーザーは仕事や個人的な理由で旅行している場合があります。 この例では、これらの国や地域をブロックする条件付きアクセス ポリシーに、ポリシーから除外されたユーザー専用のクラウド セキュリティ グループがあります。 旅行中にアクセスする必要があるユーザーは、[Azure AD のセルフサービスによるグループ管理](../users-groups-roles/groups-self-service-management.md)を使用して、グループに自分自身を追加することができます。

もう 1 つの例では、[大半のユーザーのレガシ認証をブロックする](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/07/azure-ad-conditional-access-support-for-blocking-legacy-auth-is-in-public-preview/)条件付きアクセス ポリシーがあります。 Microsoft は、セキュリティ状態を改善するために、テナントでの従来のプロトコルの使用をブロックすることを強くお勧めしています。 ただし、Office 2010 または IMAP/SMTP/POP ベースのクライアントを使用してリソースにアクセスするために、従来の認証方法を絶対に使用しなければならないユーザーがいる場合は、従来の認証方法をブロックするポリシーからこれらのユーザーを除外することができます。

## <a name="why-are-exclusions-challenging"></a>除外が困難な理由

Azure AD では、ユーザーのグループに条件付きアクセス ポリシーの範囲を指定できます。 また、Azure AD ロール、個々のユーザー、またはゲスト ユーザーを選択することで、これらのユーザーの一部を除外することができます。 このような除外を構成すると、それらのユーザーにはポリシーの意図を適用できなくなることに注意してください。 これらの除外が、個々のユーザーのリストとして、または従来のオンプレミスのセキュリティ グループを使用して構成されている場合は、この除外リストの表示が制限され (ユーザーにその存在がわからないこともあります)、それに対する IT 管理者の制御が制限されます (ユーザーはセキュリティ グループに参加することでポリシーを回避できます)。 さらに、一度除外の対象となったユーザーは、ポリシーを必要としなくなったり、適格ではなくなったりする場合があります。

除外の開始時は、ポリシーをバイパスするユーザーのリストは短いリストです。 時間が経つにつれて、除外されたユーザーが増えていき、リストが長くなっていきます。 ある時点でリストを見直して、これらのユーザーのそれぞれを引き続き除外しておくべきかどうかを確認する必要があります。 技術的な観点でのリストの管理は比較的簡単ですが、誰がビジネス上の意思決定を行い、すべて監査可能であることをどのように確認するのでしょうか。

しかし、Azure AD グループを使用して条件付きアクセス ポリシーに対する除外を構成すれば、アクセス レビューを補完的な制御として使用して、可視性を促進し、例外のあるユーザーの数を減らすことができます。

## <a name="how-to-create-an-exclusion-group-in-a-conditional-access-policy"></a>条件付きアクセス ポリシーの除外グループを作成する方法

次の手順に従って、新しい Azure AD グループと、そのグループに適用されない条件付きアクセス ポリシーを作成します。

### <a name="create-an-exclusion-group"></a>除外グループの作成

1. Azure ポータルにサインインします。

1. 左側のナビゲーションで、 **[Azure Active Directory]** をクリックして、 **[グループ]** をクリックします。

1. 上部のメニューで、 **[新しいグループ]** をクリックしてグループ ウィンドウを開きます。

1. **[グループの種類]** リストで **[セキュリティ]** を選択します。 名前と説明を指定します。

1. **[メンバーシップ]** の種類は必ず **[割り当て済み]** に設定してください。

1. この除外グループに含める必要があるユーザーを選択し、 **[作成]** をクリックします。

    ![Azure Active Directory の [新しいグループ] ウィンドウ](./media/conditional-access-exclusion/new-group.png)

### <a name="create-a-conditional-access-policy-that-excludes-the-group"></a>グループを除外する条件付きアクセス ポリシーの作成

これで、この除外グループを使用する条件付きアクセス ポリシーを作成できるようになりました。

1. 左側のナビゲーションで **[Azure Active Directory]** をクリックしてから、 **[条件付きアクセス]** をクリックして **[ポリシー]** ブレードを開きます。

1. **[新しいポリシー]** をクリックして **[新規]** ウィンドウを開きます。

1. 名前を指定します。

1. [割り当て] の下の **[ユーザーとグループ]** をクリックします。

1. **[含める]** タブで **[すべてのユーザー]** を選択します。

1. **[除外**] タブで、 **[ユーザーとグループ]** にチェック マークを追加して、 **[対象外とするユーザーの選択]** をクリックします。

1. 作成した除外グループを選択します。

    > [!NOTE]
    > ベスト プラクティスとして、テナントから確実にロックアウトされていないことをテストする際は、少なくとも 1 つの管理者アカウントをポリシーから除外することをお勧めします。

1. 引き続き、組織の要件に基づいて条件付きアクセス ポリシーを設定します。

    ![条件付きアクセスでの [対象外とするユーザーの選択] ウィンドウ](./media/conditional-access-exclusion/select-excluded-users.png)

アクセス レビューを使用して条件付きアクセス ポリシーの除外を管理する 2 つの例について説明します。

## <a name="example-1-access-review-for-users-accessing-from-blocked-countriesregions"></a>例 1:ブロックされている国や地域からアクセスするユーザーのアクセス レビュー

特定の国や地域からのアクセスをブロックする条件付きアクセス ポリシーがあるとします。 これには、ポリシーから除外されているグループが含まれます。 次に示す推奨されるアクセス レビューでは、グループのメンバーがレビューされます。

> [!NOTE]
> アクセス レビューを作成するには、グローバル管理者またはユーザー管理者のロールが必要です。

1. このレビューは毎週繰り返し行われ、

2. この除外グループを常に最新の状態に保つために、終わることはありません。

3. このグループのすべてのメンバーがレビューの対象範囲に入ります。

4. 各ユーザーは、自分がこれらのブロックされている国や地域から引き続きアクセスする必要があり、そのために引き続きグループのメンバーである必要があることを、自分で証明する必要があります。

5. ユーザーがレビュー要求に応答しない場合、そのユーザーはグループから自動的に削除されるため、これらの国や地域に旅行中はテナントにアクセスできなくなります。

6. メール通知を有効にして、ユーザーにアクセス レビューの開始と完了について通知されるようにします。

    ![例 1 の [アクセス レビューを作成する] ウィンドウ](./media/conditional-access-exclusion/create-access-review-1.png)

## <a name="example-2-access-review-for-users-accessing-with-legacy-authentication"></a>例 2:例 2: レガシ認証を使用してアクセスしているユーザーのアクセス レビュー

レガシ認証と古いバージョンのクライアントを使用しているユーザーのアクセスをブロックする条件付きアクセス ポリシーがあるとします。 これには、ポリシーから除外されているグループが含まれます。 次に示す推奨されるアクセス レビューでは、グループのメンバーがレビューされます。

1. このレビューは定期的なレビューである必要があります。

2. グループの全員がレビューされる必要があります。

3. 選択したレビュー担当者として部署の所有者をリストするように構成できます。

4. 結果を自動適用し、従来の認証方法を引き続き使用することが承認されなかったユーザーを削除します。

5. 大規模なグループのレビュー担当者が簡単に意思決定できるような推奨事項を有効にすることをお勧めします。

6. メール通知を有効にして、ユーザーにアクセス レビューの開始と完了について通知されるようにします。

    ![例 2 の [アクセス レビューを作成する] ウィンドウ](./media/conditional-access-exclusion/create-access-review-2.png)

**Pro ヒント**:多数の除外グループがあるために、複数のアクセス レビューを作成する必要がある場合は、Microsoft Graph のベータ版エンドポイントの API で、それらをプログラムで作成および管理できるようになりました。 最初に、[Azure AD アクセス レビューの API リファレンス](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/accessreviews_root)に関するページと、[Microsoft Graph を使用した Azure AD アクセスの取得の例](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/td-p/236096)に関するページを参照してください。

## <a name="access-review-results-and-audit-logs"></a>アクセス レビューの結果と監査ログ

これで、グループ、条件付きアクセス ポリシー、およびアクセス レビューの準備がすべて整ったので、今度はこれらのレビューの結果を監視および追跡してみましょう。

1. Azure portal で **[アクセス レビュー]** ブレードを開きます。

1. 除外グループを管理するために作成したコントロールとプログラムを開きます。

1. **[結果]** をクリックして、リストに残すことが承認されたユーザーと削除されたユーザーを確認します。

    ![承認されたユーザーを示すアクセス レビューの結果](./media/conditional-access-exclusion/access-reviews-results.png)

1. 次に、 **[監査ログ]** をクリックして、このレビュー中に実行されたアクションを確認します。

    ![アクションの一覧を示すアクセス レビューの監査ログ](./media/conditional-access-exclusion/access-reviews-audit-logs.png)

IT 管理者は、ポリシーに照らした除外グループの管理が、時には不可避であることがわかっています。 ただし、これらのグループの維持、ビジネス オーナーやユーザー自身による定期的なレビュー、これらの変更の監査は、Azure AD アクセス レビューを使えば簡単に行えます。

## <a name="next-steps"></a>次の手順

- [グループまたはアプリケーションのアクセス レビューを作成する](create-access-review.md)
- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)
