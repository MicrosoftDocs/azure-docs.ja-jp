---
title: すべての Azure 管理者に対して MFA を有効にする
description: 全体管理者を有効にするためのガイダンス
ms.service: security
author: barclayn
manager: barbkess
editor: TomSh
ms.topic: article
ms.date: 03/20/2018
ms.author: barclayn
ms.openlocfilehash: 2dcd7f42d86000dd3b642c10f6d3db0b0d0fcb03
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2019
ms.locfileid: "56116339"
---
# <a name="enforce-multi-factor-authentication-mfa-for-subscription-administrators"></a>多要素認証 (MFA) をサブスクリプション管理者に適用する

全体管理者アカウントを含め、管理者を作成するときには、非常に強固な認証方法を使用することが不可欠です。

必要に応じて、Exchange 管理者やパスワード管理者などの特定の管理者ロールを IT スタッフのユーザー アカウントに割り当てて、日常的な管理を実行できます。
さらに、管理者について [Azure Multi-factor Authentication (MFA)](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) を有効にすると、ユーザーのサインインとトランザクションに 2 つ目のセキュリティ層が追加されます。 また、Azure MFA は不正に取得された資格情報によって組織データにアクセスされる危険を減らすためにも役立ちます。

たとえば、ユーザーに Azure MFA を適用し、検証手段として電話またはテキスト メッセージを使用するように MFA を構成します。 ユーザーの資格情報が侵害された場合でも、攻撃者はユーザーの電話にアクセスできないので、リソースにアクセスすることはできません。 新しい ID 保護層を追加しない場合、資格情報盗用攻撃を受けやすくなり、データの侵害につながる可能性があります。

認証全体をオンプレミスで管理する必要がある場合は、代わりに [Azure Multi-Factor Authentication Server](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-server) ("MFA オンプレミス" とも呼ばれます) を利用できます。 この手法を利用すると、オンプレミスに MFA サーバーを置いていても、多要素認証を適用することができます。

組織内で管理者特権を持っているユーザーを調べる場合は、次の Microsoft Azure AD V2 の PowerShell コマンドを使用して確認できます。

```azurepowershell-interactive
Get-AzureADDirectoryRole | Where { $_.DisplayName -eq "Company Administrator" } | Get-AzureADDirectoryRoleMember | Ft DisplayName
```

## <a name="enabling-mfa"></a>MFA を有効にする

作業を進める前に、[MFA](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next) がどのように動作するかを確認します。

ユーザーが Azure Multi-Factor Authentication を利用できるライセンスをお持ちであれば、Azure MFA を有効にするうえで必要な作業は特にありません。 ユーザーごとに 2 段階認証の適用を開始できます。 Azure MFA を利用できるライセンスは、次の 3 種類です。

- Azure Multi-Factor Authentication
- Azure Active Directory Premium
- Enterprise Mobility + Security

## <a name="turn-on-two-step-verification-for-users"></a>ユーザーに対する 2 段階認証をオンにする

ユーザーまたはグループに [2 段階認証を要求する方法](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states)に関するページに記載されているいずれかの手順を使用して、実際に Azure MFA を使ってみましょう。 すべてのサインインに対して 2 段階認証を実施するか、条件付きアクセス ポリシーを作成して必要なときにのみ 2 段階認証を要求するかを選ぶことができます。

