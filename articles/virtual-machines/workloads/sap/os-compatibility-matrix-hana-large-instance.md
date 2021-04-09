---
title: SAP HANA (L インスタンス) のオペレーティング システムの互換性マトリックス |Microsoft Docs
description: 互換性マトリックスでは、さまざまなバージョンのオペレーティング システムとさまざまな種類のハードウェア (L インスタンス) との互換性を表します
services: virtual-machines-linux
documentationcenter: ''
author: sasarava
manager: hrushib
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/21/2020
ms.author: sasarava
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ccfdffc4e488de7f3cecb150305596743b3a9e44
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101675399"
---
# <a name="compatible-operating-systems-for-hana-large-instances"></a>HANA L インスタンスと互換性があるオペレーティング システム

## <a name="hana-large-instance-type-i"></a>HANA L インスタンス タイプ I     
  | オペレーティング システム | 可用性        | SKU                                                          |
  |------------------|---------------------|---------------------------------------------------------------|
  | SLES 12 SP2      | 提供終了 | S72、S72m、S96、S144、S144m、S192、S192m、S192xm              |
  | SLES 12 SP3      | 利用可能           | S72、S72m、S96、S144、S144m、S192、S192m、S192xm              |
  | SLES 12 SP4      | 利用可能           | S72、S72m、S96、S144、S144m、S192、S192m、S192xm、S224、S224m |
  | SLES 12 SP5      | 利用可能           | S72、S72m、S96、S144、S144m、S192、S192m、S192xm、S224、S224m |
  | SLES 15 SP1      | 利用可能           | S72、S72m、S96、S144、S144m、S192、S192m、S192xm、S224、S224m |
  | RHEL 7.6         | 利用可能           | S72、S72m、S96、S144、S144m、S192、S192m、S192xm、S224、S224m |

  
### <a name="persistent-memory-skus"></a>永続メモリ SKU
  | オペレーティング システム | 可用性 | SKU                             |
  |------------------|--------------|----------------------------------|
  | SLES 12 SP4      | 利用可能    | S224oo、S224om、S224ooo、S224oom |
  
## <a name="hana-large-instance-type-ii"></a>HANA L インスタンス タイプ II     
  |  オペレーティング システム       | 可用性        | SKU                                                                     |
  |-------------------------|---------------------|--------------------------------------------------------------------------|
  | SLES 12 SP2             | 提供終了 | S384、S384m、S384xm、S384xxm、S576m、S576xm、S768m、S768xm、S960m        |
  | SLES 12 SP3             | 利用可能           | S384、S384m、S384xm、S384xxm、S576m、S576xm、S768m、S768xm、S960m        |
  | SLES 12 SP4             | 利用可能           | S384、S384m、S384xm、S384xxm、S576m、S576xm、S768m、S768xm、S960m        |
  | SLES 12 SP5             | 利用可能           | S384、S384m、S384xm、S384xxm、S576m、S576xm、S768m、S768xm、S896m、S960m |
  | SLES 15 SP1             | 利用可能           | S384、S384m、S384xm、S384xxm、S576m、S576xm、S768m、S768xm、S896m、S960m |
  | RHEL 7.6                | 利用可能           | S384、S384m、S384xm、S384xxm、S576m、S576xm、S768m、S768xm、S896m、S960m |

## <a name="related-documents"></a>関連ドキュメント

- [利用可能な SKU](hana-available-skus.md) に関する詳細を確認する
- [オペレーティング システムのアップグレード](os-upgrade-hana-large-instance.md)に関する詳細を確認する
  

  
  
