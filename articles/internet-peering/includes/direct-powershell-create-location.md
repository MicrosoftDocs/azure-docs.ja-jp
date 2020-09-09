---
title: インクルード ファイル
titleSuffix: Azure
description: インクルード ファイル
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: dbaa0b5fc87cb5393b323b8a9b7a38b72efe9518
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "81680755"
---
PowerShell コマンドレット **Get-AzPeeringLocation** の必須パラメーター `Kind` によって、ピアリングの場所の一覧が返されます。これは後述の手順で使用します。

```powershell
Get-AzPeeringLocation -Kind Direct
```

直接ピアリングの場所には、次のフィールドが含まれます。
* PeeringLocation 
* Country
* PeeringDBFacilityId
* PeeringDBFacilityLink
* BandwidthOffers

[PeeringDB](https://www.peeringdb.com) を参照して、そこが目的のピアリング ファシリティであることを検証します。

この例では、ピアリングの場所として Seattle を使用し、Direct ピアリングを作成する方法を示します。

```powershell
$peeringLocations = Get-AzPeeringLocation -Kind Direct
$peeringLocation = $peeringLocations | where {$_.PeeringLocation -contains "Seattle"}
$peeringLocation

PeeringLocation       : Seattle
Address               : 2001 Sixth Avenue
Country               : US
PeeringDBFacilityId   : 71
PeeringDBFacilityLink : https://www.peeringdb.com/fac/71
BandwidthOffers       : {10Gbps, 100Gbps}
```