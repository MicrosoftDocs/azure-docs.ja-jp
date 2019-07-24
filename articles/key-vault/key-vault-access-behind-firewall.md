---
title: ファイアウォールの内側から Key Vault へのアクセス | Microsoft Docs
description: ファイアウォールの内側にあるアプリケーションから Azure Key Vault にアクセスする方法について説明します。
services: key-vault
documentationcenter: ''
author: amitbapat
manager: barbkess
tags: azure-resource-manager
ms.assetid: 50d21774-2ee1-4212-8995-570c9de603c5
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: ambapat
ms.openlocfilehash: 36b431d07ad517d495316963fd6710dafab6c297
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2019
ms.locfileid: "56108638"
---
# <a name="access-azure-key-vault-behind-a-firewall"></a>ファイアウォールの向こう側にある Azure Key Vault へのアクセス

## <a name="what-ports-hosts-or-ip-addresses-should-i-open-to-enable-my-key-vault-client-application-behind-a-firewall-to-access-key-vault"></a>ファイアウォールの内側のキー コンテナー クライアント アプリケーションがキー コンテナーにアクセスできるようにするには、どのポート、ホスト、または IP アドレスを開く必要がありますか。

キー コンテナーにアクセスするには、Key Vault クライアント アプリケーションが、各種の機能のために複数のエンドポイントにアクセスする必要があります。

* Azure Active Directory (Azure AD) 経由の認証。
* Azure Key Vault の管理。 これには、Azure Resource Manager を使用した作成、読み取り、更新、削除、アクセス ポリシーの設定が含まれます。
* Key Vault 自体に格納されているオブジェクト (キーとシークレット) へのアクセスと管理は、Key Vault 固有のエンドポイントを経由して行われます (例: [https://yourvaultname.vault.azure.net](https://yourvaultname.vault.azure.net))。  

実際の構成と環境に応じて、いくつかのバリエーションがあります。

## <a name="ports"></a>ポート

認証、管理、データ プレーン アクセスの 3 つの機能すべてに関して、Key Vault への全トラフィックはポート 443 (HTTPS) を経由します。 ただし CRL については、トラフィックがポート 80 (HTTP) を経由する場合があります。 OCSP がサポートされているクライアントは、CRL には到達しませんが、[http://cdp1.public-trust.com/CRL/Omniroot2025.crl](http://cdp1.public-trust.com/CRL/Omniroot2025.crl) に到達する場合があります。  

## <a name="authentication"></a>Authentication

Key Vault クライアント アプリケーションは、認証用の Azure Active Directory エンドポイントにアクセスする必要があります。 使用されるエンドポイントは、Azure AD テナント構成とプリンシパルの種類 (ユーザー プリンシパルまたはサービス プリンシパル)、アカウントの種類 (Microsoft アカウントや職場または学校のアカウンなど) によって異なります。  

| プリンシパルの種類 | エンドポイント:ポート |
| --- | --- |
| Microsoft アカウントを使用しているユーザー<br> (例: user@hotmail.com) |**グローバル:**<br> login.microsoftonline.com:443<br><br> **Azure China:**<br> login.chinacloudapi.cn:443<br><br>**Azure US Government:**<br> login.microsoftonline.us:443<br><br>**Azure Germany:**<br> login.microsoftonline.de:443<br><br> と <br>login.live.com:443 |
| Azure AD と共に職場または学校アカウントを使用しているユーザーまたはサービス プリンシパル (例: user@contoso.com) |**グローバル:**<br> login.microsoftonline.com:443<br><br> **Azure China:**<br> login.chinacloudapi.cn:443<br><br>**Azure US Government:**<br> login.microsoftonline.us:443<br><br>**Azure Germany:**<br> login.microsoftonline.de:443 |
| Active Directory フェデレーション サービス (AD FS) または他のフェデレーション エンドポイントと共に職場または学校アカウントを使用しているユーザーまたはサービス プリンシパル (例: user@contoso.com) |職場または学校のアカウント用のすべてのエンドポイントと AD FS またはその他のフェデレーション エンドポイント |

ほかにも複雑なシナリオが存在する場合があります。 詳細については、[Azure Active Directory の認証フロー](../active-directory/develop/authentication-scenarios.md)に関するページ、「[Azure Active Directory とアプリケーションの統合](../active-directory/develop/active-directory-how-to-integrate.md)」、[Active Directory の認証プロトコル](https://msdn.microsoft.com/library/azure/dn151124.aspx)に関するページを参照してください。  

## <a name="key-vault-management"></a>キー コンテナーの管理

キー コンテナーの管理 (CRUD とアクセス ポリシーの設定) のために、Key Vault クライアント アプリケーションは Azure Resource Manager エンドポイントにアクセスする必要があります。  

| 操作の種類 | エンドポイント:ポート |
| --- | --- |
| Key Vault コントロール プレーン操作<br> (Azure Resource Manager を使用) |**グローバル:**<br> management.azure.com:443<br><br> **Azure China:**<br> management.chinacloudapi.cn:443<br><br> **Azure US Government:**<br> management.usgovcloudapi.net:443<br><br> **Azure Germany:**<br> management.microsoftazure.de:443 |
| Azure Active Directory Graph API |**グローバル:**<br> graph.windows.net:443<br><br> **Azure China:**<br> graph.chinacloudapi.cn:443<br><br> **Azure US Government:**<br> graph.windows.net:443<br><br> **Azure Germany:**<br> graph.cloudapi.de:443 |

## <a name="key-vault-operations"></a>Key Vault の操作

すべてのキー コンテナー オブジェクト (キーとシークレット) の管理と暗号化の操作のために、Key Vault クライアントはキー コンテナー エンドポイントにアクセスする必要があります。 キー コンテナーの場所によって、エンドポイント DNS サフィックスが異なります。 キー コンテナー エンドポイントは、次の表で説明しているように *vault-name*.*region-specific-dns-suffix* の形式になります。  

| 操作の種類 | エンドポイント:ポート |
| --- | --- |
| キーの暗号化操作、キーとシークレットの作成、読み取り、更新、削除、キー コンテナー オブジェクト (キーまたはシークレット) に対するタグと他の属性の設定または取得などの操作 |**グローバル:**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure China:**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Azure US Government:**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Germany:**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 |

## <a name="ip-address-ranges"></a>IP アドレス範囲

Key Vault サービスでは、PaaS インフラストラクチャなどの他の Azure リソースを使用します。 そのため、Key Vault サービス エンドポイントが常に持つことになる、特定の範囲の IP アドレスは提供されません。 ファイアウォールで IP アドレス範囲のみがサポートされている場合は、「[Microsoft Azure Datacenter IP Ranges (Microsoft Azure データセンターの IP 範囲)](https://www.microsoft.com/download/details.aspx?id=41653)」のドキュメントを参照してください。 認証と ID (Azure Active Directory) はグローバル サービスであり、予告なしに他のリージョンにフェールオーバーしたり、トラフィックを移動したりする可能性があります。 このシナリオでは、[認証と ID の IP アドレス](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity_ip)に関する記事に記載されているすべての IP 範囲を、ファイアウォールに追加する必要があります。

## <a name="next-steps"></a>次の手順

Key Vault に関する質問がある場合は、[Azure Key Vault フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)にアクセスしてください。
