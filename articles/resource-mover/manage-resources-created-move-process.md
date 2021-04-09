---
title: Azure Resource Mover で VM 移動プロセス中に作成されたリソースを管理する
description: Azure Resource Mover で VM 移動プロセス中に作成されたリソースを管理する方法について説明します
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: d3c4c4e86e2461ea1d05af284e724a5a2991f040
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101727041"
---
# <a name="manage-resources-created-for-the-vm-move"></a>VM 移動用に作成されたリソースを管理する

この記事では、VM 移動プロセスを支援するために [Azure Resource Mover](overview.md) によって明示的に作成されたリソースを管理する方法について説明します。 

リージョン間で VM を移動した後は、Resource Mover によって作成されたリソースが多数存在するようになり、手動でのクリーンアップが必要となります。

## <a name="delete-resources-created-for-vm-move"></a>VM 移動用に作成されたリソースを削除する

移動コレクションと、VM 移動用に作成された Site Recovery リソースを手動で削除します。

1. リソース グループ ```ResourceMoverRG-<sourceregion>-<target-region>-<metadataRegionShortName>``` でリソースを確認します。
2. VM、および移動コレクション内の他のすべてのソース リソースが移動または削除されていることを確認します。 これにより、それらを使用している保留中のリソースがないことが保証されます。
2. これらのリソースを削除します。

    - 移動コレクションの名前は ```movecollection-<sourceregion>-<target-region>-<metadata-region>``` です。
    - キャッシュ ストレージ アカウントの名前は ```resmovecache<guid>``` です
    - コンテナーの名前は ```ResourceMove-<sourceregion>-<target-region>-GUID``` です。

## <a name="next-steps"></a>次のステップ

Resource Mover を使用して、別のリージョンへの [VM の移動](tutorial-move-region-virtual-machines.md)を試行する。
