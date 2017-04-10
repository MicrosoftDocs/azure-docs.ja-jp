---
title: "Azure の Linux VM 向けストレージ ソリューション | Microsoft Docs"
description: "Azure インフラストラクチャ サービスでのストレージ ソリューションのデプロイに関する主要な設計と実装のガイドラインについて説明します。"
documentationcenter: 
services: virtual-machines-linux
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3c368f07-189b-4520-bbe2-f4080253bbf2
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 6b36c479c987c801d34f90a4300b7221354b9615
ms.lasthandoff: 03/18/2017


---
# <a name="azure-storage-infrastructure-guidelines-for-linux-vms"></a>Linux VM 用の Azure Storage インフラストラクチャのガイドライン

[!INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)]

この記事は、最適な仮想マシン (VM) のパフォーマンスを実現するための、ストレージのニーズと設計に関する考慮事項について説明します。

## <a name="implementation-guidelines-for-storage"></a>ストレージに関する実装ガイドライン
決めること:

* Azure Managed Disks または非管理対象ディスクのどちらを使用するか
* ワークロードのために Standard Storage または Premium Storage のうちのいずれを使用する必要があるか
* 1023 GB を超えるディスクを作成するためにディスクのストライピングが必要か
* ワークロードに最適な I/O パフォーマンスを実現するためにディスクのストライピングが必要か
* IT ワークロードやインフラストラクチャをホストするために必要なストレージ アカウントのセット

タスク:

* デプロイするアプリケーションの I/O 要求を確認し、適切なストレージ アカウントの数と種類を計画します。
* 名前付け規則を使用してストレージ アカウントのセットを作成します。 Azure CLI またはポータルを使用することができます。

## <a name="storage"></a>Storage
Azure Storage は仮想マシン (VM) とアプリケーションをデプロイし、管理するための重要な要素です。 Azure Storage はファイル データ、構造化されていないデータ、メッセージを保存するためのサービスを提供します。VM をサポートするインフラストラクチャの一部でもあります。

[Azure Managed Disks](../storage/storage-managed-disks-overview.md) はバックグラウンドでストレージを管理します。 非管理対象ディスクでは、Azure VM のディスク (VHD ファイル) を保持するストレージ アカウントを作成します。 スケールアップするときは、それぞれのディスクでストレージの IOPS の上限を超えないように、追加のストレージ アカウントを作成する必要があります。 Managed Disks でストレージを管理すれば、ストレージ アカウントの制限 (アカウントあたり 20,000 IOPS など) に縛られることはなくなります。 また、カスタム イメージ (VHD ファイル) を複数のストレージ アカウントにコピーする必要もなくなります。 カスタム イメージを 1 か所 (Azure リージョンごとに 1 つのストレージ アカウント) で管理し、これらのイメージを使用して 1 つのサブスクリプションで数百台の VM を作成できます。 新規デプロイでは Managed Disks を使用することをお勧めします。

VM をサポートするために 2 種類のストレージ アカウントを使用できます。

* Standard Storage アカウントでは、BLOB ストレージ (Azure VM ディスクの保存に利用)、テーブル ストレージ、キュー ストレージ、ファイル ストレージにアクセスできます。
* [Premium Storage](../storage/storage-premium-storage.md) アカウントは、MongoDB シャード クラスターなどの高負荷 I/O ワークロードを対象に、高パフォーマンスで待ち時間の少ないディスク サポートを提供します。 Premium Storage では、現在、Azure VM ディスクのみがサポートされています。

Azure で作成される VM には、オペレーティング システム ディスク、一時ディスク、および 0 個以上のオプションのデータ ディスクが含まれます。 オペレーティング システム ディスクとデータ ディスクは Azure ページ BLOB です。一時ディスクは、コンピューターが存在するノードにローカル保存されます。 メンテナンス イベント中 VM をホスト間に移行する場合があるため、一時ディスクを非永続的データに使用するためだけにアプリケーションを設計する場合は注意が必要です。 一時ディスクに格納されているデータが失われます。

持続性と高可用性が、基盤となる Azure Storage 環境に備わっており、計画外メンテナンスやハードウェアの故障からデータを保護します。 Azure Storage 環境を設計するとき、次のように VM ストレージをレプリケートするように指定できます。

* 特定の Azure データセンター内でローカルにレプリケートする
* 指定したリージョン内の Azure データセンター間でレプリケートする
* さまざまなリージョンの Azure データセンター間でレプリケートする。

高可用性のためのレプリケーション オプションの詳細については、 [こちら](../storage/storage-introduction.md#replication-for-durability-and-high-availability)をご覧ください。

オペレーティング システム ディスクとデータ ディスクの最大サイズは 1023 ギガバイト (GB) です。 BLOB の最大サイズは 1024 GB で、VHD ファイルのメタデータ (フッター) を含める必要があります (1 GB は 1024<sup>3</sup> バイト)。 論理ボリューム マネージャー (LVM) を使用してデータ ディスクをプールし、1023 GB を超える論理ボリュームを VM に割り当てることで、この制限を超えることができます。

Azure Storage のデプロイを設計する場合、スケーラビリティ制限がいくつか適用されます。詳しくは、[Microsoft Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md#storage-limits)に関するページをご覧ください。 また、「[Azure ストレージのスケーラビリティおよびパフォーマンスのターゲット](../storage/storage-scalability-targets.md)」もご覧ください。

アプリケーション ストレージについては、BLOB ストレージを使用して、ドキュメント、イメージ、バックアップ、構成データ、ログなどの非構造化データを 保存できます。 アプリケーションが VM に接続されている仮想ディスクに書き込むのではなく、アプリケーションが Azure BLOB ストレージに直接書き込むことができます。 BLOB ストレージには、可用性ニーズとコスト面の制約に応じて、[ホット ストレージ層とクール ストレージ層](../storage/storage-blob-storage-tiers.md)のオプションも用意されています。

## <a name="striped-disks"></a>ストライピングされたディスク
データ ディスクにストライピングを使用すると、1023 GB より大きいディスクを作成できるだけでなく、多くの場合、複数の BLOB で単一ボリュームのストレージをバックアップできるため、パフォーマンスが向上します。 ストライピングにより、単一の論理ディスクのデータを読み書きするのに必要な I/O が並列化されます。

Azure では、使用できるデータ ディスクの数と帯域幅が、VM のサイズに応じて制限されます。 詳細については、「 [仮想マシンのサイズ](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。

Azure データ ディスクにディスク ストライピングを使用する場合は、次のガイドラインを考慮してください。

* データ ディスクは、常に最大サイズ (1023 GB) にする必要があります。
* VM のサイズで許可されている最大数のデータ ディスクをアタッチします。
* LVM を使用します。
* Azure データ ディスクのキャッシュ オプションを使わないようにします (キャッシュ ポリシー = なし)。

詳細については、[Linux VM での LVM の構成](virtual-machines-linux-configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関するページをご覧ください。

## <a name="multiple-storage-accounts"></a>複数のストレージ アカウント
個別のストレージ アカウントを作成しないため、このセクションは [Azure Managed Disks](../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) には適用されません。 

非管理対象ディスクの Azure Storage 環境を設計するとき、デプロイする VM の増加に伴って複数のストレージ アカウントを使用できます。 このアプローチにより、I/O を基盤となる Azure Storage インフラストラクチャ間に分散させ、VM とアプリケーションの最適なパフォーマンスを維持することができます。 デプロイするアプリケーションを設計する場合は、各 VM の I/O 要件を考慮し、Azure Storage アカウント間に VM を分散してください。 I/O 要求の高いすべての VM を、1 ～ 2 個のストレージ アカウントだけにまとめることは避けてください。

さまざまな Azure Storage オプションの I/O 機能と推奨する最大値の詳細については、「 [Azure Storage のスケーラビリティおよびパフォーマンスのターゲット](../storage/storage-scalability-targets.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
[!INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)]


