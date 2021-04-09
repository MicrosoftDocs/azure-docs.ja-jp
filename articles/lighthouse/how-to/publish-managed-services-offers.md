---
title: Azure Marketplace にマネージド サービス オファーを発行する
description: Azure Lighthouse に顧客をオンボードするマネージド サービス オファーを発行する方法について説明します。
ms.date: 02/17/2021
ms.topic: how-to
ms.openlocfilehash: b906ba9961ae4f77ca16890fd0a9036123343111
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "101092148"
---
# <a name="publish-a-managed-service-offer-to-azure-marketplace"></a>Azure Marketplace にマネージド サービス オファーを発行する

この記事では、Partner Center の[コマーシャル マーケットプレース](../../marketplace/overview.md) プログラムを使用して、パブリックまたはプライベートのマネージド サービス オファーを [Azure Marketplace](https://azuremarketplace.microsoft.com) に発行する方法について説明します。 オファーを購入した顧客は、その後サブスクリプションまたはリソース グループを委任します。これにより、お客様は [Azure Lighthouse](../overview.md) を通じて顧客を管理できるようになります。

## <a name="publishing-requirements"></a>発行要件

オファーを作成して発行するには、[パートナー センターの有効なアカウント](../../marketplace/partner-center-portal/create-account.md)が必要です。 まだアカウントをお持ちでない場合は、[サインアップ プロセス](https://aka.ms/joinmarketplace)の手順に従って、パートナー センターのアカウントを作成し、コマーシャル マーケットプレース プログラムに登録します。

[マネージド サービス オファーの認定要件](/legal/marketplace/certification-policies#700-managed-services)に従って、マネージド サービス オファーを発行するには、[Silver または Gold Cloud Platform コンピテンシー レベル](/partner-center/learn-about-competencies)を取得しているか、または [Azure Expert MSP](https://partner.microsoft.com/membership/azure-expert-msp) である必要があります。 また、顧客がオファーをデプロイするたびに、[CRM システムにレコードを作成するリードのターゲットを入力する](../../marketplace/plan-managed-service-offer.md#customer-leads)必要があります。

オファーを Azure Marketplace に発行しない場合や、すべての要件を満たしていない場合は、Azure Resource Manager テンプレートを使用して、顧客を手動でオンボードできます。 詳細については、「[Azure Lighthouse への顧客のオンボード](onboard-customer.md)」をご覧ください。

次の表は、顧客をオンボードするために、マネージド サービス オファーを発行するか、Azure Resource Manager テンプレートを使用するかを判断するのに役立ちます。

|**考慮事項**  |**マネージド サービス オファー**  |**ARM テンプレート**  |
|---------|---------|---------|
|[パートナー センター アカウント](../../marketplace/partner-center-portal/create-account.md)が必要   |はい         |いいえ        |
|[Silver または Gold Cloud Platform コンピテンシー レベル](/partner-center/learn-about-competencies)または [Azure Expert MSP](https://partner.microsoft.com/membership/azure-expert-msp) が必要      |はい         |いいえ         |
|新規顧客は Azure Marketplace を通して利用可能     |はい     |いいえ       |
|特定の顧客に対してオファーを制限できる     |はい (プライベート オファーを使用する場合のみ。これは、クラウド ソリューション プロバイダー (CSP) プログラムのリセラーを通して確立されたサブスクリプションでは使用できません)         |はい         |
|顧客が Azure portal で同意する必要がある     |はい     |いいえ   |
|オートメーションを使用して複数のサブスクリプション、リソースグループ、または顧客をオンボードできる |いいえ     |はい    |
|新しい組み込みロールと Azure Lighthouse 機能への即座のアクセス     |常にではない (しばらく延期の後に一般公開)         |はい         |

> [!NOTE]
> マネージド サービス オファーは、Azure Government およびその他の国内クラウドでは利用できない場合があります。

## <a name="create-your-offer"></a>プランを作成する

提供する必要があるすべての情報と資産を含む、オファーを作成する方法の詳細については、「[管理サービス オファーの作成](../../marketplace/create-managed-service-offer.md)」を参照してください。

一般的な発行プロセスについては、[コマーシャル マーケットプレイスのドキュメント](../../marketplace/overview.md)を参照してください。 さらに、「[商業マーケットプレースの認定ポリシー](/legal/marketplace/certification-policies)」(特に「[マネージド サービス](/legal/marketplace/certification-policies#700-managed-services)」セクション) も確認してください。

顧客がオファーを追加すると、1 つ以上のサブスクリプションまたはリソース グループを委任できるようになり、これらは [Azure Lighthouse にオンボード](#the-customer-onboarding-process)されます。

> [!IMPORTANT]
> マネージド サービス オファー内の各プランには、 **[Manifest Details]\(マニフェストの詳細\)** セクションが含まれています。そこでは、そのプランを購入したお客様に代わり、委任されたリソース グループやサブスクリプションへのアクセス権を持つ Azure Active Directory (Azure AD) エンティティをお客様のテナント内に定義します。 含まれるグループ (またはユーザーまたはサービス プリンシパル) は、そのプランを購入したすべての顧客に対して同じアクセス許可を持つことに注意する必要があります。 顧客ごとに異なる担当グループを割り当てるには、各顧客専用の個別の[プライベート プラン](../../marketplace/private-offers.md)を発行することができます。 プライベート プランは、クラウド ソリューション プロバイダー (CSP) プログラムのリセラーを通じて確立されたサブスクリプションではサポートされないことにご注意ください。

## <a name="publish-your-offer"></a>実際のオファーを発行する

すべてのセクションの指定が済んだら、Azure Marketplace にオファーを発行します。 **[発行]** ボタンを選択して、お客様のオファーを一般公開するプロセスを始めます。 このプロセスの詳細については、[こちら](../../marketplace/review-publish-offer.md)を参照してください。

いつでも[更新済みバージョンのオファー](../..//marketplace/partner-center-portal/update-existing-offer.md)を公開できます。 たとえば、以前に公開されたオファーに新しいロールの定義を追加したい場合があります。 そうすると、オファーを既に追加している顧客には、Azure portal の [ **[サービス プロバイダー]**](view-manage-service-providers.md) ページに、更新が利用可能であることを知らせるアイコンが表示されます。 各顧客は、[変更を確認](view-manage-service-providers.md#update-service-provider-offers)して、新しいバージョンに更新するかどうかを決定できます。 

## <a name="the-customer-onboarding-process"></a>顧客オンボーディング プロセス

顧客がオファーを追加すると、[1 つ以上の特定のサブスクリプションまたはリソース グループを委任](view-manage-service-providers.md#delegate-resources)できるようになり、これらは Azure Lighthouse にオンボードされます。 顧客がオファーを承諾しても、まだリソースを委任していなければ、Azure portal の [ **[サービス プロバイダー]**](view-manage-service-providers.md) ページの **[プロバイダーのオファー]** セクションの上部に注意書きが表示されます。

> [!IMPORTANT]
> 委任は、オンボード対象の (またはオンボード対象のリソース グループを含む) サブスクリプションに対して、`Microsoft.Authorization/roleAssignments/write` のアクセス許可を持つ[所有者](../../role-based-access-control/built-in-roles.md#owner)などのロールが割り当てられている、顧客のテナント内のゲスト以外のアカウントで実行する必要があります。 サブスクリプションを委任できるユーザーを見つけるには、顧客のテナント内のユーザーが Azure portal 上でサブスクリプションを選択し、 **[アクセス制御 (IAM)]** を開くと、[所有者ロールを持つすべてのユーザーを表示](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription)することができます。

顧客がサブスクリプション (またはサブスクリプション内の 1 つまたは複数のリソース グループ) を委任した後、そのサブスクリプションに **Microsoft.ManagedServices** リソース プロバイダーが登録され、テナント内のユーザーは、オファー内の承認に従って、委任されたリソースにアクセスできるようになります。

## <a name="next-steps"></a>次のステップ

- [コマーシャル マーケットプレース](../../marketplace/overview.md)について説明します。
- [パートナー ID をリンク](partner-earned-credit.md)して、顧客エンゲージメント全体に対するお客様の影響を追跡します。
- [テナント間の管理エクスペリエンス](../concepts/cross-tenant-management-experience.md)について学習します。
- Azure portal の **[マイ カスタマー]** に移動して、[顧客を表示および管理](view-manage-customers.md)します。
