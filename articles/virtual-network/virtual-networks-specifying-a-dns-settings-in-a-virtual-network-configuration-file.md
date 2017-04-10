---
title: "仮想ネットワーク構成ファイルでの DNS 設定の指定 | Microsoft Docs"
description: "クラシック デプロイ モデルで仮想ネットワーク構成ファイルを使用して仮想ネットワークの DNS サーバーの設定を変更する方法"
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
tags: azure-service-management
ms.assetid: a8905927-92ac-42b5-8c33-8e42c000692c
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ec33268915a1888509834ce6a5b2bc782a12ce4a


---
# <a name="specifying-dns-settings-in-a-virtual-network-configuration-file"></a>仮想ネットワーク構成ファイルでの DNS 設定の指定
ネットワーク構成ファイルの 2 つの要素を使用して、ドメイン ネーム システム (DNS) の設定 **DnsServers** と **DnsServerRef** を指定することができます。 **DnsServers** 要素に IP アドレスと参照名を指定することにより、DNS サーバーのリストを追加できます。 その後、 **DnsServerRef** 要素を使用して、DnsServers 要素から、仮想ネットワーク内のネットワーク サイトに使用する DNS サーバー エントリを指定できます。

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

この記事では、クラシック デプロイメント モデルについて説明します。

ネットワーク構成ファイルは、次の要素を含むことができます。 各要素のタイトルは、要素値の設定に関する追加情報を提供するページにリンクされています。

> [!IMPORTANT]
> ネットワーク構成ファイルの構成方法について詳しくは、「 [ネットワーク構成ファイルを使用した仮想ネットワークの構成](virtual-networks-using-network-configuration-file.md)」をご覧ください。 ネットワーク構成ファイルに含まれる各要素については、「 [Azure 仮想ネットワークの構成スキーマ](https://msdn.microsoft.com/library/azure/jj157100.aspx)」をご覧ください。
> 
> 

[Dns 要素](http://go.microsoft.com/fwlink/?LinkId=248093)

    <Dns>
      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>
    </Dns>

> [!WARNING]
> **DnsServer** 要素の **name** 属性は、**DnsServerRef** 要素の参照としてのみ使用されます。 DNS サーバーのホスト名を表してはいません。 各 **DnsServer** 属性の値は、Microsoft Azure サブスクリプション全体で一意である必要があります
> 
> 

[VirtualNetworkSites 要素](http://go.microsoft.com/fwlink/?LinkId=248093)

    <DnsServersRef>
      <DnsServerRef name="ID1" />
      <DnsServerRef name="ID2" />
      <DnsServerRef name="ID3" />
    </DnsServersRef>

> [!NOTE]
> VirtualNetworkSites 要素に対してこの設定を指定するには、その前に DNS 要素で定義されている必要があります。 VirtualNetworkSites 要素の DnsServerRef *name* は、DNS 要素で DnsServer *name* に対して指定されている名前の値を参照している必要があります。
> 
> 

## <a name="next-steps"></a>次のステップ
* [Azure Virtual Network の構成スキーマ](http://go.microsoft.com/fwlink/?LinkId=248093)について理解します。
* [Azure サービスの構成スキーマ](https://msdn.microsoft.com/library/windowsazure/ee758710)について理解します。
* [ネットワーク構成ファイルを使用して仮想ネットワークを構成](virtual-networks-using-network-configuration-file.md)します。




<!--HONumber=Nov16_HO3-->


