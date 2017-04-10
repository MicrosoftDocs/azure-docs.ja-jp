---
title: "Azure での SharePoint サーバー ファームの作成 | Microsoft Docs"
description: "Azure Portal Marketplace を使用して Azure で SharePoint 2013 または SharePoint 2016 の新しいファームを短時間で作成します。"
services: virtual-machines-windows
documentationcenter: 
author: JoeDavies-MSFT
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 89b124da-019d-4179-86dd-ad418d05a4f2
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/30/2016
ms.author: josephd
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 9e8eb54913b1e7e3f40ac2c9b80b0818eee3a35f
ms.lasthandoff: 03/18/2017


---
# <a name="create-sharepoint-server-farms-using-the-azure-portal-marketplace"></a>Azure Portal Marketplace を使用して、SharePoint サーバー ファームを作成する

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="sharepoint-2013-farms"></a>SharePoint 2013 ファーム
Microsoft Azure Portal Marketplace では、事前に構成された SharePoint Server 2013 ファームを簡単に作成できます。 これにより、開発テスト環境で基本または高可用性の SharePoint ファームが必要な場合や、組織のコラボレーション ソリューションとして SharePoint Server 2013 を評価する場合の所要時間を大きく節約できます。

> [!NOTE]
> Azure Portal の Azure Marketplace の **SharePoint サーバー ファーム** 項目は削除されました。 これは、**SharePoint 2013 非 HA ファーム**項目および **SharePoint 2013 HA ファーム**項目に置き換えられました。
>
>

この構成では、次の&3; つの仮想マシンの基本的な SharePoint ファームで構成されます。

![sharepointfarm](./media/virtual-machines-windows-sharepoint-farm/Non-HAFarm.png)

このファーム構成は、SharePoint アプリ開発の簡略化セットアップまたは、SharePoint 2013 の初回の評価で使用することができます。

基本的な (3 サーバー) SharePoint ファームを作成するには:

1. [ここ](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-nonha/)をクリックしてください。
2. **[デプロイ]**をクリックします。
3. **[SharePoint 2013 非 HA ファーム]** ウィンドウで、**[作成]** をクリックします。
4. **[SharePoint 2013 非 HA ファームの作成]** ウィンドウの手順で設定を指定して、**[作成]** をクリックします。

高可用性 SharePoint ファームは、次に示す構成の&9; 台の仮想マシンで構成されます。

![sharepointfarm](./media/virtual-machines-windows-sharepoint-farm/HAFarm.png)

このファーム構成は、より大量のクライアント読み込みや外部の SharePoint サイトの高可用性をテストしたり、SharePoint ファームの SQL Server AlwaysOn 可用性グループをテストしたりする場合に使用できます。 この構成は、高可用環境での SharePoint アプリケーション開発でも使用できます。

高可用性 (9 サーバー) の SharePoint ファームを作成するには:

1. [ここ](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-ha/)をクリックしてください。
2. **[デプロイ]**をクリックします。
3. **[SharePoint 2013 HA ファーム]** ウィンドウで、**[作成]** をクリックします。
4. **[Create SharePoint 2013 HA Farm (SharePoint 2013 HA ファームの作成)]** ウィンドウの 7 つの手順で設定を指定して、**[作成]** をクリックします。

> [!NOTE]
> Azure 無料試用版では **SharePoint 2013 非 HA ファーム**または **SharePoint 2013 HA ファーム**を作成することはできません。
>
>

Azure Portal は、インターネットに接続する Web プレゼンスを持つクラウド専用の仮想ネットワークにこれら両ファームを作成します。 組織のネットワークに戻るサイト間 VPN 接続や ExpressRoute 接続はありません。

> [!NOTE]
> Azure Portal を使用して可用性が基本レベルか高い SharePoint ファームを作成する場合、既存のリソース グループを指定することはできません。 この制限を回避するには、Azure PowerShell を使用してこれらのファームを作成します。 詳細については、[Azure PowerShell を使った SharePoint 2013 開発/テスト ファームの作成](https://technet.microsoft.com/library/mt743093.aspx#powershell)をご覧ください。
>
>

## <a name="sharepoint-2016-farms"></a>SharePoint 2016 ファーム
次の SharePoint Server 2016 単一サーバー ファームの構築については、[こちらの記事](https://technet.microsoft.com/library/mt723354.aspx)をご覧ください。

![sharepointfarm](./media/virtual-machines-windows-sharepoint-farm/SP2016Farm.png)

## <a name="managing-the-sharepoint-farms"></a>SharePoint ファームの管理
これらのファームのサーバーはリモート デスクトップ接続を通じて管理できます。 詳細については、「[仮想マシンへのログオン](virtual-machines-windows-quick-create-portal.md#connect-to-virtual-machine)」をご覧ください。

SharePoint のサーバーの全体管理サイトで [My Sites]、SharePoint アプリケーション、その他の機能を構成できます。 詳細については、 [SharePoint の構成](http://technet.microsoft.com/library/ee836142.aspx)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ
* Azure インフラストラクチャ サービスで、追加の [SharePoint の構成](https://technet.microsoft.com/library/dn635309.aspx) を検出します。

