---
title: Azure AD Domain Services での Secure LDAP のトラブルシューティング | Microsoft Docs
description: Azure AD Domain Services のマネージド ドメインに対する Secure LDAP (LDAPS) をトラブルシューティングする方法について説明します
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 445c60da-e115-447b-841d-96739975bdf6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/19/2019
ms.author: iainfou
ms.openlocfilehash: 96aa463441c9e0f21e2ef1aa27c566b94e1e5f4f
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71257885"
---
# <a name="troubleshoot-secure-ldap-connectivity-issues-to-an-azure-active-directory-domain-services-managed-domain"></a>Azure Active Directory Domain Services のマネージド ドメインに対する Secure LDAP の接続に関する問題のトラブルシューティング

ライトウェイト ディレクトリ アクセス プロトコル (LDAP) を使用して Azure Active Directory Domain Services (Azure AD DS) と通信するアプリケーションとサービスは、[Secure LDAP を使用するように構成](tutorial-configure-ldaps.md)できます。 Secure LDAP が正しく機能するためには、適切な証明書と必要なネットワーク ポートが開いている必要があります。

この記事は、Azure AD DS での Secure LDAP アクセスに関する問題のトラブルシューティングに役立ちます。

## <a name="common-connection-issues"></a>一般的な接続に関する問題

Secure LDAP を使用した Azure AD DS マネージド ドメインへの接続で問題がある場合は、次のトラブルシューティングの手順を確認してください。 トラブルシューティングの各手順を実行した後、Azure AD DS マネージド ドメインに再度接続を試みてください。

* Secure LDAP 証明書の発行者チェーンは、クライアントで信頼されている必要があります。 信頼を確立するために、クライアント上の信頼されたルート証明書ストアにルート証明機関 (CA) を追加できます。
    * [クライアント コンピューターに証明書をエクスポートして適用していること][client-cert]を確認します。
* マネージド ドメインの Secure LDAP 証明書の *Subject* (サブジェクト) 属性または *Subject Alternative Names* (サブジェクトの別名) 属性に、DNS 名が含まれていることを確認します。
    * [Secure LDAP 証明書の要件][certs-prereqs]を確認し、必要に応じて代替証明書を作成します。
* LDAP クライアント (*ldp.exe* など) が、IP アドレスではなく、DNS 名を使用して Secure LDAP エンドポイントに接続していることを確認します。
    * Azure AD DS マネージド ドメインに適用される証明書には、サービスの IP アドレスは含まれず、DNS 名のみが含まれます。
* LDAP クライアントの接続先の DNS 名を確認します。 これは、Azure AD DS マネージド ドメイン上の Secure LDAP のパブリック IP アドレスに解決される必要があります。
    * DNS 名が内部 IP アドレスに解決される場合は、外部 IP アドレスに解決されるように DNS レコードを更新します。
* 外部接続の場合、ネットワーク セキュリティ グループに、インターネットから TCP ポート 636 へのトラフィックを許可する規則が含まれている必要があります。
    * 仮想ネットワークに直接接続するリソースから Secure LDAP を使用して Azure AD DS マネージド ドメインには接続できるが、外部接続ができない場合は、[Secure LDAP トラフィックを許可するネットワーク セキュリティ グループ規則を作成][ldaps-nsg]してください。

## <a name="next-steps"></a>次の手順

まだ問題が解決しない場合は、さらなるトラブルシューティングの支援を求めて、[Azure サポート リクエストを開いて][azure-support]ください。

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
[certs-prereqs]: tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap
[client-cert]: tutorial-configure-ldaps.md#export-a-certificate-for-client-computers
[ldaps-nsg]: tutorial-configure-ldaps.md#lock-down-secure-ldap-access-over-the-internet
