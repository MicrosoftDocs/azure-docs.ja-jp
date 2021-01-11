---
title: Windows Virtual Desktop ユーザー接続の待機時間 - Azure
description: Windows Virtual Desktop ユーザー用の接続の待機時間
author: Heidilohr
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 403cf584c79bc0a166054ae36c9d2ea50e4b9d9e
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2020
ms.locfileid: "88008731"
---
# <a name="determine-user-connection-latency-in-windows-virtual-desktop"></a>Windows Virtual Desktop でのユーザー接続の待機時間の決定

Windows Virtual Desktop はグローバルに使用できます。 管理者は、必要な任意の Azure リージョンに仮想マシン (VM) を作成できます。 接続の待機時間は、ユーザーと仮想マシンの場所によって異なります。 Windows Virtual Desktop サービスは、待機時間を短縮するために、新しい地域に継続的にロールアウトされます。

[Windows Virtual Desktop エクスペリエンス見積もりツール](https://azure.microsoft.com/services/virtual-desktop/assessment/)を使用すると、VM の待機時間を最適化するための最適な場所を判断できます。 Windows Virtual Desktop が新しい領域にロールアウトされるときに、最適な場所が変更されていないことを確認するために、2 - 3 か月おきにこのツールを使用することをお勧めします。

## <a name="azure-traffic-manager"></a>Azure の Traffic Manager

Windows Virtual Desktop では Azure Traffic Manager が使用されます。これにより、ユーザーの DNS サーバーの場所が確認され、最も近い Windows Virtual Desktop サービス インスタンスが見つかります。 管理者は、VM の場所を選択する前に、ユーザーの DNS サーバーの場所を確認することをお勧めします。

## <a name="next-steps"></a>次のステップ

- 最適な待機時間のための最適な場所を確認するには、「[Windows Virtual Desktop エクスペリエンス見積もりツール](https://azure.microsoft.com/services/virtual-desktop/assessment/)」を参照してください。
- 価格プランの詳細については、「[Windows Virtual Desktop の価格](https://azure.microsoft.com/pricing/details/virtual-desktop/)」を参照してください。
- Windows Virtual Desktop のデプロイを開始するには、[こちらのチュートリアル](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)を確認してください。