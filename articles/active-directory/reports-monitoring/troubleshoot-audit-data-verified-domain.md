---
title: 確認済みドメインの変更に関する監査データをトラブルシューティングする | Microsoft Docs
description: ユーザーの確認済みドメインを変更したときに Azure Active Directory のアクティビティ ログに表示される情報を示します。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 07/22/2020
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3c9ec3b1e96e47dbf46c6acb2c81147b614d069
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87117416"
---
# <a name="troubleshoot-audit-data-on-verified-domain-change"></a>トラブルシューティング:検証済みドメインの変更に関する監査データ 


## <a name="i-have-a-lot-of-changes-to-my-users-and-i-am-not-sure-what-the-cause-of-it-is"></a>ユーザーに対する多数の変更が発生していますが、その原因がわかりません。

### <a name="symptoms"></a>現象

Azure AD 監査ログをチェックして、Azure AD テナントで複数のユーザーの更新が発生していることを確認しています。 これらの **ユーザーの更新** イベントには **アクター** 情報が表示されません。このため、何が/誰がユーザーの大量の変更をトリガーしたのか不明です。 

### <a name="cause"></a>原因

 オブジェクトで大量の変更が発生する一般的な理由は、**ProxyCalc** と呼ばれる非同期バックエンド操作です。  **ProxyCalc** は、適切な **UserPrincipalName** と **プロキシ アドレス** を決定するロジックであり、Azure AD ユーザー、グループ、または連絡先の更新を実行します。 **ProxyCalc** の背後にある意図は、すべての **UserPrincipalName** と **プロキシ アドレス** が常に Azure AD と一貫性があることを保証することです。 **ProxyCalc** は、確認済みドメインの変更などの明示的な変更によってトリガーされる必要があり、タスクとしてバックグラウンドで常に実行されることはありません。 

  

#### <a name="what-does-userprincipalname-consistency-mean"></a>UserPrincipalName の一貫性とはどういう意味ですか? 

クラウド限定ユーザーの場合、一貫性とは **UserPrincipalName** が確認済みドメイン サフィックスに設定されていることを意味します。 一貫性のない **UserPrincipalName** が処理されるとき、**ProxyCalc** によって既定の onmicrosoft.com のサフィックスに変換されます (例: username@Contoso.onmicrosoft.com)。 

同期ユーザーの場合、一貫性とは **UserPrincipalName** が確認済みドメイン サフィックスに設定され、オンプレミスの **UserPrincipalName** の値 (ShadowUserPrincipalName) と一致していることを意味します。 一貫性のない **UserPrincipalName** が処理されるとき、**ProxyCalc** によって **ShadowUserPrincipalName** と同じ値に戻されます。ドメイン サフィックスがテナントから削除されている場合は、既定の *.onmicrosoft.com ドメイン サフィックスに変換されます。 

  

#### <a name="what-does-proxy-address-consistency-mean"></a>プロキシ アドレスの一貫性とはどういう意味ですか? 

クラウド限定ユーザーの場合、一貫性とは プロキシ アドレスが確認済みドメイン サフィックスと一致していることを意味します。 一貫性のないプロキシ アドレスが処理されるとき、**ProxyCalc** によって既定の *.onmicrosoft.com ドメイン サフィックスに変換されます (例: SMTP:username@Contoso.onmicrosoft.com)。 

同期ユーザーの場合、一貫性とは、プロキシ アドレスがオンプレミスのプロキシ アドレスの値 (つまり、 ShadowProxyAddresses) と一致していることを意味します。 **ProxyAddresses** は **ShadowProxyAddresses** と同期されていることが期待されています。 同期ユーザーに Exchange ライセンスが割り当てられている場合、プロキシ アドレスはオンプレミスのプロキシ アドレスの値と一致している必要があり、確認済みドメイン サフィックスとも一致している必要があります。 このシナリオでは、**ProxyCalc** によって確認されていないドメインのサフィックスを持つ一貫性のないプロキシ アドレスがサニタイズされ、Azure AD 内のオブジェクトから削除されます。 確認されていないドメインが後で確認された場合、**ProxyCalc** によって **ShadowProxyAddresses** からプロキシ アドレスが再計算され、Azure AD 内のオブジェクトに追加されます。  

> [!NOTE]
> 同期オブジェクトの場合、**ProxyCalc** ロジックによる予期しない計算結果を避けるために、オンプレミスのオブジェクトの Azure AD 確認済みドメインにプロキシ アドレスを設定することをお勧めします。  

  
確認済みドメインの変更があると、**ProxyCalc** をトリガーできるいずれかの管理タスクによって、その操作が常に発生します。 このタスクは、確認済みドメインが Azure AD テナントに対して追加/削除されるたびに発生します。これにより、内部で **ProxyCalc** がトリガーされます。  

たとえば、確認済みドメイン Fabrikam.com を Contoso.onmicrosoft.com テナントに追加した場合、このアクションによって、テナント内のすべてのオブジェクトに対して ProxyCalc 操作がトリガーされます。 このイベントは、**確認済みドメインの追加** イベントが先行する **ユーザーの更新** イベントとして Azure AD 監査ログにキャプチャされます。 一方、Fabrikam.com が Contoso.onmicrosoft.com テナントから削除された場合、すべての **ユーザーの更新** イベントには **確認済みドメインの削除** イベントが先行します。   

#### <a name="additional-notes"></a>その他のメモ:

ProxyCalc では、以下に該当する特定のオブジェクトに対する変更は行われません。 

- アクティブな Exchange ライセンスがない 
- **MSExchRemoteRecipientType** が Null に設定されている 
- 共有リソースとみなされない。 共有リソースとは、**CloudMSExchRecipientDisplayType** に次の値のいずれかが含まれているものです。**MailboxUser (shared)** 、**PublicFolder**、**ConferenceRoomMailbox**、**EquipmentMailbox**、**ArbitrationMailbox**、**RoomList**、**TeamMailboxUser**、**Group mailbox**、**Scheduling mailbox**、**ACLableMailboxUser**、**ACLableTeamMailboxUser** 
  
 この 2 つの異なるイベント間の相関関係をさらに構築するため、Microsoft では、これらの変更が確認済みドメインの変更によってトリガーされていることを識別するように監査ログの **アクター** 情報を更新することに取り組んできます。 このアクションにより、確認済みドメインの変更イベントがいつ発生し、各自のテナント内のオブジェクトの大量更新がいつ開始されたかをチェックすることができるようになります。 

さらに、ほとんどの場合、ユーザーの **UserPrincipalName** と **プロキシ アドレス** には一貫性があるため、ユーザーが変更されることはありません。このため、Microsoft では、オブジェクトを実際に変更させる原因となった更新のみを監査ログに表示することに取り組んでいます。 このアクションにより、監査ログのノイズが防止され、管理者は、残りの確認済みドメインに対するユーザーの変更イベントを上記の説明に従って相互に関連付けることができます。 

## <a name="next-steps"></a>次の手順

[Azure AD Connect 同期サービスのシャドウ属性](../hybrid/how-to-connect-syncservice-shadow-attributes.md)
