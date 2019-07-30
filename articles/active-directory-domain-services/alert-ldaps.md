---
title: Azure Active Directory Domain Services:Secure LDAP の構成のトラブルシューティング | Microsoft Docs
description: Azure AD Domain Services の Secure LDAP のトラブルシューティング
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: ''
editor: ''
ms.assetid: 81208c0b-8d41-4f65-be15-42119b1b5957
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: 453018f486ca3fda91d8447208fe3d936722522e
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2019
ms.locfileid: "67473954"
---
# <a name="azure-ad-domain-services---troubleshooting-secure-ldap-configuration"></a>Azure AD Domain Services - Secure LDAP の構成のトラブルシューティング

この記事では、Azure AD Domain Services に [Secure LDAP を構成する](configure-ldaps.md)ときの一般的な問題に対する解決策を示します。

## <a name="aadds101-secure-ldap-network-security-group-configuration"></a>AADDS101:Secure LDAP ネットワーク セキュリティ グループの構成

**アラート メッセージ:**

"*マネージド ドメインに対してインターネット経由のセキュリティで Secure LDAP が有効になっています。ただし、ポート 636 へのアクセスはネットワーク セキュリティ グループを使用してロックダウンされていません。これにより、マネージド ドメインのユーザー アカウントがパスワードの総当り攻撃の対象になる可能性があります。* "

### <a name="secure-ldap-port"></a>Secure LDAP ポート

Secure LDAP を有効にするときは、特定の IP アドレスからの受信 LDAPS アクセスのみを許可する追加規則を作成することをお勧めします。 これらの規則は、セキュリティの脅威となる可能性があるブルート フォース攻撃からドメインを保護します。 ポート 636 は、マネージド ドメインへのアクセスを許可します。 Secure LDAP へのアクセスを許可するように NSG を更新する方法を次に示します。

1. Azure Portal で、[[ネットワーク セキュリティ グループ] タブ](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups)に移動します
2. テーブルから、ドメインに関連付けられている NSG を選びます。
3. **[受信セキュリティ規則]** をクリックします
4. ポート 636 の規則を作成します
   1. 上部ナビゲーション バーの **[追加]** をクリックします。
   2. 接続元の **[IP アドレス]** を選びます。
   3. この規則の接続元ポートの範囲を指定します。
   4. 接続先ポートの範囲には「636」と入力します。
   5. プロトコルは **[TCP]** です。
   6. 規則に、適切な名前、説明、優先順位を指定します。 この規則の優先順位は、"すべて拒否" 規則 (ある場合) の優先順位より高くする必要があります。
   7. Click **OK**.
5. 規則が作成されたことを確認します。
6. 手順を正しく完了したことを確認するには、2 時間後にドメインの正常性をチェックします。

> [!TIP]
> Azure AD Domain Services を円滑に動作させるために必要なのはポート 636 だけではありません。 詳細については、[ネットワークに関するガイドライン](network-considerations.md)または [NSG 構成のトラブルシューティング](alert-nsg.md)に関する記事を参照してください。
>

## <a name="aadds502-secure-ldap-certificate-expiring"></a>AADDS502:Secure LDAP 証明書の期限切れ間近

**アラート メッセージ:**

*マネージド ドメインのセキュリティで保護された LDAP 証明は[日付] に有効期限が切れます。*

**解決策:**

[セキュリティで保護された LDAP の構成](configure-ldaps.md)に関する記事で説明されている手順に従って、セキュリティで保護された LDAP 証明書を新規に作成します。

## <a name="contact-us"></a>お問い合わせ
[フィードバックの共有およびサポートについては](contact-us.md)、Azure Active Directory Domain Services 製品チームにお問い合わせください。
