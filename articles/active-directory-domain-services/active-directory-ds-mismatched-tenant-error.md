---
title: "既存の Azure AD Domain Services 管理対象ドメインでのディレクトリの不一致エラーを解決する | Microsoft Docs"
description: "既存の Azure AD Domain Services 管理対象ドメインでのディレクトリの不一致エラーの詳細と解決方法"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 40eb75b7-827e-4d30-af6c-ca3c2af915c7
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: maheshu
ms.openlocfilehash: 118773be1f03701246051b8832695c591d76b1e5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="resolve-mismatched-directory-errors-for-existing-azure-ad-domain-services-managed-domains"></a>既存の Azure AD Domain Services 管理対象ドメインでのディレクトリの不一致エラーを解決する
Azure クラシック ポータルを使用して有効化された、既存の管理対象ドメインがあるとします。 新しい Azure Portal にアクセスしてこの管理対象ドメインを表示すると、次のエラー メッセージが表示されます。

![ディレクトリの不一致エラー](.\media\getting-started\mismatched-tenant-error.png)

エラーが解決されるまで、この管理対象ドメインを管理することはできません。


## <a name="whats-causing-this-error"></a>このエラーの原因
このエラーは、管理対象ドメインとこのドメインが有効になっている仮想ネットワークが、2 つの異なる Azure AD テナントに属している場合に生じます。 たとえば、'contoso.com' という管理対象ドメインがあり、Contoso の Azure AD テナントに対して有効化されているとします。 ただし、管理対象ドメインが有効になっている Azure 仮想ネットワークは、別の Azure AD テナントである Fabrikam に属しています。

新しい Azure Portal (および特に Azure AD Domain Services 拡張機能) は、Azure Resource Manager 上に構築されています。 最新の Azure Resource Manager 環境では、より高度なセキュリティを提供し、リソースに対するロールベースのアクセス制御 (RBAC) を実現するため、特定の制限が掛けられています。 Azure AD テナントに対して Azure AD Domain Services を有効化すると、資格情報ハッシュが管理対象ドメインと同期されるようになるため、慎重に行う必要があります。 この操作では、ユーザーはディレクトリのテナント管理者である必要があります。 さらに、管理対象ドメインを有効化する仮想ネットワークに対する管理特権を持っている必要もあります。 RBAC によるチェックが絶えず行われるようにするには、管理対象ドメインと仮想ネットワークが同一の Azure AD テナントに属している必要があります。

つまり、"contoso.com" という Azure AD テナントの管理対象ドメインを、別のAzure AD テナント "fabrikam.com" が所有する Azure サブスクリプションに属する仮想ネットワークで有効化することはできないということです。 Azure クラシック ポータルは Resource Manager プラットフォーム上に構築されていないため、このような制限は適用されません。

**有効な構成**: 次のデプロイ シナリオでは、Contoso の管理対象ドメインが Contoso という Azure AD テナントに対して有効化されています。 この管理対象ドメインは、Azure AD テナント Contoso が所有する Azure サブスクリプションに属する仮想ネットワークで公開されています。 このため、管理対象ドメインと仮想ネットワークの両方が同じ Azure AD テナントに属しています。 この構成は有効であり、完全にサポートされます。

![有効なテナント構成](./media/getting-started/valid-tenant-config.png)

**テナントが一致しない構成**: 次のデプロイ シナリオでは、Contoso の管理対象ドメインが Contoso という Azure AD テナントに対して有効化されています。 しかし、この管理対象ドメインが公開されている仮想ネットワークは、Fabrikam という Azure AD テナントが所有する Azure サブスクリプションに属しています。 このため、管理対象ドメインと仮想ネットワークは 2 つの異なる Azure AD テナントに属しています。 この構成はテナントが一致しない構成であり、サポートされません。 仮想ネットワークを、管理対象ドメインと同じ Azure AD テナント (つまり、Contoso) に移動する必要があります。 詳細については、「[解決策](#resolution)」セクションを参照してください。

![テナントが一致しない構成](./media/getting-started/mismatched-tenant-config.png)

このため、管理対象ドメインとこのドメインが有効になっている仮想ネットワークが 2 つの異なる Azure AD テナントに属しているシナリオでは、上記のエラーが表示されます。

Resource Manager 環境では次のルールが適用されます。
- 1 つの Azure AD ディレクトリで複数の Azure サブスクリプションを所有することができる。
- 1 つの Azure サブスクリプションで複数のリソース (仮想ネットワークなど) を所有することができる。
- 1 つの Azure AD ディレクトリに対して有効化する Azure AD Domain Services 管理対象ドメインは 1 つだけである。
- Azure AD Domain Services 管理対象ドメインは、同一の Azure AD テナント内にあるいずれかの Azure サブスクリプションに属する仮想ネットワークで有効化することができる。


## <a name="resolution"></a>解決策
ディレクトリの不一致エラーの解決策は 2 つあります。 次のいずれかの手順を実施します。

- **[削除]** ボタンをクリックして、既存の管理対象ドメインを削除します。 [Azure Portal](https://portal.azure.com) を使用して管理対象ドメインを再作成し、管理対象ドメインと、このドメインを利用可能な仮想ネットワークが同じ Azure AD ディレクトリに属するようにします。 削除したドメインに以前参加していたマシンすべてを、新しく作成した管理対象ドメインにもう一度参加させる必要があります。

- 仮想ネットワークが含まれる Azure サブスクリプションを、管理対象ドメインが属する Azure AD ディレクトリへ移動するように Azure サポートに依頼します。 **[新しいサポート要求]** をクリックし、サポート要求の **[詳細]** セクションに「**mismatched directory (一致しないディレクトリ)**」と記入します。 エラー メッセージで表示された情報を、サポート要求の一部に含めます。


## <a name="related-content"></a>関連コンテンツ
* [Azure AD Domain Services - 作業開始ガイド](active-directory-ds-getting-started.md)
* [トラブルシューティング ガイド - Azure AD Domain Services](active-directory-ds-troubleshooting.md)
