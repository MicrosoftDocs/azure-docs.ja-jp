---
title: Azure AD Domain Services での Secure LDAP (LDAPS) のトラブルシューティング | Microsoft Docs
description: Azure AD Domain Services のマネージド ドメインに対する Secure LDAP (LDAPS) のトラブルシューティング
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 445c60da-e115-447b-841d-96739975bdf6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: a194919a488f6cb59c76315c9d8a3db1e9c1feb1
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2019
ms.locfileid: "67472198"
---
# <a name="troubleshoot-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services のマネージド ドメインに対する Secure LDAP (LDAPS) のトラブルシューティング

## <a name="connection-issues"></a>接続に関する問題
Secure LDAP を使用したマネージド ドメインへの接続に問題がある場合:

* Secure LDAP 証明書の発行者チェーンは、クライアントで信頼されている必要があります。 信頼を確立するために、信頼されたルート証明書ストアにルート証明機関を追加することもできます。
* LDAP クライアント (ldp.exe など) が、IP アドレスではなく、DNS 名を使用して Secure LDAP エンドポイントに接続していることを確認します。
* LDAP クライアントの接続先の DNS 名を確認します。 この DNS 名は、マネージド ドメイン上の Secure LDAP に対するパブリック IP アドレスに解決される必要があります。
* マネージド ドメインの Secure LDAP 証明書の "サブジェクト" 属性または "サブジェクトの別名" 属性に、上記の DNS 名が含まれていることを確認します。
* 仮想ネットワークの NSG の設定では、インターネットからポート 636 へのトラフィックを許可する必要があります。 このステップは、インターネット経由での Secure LDAP を有効にしている場合にのみ適用されます。


## <a name="need-help"></a>お困りの際は、
Secure LDAP を使用したマネージド ドメインへの接続の問題が解決しない場合は、支援を得るために[製品チームに連絡](contact-us.md)してください。 問題を適切に診断できるように、次の情報を含めます。
* ldp.exe が接続に失敗したことを示しているスクリーンショット。
* Azure AD テナント ID と、マネージド ドメインの DNS ドメイン名。
* バインドしようとしている正確なユーザー名。


## <a name="related-content"></a>関連コンテンツ
* [Azure AD ドメイン サービス - 作業開始ガイド](create-instance.md)
* [Azure AD Domain Services ドメインを管理する](manage-domain.md)
* [LDAP query basics](https://technet.microsoft.com/library/aa996205.aspx) (LDAP クエリの基本)
* [Azure AD Domain Services のグループ ポリシーを管理する](manage-group-policy.md)
* [ネットワーク セキュリティ グループ](../virtual-network/security-overview.md)
* [ネットワーク セキュリティ グループの作成](../virtual-network/tutorial-filter-network-traffic.md)
