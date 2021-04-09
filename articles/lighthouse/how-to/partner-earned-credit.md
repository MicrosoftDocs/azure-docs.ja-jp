---
title: パートナー ID をリンクして、委任されたリソースに対する影響を追跡する
description: パートナー ID を関連付けて、Azure Lighthouse を通じて管理している顧客のリソースでパートナー獲得クレジット (PEC) を受け取る方法について説明します。
ms.date: 02/12/2021
ms.topic: how-to
ms.openlocfilehash: 4c18aae38570ab3fd84df7d45fb18e35404158be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100372095"
---
# <a name="link-your-partner-id-to-track-your-impact-on-delegated-resources"></a>パートナー ID をリンクして、委任されたリソースに対する影響を追跡する 

[Microsoft Partner Network](https://partner.microsoft.com/) のメンバーは、自分のパートナー ID と、委任された顧客リソースの管理に使用される資格情報をリンクできます。これにより、Microsoft は、Azure の利用者を成功に導いているパートナーを特定し、評価できます。 また、このリンクにより、[CSP (Cloud Solution Provider)](/partner-center/csp-overview) パートナーは、[Microsoft 顧客契約 (MCA) に署名](/partner-center/confirm-customer-agreement)して [Azure プランに加入](/partner-center/azure-plan-get-started)している顧客について、[管理サービスに対するパートナー獲得クレジット (PEC)](/partner-center/partner-earned-credit) を受け取ることができます。

Azure Lighthouse アクティビティの認識を得るには、管理テナントの少なくとも 1 つのユーザー アカウントに [MPN ID をリンク](../../cost-management-billing/manage/link-partner-id.md)し、リンクされたアカウントがオンボードされた各サブスクリプションにアクセスできることを確認する必要があります。

## <a name="associate-your-partner-id-when-you-onboard-new-customers"></a>新しい顧客をオンボードするときにパートナー ID を関連付ける

次のプロセスを使用してパートナー ID をリンクします (また、該当する場合はパートナー獲得クレジットを有効にします)。 これらの手順を完了するには、自分の [MPN パートナー ID](/partner-center/partner-center-account-setup#locate-your-mpn-id) を把握しておく必要があります。 パートナー プロファイルに表示されている **関連付けられている MPN ID** を必ず使用してください。

わかりやすくするために、テナントにサービス プリンシパル アカウントを作成して **関連付けられている MPN ID** にリンクし、[PEC の対象となる Azure の組み込みロール](/partner-center/azure-roles-perms-pec)を使用してオンボードするすべての顧客へのアクセス権を付与することをお勧めします。

1. 管理しているテナント内に[サービス プリンシパルのユーザー アカウントを作成](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)します。 この例では、このサービス プリンシパルのアカウントに *"プロバイダー オートメーション アカウント"* という名前を使用します。
1. そのサービス プリンシパル アカウントを使用して、管理しているテナント内の[関連付けられている MPN ID にリンク](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id)します。 これを行う必要があるのは 1 回だけです。
1. [ARM テンプレート](onboard-customer.md)または[マネージド サービス オファー](publish-managed-services-offers.md)を使用して顧客をオンボードするときに、[PEC の資格がある Azure 組み込みロール](/partner-center/azure-roles-perms-pec)を持つユーザーとして、プロバイダー オートメーション アカウントを含む認可を必ず含めてください。

この手順のようにすると、管理しているすべての顧客テナントがパートナー ID に関連付けられます。 プロバイダー オートメーション アカウントを使用して、顧客テナントでの認証や操作を行う必要はありません。

:::image type="content" source="../media/lighthouse-pal.jpg" alt-text="Azure Lighthouse を使用したパートナー ID のリンク プロセスを示す図。":::

## <a name="add-your-partner-id-to-previously-onboarded-customers"></a>以前にオンボードされた顧客にパートナー ID を追加する

既に顧客をオンボードしている場合、プロバイダー オートメーション アカウントのサービス プリンシパルを追加するために、別のデプロイを実行する必要はありません。 代わりに、その顧客のテナントで作業するためのアクセス権が既に与えられているユーザー アカウントに、**関連付けられている MPN ID** をリンクすることができます。 [PEC の資格がある Azure 組み込みロール](/partner-center/azure-roles-perms-pec)がアカウントに付与されていることを確認してください。

アカウントが、管理しているテナント内の[関連付けられている MPN ID にリンク](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id)されると、その顧客への影響に対する表彰を追跡できるようになります。

## <a name="confirm-partner-earned-credit"></a>パートナー獲得クレジットを確認する

[Azure portal で PEC の詳細を表示](/partner-center/partner-earned-credit-explanation#azure-cost-management)し、PEC の特典を受けたコストを確認することができます。 PEC は、MCA に署名して Azure プランに加入している CSP 顧客にのみ適用されることに注意してください。

上記の手順に従っても、期待される関連付けが表示されない場合は、Azure portal でサポート リクエストを開いてください。

また、[パートナー センター SDK](/partner-center/develop/get-invoice-unbilled-consumption-lineitems) を使用し、`rateOfPartnerEarnedCredit` にフィルターを適用して、サブスクリプションの PEC 検証を自動化することもできます。

## <a name="next-steps"></a>次の手順

- 詳細については、[Microsoft Partner Network](/partner-center/mpn-overview) に関するページを参照してください。
- [PEC の計算と支払いの方法](/partner-center/partner-earned-credit-explanation)を確認する。
- Azure Lighthouse に[顧客をオンボードする](onboard-customer.md)。