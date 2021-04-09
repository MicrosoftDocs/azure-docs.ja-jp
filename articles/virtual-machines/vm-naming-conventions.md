---
title: Azure VM のサイズの名前付け規則
description: Azure VM のサイズに使用される名前付け規則について説明します
ms.service: virtual-machines
subservice: sizes
author: mimckitt
ms.topic: conceptual
ms.date: 7/22/2020
ms.author: mimckitt
ms.custom: sttsinar
ms.openlocfilehash: 2fa362a56eb1246381fcc944e82ea85d31ff3d39
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599903"
---
# <a name="azure-virtual-machine-sizes-naming-conventions"></a>Azure 仮想マシンのサイズの名前付け規則

このページでは、Azure VM に使用される名前付け規則の概要について説明します。 VM では、これらの名前付け規則を使用して、さまざまな機能と仕様が示されます。

## <a name="naming-convention-explanation"></a>名前付け規則の説明

**[ファミリ]**  +  **[サブファミリ*]**  +  **[vCPU の数]**  +  **[制約付き vCPU*]**  +  **[追加機能]**  +  **[アクセラレータの種類*]**  +  **[バージョン]**

|値 | 説明|
|---|---|
| ファミリ | VM ファミリのシリーズを示します| 
| \* サブファミリ | 特殊化された VM を区別するためにのみ使用されます|
| vCPU の数| VM の vCPU の数を示します |
| *制約付き vCPU| 特定の VM サイズにのみ使用されます。 [制約付き vCPU 対応のサイズ](./constrained-vcpu.md)に対する vCPU の数を示します |
| 追加機能 | 1 つ以上の小文字により、次のような追加機能が示されます。 <br> a = AMD ベースのプロセッサ <br> d = ディスク (ローカル一時ディスクが存在します)。これは、新しい Azure VM 用です。「[Ddv4 および Ddsv4 シリーズ](./ddv4-ddsv4-series.md)」を参照してください <br> h = 休止状態対応 <br> i = 分離サイズ <br> l = 低メモリ。メモリ集中型のサイズよりも少ないメモリ量 <br> m = メモリ集中型。特定のサイズの最大メモリ容量 <br> t = 最少メモリ。特定のサイズの最小メモリ容量 <br> r = RDMA 対応 <br> s = Premium Storage 対応。[Ultra SSD](./disks-types.md#ultra-disk) を使用できる可能性が含まれます (注: M128、M64 などの一部の新しいサイズでは、s 属性がなくても、Premium Storage がサポートされます)<br> |
| \* アクセラレータの種類 | 特殊化された SKU および GPU SKU でハードウェア アクセラレータの種類を示します。 名前にハードウェア アクセラレータが含まれるのは、2020 年第 3 四半期以降に公開された新しい特殊化された SKU および GPU SKU だけです。 |
| Version | VM ファミリ シリーズのバージョンを示します |

## <a name="example-breakdown"></a>具体的な例

**[ファミリ]**  +  **[サブファミリ*]**  +  **[vCPU の数]**  +  **[追加機能]**  +  **[アクセラレータの種類*]**  +  **[バージョン]**

### <a name="example-1-m416ms_v2"></a>例 1:M416ms_v2

|値 | 説明|
|---|---|
| ファミリ | M | 
| vCPU の数 | 416 |
| 追加機能 | m = メモリ集中型 <br> s = Premium Storage 対応 |
| Version | v2 |

### <a name="example-2-nv16as_v4"></a>例 2:NV16as_v4

|値 | 説明|
|---|---|
| ファミリ | N | 
| サブファミリ | V |
| vCPU の数 | 16 |
| 追加機能 | a = AMD ベースのプロセッサ <br> s = Premium Storage 対応 |
| Version | v4 |

### <a name="example-3-nc4as_t4_v3"></a>例 3: NC4as_T4_v3

|値 | 説明|
|---|---|
| ファミリ | N | 
| サブファミリ | C |
| vCPU の数 | 4 |
| 追加機能 | a = AMD ベースのプロセッサ <br> s = Premium Storage 対応 |
| アクセラレータの種類 | T4 |
| Version | v3 |

### <a name="example-4-m8-2ms_v2-constrained-vcpu"></a>例 4: M8-2ms_v2 (制約付き vCPU)

|値 | 説明|
|---|---|
| ファミリ | M | 
| vCPU の数 | 8 |
| 制約付きの (実際の) vCPU の数 | 2 |
| 追加機能 | m = メモリ集中型 <br> s = Premium Storage 対応 |
| Version | v2 |

## <a name="next-steps"></a>次のステップ

Azure で使用可能な [VM サイズ](./sizes.md)の詳細について確認します。