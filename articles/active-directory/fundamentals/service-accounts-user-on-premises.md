---
title: ユーザーベースのサービス アカウントのセキュリティ保護 | Azure Active Directory
description: オンプレミス ユーザー アカウントをセキュリティで保護するためのガイド。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 2/15/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: e484bdda33142024f2067649eaa67042fe7776f8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100416619"
---
# <a name="securing-user-based-service-accounts-in-active-directory"></a>Active Directory での、ユーザーベースのサービス アカウントのセキュリティ保護

オンプレミス ユーザー アカウントは、Windows で実行されているサービスをセキュリティで保護するための従来のアプローチです。 グローバルな管理サービス アカウント (gMSA) やスタンドアロンの管理サービス アカウント (sMSA) がサービスでサポートされていない場合は、これらのアカウントを最後の手段として使用します。 使用するのに最も適した種類のアカウントを選択する方法については、オンプレミス サービス アカウントの概要を参照してください。 また、マネージド ID やサービス プリンシパルなどの Azure サービス アカウントを使用するようにサービスを移行できるかどうかも調査します。 

オンプレミス ユーザー アカウントを作成して、サービスのセキュリティ コンテキストを提供したり、サービスがローカル リソースやネットワーク リソースにアクセスするために必要な特権を付与したりできます。 他の Active Directory (AD) ユーザー アカウントと同様に、手動でパスワードを管理する必要があります。 これらのアカウントのセキュリティを維持するために、サービスとドメインの管理者は、強力なパスワード管理プロセスを監視する必要があります。

ユーザー アカウントをサービス アカウントとして使用する場合は、単一のサービスに対してのみ使用します。 それがサービス アカウントであることや、どのサービスに使用するかが明確になるように、名前を指定します。 

## <a name="benefits-and-challenges"></a>メリットと課題

メリット

オンプレミス ユーザー アカウントは、サービスで使用するための、最も汎用性が高い種類のアカウントです。 サービス アカウントとして使用するユーザー アカウントは、通常のユーザー アカウントを管理するすべてのポリシーによって制御できます。 ただし、MSA を使用できない場合にのみ使用してください。 また、コンピューター アカウントがより適切なオプションであるかどうかも評価します。 

オンプレミス ユーザー アカウントに関する課題

オンプレミス ユーザー アカウントの使用に関連して、次の課題があります。

| 課題| 軽減策 |
| - | - |
| パスワード管理は手動のプロセスであり、セキュリティの低下やサービスのダウンタイムが発生する可能性があります。| パスワードの複雑さとパスワードの変更が堅牢なプロセスによって管理され、強力なパスワードを使用した定期的な更新が保証されていることを確認します。 <br> パスワード変更を、サービスのダウンタイムを発生させるサービスのパスワード更新と合わせて調整します。 |
| サービス アカウントとして機能しているオンプレミス ユーザー アカウントを識別するのが困難な場合があります。| 環境にデプロイされたサービス アカウントの記録を文書化し、維持します。 <br> アカウント名と、それらにアクセスが割り当てられているリソースを追跡します。 <br> サービス アカウントとして使用されるすべてのユーザー アカウントに、svc_ というプレフィックスを追加することを検討してください。 |


## <a name="find-on-premises-user-accounts-used-as-service-accounts"></a>サービス アカウントとして使用されるユーザー アカウントの検索

オンプレミス ユーザー アカウントは、他の AD ユーザー アカウントと良く似ています。 サービス アカウントとして識別されるユーザー アカウントの属性が 1 つも存在しないため、これらのアカウントを判別するのが困難な場合があります。 

サービス アカウントとして使用されるすべてのユーザー アカウントに対して、わかりやすい名前付け規則を作成することをお勧めします。

たとえば、プレフィックスとして "service-" を追加して、サービスに "service-HRDataConnector" という名前を付けます。

次に挙げる条件のいくつかに基づいて、これらのサービス アカウントを見つけることができます。ただし、そのようなアカウントがすべて見つかるとは限りません。

* アカウントが委任に対して信頼されている。

* アカウントにサービス プリンシパル名がある。

* アカウントのパスワードが無期限に設定されている。

次の PowerShell コマンドを実行して、サービス用に作成されたオンプレミス ユーザー アカウントを見つけることができます。

### <a name="find-accounts-trusted-for-delegation"></a>委任に対して信頼されているアカウントの検索

```PowerShell

Get-ADObject -Filter {(msDS-AllowedToDelegateTo -like '*') -or (UserAccountControl -band 0x0080000) -or (UserAccountControl -band 0x1000000)} -prop samAccountName,msDS-AllowedToDelegateTo,servicePrincipalName,userAccountControl | select DistinguishedName,ObjectClass,samAccountName,servicePrincipalName, @{name='DelegationStatus';expression={if($_.UserAccountControl -band 0x80000){'AllServices'}else{'SpecificServices'}}}, @{name='AllowedProtocols';expression={if($_.UserAccountControl -band 0x1000000){'Any'}else{'Kerberos'}}}, @{name='DestinationServices';expression={$_.'msDS-AllowedToDelegateTo'}}

```

### <a name="find-accounts-with-service-principle-names"></a>サービス プリンシパル名があるアカウントの検索

```PowerShell

Get-ADUser -Filter * -Properties servicePrincipalName | where {$_.servicePrincipalName -ne $null}

```

 

### <a name="find-accounts-with-passwords-set-to-never-expire"></a>パスワードが無期限に設定されているアカウントの検索

```PowerShell

Get-ADUser -Filter * -Properties PasswordNeverExpires | where {$_.PasswordNeverExpires -eq $true}

```


また、機密性の高いリソースへのアクセスを監査し、監査ログをセキュリティ情報イベント管理 (SIEM) システムにアーカイブすることもできます。 Azure Log Analytics や Azure Sentinel などのシステムを使用して、サービス アカウントを検索し、分析できます。

## <a name="assess-security-of-on-premises-user-accounts"></a>オンプレミス ユーザー アカウントのセキュリティの評価

次の条件を使用して、サービス アカウントとして使用されているオンプレミス ユーザー アカウントのセキュリティを評価します。

* パスワード管理ポリシーはどのようなものか。

* アカウントが特権グループのメンバーか。

* アカウントが重要なリソースに対する読み取り/書き込みアクセス権限を持っているか。

### <a name="mitigate-potential-security-issues"></a>潜在的なセキュリティの問題の軽減

次の表は、潜在的なセキュリティの問題と、オンプレミス ユーザー アカウントについて、対応する軽減策を示しています。

| セキュリティの問題| 軽減策 |
| - | - |
| パスワード管理|* パスワードの複雑さとパスワードの変更が堅牢なプロセスによって管理され、強力なパスワード要件に従った定期的な更新が保証されていることを確認します。 <br> * サービスのダウンタイムを最小限に抑えるように、パスワード変更とパスワード更新を調整します。 |
| アカウントが特権グループのメンバーの場合| グループ メンバーシップを確認します。 そのアカウントを特権グループから削除します。 アカウントに、そのサービスを実行するために必要な権限とアクセス許可のみを付与します (サービス ベンダーに問い合わせてください)。 たとえば、ローカルでのサインインを拒否したり、対話型のサインインを拒否したりできます。 |
| アカウントに、機密性の高いリソースに対する読み取り/書き込みアクセス権限が付与されている場合| 機密性の高いリソースへのアクセスを監査します。 監査ログを分析のために SIEM (Azure Log Analytics や Azure Sentinel) にアーカイブします。 望ましくないレベルのアクセスが検出された場合は、リソース アクセス許可を修正します。 |


## <a name="move-to-more-secure-account-types"></a>より安全な種類のアカウントへの移行

Microsoft では、顧客がオンプレミス ユーザー アカウントをサービス アカウントとして使用することはお勧めしていません。 この種類のアカウントを使用しているサービスについては、gMSA または sMSA を使用するように構成できるかどうかを評価してください。

さらに、より安全な種類のサービス アカウントを使用できるように、サービス自体を Azure に移行できるかどうかを評価します。 

## <a name="next-steps"></a>次のステップ
サービス アカウントのセキュリティ保護に関する次の記事を参照してください。

* [オンプレミス サービス アカウントの概要](service-accounts-on-premises.md)

* [グループ管理サービス アカウントをセキュリティで保護する](service-accounts-group-managed.md)

* [スタンドアロン管理サービス アカウントをセキュリティで保護する](service-accounts-standalone-managed.md)

* [コンピューター アカウントをセキュリティで保護する](service-accounts-computer.md)

* [ユーザー アカウントをセキュリティで保護する](service-accounts-user-on-premises.md)

* [オンプレミス サービス アカウントを管理する](service-accounts-govern-on-premises.md)

 
