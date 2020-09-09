---
title: サブスクライバーと CSP の間で Azure サブスクリプションを譲渡する
description: サブスクライバーと CSP の間で Azure サブスクリプションを譲渡する方法について説明します。
author: bandersmsft
ms.reviewer: dhgandhi
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 52dd9d2f6299f8d574934e7baec54333d2ffc0c8
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "88997576"
---
# <a name="transfer-azure-subscriptions-between-subscribers-and-csps"></a>サブスクライバーと CSP の間で Azure サブスクリプションを譲渡する

この記事では、クラウド ソリューション プロバイダー (CSP) パートナーと CSP の顧客との間で、Azure サブスクリプションを譲渡するために使用される手順の概要について説明します。

## <a name="transfer-ea-subscriptions-to-a-csp-partner"></a>EA サブスクリプションを CSP パートナーに譲渡する

[Azure Expert マネージド サービス プロバイダー (MSP)](https://partner.microsoft.com/membership/azure-expert-msp) として認定されている CSP 直接請求パートナーは、ダイレクト Enterprise Agreement (EA) を使用している顧客に対して Azure サブスクリプションの譲渡を要求できます。 サブスクリプションの譲渡は、Microsoft 顧客契約 (MCA) に同意し、Azure プランを購入した顧客に対してのみ許可されます。

要求が承認されると、CSP は顧客に対して結合された請求書を提供できます。 CSP のサブスクリプション譲渡の詳細については、「[MPA アカウントに Azure サブスクリプションの課金所有権を取得する](mpa-request-ownership.md)」を参照してください。

>[!IMPORTANT]
> EA サブスクリプションを CSP パートナーに譲渡した後は、以前に EA サブスクリプションに適用されたすべてのクォータの増加が既定値にリセットされます。 サブスクリプションの譲渡後に追加のクォータが必要な場合は、CSP プロバイダーに[クォータの増加](https://docs.microsoft.com/azure/azure-portal/supportability/regional-quota-requests)の要求を送信してもらってください。 

## <a name="other-subscription-transfers-to-a-csp-partner"></a>CSP パートナーへの他のサブスクリプション譲渡

他の Azure サブスクリプションを CSP パートナーに譲渡するには、サブスクライバーがソース サブスクリプションから CSP サブスクリプションにリソースを移動する必要があります。 サブスクリプション間でリソースを移動するには、次のガイダンスに従います。

1. CSP パートナーと協力して、ターゲットの Azure CSP サブスクリプションを作成します。
1. ソース サブスクリプションとターゲットの CSP サブスクリプションが同じ Azure Active Directory (Azure AD) テナントにあることを確認します。  
    Azure CSP サブスクリプションの Azure AD テナントを変更することはできません。 その代わりに、ソース サブスクリプションを CSP Azure AD テナントに追加するか、関連付ける必要があります。 詳細については、「[Azure サブスクリプションを Azure Active Directory テナントに関連付けるまたは追加する](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)」を参照してください。
    > [!IMPORTANT]
    > - サブスクリプションを別の Azure AD ディレクトリに関連付けると、[Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md) を使用してロールが割り当てられているユーザーはアクセスできなくなります。 サービス管理者や共同管理者などの従来のサブスクリプション管理者もアクセスできなくなります。
    > - そのサブスクリプションが別のディレクトリに関連付けられている場合は、ポリシー割り当てもサブスクリプションから削除されます。
1. 譲渡を実行するために使用するユーザー アカウントには、両方のサブスクリプションで [RBAC](add-change-subscription-administrator.md) 所有者アクセス権が必要です。
1. 開始する前に、すべての Azure リソースをソース サブスクリプションから宛先サブスクリプションに移動できることを[検証](/rest/api/resources/resources/validatemoveresources)します。  
    サブスクリプション間で移動することができない Azure リソースもあります。 移動できる Azure リソースの完全な一覧については、「[リソースの移動操作のサポート](../../azure-resource-manager/management/move-support-resources.md)」を参照してください。
    > [!IMPORTANT]
    >  - Azure CSP は Azure Resource Manager のリソースにのみ対応しています。 ソース サブスクリプションのいずれかの Azure リソースが Azure クラシック デプロイ モデルを使って作成されている場合は、移行前にそれらを [Azure Resource Manager](https://docs.microsoft.com/azure/cloud-solution-provider/migration/ea-payg-to-azure-csp/ea-open-direct-asm-to-arm) に移す必要があります。 Web ページを表示するには、パートナーである必要があります。

1. すべてのソース サブスクリプション サービスが Azure Resource Manager モデルを使用していることを確認します。 次に、[Azure Resource Move](../../azure-resource-manager/management/move-resource-group-and-subscription.md) を使用して、ソース サブスクリプションから宛先サブスクリプションにリソースを譲渡します。
    > [!IMPORTANT]
    >  - サブスクリプション間で Azure リソースを移動すると、サブスクリプションのリソースに基づいてサービスのダウンタイムが発生する可能性があります。

## <a name="transfer-csp-subscription-to-other-offer"></a>CSP サブスクリプションを他のオファーに譲渡する

他のサブスクリプションを CSP パートナーから他の Azure オファーに譲渡するには、サブスクライバーがソース CSP サブスクリプションとターゲット サブスクリプションの間でリソースを移動する必要があります。

1. ターゲットの Azure サブスクリプションを作成します。
1. 移行元サブスクリプションと移行先サブスクリプションが同じ Azure Active Directory (Azure AD) テナントにあることを確認します。 Azure AD テナントの変更について詳しくは、「[Azure サブスクリプションを Azure Active Directory テナントに関連付けるまたは追加する](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)」を参照してください。
    変更ディレクトリは CSP サブスクリプションではないことに注意してください。 たとえば、CSP から従量課金制のサブスクリプションに譲渡するとします。 従量課金制のサブスクリプションのディレクトリを変更して、そのディレクトリに一致させる必要があります。

    > [!IMPORTANT]
    >  - サブスクリプションを別のディレクトリに関連付けると、[RBAC](../../role-based-access-control/role-assignments-portal.md) を使用してロールが割り当てられているユーザーはアクセスできなくなります。 サービス管理者や共同管理者などの従来のサブスクリプション管理者もアクセスできなくなります。
    >  - そのサブスクリプションが別のディレクトリに関連付けられている場合は、ポリシー割り当てもサブスクリプションから削除されます。

1. 譲渡を実行するために使用するユーザー アカウントには、両方のサブスクリプションで [RBAC](add-change-subscription-administrator.md) 所有者アクセス権が必要です。
1. 開始する前に、すべての Azure リソースをソース サブスクリプションから宛先サブスクリプションに移動できることを[検証](/rest/api/resources/resources/validatemoveresources)します。
    > [!IMPORTANT]
    >  - サブスクリプション間で移動することができない Azure リソースもあります。 移動できる Azure リソースの完全な一覧については、「[リソースの移動操作のサポート](../../azure-resource-manager/management/move-support-resources.md)」を参照してください。

1. [Azure Resource Move](../../azure-resource-manager/management/move-resource-group-and-subscription.md) を使用して、ソース サブスクリプションから宛先サブスクリプションにリソースを譲渡します。
    > [!IMPORTANT]
    >  - サブスクリプション間で Azure リソースを移動すると、サブスクリプションのリソースに基づいてサービスのダウンタイムが発生する可能性があります。

## <a name="next-steps"></a>次のステップ
- [MPA アカウントに Azure サブスクリプションの課金所有権を取得する](mpa-request-ownership.md)。
- [Azure Billing を使用したアカウントとサブスクリプションの管理](../index.yml)方法を確認する。
