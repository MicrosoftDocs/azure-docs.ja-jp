---
title: "Azure AD Privileged Identity Management セキュリティ ウィザード"
description: "Azure Active Directory Privileged Identity Management 拡張機能を初めて使用するときは、セキュリティ ウィザードが表示されます。 この記事では、ウィザードを使用する手順について説明します。"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: a53a3719-8cc7-4fc7-8164-aafca192871b
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2017
ms.author: billmath
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: b9a3b64d9de48f17a295ca7a9ea58cf26e8f83ed
ms.openlocfilehash: ba06eb7e9f3f09e2d1d04cb4118dd81b9e66853e
ms.lasthandoff: 02/28/2017

---
# <a name="using-the-security-wizard-in-azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management でのセキュリティ ウィザードの使用 
組織内で Azure Privileged Identity Management (PIM) を初めて実行するユーザーには、ウィザードが表示されます。 このウィザードを使用すると、特権 ID のセキュリティ リスクと、PIM を使用してそのリスクを軽減する方法がわかります。 既存のロール割り当てを後で変更する場合は、ウィザードで実行する必要はありません。

## <a name="what-to-expect"></a>ウィザードの内容
組織で PIM の使用を開始する前の段階では、すべてのロール割り当ては永続的な割り当てになっています。そのため、ユーザーが現在その権限を必要としていない場合でも、常にロールは割り当てられている状態です。  ウィザードの最初の手順には、高い権限を持つロールの一覧と、それらのロールに割り当てられたユーザーの数が表示されます。 ロールの中によく知らないものがある場合は、そのロールの詳細を表示して、ユーザーを詳しく確認できます。

ウィザードの&2; 番目の手順では、管理者のロール割り当てを変更できます。  

> [!WARNING]
> ここで重要なのは、全体管理者が少なくとも&1; 人存在し、組織アカウント (Microsoft アカウントではない) を持つ特権ロール管理者が複数存在していることです。 特権ロール管理者が&1; 人しかいない場合、そのアカウントが削除されると、組織は PIM を管理できなくなります。
> また、ユーザーに Microsoft アカウント (Skype、Outlook.com などの Microsoft サービスへのサインインに使用するアカウント) がある場合は、ロールの割り当てを永続的に維持してください。 そのロールのアクティブ化に MFA を要求すると、ユーザーはロックアウトされます。
> 
> 

変更を行った後は、ウィザードは表示されなくなります。 ウィザードを実行したユーザーや他の特権ロール管理者が次に PIM を使用するときには、PIM ダッシュボードが表示されます。  

* ロールに対するユーザーの追加や削除、または永続的から対象的への割り当ての変更を行う場合は、「 [ユーザー ロールを追加または削除する方法](active-directory-privileged-identity-management-how-to-add-role-to-user.md)」を参照してください。
* PIM を管理するためのアクセス権を付与するユーザーを増やす場合は、「 [PIM へのアクセス権を付与する方法](active-directory-privileged-identity-management-how-to-give-access-to-pim.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]


