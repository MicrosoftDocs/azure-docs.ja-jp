---
title: Azure AD Domain Services のレプリカ セットの概念 | Microsoft Docs
description: Azure Active Directory Domain Services のレプリカ セットと、これが ID サービスを必要とするアプリケーションにどのようにして冗長性を提供するかについて説明します。
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2021
ms.author: justinha
ms.openlocfilehash: 8eb1560887c08c3f64fa599c39e5577242d2a1e8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101689064"
---
# <a name="replica-sets-concepts-and-features-for-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services のレプリカ セットの概念と機能

Azure Active Directory Domain Services (Azure AD DS) のマネージド ドメインを作成する場合は、一意の名前空間を定義します。 この名前空間はドメイン名 (*aaddscontoso.com* など) であり、2 つのドメイン コントローラー (DC) はその後、選択した Azure リージョンにデプロイされます。 この DC のデプロイは、レプリカ セットと呼ばれます。

マネージド ドメインを拡張して、Azure AD テナントごとに複数のレプリカ セットを使用できます。 レプリカ セットは、Azure AD DS がサポートされている任意の Azure リージョンの、ピアリングされた任意の仮想ネットワークに追加できます。 異なる Azure リージョンにレプリカ セットを追加することで、1 つの Azure リージョンがオフラインになった場合に、レガシ アプリケーションの地理的なディザスター リカバリーが実現されます。

> [!NOTE]
> レプリカ セットでは、1 つの Azure テナント内に複数の一意のマネージド ドメインをデプロイすることはできません。 各レプリカ セットには、同じデータが含まれます。

## <a name="how-replica-sets-work"></a>レプリカ セットのしくみ

*aaddscontoso.com* などのマネージド ドメインを作成すると、初期レプリカ セットが作成されます。 追加のレプリカ セットは同じ名前空間と構成を共有します。 構成、ユーザー ID と資格情報、グループ、グループ ポリシー オブジェクト、コンピューター オブジェクト、およびその他の変更を含む Azure AD DS への変更は、AD DS レプリケーションを使用して、マネージド ドメイン内のすべてのレプリカ セットに適用されます。

仮想ネットワーク内に各レプリカ セットを作成します。 各仮想ネットワークは、マネージド ドメインのレプリカ セットをホストする他のすべての仮想ネットワークにピアリングされている必要があります。 この構成によって、ディレクトリ レプリケーションをサポートするメッシュ ネットワーク トポロジが作成されます。 各レプリカ セットが異なる仮想サブネットにある場合、仮想ネットワークでは複数のレプリカ セットをサポートできます。

すべてのレプリカ セットは同じ Active Directory サイトに配置されます。 その結果、すべての変更は、迅速な収束のためにサイト内レプリケーションを使用して伝搬されます。

> [!NOTE]
> 個別のサイトを定義し、レプリカ セット間のレプリケーション設定を定義することはできません。

次の図は、2 つのレプリカ セットを持つマネージド ドメインを示しています。 最初のレプリカ セットは、ドメイン名前空間を使用して作成されます。 その後、2 番目のレプリカ セットが作成されます。

![2 つのレプリカ セットを持つマネージド ドメインの例の図](./media/concepts-replica-sets/two-replica-set-example.png)

> [!NOTE]
> レプリカ セットが構成されているリージョンでは、レプリカ セットによって認証サービスの可用性が保証されます。 リージョンの障害が発生した場合にアプリケーションで地理的な冗長性を確保するには、マネージド ドメインに依存しているアプリケーション プラットフォームも、もう一方のリージョンに存在する必要があります。
>
> アプリケーションが機能するために必要な他のサービス (Azure VM や Azure App Services など) の回復性は、レプリカ セットによって提供されません。 他のアプリケーション コンポーネントの可用性設計では、アプリケーションを構成するサービスの回復性機能を考慮する必要があります。

次の例では、3 つのレプリカ セットを持つマネージド ドメインを示します。これにより、回復性が向上し、認証サービスの可用性が保証されます。 どちらの例でも、アプリケーション ワークロードは、マネージド ドメインのレプリカ セットと同じリージョンに存在します。

![3 つのレプリカ セットを持つマネージド ドメインの例の図](./media/concepts-replica-sets/three-replica-set-example.png)

## <a name="deployment-considerations"></a>デプロイに関する考慮事項

マネージド ドメインの既定の SKU は *Enterprise* SKU で、複数のレプリカ セットをサポートします。 *Standard* SKU に変更した場合に追加のレプリカ セットを作成するには、マネージド ドメインを *Enterprise* または *Premium* に [アップグレード](change-sku.md)します。

サポートされるレプリカ セットの最大数は、マネージド ドメインを作成したときに作成された最初のレプリカを含めて 4 つです。

各レプリカ セットに対する課金は、ドメイン構成 SKU に基づきます。 たとえば、*Enterprise* SKU を使用し、3 つのレプリカ セットがあるマネージド ドメインがある場合、3 つのレプリカ セットごとに、1 時間単位でサブスクリプションに請求されます。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="can-i-create-a-replica-set-in-subscription-different-from-my-managed-domain"></a>マネージド ドメインとは異なるサブスクリプションにレプリカ セットを作成できますか?

いいえ。 レプリカ セットは、マネージド ドメインと同じサブスクリプションに含まれている必要があります。

### <a name="how-many-replica-sets-can-i-create"></a>レプリカ セットはいくつ作成できますか?

最大 4 つのレプリカ セットを作成できます。つまり、マネージド ドメインの初期レプリカ セットに加えて、3 つのレプリカ セットを追加できます。

### <a name="how-does-user-and-group-information-get-synchronized-to-my-replica-sets"></a>ユーザーとグループの情報はレプリカ セットにどのように同期されますか?

すべてのレプリカ セットは、メッシュ仮想ネットワーク ピアリングを使用して相互に接続されます。 Azure AD からのユーザーとグループの更新は、1 つのレプリカ セットによって受け取られます。 その後、これらの変更は、ピアリングされたネットワーク上のサイト内 AD DS レプリケーションを使用して、他のレプリカ セットにレプリケートされます。

オンプレミスの AD DS と同様に、オフラインの状態が長引くと、レプリケーションが中断される可能性があります。 ピアリングされた仮想ネットワークは推移的ではないため、レプリカ セットの設計要件には、完全にメッシュ化されたネットワーク トポロジが必要です。

### <a name="how-do-i-make-changes-in-my-managed-domain-after-i-have-replica-sets"></a>レプリカ セットを作成した後にマネージド ドメインに変更を加えるにはどうしたらよいですか?

マネージド ドメイン内の変更は、以前と同じように機能します。 [マネージド ドメインに参加している RSAT ツールを使用して、管理 VM を作成して使用](tutorial-create-management-vm.md)します。 マネージド ドメインには、必要な数の管理 VM を参加させることができます。

## <a name="next-steps"></a>次のステップ

レプリカ セットの使用を開始するには、[Azure AD DS マネージド ドメインを作成して構成します][tutorial-create-advanced]。 デプロイされたら、[追加のレプリカ セットを作成して使用します][create-replica-set]。

<!-- LINKS - INTERNAL -->
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-replica-set]: tutorial-create-replica-set.md
