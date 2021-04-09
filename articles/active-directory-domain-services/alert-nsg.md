---
title: Azure AD DS でネットワーク セキュリティ グループのアラートを解決する | Microsoft Docs
description: Azure Active Directory Domain Services のネットワーク セキュリティ グループ構成アラートをトラブルシューティングして解決する方法について説明します
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 12/16/2020
ms.author: justinha
ms.openlocfilehash: 5b48d326efad889adbcf25d487ee27b8200f558f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97693926"
---
# <a name="known-issues-network-configuration-alerts-in-azure-active-directory-domain-services"></a>既知の問題:Azure Active Directory Domain Services でのネットワーク構成アラート

アプリケーションとサービスが Azure Active Directory Domain Services (Azure AD DS) マネージド ドメインと正しく通信できるようにするには、トラフィックのフローを許可するために、特定のネットワーク ポートを開く必要があります。 Azure では、ネットワーク セキュリティ グループを使用してトラフィックのフローを制御します。 Azure AD DS マネージド ドメインの正常性状態は、必要なネットワーク セキュリティ グループ規則が適用されていない場合にアラートを表示します。

この記事は、ネットワーク セキュリティ グループの構成に関する問題の一般的なアラートを理解し、解決するのに役立ちます。

## <a name="alert-aadds104-network-error"></a>アラート AADDS104:ネットワーク エラー

### <a name="alert-message"></a>アラート メッセージ

"*このマネージド ドメインのドメイン コントローラーに到達できません。これは、仮想ネットワークに構成されているネットワーク セキュリティ グループ (NSG) がマネージド ドメインへのアクセスをブロックしている場合に発生する可能性があります。別の理由として、インターネットからの着信トラフィックをブロックするユーザー定義ルートが存在していることが考えられます。*

無効なネットワーク セキュリティ グループ規則は、Azure AD DS のネットワーク エラーの最も一般的な原因です。 仮想ネットワーク用のネットワーク セキュリティ グループは、特定のポートおよびプロトコルへのアクセスを許可する必要があります。 これらのポートがブロックされている場合、Azure プラットフォームはマネージド ドメインの監視および更新を行うことができません。 Azure AD ディレクトリと Azure AD DS の間の同期にも影響があります。 サービスが中断されないように、既定のポートを開いたままにするようにしてください。

## <a name="default-security-rules"></a>既定セキュリティ規則

マネージド ドメインのネットワーク セキュリティ グループには、次の既定の受信および送信のセキュリティ規則が適用されます。 これらの規則は Azure AD DS のセキュリティを維持し、Azure プラットフォームがマネージド ドメインを監視、管理、および更新できるようにします。

### <a name="inbound-security-rules"></a>受信セキュリティ規則

| Priority | 名前 | Port | Protocol | source | 到着地 | アクション |
|----------|------|------|----------|--------|-------------|--------|
| 301      | AllowPSRemoting | 5986| TCP | AzureActiveDirectoryDomainServices | Any | Allow |
| 201      | AllowRD | 3389 | TCP | CorpNetSaw | Any | Deny<sup>1</sup> |
| 65000    | AllVnetInBound | Any | Any | VirtualNetwork | VirtualNetwork | Allow |
| 65001    | AllowAzureLoadBalancerInBound | Any | Any | AzureLoadBalancer | Any | Allow |
| 65500    | DenyAllInBound | Any | Any | Any | Any | 拒否 |


<sup>1</sup>デバッグでは省略可。 高度なトラブルシューティングに必要な場合に許可。

> [!NOTE]
> [Secure LDAP を構成][configure-ldaps]する場合は、さらに、受信トラフィックを許可する追加の規則を持つことができます。 正しい LDAPS 通信にはこの追加規則が必要です。

### <a name="outbound-security-rules"></a>送信セキュリティ規則

| Priority | 名前 | Port | Protocol | source | 到着地 | アクション |
|----------|------|------|----------|--------|-------------|--------|
| 65000    | AllVnetOutBound | Any | Any | VirtualNetwork | VirtualNetwork | Allow |
| 65001    | AllowAzureLoadBalancerOutBound | Any | Any |  Any | インターネット | Allow |
| 65500    | DenyAllOutBound | Any | Any | Any | Any | 拒否 |

>[!NOTE]
> Azure AD DS には、仮想ネットワークからの無制限の発信アクセスが必要です。 仮想ネットワークの発信アクセスを制限する追加の規則を作成することは推奨されません。

## <a name="verify-and-edit-existing-security-rules"></a>既存のセキュリティ規則を確認および編集する

既存のセキュリティ規則を確認し、既定のポートが開いていることを確実にするには、次の手順を実行します。

1. Azure portal で、**ネットワーク セキュリティ グループ** を検索して選択します。
1. マネージド ドメインに関連付けられているネットワーク セキュリティ グループ (*AADDS-contoso.com-NSG* など) を選択します。
1. **[概要]** ページに、既存の受信および送信のセキュリティ規則が表示されます。

    受信および送信の規則を確認し、前のセクションにある必要な規則リストと比較します。 必要に応じて、必要なトラフィックをブロックするカスタム規則を選択して削除します。 必要な規則のいずれかが不足している場合は、次のセクションで規則を追加します。

    必要なトラフィックを許可するために規則を追加または削除した後、マネージド ドメインの正常性が 2 時間以内に自動的に更新され、アラートが削除されます。

### <a name="add-a-security-rule"></a>セキュリティ規則を追加する

不足しているセキュリティ規則を追加するには、次の手順を実行します。

1. Azure portal で、**ネットワーク セキュリティ グループ** を検索して選択します。
1. マネージド ドメインに関連付けられているネットワーク セキュリティ グループ (*AADDS-contoso.com-NSG* など) を選択します。
1. 左側のパネルの **[設定]** で、追加する必要がある規則に応じて、 *[受信セキュリティ規則]* または *[送信セキュリティ規則]* をクリックします。
1. **[追加]** を選択し、ポート、プロトコル、方向などに基づいて必要な規則を作成します。準備ができたら **[OK]** を選択します。

セキュリティ規則が追加されて一覧に表示されるまでにしばらく時間がかかります。

## <a name="next-steps"></a>次のステップ

まだ問題が解決しない場合は、さらなるトラブルシューティングの支援を求めて、[Azure サポート リクエストを開いて][azure-support]ください。

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
