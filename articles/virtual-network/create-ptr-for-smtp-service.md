---
title: Azure で SMTP バナー チェック用に逆引き参照ゾーンを構成する
titlesuffix: Azure Virtual Network
description: Azure で SMTP バナー チェック用に逆引き参照ゾーンを構成する方法について説明します
services: virtual-network
documentationcenter: virtual-network
author: genlin
manager: dcscontentpm
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 084fdb7f850f3819738a982127fa98efab114197
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059018"
---
# <a name="configure-reverse-lookup-zones-for-an-smtp-banner-check"></a>SMTP バナー チェック用に逆引き参照ゾーンを構成する

この記事では、Azure DNS での逆引きゾーンの使用方法と、SMTP バナー チェック用の逆引き DNS (PTR) レコードの作成方法について説明します。

## <a name="symptom"></a>症状

Microsoft Azure で SMTP サーバーをホストしている場合、リモート メール サーバーとの間でメッセージを送受信したときに、次のエラー メッセージが返されることがあります。

**554:PTR レコードがありません**

## <a name="solution"></a>解決策

Azure の仮想 IP アドレスの場合、逆引きレコードは、カスタム ドメイン ゾーンではなく Microsoft 所有のドメイン ゾーンに作成されます。

Microsoft 所有のゾーンで PTR レコードを構成するには、PublicIpAddress リソースで -ReverseFqdn プロパティを使用します。 詳細については、「[Azure でホストされているサービスの逆引き DNS を構成する](../dns/dns-reverse-dns-for-azure-services.md)」を参照してください。

PTR レコードを構成する際には、IP アドレスと逆引き FQDN がサブスクリプションによって所有されていることを確認してください。 サブスクリプションに属していない逆引き FQDN を設定しようとすると、次のエラー メッセージが返されます。

    Set-AzPublicIpAddress : ReverseFqdn mail.contoso.com that PublicIPAddress ip01 is trying to use does not belong to subscription <Subscription ID>. One of the following conditions need to be met to establish ownership:
                        
    1) ReverseFqdn がサブスクリプションのいずれかのパブリック IP リソースの FQDN と一致する、
    2) ReverseFqdn が (CName レコード チェーンによって) サブスクリプションのいずれかのパブリック IP リソースの FQDN に解決される、
    3) ReverseFqdn が (CNAME レコード チェーンによって) サブスクリプションの静的パブリック IP リソースの IP アドレスに解決される。

既定の逆引き FQDN に一致するように SMTP バナーを手動で変更しても、リモート メール サーバーは、SMTP バナー ホストがドメインの MX レコードに一致することを予期する場合があるため、やはりエラーを起こす可能性があります。
