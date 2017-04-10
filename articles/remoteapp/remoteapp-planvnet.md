---
title: "Azure RemoteApp コレクション用に仮想ネットワークを計画する方法 | Microsoft Docs"
description: "Azure RemoteApp コレクション用に仮想ネットワークを計画する方法について説明します。"
services: remoteapp
documentationcenter: 
author: mghosh1616
manager: mbaldwin
ms.assetid: ad9aff0e-f374-49c0-951d-4a7be1c36de0
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 71537845edcca85a7d0722dd32264da9f6b579e9


---
# <a name="how-to-plan-your-virtual-network-for-azure-remoteapp"></a>Azure RemoteApp の仮想ネットワークを計画する方法
> [!IMPORTANT]
> Azure RemoteApp の提供は終了しました。 詳細については、 [お知らせ](https://go.microsoft.com/fwlink/?linkid=821148) をご覧ください。
> 
> 

このドキュメントでは、Azure RemoteApp 用に Azure の仮想ネットワーク (VNET) とサブネットを設定する方法について説明します。 Azure の仮想ネットワークを熟知していない場合、この機能は、クラウドにネットワーク インフラストラクチャを仮想化し、Azure とオンプレミスのリソースを使用したハイブリッド ソリューションを作成するのに役立ちます。 詳細については、 [こちら](../virtual-network/virtual-networks-overview.md)を参照してください。

Azure RemoteApp をデプロイする仮想ネットワーク内の送受信トラフィックについてセキュリティ ポリシーを定義する場合は、Azure RemoteApp 用には、Azure の仮想ネットワーク内の他のデプロイとは別にサブネットを作成することを強くお勧めします。 Azure の仮想ネットワークのサブネットにセキュリティ ポリシーを定義する方法の詳細については、「 [ネットワーク セキュリティ グループ (NSG) について](../virtual-network/virtual-networks-nsg.md)」を参照してください。

## <a name="types-of-azure-remoteapp-collections-with-azure-virtual-networks"></a>Azure の仮想ネットワークを使用した Azure RemoteApp コレクションの種類
次の図は、仮想ネットワークを使用する場合に選択できる 2 つの異なるコレクションを示しています。

### <a name="azure-remoteapp-cloud-collection-with-vnet"></a>VNET を使用した Azure RemoteApp クラウド コレクション
 ![Azure RemoteApp - VNET を使用したクラウド コレクション](./media/remoteapp-planvpn/ra-cloudvpn.png)

この Azure RemoteApp コレクションでは、RemoteApp セッション ホストがアクセスする必要があるすべてのリソースが Azure にデプロイされています。 これらは Azure 内の RemoteApp VNET と同じ VNET にも、別の VNET にもデプロイできます。

### <a name="azure-remoteapp-hybrid-collection-with-vnet"></a>VNET を使用した Azure RemoteApp ハイブリッド コレクション
![Azure RemoteApp - VNET を使用したハイブリッド コレクション](./media/remoteapp-planvpn/ra-hybridvpn.png)

この Azure RemoteApp コレクションでは、RemoteApp セッション ホストがアクセスする必要がある一部のリソースがオンプレミスでデプロイされています。 RemoteApp VNET は、サイト間 VPN または Express Route のような Azure のハイブリッド テクノロジを使用して、オンプレミスのネットワークにリンクされます。

## <a name="how-the-system-works"></a>システムの動作
内部では、Azure RemoteApp は、プロビジョニング中に選択した仮想ネットワークのサブネットに、(アップロードされたイメージを使用して) Azure 仮想マシンをデプロイします。 ハイブリッド コレクションを選択した場合は、仮想ネットワークで提供される DNS サーバーのプロビジョニング ワークフローで入力したドメイン コントローラーの FQDN の解決が試行されます。  
既存の仮想ネットワークに接続する場合は、Azure RemoteApp のサブネットでネットワーク セキュリティ グループ内の必要なポートを公開してください。 

[Azure RemoteApp にとって十分な規模のサブネット](remoteapp-vnetsizing.md)を使用することをお勧めします。 Azure Virtual Network でサポートされている最大数は /8 です (CIDR のサブネット定義を使用)。 サブネットは、スケール アップ時に Azure RemoteApp のすべての VM を収容するのに十分な規模にし、より多くのユーザーがアプリにアクセスできるようにする必要があります。 

次は、仮想ネットワークのサブネット上で有効にする必要がある機能を示しています。 

1. サブネットからの送信トラフィックは、内部の Azure RemoteApp サービスのいずれかと通信するために、ポート範囲 10101 ～ 10175 で許可されている必要があります。
2. ポート 443 で、Azure Storage に接続するサブネットからの送信トラフィックを許可する必要があります。
3. Active Directory を Azure でホストしている場合は、Azure RemoteApp 用の仮想ネットワークのサブネットで、どの VM もドメイン コントローラーに接続できることを確認します。 仮想ネットワークの DNS によって、このドメイン コントローラーの FQDN を解決できる必要があります。

## <a name="virtual-network-with-forced-tunneling"></a>強制トンネリングを使用した仮想ネットワーク
[強制トンネリング](../vpn-gateway/vpn-gateway-about-forced-tunneling.md) が、すべての新しい Azure RemoteApp コレクションでサポートされるようになりました。 現在、既存のコレクションを移行して、強制トンネリングをサポートすることはできません。  コレクションで強制トンネリングを有効にするには、Azure RemoteApp にリンクしている VNET を使用する既存のコレクションをすべて削除し、新しいコレクションを作成する必要があります。 




<!--HONumber=Nov16_HO3-->


