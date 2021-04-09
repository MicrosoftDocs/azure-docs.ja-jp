---
title: Azure AD Domain Services で Secure LDAP アラートを解決する | Microsoft Docs
description: Azure Active Directory Domain Services での Secure LDAP の一般的なアラートをトラブルシューティングおよび解決する方法について説明します。
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 81208c0b-8d41-4f65-be15-42119b1b5957
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: 15c1f3a1731edf7b45061646d43688b4aacc6104
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96620309"
---
# <a name="known-issues-secure-ldap-alerts-in-azure-active-directory-domain-services"></a>既知の問題:Azure Active Directory Domain Services の Secure LDAP アラート

ライトウェイト ディレクトリ アクセス プロトコル (LDAP) を使用して Azure Active Directory Domain Services (Azure AD DS) と通信するアプリケーションとサービスは、[Secure LDAP を使用するように構成](tutorial-configure-ldaps.md)できます。 Secure LDAP が正しく機能するためには、適切な証明書と必要なネットワーク ポートが開いている必要があります。

この記事は、Azure AD DS での Secur LDAP アクセスの一般的なアラートを理解して解決するのに役立ちます。

## <a name="aadds101-secure-ldap-network-configuration"></a>AADDS101:Secure LDAP ネットワーク構成

### <a name="alert-message"></a>アラート メッセージ

"*マネージド ドメインに対してインターネット経由のセキュリティで Secure LDAP が有効になっています。ただし、ポート 636 へのアクセスはネットワーク セキュリティ グループを使用してロックダウンされていません。これにより、マネージド ドメインのユーザー アカウントがパスワードの総当り攻撃の対象になる可能性があります。* "

### <a name="resolution"></a>解決方法

Secure LDAP を有効にする場合は、特定の IP アドレスへの受信 LDAPS アクセスを制限する追加の規則を作成することをお勧めします。 これらの規則は、マネージド ドメインをブルート フォース攻撃から保護します。 Secure LDAP への TCP ポート 636 アクセスを制限するようにネットワーク セキュリティ グループを更新するには、次の手順を実行します。

1. Azure portal で、**ネットワーク セキュリティ グループ** を検索して選択します。
1. マネージド ドメインに関連付けられているネットワーク セキュリティ グループ (*AADDS-contoso.com-NSG* など) を選択し、次に **[受信セキュリティ規則]** を選択します。
1. **[+ 追加]** を選択して、TCP ポート 636 のルールを作成します。 必要に応じて、ウィンドウで **[詳細]** を選択して規則を作成します。
1. **[Source]\(ソース\)** には、ドロップダウン メニューから *[IP アドレス]* を選択します。 Secure LDAP トラフィックのアクセス権を付与するソース IP アドレスを入力します。
1. **[宛先]** として *[Any]\(任意\)* を選択し、 **[宛先ポート範囲]** に「*636*」を入力します。
1. **[プロトコル]** を *[TCP]* と設定し、 **[アクション]** を *[許可]* と設定します。
1. 規則の優先順位を指定し、名前 (*RestrictLDAPS* など) を入力します。
1. 準備ができたら、 **[追加]** を選択して規則を作成します。

マネージド ドメインの正常性が 2 時間以内に自動的に更新され、アラートが削除されます。

> [!TIP]
> Azure AD DS を円滑に実行するために必要な規則は、TCP ポート 636 だけではありません。 詳細については、[Azure AD DS ネットワーク セキュリティ グループと必要なポート](network-considerations.md#network-security-groups-and-required-ports)に関するページを参照してください。

## <a name="aadds502-secure-ldap-certificate-expiring"></a>AADDS502:Secure LDAP 証明書の期限切れ間近

### <a name="alert-message"></a>アラート メッセージ

*マネージド ドメインのセキュリティで保護された LDAP 証明は[日付] に有効期限が切れます。*

### <a name="resolution"></a>解決方法

交換用の Secure LDAP 証明書を作成するには、「[Secure LDAP 用の証明書を作成する](tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap)」の手順を実行してください。 交換用の証明書を Azure AD DS に適用し、Secure LDAP を使用して接続するすべてのクライアントにその証明書を配布します。

## <a name="next-steps"></a>次のステップ

まだ問題が解決しない場合は、さらなるトラブルシューティングの支援を求めて、[Azure サポート リクエストを開いて][azure-support]ください。

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
