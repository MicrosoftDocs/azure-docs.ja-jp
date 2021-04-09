---
title: Azure レンダリングの参照アーキテクチャ
description: クラウドにバーストすることで Azure Batch およびその他の Azure サービスを使用してオンプレミス レンダー ファームを拡張するためのアーキテクチャ
ms.date: 02/07/2019
ms.topic: how-to
ms.custom: seodec18
ms.openlocfilehash: 784fd89c3dea88e25a2058713897c7a655c8a3af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "83726521"
---
# <a name="reference-architectures-for-azure-rendering"></a>Azure レンダリング向け参照アーキテクチャ

この記事では、オンプレミス レンダー ファームを Azure に拡張、つまり "バースト" するためのアーキテクチャ概要の図を示します。 例は、Azure コンピューティング、ネットワーク、およびストレージ サービスで使用できるさまざまなオプションを示しています。

## <a name="hybrid-with-nfs-or-cfs"></a>NFS または CFS を持つハイブリッド

次の図は、以下の Azure サービスを含むハイブリッド シナリオを示しています。

* **コンピューティング** - Azure Batch プールまたは仮想マシン スケール セット。

* **ネットワーク** - オンプレミス: Azure ExpressRoute または VPN。 Azure: Azure VNet。

* **ストレージ** - 入力ファイルと出力ファイル:Azure VM を使用した NFS または CFS。Azure File Sync または RSync を介してオンプレミス ストレージと同期されています。 または、NFS を使用してオンプレミスの NAS デバイスからファイルを入力または出力するための Avere vFXT。

  ![クラウド バースティング - NFS または CFS を持つハイブリッド](./media/batch-rendering-architectures/hybrid-nfs-cfs-avere.png)

## <a name="hybrid-with-blobfuse"></a>Blobfuse を持つハイブリッド

次の図は、以下の Azure サービスを含むハイブリッド シナリオを示しています。

* **コンピューティング** - Azure Batch プールまたは仮想マシン スケール セット。

* **ネットワーク** - オンプレミス: Azure ExpressRoute または VPN。 Azure: Azure VNet。

* **ストレージ** - 入力ファイルと出力ファイル: Blob Storage。Azure Blobfuse を介してコンピューティング リソースにマウントされています。

  ![クラウド バースティング - Blobfuse を持つハイブリッド](./media/batch-rendering-architectures/hybrid-blob-fuse.png)

## <a name="hybrid-compute-and-storage"></a>ハイブリッド コンピューティングとストレージ

次の図は、コンピューティングとストレージの両方を対象とした完全結合されたハイブリッド シナリオを示しており、次の Azure サービスが含まれています。

* **コンピューティング** - Azure Batch プールまたは仮想マシン スケール セット。

* **ネットワーク** - オンプレミス: Azure ExpressRoute または VPN。 Azure: Azure VNet。

* **ストレージ** - クロスプレミス: Avere vFXT。 (省略可能) Azure Data Box を介した BLOB ストレージへのオンプレミス ファイルのアーカイブ、または NAS 高速化のためのオンプレミスの Avere FXT。

  ![クラウド バースティング - ハイブリッド コンピューティングとストレージ](./media/batch-rendering-architectures/hybrid-compute-storage-avere.png)


## <a name="next-steps"></a>次のステップ

* Azure Batch での[レンダー マネージャー](batch-rendering-render-managers.md)の使用について確認します。

* [Azure でのレンダリング](batch-rendering-service.md)用オプションを確認します。