---
title: "サービス構成ファイルでの DNS 設定の指定 | Microsoft Docs"
description: "仮想ネットワークのサービス構成ファイルを使用してカスタム DNS 設定を指定する"
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: 467a4b99-8691-40b3-b640-e25e49675c71
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0fba2ea06827aff29a7a092933edb8120d668b29


---
# <a name="specifying-dns-settings-in-a-service-configuration-file"></a>サービス構成ファイルでの DNS 設定の指定
## <a name="dns-elements"></a>DNS 要素
サービス構成ファイルでは、サービスが使用するドメイン ネーム システム (DNS) サーバーの IPv4 アドレスのリストである DnsServers 要素を指定できます。 サービス構成ファイルの設定は、ネットワーク構成ファイルの設定より優先されます。 詳細については、「 [Microsoft Azure サービスの構成スキーマ (.cscfg ファイル)](https://msdn.microsoft.com/library/azure/ee758710.aspx)」を参照してください。

**NetworkConfiguration 要素**

      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>

> [!WARNING]
> **DnsServer** 要素の **name** 属性は、参照名としてのみ使用されます。 DNS サーバーのホスト名を表してはいません。 各 **DnsServer** 属性の値は、Microsoft Azure サブスクリプション全体で一意である必要があります。
> 
> 

## <a name="see-also"></a>関連項目
[Azure サービス構成スキーマ (.cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710)

[Azure Virtual Network の構成スキーマ](http://go.microsoft.com/fwlink/?LinkId=248093)

[ネットワーク構成ファイルを使用した Virtual Network の構成](http://go.microsoft.com/fwlink/?LinkId=248094)

[管理ポータルでの Virtual Network の設定について](http://go.microsoft.com/fwlink/?LinkId=248092)




<!--HONumber=Nov16_HO3-->


