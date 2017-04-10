---
title: "Azure Storage のスケーラビリティおよびパフォーマンスのターゲット | Microsoft Docs"
description: "Standard Storage アカウントと Premium Storage アカウントの両方の容量、要求レート、および送受信の帯域幅を含む、Azure Storage のスケーラビリティとパフォーマンスのターゲットについて説明します。 各 Azure Storage サービス内にあるパーティションのパフォーマンス ターゲットを理解します。"
services: storage
documentationcenter: na
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: be721bd3-159f-40a1-88c1-96418537fe75
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 03/07/2017
ms.author: robinsh
translationtype: Human Translation
ms.sourcegitcommit: 2ab1a58aab138cc81b25731a9454b083d54ded81
ms.openlocfilehash: eff408bc70288ada1039df7432f43b02420f2c37
ms.lasthandoff: 02/15/2017


---
# <a name="azure-storage-scalability-and-performance-targets"></a>Azure Storage のスケーラビリティおよびパフォーマンスのターゲット
## <a name="overview"></a>概要
このトピックでは、Microsoft Azure Storage のスケーラビリティとパフォーマンスについて説明します。 その他の Azure の制約に関する概要は、「 [Azure サブスクリプションとサービスの制限、クォータ、および制約](../azure-subscription-service-limits.md)」を参照してください。

> [!NOTE]
> すべてのストレージ アカウントは新しいフラット ネットワーク トポロジで実行され、ストレージ アカウントがいつ作成されたかにかかわらず、以下に示すスケーラビリティおよびパフォーマンスのターゲットがサポートされます。 のフラット ネットワーク アーキテクチャおよびスケーラビリティの詳細については、 [Microsoft Azure Storage の高い整合性を持つ高可用クラウド ストレージ サービス](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)に関するページを参照してください。
> 
> [!IMPORTANT]
> ここで示すスケーラビリティおよびパフォーマンスのターゲットは、ハイエンドのターゲットですが、達成可能です。 いかなる場合でも、ストレージ アカウントで達成される要求レートおよび帯域幅は、格納されたオブジェクトのサイズ、使用されているアクセス パターン、およびアプリケーションで実行されているワークロードの種類によって異なります。 必ずサービスをテストして、パフォーマンスがユーザー要件を満たしているかどうかを確認してください。 可能であれば、トラフィック量の急増を回避し、トラフィックがパーティション間でうまく分散されるようにしてください。
> 
> ワークロードがアプリケーションのパーティションで処理できる上限に達すると、Azure Storage はエラー コード 503 (サーバーがビジー状態) またはエラー コード 500 (操作タイムアウト) の応答を返しはじめます。 このような状況になった場合、アプリケーションで指数関数的バックオフによる再試行ポリシーを使用する必要があります。 指数関数的バックオフによって、そのパーティションへの負荷が減少し、そのパーティションへのトラフィック量の増加が緩和されます。
> 
> 

アプリケーションで必要とされるスケーラビリティが、単一ストレージ アカウントあたりのスケーラビリティ ターゲットを超えている場合、複数のストレージ アカウントを使用し、それらのストレージ アカウント間でデータが分割されるようにアプリケーションを構築できます。 ボリューム価格については、「 [Azure Storage 料金](https://azure.microsoft.com/pricing/details/storage/) 」をご覧ください。

## <a name="scalability-targets-for-blobs-queues-tables-and-files"></a>Blob、キュー、テーブル、およびファイルのスケーラビリティ ターゲット
[!INCLUDE [azure-storage-limits](../../includes/azure-storage-limits.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
## <a name="scalability-targets-for-virtual-machine-disks"></a>仮想マシンのディスクのスケーラビリティ ターゲット
[!INCLUDE [azure-storage-limits-vm-disks](../../includes/azure-storage-limits-vm-disks.md)]

詳細については、[Windows VM のサイズ](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)と [Linux VM のサイズ](../virtual-machines/virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関するページをご覧ください。

## <a name="managed-virtual-machine-disks"></a>管理対象の仮想マシン ディスク

[!INCLUDE [azure-storage-limits-vm-disks-managed](../../includes/azure-storage-limits-vm-disks-managed.md)]

## <a name="unmanaged-virtual-machine-disks"></a>非管理対象の仮想マシン ディスク
[!INCLUDE [azure-storage-limits-vm-disks-standard](../../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../../includes/azure-storage-limits-vm-disks-premium.md)]

## <a name="scalability-targets-for-azure-resource-manager"></a>Azure リソース マネージャーのスケーラビリティ ターゲット
[!INCLUDE [azure-storage-limits-azure-resource-manager](../../includes/azure-storage-limits-azure-resource-manager.md)]

## <a name="partitions-in-azure-storage"></a>Azure Storage 内のパーティション
Azure Storage に格納されているデータを含むすべてのオブジェクト (BLOB、メッセージ、エンティティ、およびファイル) はパーティションに属し、パーティション キーによって識別されます。 Azure Storage では、BLOB、メッセージ、エンティティ、およびファイルのトラフィック ニーズに合わせて、どのようにこれらのオブジェクトの負荷をサーバー間で分散するかがパーティションによって決まります。 パーティション キーは一意であり、BLOB、メッセージ、またはエンティティを見つけるために使用されます。

上の「 [標準的なストレージ アカウントのスケーラビリティ ターゲット](#standard-storage-accounts) 」の表は、サービスごとの&1; つのパーティションにおけるパフォーマンス ターゲットの一覧です。

パーティションは、各ストレージ サービスの負荷分散およびスケーラビリティに以下のような影響を与えます。

* **BLOB**: BLOB のパーティション キーは、アカウント名とコンテナー名と BLOB 名を組み合わせたものです。 つまり、BLOB への負荷からの要求に応じて、各 BLOB は独自のパーティションを持つことができます。 BLOB は、アクセスをスケールアウトするために多数のサーバーに分散させることができますが、1 つの BLOB を処理できるのは&1; 台のサーバーのみです。 BLOB は BLOB コンテナーに論理的にグループ化できますが、このグループ化によってパーティション分割は影響を受けません。
* **ファイル**: ファイルのパーティション キーは、アカウント名とファイル共有を組み合わせた名前です。 これは、ファイル共有内のすべてのファイルも、1 つのパーティションに存在することを意味します。
* **メッセージ**: メッセージのパーティション キーはアカウント名とキュー名の組み合わせであるため、キュー内のすべてのメッセージは&1; つのパーティションにグループ化され、1 台のサーバーで処理されます。 ストレージ アカウント内のキューがどんなに多くとも、負荷が分散されるように、異なるキューが別のサーバーによって処理されることがあります。
* **エンティティ**: エンティティのパーティション キーは、アカウント名とテーブル名とパーティション キーを組み合わせたものです。ここでパーティション キーは、エンティティに必要なユーザー定義された **PartitionKey** プロパティの値です。 同じパーティション キー値を持つすべてのエンティティは、同じパーティションにグループ化され、同じパーティション サーバーで処理されます。 これは、アプリケーションを設計する際に理解しておくべき重要なポイントです。 アプリケーションでは、エンティティを複数のパーティションに分散することで得られるスケーラビリティ上のメリットと、エンティティを&1; つのパーティションにグループ化することで得られるデータ アクセスのメリットのバランスをとってください。  

テーブル内のエンティティのセットを単一のパーティションにグループ化する主な利点は、パーティションが&1; 台のサーバー上にあるため、同じパーティション内のエンティティすべてにアトミック バッチ操作を実行できるということです。 そのため、エンティティのグループに対してバッチ操作を実行する場合は、同じパーティション キーでエンティティをグループ化することを検討します。 

一方、同じテーブル内にあっても、パーティション キーが異なるエンティティは、異なるサーバー間で負荷分散できるため、スケーラビリティもさらに大きくなります。

テーブルのパーティション分割方法の設計に関する詳しい推奨事項については、 [こちら](https://msdn.microsoft.com/library/azure/hh508997.aspx)をご覧ください。

## <a name="see-also"></a>関連項目
* [Storage の料金詳細](https://azure.microsoft.com/pricing/details/storage/)
* [Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md)
* [Premium Storage: Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ](storage-premium-storage.md)
* [Azure Storage のレプリケーション](storage-redundancy.md)
* [Microsoft Azure Storage のパフォーマンスとスケーラビリティに対するチェック リスト](storage-performance-checklist.md)
* [Microsoft Azure Storage: 強力な整合性を備えた高可用クラウド ストレージ サービス](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)


