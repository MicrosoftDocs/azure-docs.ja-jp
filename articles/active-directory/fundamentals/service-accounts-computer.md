---
title: コンピューター アカウントのセキュリティ保護 | Azure Active Directory
description: オンプレミス コンピューター アカウントをセキュリティで保護するためのガイド。
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
ms.openlocfilehash: 22faba20cb12ae755f19fe43c295d98f9b364cbe
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100416628"
---
# <a name="securing-computer-accounts"></a>コンピューター アカウントのセキュリティ保護

コンピューター アカウント (LocalSystem アカウント) は、ローカル コンピューター上の実質的にすべてのリソースにアクセスできる、高い特権を持つ組み込みのアカウントです。 このアカウントは、サインオンしたユーザー アカウントに関連付けられていません。 LocalSystem として実行されているサービスは、コンピューターの資格情報をリモート サーバーに提示することによって、ネットワーク リソースにアクセスします。 このとき、資格情報は <domain_name>\<computer_name>$ の形式で提示されます。 コンピューター アカウントの定義済みの名前は NT AUTHORITY\SYSTEM です。 これは、サービスを開始し、そのサービスのセキュリティ コンテキストを提供するために使用できます。

![[画像 4](.\media\securing-service-accounts\secure-computer-accounts-image-1.png)](.\media\securing-service-accounts\secure-computer-accounts-image-1.png)

## <a name="benefits-of-using-the-computer-account"></a>コンピューター アカウントを使用する利点

コンピューター アカウントには、次のような利点があります。

* **無制限のローカル アクセス**:コンピューター アカウントでは、コンピューターのローカル リソースに対して完全なアクセス権が付与されます。

* **自動的なパスワード管理**:コンピューター アカウントでは、パスワードを手動で変更する必要がありません。 代わりに、このアカウントは Active Directory のメンバーであり、アカウントのパスワードは自動的に変更されます。 また、サービスのサービス プリンシパル名を登録する必要もありません。

* **コンピューター外部での制限付きアクセス権**:Active Directory Domain Services の既定のアクセス制御リスト (ACL) では、コンピューター アカウントに対して最小限のアクセスが許可されます。 このサービスがハッキングされた場合、ネットワーク上のリソースに対して制限されたアクセスのみが許可されます。

## <a name="assess-security-posture-of-computer-accounts"></a>コンピューター アカウントのセキュリティ体制を評価する

コンピューター アカウントを使用する場合の潜在的な課題および関連する軽減策。 

| 発行| 軽減策 |
| - | - |
| コンピューターがドメインから離脱し、再度参加すると、コンピューター アカウントが削除され、再作成される可能性がある。| コンピューターを AD グループに追加する必要があるかどうかを検証し、このページに記載されているサンプル スクリプトを使用して、どのコンピューター アカウントがグループに追加されているかを確認します。| 
| コンピューター アカウントをグループに追加すると、そのコンピューターで LocalSystem として実行されているすべてのサービスに、そのグループのアクセス権が付与される。| コンピューター アカウントのグループ メンバーシップを選択してください。 関連付けられたサービスには Active Directory Domain Services への完全なアクセス権があるため、コンピューター アカウントをドメイン管理者グループのメンバーにしないでください。 |
| LocalSystem のネットワークの既定値が正しくない。| コンピューター アカウントに、ネットワーク リソースへの既定の制限付きアクセス権があると想定しないでください。 代わりに、このアカウントのグループ メンバーシップを慎重に確認してください。 |
| 不明なサービスが LocalSystem として実行されている。| LocalSystem アカウントで実行されているすべてのサービスが、Microsoft サービスまたはサードパーティの信頼済みサービスであることを確認します。 |


## <a name="find-services-running-under-the-computer-account"></a>コンピューター アカウントで実行されているサービスを検索する

LocalSystem コンテキストで実行されているサービスを検索するには、次の PowerShell コマンドレットを使用します。

```powershell

Get-WmiObject win32_service | select Name, StartName | Where-Object {($_.StartName -eq "LocalSystem")}
```

**特定のグループのメンバーであるコンピューター アカウントを検索する**

特定のグループのメンバーであるコンピューター アカウントを検索するには、次の PowerShell コマンドレットを使用します。

```powershell

```Get-ADComputer -Filter {Name -Like "*"} -Properties MemberOf | Where-Object {[STRING]$_.MemberOf -like "Your_Group_Name_here*"} | Select Name, MemberOf
```

**特権グループのメンバーであるコンピューター アカウントを検索する**

ID 管理者グループ (ドメイン管理者、エンタープライズ管理者、管理者) のメンバーであるコンピューター アカウントを検索するには、次の PowerShell コマンドレットを使用します。

```powershell
Get-ADGroupMember -Identity Administrators -Recursive | Where objectClass -eq "computer"
```
## <a name="move-from-computer-accounts"></a>コンピューター アカウントから移動する

> [!IMPORTANT]
> コンピューター アカウントは高い特権を持つアカウントです。サービスがコンピューター上のローカル リソースに対して無制限のアクセスを必要とし、管理サービス アカウント (MSA) を使用できない場合にのみ使用してください。

* MSA を使用してサービスを実行できるかどうかをサービスの所有者に確認し、サービスでサポートされている場合は、グループ管理サービス アカウント (gMSA) またはスタンドアロン管理サービス アカウント (sMSA) を使用します。

* サービスの実行に必要な特権だけを持つドメイン ユーザー アカウントを使用します。

## <a name="next-steps"></a>次の手順 

サービス アカウントのセキュリティ保護に関する次の記事を参照してください。

* [オンプレミス サービス アカウントの概要](service-accounts-on-premises.md)

* [グループ管理サービス アカウントをセキュリティで保護する](service-accounts-group-managed.md)

* [スタンドアロン管理サービス アカウントをセキュリティで保護する](service-accounts-standalone-managed.md)

* [コンピューター アカウントをセキュリティで保護する](service-accounts-computer.md)

* [ユーザー アカウントをセキュリティで保護する](service-accounts-user-on-premises.md)

* [オンプレミス サービス アカウントを管理する](service-accounts-govern-on-premises.md)

 

 
