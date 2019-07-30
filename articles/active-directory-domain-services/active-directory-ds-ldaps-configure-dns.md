---
title: LDAPS を使用してインターネット経由でマネージド ドメインにアクセスするよう DNS を構成する |Microsoft Docs
description: LDAPS を使用してインターネット経由で Azure AD Domain Services のマネージド ドメインにアクセスするよう DNS を構成します
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: a47f0f3e-2578-422a-a421-034f66de38f5
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: iainfou
ms.openlocfilehash: 1c5a119c686bc879b11b8fa87a9c81ba36b0beb4
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2019
ms.locfileid: "67474018"
---
# <a name="configure-dns-to-access-an-azure-ad-domain-services-managed-domain-using-secure-ldap-ldaps"></a>セキュア LDAP (LDAPS) を使用して Azure AD Domain Services のマネージド ドメインにアクセスするよう DNS を構成する

## <a name="before-you-begin"></a>開始する前に
「[タスク 3: Azure Portal を使用して、マネージド ドメインに対してセキュリティで保護された LDAP を有効にする](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)」を完了してください

## <a name="task-4-configure-dns-to-access-the-managed-domain-from-the-internet"></a>タスク 4: インターネットからマネージド ドメインにアクセスできるように DNS を構成する
> [!TIP]
> **オプションのタスク** - インターネット経由で LDAPS を使用してマネージド ドメインにアクセスする予定がない場合、この構成タスクは飛ばしてください。
>
>

このタスクを開始する前に、[タスク 3](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md) で説明されている手順を完了します。

インターネット経由でのセキュリティで保護された LDAP アクセスを有効にしたら、クライアント コンピューターがこのマネージド ドメインを見つけられるようにするために、DNS を更新する必要があります。 **[プロパティ]** タブの **[LDAPS アクセスのための外部 IP アドレス]** に、外部 IP アドレスが表示されます。

マネージド ドメインの DNS 名 (例: ldaps.contoso100.com) がこの外部 IP アドレスをポイントするように、外部 DNS プロバイダーを構成します。 この例では、次の DNS エントリを作成します。

    ldaps.contoso100.com  -> 52.165.38.113

これで完了です。 これで、インターネット経由でセキュリティで保護された LDAP を使用してマネージド ドメインに接続する準備ができました。

> [!WARNING]
> LDAPS を使用してマネージド ドメインに正常に接続できるようにするには、クライアント コンピューターで LDAPS 証明書の発行者を信頼する必要があることに注意してください。 公的に信頼された証明機関を使用している場合は、クライアント コンピューターでこれらの証明書発行者が信頼されるため、特に設定を行う必要はありません。 自己署名証明書を使用している場合は、自己署名証明書の公開部分をクライアント コンピューター上の信頼された証明書ストアにインストールします。
>
>

## <a name="next-step"></a>次のステップ
[タスク 5: マネージド ドメインにバインドして LDAP アクセスをロックダウンする](active-directory-ds-ldaps-bind-lockdown.md)
