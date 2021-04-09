---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/30/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 22a1a4b99717df32a40ea69ebb65a3a8e14ee2b4
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102511140"
---
一部の種類のディスクでのみ、共有ディスクを有効にできます。 現在のところ、Ultra ディスクと Premium SSD のみで共有ディスクを有効にできます。 共有ディスクが有効になっているマネージド ディスクはそれぞれ、次の制約を受けます。制限はディスクの種類別に整理されています。

### <a name="ultra-disks"></a>Ultra ディスク

Ultra ディスクには、共有ディスクとは関係なく、独自の制限リストがあります。 Ultra ディスクの制限については、「[Azure Ultra ディスクの使用](../articles/virtual-machines/disks-enable-ultra-ssd.md)」を参照してください。

Ultra ディスクを共有するとき、次の追加制限があります。

- Azure Resource Manager または SDK サポートに現在、制限されています。 
- Windows Server フェールオーバー クラスターの一部のバージョンでは、ベーシック ディスクのみを使用できます。詳細については、「[フェールオーバー クラスタリングのハードウェア要件と記憶域オプション](/windows-server/failover-clustering/clustering-requirements)」を参照してください。

共有 Ultra ディスクは、既定で、Ultra ディスクをサポートするすべてのリージョンで利用でき、それらを使用するためのアクセスに対してサインアップする必要はありません。

### <a name="premium-ssds"></a>Premium SSD

- Azure Resource Manager または SDK サポートに現在、制限されています。 
- OS ディスクではなく、データ ディスクでのみ有効にすることができます。
- **ReadOnly** ホスト キャッシュは、`maxShares>1` の Premium SSD では使用できません。
- ディスク バーストは、`maxShares>1` の Premium SSD では使用できません。
- Azure 共有ディスクで可用性セットと仮想マシン スケール セットを使用している場合、仮想マシン障害ドメインによる[ストレージ障害ドメイン配置](../articles/virtual-machines/availability.md)は共有データ ディスクには適用されません。
- [近接配置グループ (PPG)](../articles/virtual-machines/windows/proximity-placement-groups.md) を使用する場合は、ディスクを共有するすべての仮想マシンが同じ PPG に含まれている必要があります。
- Windows Server フェールオーバー クラスターの一部のバージョンでは、ベーシック ディスクのみを使用できます。詳細については、「[フェールオーバー クラスタリングのハードウェア要件と記憶域オプション](/windows-server/failover-clustering/clustering-requirements)」を参照してください。
- Azure Site Recovery のサポートは、まだ使用できません。
- Azure Backup は [Azure ディスク バックアップ (プレビュー)](../articles/backup/disk-backup-overview.md) を通じて利用できます。

#### <a name="regional-availability"></a>リージョン別の提供状況

共有 Premium SSD は、マネージド ディスクが使用可能なすべてのリージョンで利用できます。