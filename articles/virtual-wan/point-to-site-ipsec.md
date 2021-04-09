---
title: 仮想 WAN ポイント対サイト IPsec ポリシー
titleSuffix: Azure Virtual WAN
description: Azure Virtual WAN ポイント対サイト IPsec 接続ポリシーについて説明します。
services: virtual-wan
author: wellee
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 02/24/2021
ms.author: wellee
ms.openlocfilehash: d64748bf7719af52b76c5e4141bfbbabe80bbae2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101743957"
---
# <a name="point-to-site-ipsec-policies"></a>ポイント対サイト IPsec ポリシー

この記事では、Azure Virtual WAN におけるポイント対サイト VPN 接続でサポートされている IPsec ポリシーの組み合わせを示します。

## <a name="default-ipsec-policies"></a>既定の IPsec ポリシー

次の表は、ポイント対サイト VPN 接続の既定の IPsec パラメーターを示しています。 これらのパラメーターは、ポイント対サイト プロファイルが作成されると、仮想 WAN ポイント対サイト VPN ゲートウェイによって自動的に選択されます。

| 設定 | パラメーター |
|--- |--- |
| フェーズ 1 IKE 暗号化 | AES256 |
| フェーズ 1 IKE 整合性 |  SHA256 |
| DH グループ | DHGroup24 |
| フェーズ 2 IPsec 暗号化 | GCMAES256|
| フェーズ 2 IPsec 整合性 | GCMAES25 |
| PFS グループ |PFS24|

## <a name="custom-ipsec-policies"></a>カスタムの IPsec ポリシー

カスタム IPsec ポリシーを操作する場合は、次の要件に注意してください。

* **IKE** - フェーズ 1 IKE の場合、IKE 暗号化の任意のパラメーターと、IKE 整合性の任意のパラメーター、および DH グループの任意のパラメーターを選択できます。
* **IPsec** - フェーズ 2 IPsec の場合、IPsec 暗号化の任意のパラメーターと、IPsec 整合性の任意のパラメーター、および PFS を選択できます。 IPsec 暗号化または IPsec 整合性のパラメーターのいずれかが GCM の場合、IPsec 暗号化と整合性の両方で同じアルゴリズムを使用する必要があります。 たとえば、IPsec 暗号化に GCMAES128 が選択されている場合、IPsec 整合性についても GCMAES128 を選択する必要があります。  

次の表は、ポイント対サイト VPN 接続で使用可能な IPsec パラメーターを示しています。

| 設定 | パラメーター |
|--- |--- |
| フェーズ 1 IKE 暗号化 | AES128、AES256、GCMAES128、GCMAES256 |
| フェーズ 1 IKE 整合性 |  SHA256、SHA384 |
| DH グループ | DHGroup14、DHGroup24、ECP256、ECP384 |
| フェーズ 2 IPsec 暗号化 | GCMAES128、GCMAES256、SHA256|
| フェーズ 2 IPsec 整合性 | GCMAES128、GCMAES256 |
| PFS グループ |PFS14、PFS24、ECP256、ECP384|

## <a name="next-steps"></a>次のステップ

[ポイント対サイト接続を作成する方法](virtual-wan-point-to-site-portal.md)に関するページを参照してください

Virtual WAN の詳細については、[Virtual WAN FAQ](virtual-wan-faq.md) を参照してください。
