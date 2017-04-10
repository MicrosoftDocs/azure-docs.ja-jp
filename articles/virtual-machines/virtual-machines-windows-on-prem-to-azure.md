---
title: "AWS やその他のプラットフォームから Azure の Managed Disks に移行する | Microsoft Docs"
description: "AWS などの他のクラウドやその他の仮想化プラットフォームからアップロードされた VHD を使用して Azure で VM を作成し、Azure Managed Disks を活用します。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: cynthn
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 32e30b44c2f7cfa9c1069190fdc53dbe6e9f4cd5
ms.openlocfilehash: bc2a8803bdc875e9f62d9263b16cdbb5cc70ecbf
ms.lasthandoff: 03/01/2017


---

# <a name="migrate-from-amazon-web-services-aws-and-other-platforms-to-managed-disks-in-azure"></a>アマゾン ウェブ サービス (AWS) やその他のプラットフォームから Azure の Managed Disks に移行する

AWS やオンプレミスの仮想化ソリューションから Azure に VHD ファイルをアップロードして、Managed Disks を利用する VM を作成できます。 Azure Managed Disks では、Azure IaaS VM のストレージ アカウントを管理する必要がなくなります。 指定する必要があるのは必要なディスクの種類 (Premium または Standard) とサイズだけで、ディスクは Azureによって作成および管理されます。 

一般化された VHD と特殊化された VHD のいずれもアップロードできます。 
**一般化された VHD ** - 一般化した VHD では、Sysprep を使用して個人アカウント情報がすべて削除されています。 
**特殊化された VHD ** - 特殊化された VHD では、ユーザーアカウント、アプリケーション、その他のステート データが元の VM から保持されます。 

> [!IMPORTANT]
> VHD を Azure にアップロードする前に、「[Windows VHD の Azure へのアップロードの準備](virtual-machines-windows-prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」に従う必要があります
>
>


| シナリオ                                                                                                                         | ドキュメント                                                                                                                       |
|----------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| Azure Managed Disks に移行する既存の AWS EC2 インスタンスがある                                     | [アマゾン ウェブ サービス (AWS) から Azure Managed Disks に移行する](virtual-machines-windows-aws-to-azure.md)                           |
| 複数の Azure VM を作成するためのイメージとして使用する他の仮想化プラットフォームの VM がある。 | [汎用化された VHD の Azure へのアップロードと、Managed Disks を使用した新しい VM の作成](virtual-machines-windows-upload-generalized-managed.md)に関する記事 |
| Azure で再作成する一意にカスタマイズされた VM がある。                                                      | [特殊化された VHD の Azure へのアップロードと、Managed Disks を使用した新しい VM の作成](virtual-machines-windows-upload-specialized.md)に関する記事         |


## <a name="overview-of-managed-disks"></a>Managed Disks の概要

Azure Managed Disks は、ストレージ アカウントを管理する必要をなくして VM 管理をシンプルにします。 Managed Disks では、可用性セット内の VM の信頼性の向上というメリットもあります。 単一障害点を避けるために、可用性セット内の異なる VM のディスクは相互に十分に分離されます。 可用性セット内の異なる VM のディスクは異なるストレージ スケール ユニット (スタンプ) に自動的に配置されるため、ハードウェアとソフトウェアの障害を原因とする単一のストレージ スケール ユニット障害の影響が限定されます。 ニーズに基づいて、2 種類のストレージ オプションから選ぶことができます。 
 
- [Premium Managed Disks](../storage/storage-premium-storage.md) は、ソリッド ステート ドライブ (SSD) ベースのストレージ メディアで、I/O を集中的に行うワークロードを実行している仮想マシンに、高パフォーマンスで待ち時間の短いディスク サポートを提供します。 Premium Managed Disks に移行すると、これらのディスクの速度とパフォーマンスを最大限に高めることができます。  

- [Standard Managed Disks](../storage/storage-standard-storage.md) はハード ディスク ドライブ (HDD) ベースのストレージ メディアを使用し、パフォーマンス変動の影響を受けにくい開発テストやアクセス頻度の少ないワークロードに最適です。  

## <a name="plan-for-the-migration-to-managed-disks"></a>Managed Disks への移行の計画

このセクションでは、VM とディスクの種類に関する最適な決定を行います。


### <a name="location"></a>Location (場所)

Azure Managed Disks を使用できる場所を選びます。 Premium Managed Disks に移行する場合は、移行を計画しているリージョンで Premium Storage が使用可能であることも確認します。 使用できる場所に関する最新情報については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/regions/#services)」をご覧ください。

### <a name="vm-sizes"></a>VM サイズ

Premium Managed Disks に移行する場合は、VM が配置されているリージョンで利用できる Premium Storage 対応サイズに合うように VM のサイズを更新する必要があります。 Premium Storage で対応できる VM サイズをご確認ください。 Azure VM のサイズの仕様は、「 [仮想マシンのサイズ](virtual-machines-windows-sizes.md)」に記載されています。
Premium Storage で動作する仮想マシンのパフォーマンス特性を確認し、ワークロードに最適な VM を選択してください。 ディスク トラフィックが流れるのに十分な帯域幅が VM で利用できることを確認します。

### <a name="disk-sizes"></a>ディスク サイズ

**Premium Managed Disks**

VM で使える Premium 管理ディスクには&3; 種類あり、それぞれに特定の IOPS とスループットの制限があります。 VM の Premium ディスクの種類を選ぶ場合は、容量、パフォーマンス、スケーラビリティ、最大負荷に関するアプリケーションのニーズに基づいて、これらの制限を考慮してください。

| Premium ディスクの種類  | P10               | P20               | P30               |
|---------------------|-------------------|-------------------|-------------------|
| ディスク サイズ           | 128 GB            | 512 GB            | 1024 GB (1 TB)    |
| ディスクあたりの IOPS       | 500               | 2300              | 5000              |
| ディスクあたりのスループット | 100 MB/秒 | 150 MB/秒 | 200 MB/秒 |

**Standard Managed Disks**

VM で使用できる Standard Managed Disks は&5; 種類あります。 それぞれ容量は異なりますが、IOPS とスループットの制限は同じです。 アプリケーションの容量のニーズに基づいて Standard Managed Disks の種類を選択してください。

| Standard ディスクの種類  | S4               | S6               | S10              | S20              | S30              |
|---------------------|------------------|------------------|------------------|------------------|------------------|
| ディスク サイズ           | 30 GB            | 64 GB            | 128 GB           | 512 GB           | 1024 GB (1 TB)   |
| ディスクあたりの IOPS       | 500              | 500              | 500              | 500              | 500              |
| ディスクあたりのスループット | 60 MB/秒 | 60 MB/秒 | 60 MB/秒 | 60 MB/秒 | 60 MB/秒 |

### <a name="disk-caching-policy"></a>ディスク キャッシュ ポリシー 

**Premium Managed Disks**

既定では、ディスクのキャッシュ ポリシーは、すべてのPremium データ ディスクに対して「*読み取り専用*」、VM にアタッチされた Premium オペレーティング システム ディスクに対して「*読み取り/書き込み*」です。 アプリケーションの IO パフォーマンスを最適化するには、この構成をお勧めします。 書き込み量の多いディスクや書き込み専用のディスク (SQL Server ログ ファイルなど) の場合は、ディスク キャッシュを無効にすることで、アプリケーションのパフォーマンスを向上できる場合があります。

### <a name="pricing"></a>価格

[Managed Disks の価格](https://azure.microsoft.com/en-us/pricing/details/managed-disks/)をご確認ください。 Premium Managed Disks の価格は、Premium 非管理対象ディスクと同じです。 一方、Standard Managed Disks の価格は、Standard Unmanaged Disks と異なります。


## <a name="next-steps"></a>次のステップ

- VHD を Azure にアップロードする前に、「[Windows VHD の Azure へのアップロードの準備](virtual-machines-windows-prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」に従う必要があります

