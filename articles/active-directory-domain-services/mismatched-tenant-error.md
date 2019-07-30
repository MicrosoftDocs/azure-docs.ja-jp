---
title: 既存の Azure AD Domain Services のマネージド ドメインでのディレクトリの不一致エラーを解決する | Microsoft Docs
description: 既存の Azure AD Domain Services のマネージド ドメインでのディレクトリの不一致エラーの詳細と解決方法
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 40eb75b7-827e-4d30-af6c-ca3c2af915c7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: 1ab6a535c9ffebcb423e7a5cb7f158224c004bd1
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2019
ms.locfileid: "67472891"
---
# <a name="resolve-mismatched-directory-errors-for-existing-azure-ad-domain-services-managed-domains"></a>既存の Azure AD Domain Services のマネージド ドメインでのディレクトリの不一致エラーを解決する
既存の Azure AD Domain Services のマネージド ドメインがあります。 Azure Portal にアクセスしてこのマネージド ドメインを表示すると、次のエラー メッセージが表示されます。

![ディレクトリの不一致エラー](./media/getting-started/mismatched-tenant-error.png)

エラーが解決されるまで、このマネージド ドメインを管理することはできません。


## <a name="whats-causing-this-error"></a>このエラーの原因
このエラーは、マネージド ドメインとこのドメインが有効になっている仮想ネットワークが、2 つの異なる Azure AD テナントに属している場合に生じます。 たとえば、'contoso.com' というマネージド ドメインがあり、Contoso の Azure AD テナントに対して有効化されているとします。 ただし、マネージド ドメインが有効になっている Azure 仮想ネットワークは、別の Azure AD テナントである Fabrikam に属しています。

新しい Azure Portal (および特に Azure AD Domain Services 拡張機能) は、Azure Resource Manager 上に構築されています。 最新の Azure Resource Manager 環境では、より高度なセキュリティを提供し、リソースに対するロールベースのアクセス制御 (RBAC) を実現するため、特定の制限が掛けられています。 Azure AD テナントに対して Azure AD Domain Services を有効化すると、資格情報ハッシュがマネージド ドメインと同期されるようになるため、慎重に行う必要があります。 この操作では、ユーザーはディレクトリのテナント管理者である必要があります。 さらに、マネージド ドメインを有効化する仮想ネットワークに対する管理特権を持っている必要もあります。 RBAC によるチェックが絶えず行われるようにするには、マネージド ドメインと仮想ネットワークが同一の Azure AD テナントに属している必要があります。

つまり、"contoso.com" という Azure AD テナントのマネージド ドメインを、別のAzure AD テナント "fabrikam.com" が所有する Azure サブスクリプションに属する仮想ネットワークで有効化することはできないということです。 

**有効な構成**:次のデプロイ シナリオでは、Contoso のマネージド ドメインが Contoso という Azure AD テナントに対して有効化されています。 このマネージド ドメインは、Azure AD テナント Contoso が所有する Azure サブスクリプションに属する仮想ネットワークで公開されています。 このため、マネージド ドメインと仮想ネットワークの両方が同じ Azure AD テナントに属しています。 この構成は有効であり、完全にサポートされます。

![有効なテナント構成](./media/getting-started/valid-tenant-config.png)

**テナントが一致しない構成**:次のデプロイ シナリオでは、Contoso のマネージド ドメインが Contoso という Azure AD テナントに対して有効化されています。 しかし、このマネージド ドメインが公開されている仮想ネットワークは、Fabrikam という Azure AD テナントが所有する Azure サブスクリプションに属しています。 このため、マネージド ドメインと仮想ネットワークは 2 つの異なる Azure AD テナントに属しています。 この構成はテナントが一致しない構成であり、サポートされません。 仮想ネットワークを、マネージド ドメインと同じ Azure AD テナント (つまり、Contoso) に移動する必要があります。 詳細については、「[解決策](#resolution)」セクションを参照してください。

![テナントが一致しない構成](./media/getting-started/mismatched-tenant-config.png)

このため、マネージド ドメインとこのドメインが有効になっている仮想ネットワークが、2 つの異なる Azure AD テナントに属していると、このエラーが表示されます。

Resource Manager 環境では次のルールが適用されます。
- 1 つの Azure AD ディレクトリで複数の Azure サブスクリプションを所有することができる。
- 1 つの Azure サブスクリプションで複数のリソース (仮想ネットワークなど) を所有することができる。
- 1 つの Azure AD ディレクトリに対して有効化する Azure AD Domain Services のマネージド ドメインは 1 つだけである。
- Azure AD Domain Services のマネージド ドメインは、同一の Azure AD テナント内にあるいずれかの Azure サブスクリプションに属する仮想ネットワークで有効化することができる。


## <a name="resolution"></a>解決策
ディレクトリの不一致エラーの解決策は 2 つあります。 次のいずれかの手順を実施します。

- **[削除]** ボタンをクリックして、既存のマネージド ドメインを削除します。 [Azure Portal](https://portal.azure.com) を使用してマネージド ドメインを再作成し、マネージド ドメインと、このドメインを利用可能な仮想ネットワークが同じ Azure AD ディレクトリに属するようにします。 削除したドメインに以前参加していたマシンすべてを、新しく作成したマネージド ドメインに参加させます。

- 仮想ネットワークが含まれる Azure サブスクリプションを、マネージド ドメインが属する Azure AD ディレクトリに移動します。 「[Azure サブスクリプションの所有権を別のアカウントに譲渡する](../billing/billing-subscription-transfer.md)」の手順に従ってください。


## <a name="related-content"></a>関連コンテンツ
* [Azure AD Domain Services - 作業開始ガイド](create-instance.md)
* [トラブルシューティング ガイド - Azure AD Domain Services](troubleshoot.md)
