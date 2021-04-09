---
title: Azure Batch プールの Azure ファイル共有
description: Azure Batch の Linux または Windows プールの計算ノードから Azure Files 共有をマウントする方法
ms.topic: how-to
ms.date: 05/24/2018
ms.openlocfilehash: e5682e7ba853973592c3a650a06ce72615cec7b6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98735496"
---
# <a name="use-an-azure-file-share-with-a-batch-pool"></a>Batch プールと共に Azure ファイル共有を使用する

Azure Files はクラウドで、[Server Message Block (SMB) プロトコル](../storage/files/storage-files-introduction.md)を介してアクセスできる、完全に管理されたファイル共有を提供します。 この記事では、プールの計算ノード上に Azure ファイル共有をマウントして使用するための情報とコード サンプルを示します。

## <a name="considerations-for-use-with-batch"></a>Batch と共に使用するための考慮事項

* Premium 以外の Azure Files を使用する場合は、比較的少数の並行タスクを実行するプールがあるときに Azure ファイル共有の使用を検討します。 [パフォーマンスとスケールの対象](../storage/files/storage-files-scale-targets.md)を確認し、必要なプール サイズとアセット ファイル数を仮定して、(Azure Storage アカウントを利用する) Azure Files を使用する必要があるかを判断します。 

* Azure ファイル共有は[コスト効率が高く](https://azure.microsoft.com/pricing/details/storage/files/)、他のリージョンへのデータ レプリケーションを使って構成できるため、グローバルな冗長性を備えます。 

* オンプレミス コンピューターから Azure ファイル共有を同時にマウントできます。 ただし、特に REST API を使用する場合は、[コンカレンシーの影響](../storage/blobs/concurrency-manage.md)について確実に理解しておきます。

* Azure ファイル共有の一般的な[計画に関する考慮事項](../storage/files/storage-files-planning.md)についても確認してください。


## <a name="create-a-file-share"></a>ファイル共有を作成する

お使いの Batch アカウントに関連付けられたストレージ アカウント、または別個のストレージ アカウントに、[ファイル共有を作成](../storage/files/storage-how-to-create-file-share.md)します。

## <a name="mount-an-azure-file-share-on-a-batch-pool"></a>Batch プールに Azure ファイル共有をマウントする

[Batch プールに仮想ファイル システムをマウントする](virtual-file-mount.md)方法に関するドキュメントを参照してください。

## <a name="next-steps"></a>次のステップ

* Batch のデータの読み書きのその他のオプションについては、「[ジョブとタスク出力を保持する](batch-task-output.md)」を参照してください。
* また、Batch コンテナー ワークロードにファイル システムをデプロイするための[Shipyard レシピ](https://github.com/Azure/batch-shipyard/tree/master/recipes) を含む [Batch Shipyard](https://github.com/Azure/batch-shipyard) ツールキットも参照してください。