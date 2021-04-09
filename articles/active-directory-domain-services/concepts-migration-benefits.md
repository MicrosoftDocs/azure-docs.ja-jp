---
title: Azure AD Domain Services でのクラシック デプロイの移行による利点 |Microsoft Docs
description: Azure Active Directory Domain Services のクラシック デプロイを、Resource Manager デプロイ モデルに移行する利点について説明します。
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: justinha
ms.openlocfilehash: 8cc5f5ebf389d35df02474d0561dc7827cde4d0b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96620088"
---
# <a name="benefits-of-migration-from-the-classic-to-resource-manager-deployment-model-in-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services のクラシックから Resource Manager デプロイ モデルへの移行による利点

Azure Active Directory Domain Services (Azure AD DS) では、クラシック デプロイ モデルを使用する既存のマネージド ドメインを Resource Manager デプロイ モデルに移行できます。 Resource Manager デプロイ モデルを使用する Azure AD DS マネージド ドメインでは、詳細なパスワード ポリシー、監査ログ、アカウント ロックアウト保護などの追加機能が提供されます。

この記事では、移行による利点について概説します。 作業を開始するには、「[クラシック仮想ネットワーク モデルから Resource Manager への Azure AD Domain Services の移行][howto-migrate]」を参照してください。

> [!NOTE]
> 2017 年に、Azure AD Domain Services は、Azure Resource Manager ネットワークでホストするために使用できるようになりました。 それ以後、Azure Resource Manager の最新機能を使用して、より安全なサービスを構築できるようになっています。 Azure Resource Manager デプロイによってクラシック デプロイは完全に置き換えられるため、Azure AD DS のクラシック仮想ネットワークのデプロイは 2023 年 3 月 1 日に廃止されます。
>
> 詳細については、[公式の非推奨に関する通知](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)を参照してください

## <a name="migration-benefits"></a>移行の利点

移行プロセスでは、クラシック デプロイ モデルを使用する既存のマネージド ドメインを選択し、Resource Manager デプロイ モデルを使用するように移動します。 マネージド ドメインをクラシック デプロイ モデルから Resource Manager デプロイ モデルに移行すると、コンピューターをマネージド ドメインに再参加させたり、マネージド ドメインを削除して最初から作成したりする必要がなくなります。 移行プロセスの終わりの時点で、VM は引き続きマネージド ドメインに参加しています。

移行後は Azure AD DS によって、Resource Manager デプロイ モデルを使用しているドメインでのみ利用可能な多くの機能が提供されます。次に例を示します。

* [細かい設定が可能なパスワード ポリシーのサポート][password-policy]。
* Azure AD と Azure AD Domain Services 間の同期速度の向上。
* 2 つの新しい [属性が Azure AD から同期される][attributes] - *manager* と *employeeID*。
* [SKU をアップグレード][skus]した場合の、より強力なドメイン コントローラーへのアクセス。
* AD アカウントのロックアウトによる保護。
* [マネージド ドメインのアラートのための電子メール通知][email-alerts]。
* [Azure ブックと Azure Monitor を使用して、監査ログおよびサインイン アクティビティを表示します][workbooks]。
* サポートされているリージョンでの [Azure Availability Zones][availability-zones]。
* [Azure Files][azure-files]、[HD Insights][hd-insights]、[Windows Virtual Desktop][wvd] などの他の Azure 製品との統合。
* サポートはより多くのテレメトリにアクセスでき、より効果的なトラブルシューティングに役立てることができます。
* マネージド ドメイン コントローラー上のデータに対する、[Azure Managed Disks][managed-disks] を使用した保存時の暗号化。

Resource Manager デプロイ モデルを使用するマネージド ドメインは、最新の機能を使用して最新の状態を維持するのに役立ちます。 クラシック デプロイ モデルを使用するマネージド ドメインでは、新機能を使用できません。

## <a name="next-steps"></a>次のステップ

作業を開始するには、「[クラシック仮想ネットワーク モデルから Resource Manager への Azure AD Domain Services の移行][howto-migrate]」を参照してください。

<!-- LINKS - INTERNAL -->
[password-policy]: password-policy.md
[skus]: change-sku.md
[email-alerts]: notifications.md
[workbooks]: use-azure-monitor-workbooks.md
[azure-files]: ../storage/files/storage-files-identity-auth-active-directory-domain-service-enable.md
[hd-insights]: ../hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds.md
[wvd]: ../virtual-desktop/overview.md
[availability-zones]: ../availability-zones/az-overview.md
[howto-migrate]: migrate-from-classic-vnet.md
[attributes]: synchronization.md#attribute-synchronization-and-mapping-to-azure-ad-ds
[managed-disks]: ../virtual-machines/managed-disks-overview.md
