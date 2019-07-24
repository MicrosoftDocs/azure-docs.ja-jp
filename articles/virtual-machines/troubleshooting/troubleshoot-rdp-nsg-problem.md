---
title: NSG で RDP ポートが有効になっていないため、Azure VM に接続できない | Microsoft Docs
description: Azure portal での NSG の構成のために RDP が失敗する問題をトラブルシューティングする方法について説明します | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: v-jesits
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/20/2018
ms.author: genli
ms.openlocfilehash: c32612c411f275220f549eea79276fa5a7232fd0
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52954614"
---
#  <a name="cannot-connect-remotely-to-a-vm-because-rdp-port-is-not-enabled-in-nsg"></a>NSG で RDP ポートが有効ではないために VM にリモート接続できない

この記事では、リモート デスクトップ プロトコル (RDP) のポートがネットワーク セキュリティ グループ (NSG) で有効になっていないために、Azure Windows 仮想マシン (VM) に接続できない問題を解決する方法について説明します。


> [!NOTE] 
> Azure には、リソースの作成と操作に関して 2 種類のデプロイ モデルがあります。[Resource Manager とクラシック](../../azure-resource-manager/resource-manager-deployment-model.md)です。 新しいデプロイでは、クラシック デプロイ モデルではなく、Resource Manager デプロイ モデルを使用することをお勧めします。 

## <a name="symptom"></a>症状

RDP ポートがネットワーク セキュリティ グループで開かれていないために、Azure 内の VM に RDP 接続できません。

## <a name="solution"></a>解決策 

新しい VM を作成すると、インターネットからのすべてのトラフィックが既定でブロックされます。 

NSG で RDP ポートを有効にするには、次の手順のようにします。
1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. **[仮想マシン]** で、問題のある VM を選択します。 
3. **[設定]** の **[ネットワーク]** を選択します。 
4. **[受信ポートの規則]** で、RDP のポートが正しく設定されていることを確認します。 構成の例を次に示します。 

    **優先度**:300 </br>
    **ポート**:3389 </br>
    **名前**:Port_3389 </br>
    **ポート**:3389 </br>
    **プロトコル**:TCP </br>
    **ソース**:任意 </br>
    **宛先**:任意 </br>
    **アクション**:ALLOW </br>

ソース IP アドレスを指定した場合、この設定では、特定の IP アドレスまたは IP アドレス範囲からのトラフィックのみが VM への接続を許可されます。 RDP セッションを開始するために使用しているコンピューターが範囲内にあることを確認します。

NSG の詳細については、[ネットワーク セキュリティ グループ](../../virtual-network/security-overview.md)に関するページを参照してください。

> [!NOTE]
> RDP ポート 3389 がインターネットに公開されます。 そのため、このポートはテストのみに使用することをお勧めします。 運用環境では、VPN またはプライベート接続を使用することをお勧めします。

## <a name="next-steps"></a>次の手順

RDP ポートが NSG で既に有効になっている場合は、「[Azure VM での RDP 一般エラーのトラブルシューティング](./troubleshoot-rdp-general-error.md)」をご覧ください。



