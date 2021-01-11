---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/25/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 546c21802d275fe99ed2acbf00e32d37db3603f2
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2020
ms.locfileid: "88225200"
---
現時点では、Ultra Disk には次のような追加の制限があります。

Ultra ディスクで現在利用できる唯一のインフラストラクチャ冗長オプションは可用性ゾーンです。 他の冗長オプションを使用している VM では、Ultra ディスクを接続できません。

Ultra ディスクが利用できるリージョンとそれに該当する可用性オプションをまとめたものが次の表です。

> [!NOTE]
> 次の一覧のリージョンに、Ultra ディスク対応の可用性ゾーンがない場合、Ultra ディスクを接続するには、インフラストラクチャ冗長オプションなしで、そのリージョンの VM をデプロイする必要があります。

|リージョン  |Ultra ディスクをサポートする可用性ゾーンの数  |
|---------|---------|
|US Gov バージニア州     |なし         |
|US Gov アリゾナ     |なし         |
|米国中南部     |なし         |
|米国中部     |3 つのゾーン         |
|米国西部     |なし         |
|米国西部 2    |3 つのゾーン         |
|米国東部     |3 つのゾーン         |
|米国東部 2     |3 つのゾーン         |
|東南アジア     |3 つのゾーン         |
|東アジア     |なし         |
|北ヨーロッパ     |3 つのゾーン          |
|西ヨーロッパ     |3 つのゾーン          |
|英国南部     |3 つのゾーン          |
|東日本     |3 つのゾーン         |
|フランス中部    |2 つのゾーン        |
|ブラジル南部    |なし        |
|オーストラリア東部    |3 つのゾーン        |
|カナダ中部*    |3 つのゾーン        |

\* このリージョンの Availability Zones にアクセスするには、Azure サポートにお問い合わせください。

- 次の VM シリーズでのみサポートされています。
    - [ESv3](../articles/virtual-machines/ev3-esv3-series.md#esv3-series)
    - [Easv4](../articles/virtual-machines/eav4-easv4-series.md#easv4-series)
    - [Edsv4](../articles/virtual-machines/edv4-edsv4-series.md#edsv4-series)
    - [Esv4](../articles/virtual-machines/ev4-esv4-series.md#esv4-series)
    - [DSv3](../articles/virtual-machines/dv3-dsv3-series.md#dsv3-series)
    - [FSv2](../articles/virtual-machines/fsv2-series.md)
    - [LSv2](../articles/virtual-machines/lsv2-series.md)
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Ultra Disk がサポートされているすべてのリージョンで、すべての VM サイズを使用できるわけではありません
- データ ディスクとしてのみ使用可能であり、4k 物理セクター サイズのみをサポートしています。 Ultra Disk の 4K ネイティブ セクター サイズのために、Ultra Disk と互換性のないアプリケーションがいくつかあります。 1 つの例が、Oracle Database です。この場合、Ultra Disk をサポートするには、リリース 12.2 以降が必要です。  
- 空のディスクとしてのみ作成できます  
- ディスク スナップショット、VM イメージ、可用性セット、Azure 専用ホスト、Azure Disk Encryption は現在のところサポートされていません
- Azure Backup または Azure Site Recovery との統合は現在のところサポートされていません
- キャッシュされていない読み取りとキャッシュされていない書き込みのみをサポートします
- GA VM での IOPS の現在の上限は 80,000 です。

Azure Ultra ディスクからは、既定でサブスクリプション別にリージョンあたり最大 16 TiB が提供されますが、Ultra ディスクでは要求があればそれを超える容量が提供されます。 容量の増加を依頼するには、Azure サポートにお問い合わせください。
