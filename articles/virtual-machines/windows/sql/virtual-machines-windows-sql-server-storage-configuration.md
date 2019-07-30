---
title: SQL Server VM のストレージの構成 | Microsoft Docs
description: このトピックでは、プロビジョニング中に SQL Server VM のストレージが Azure によってどのように構成されるかを説明します (Resource Manager デプロイ モデル)。 また、既存の SQL Server VM のストレージを構成する方法についても説明します。
services: virtual-machines-windows
documentationcenter: na
author: ninarn
manager: craigg
tags: azure-resource-manager
ms.assetid: 169fc765-3269-48fa-83f1-9fe3e4e40947
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/05/2017
ms.author: ninarn
ms.openlocfilehash: 360ffb3d2c682d6bd2344cb3ae95447ff3df278d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67076859"
---
# <a name="storage-configuration-for-sql-server-vms"></a>SQL Server VM のストレージの構成

Azure で SQL Server 仮想マシン イメージを構成するとき、ポータルを使用して、ストレージ構成を自動化すると便利です。 これには、ストレージを VM に接続する、そのストレージが SQL Server にアクセスできるようにする、特定のパフォーマンス要件を最適化するためにストレージを構成する、などの作業が含まれます。

このトピックでは、プロビジョニング中に SQL Server VM のストレージが Azure によってどのように構成されるか、また、既存の VM のストレージがどのように構成されるかを説明します。 この構成は、SQL Server が実行されている Azure VM の [パフォーマンスに関するベスト プラクティス](virtual-machines-windows-sql-performance.md) に基づいています。

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>前提条件

自動化されたストレージ構成設定を使用するには、仮想マシンには次の特性が必要です。

* [SQL Server ギャラリー イメージ](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo)でプロビジョニングされている。
* [Resource Manager デプロイ モデル](../../../azure-resource-manager/resource-manager-deployment-model.md)を使用している。
* [premium SSD](../disks-types.md) を使用している。

## <a name="new-vms"></a>新しい VM

次のセクションでは、新しい SQL Server 仮想マシンのストレージを構成する方法について説明します。

### <a name="azure-portal"></a>Azure ポータル

SQL Server ギャラリー イメージを使用して Azure VM をプロビジョニングするとき、新しい VM のストレージを自動的に構成することを選択できます。 自動構成では、ストレージ サイズ、パフォーマンスの制限、およびワークロードの種類を指定します。 次のスクリーンショットは、SQL VM のプロビジョニング中に使用する [ストレージ構成] ブレードです。

![プロビジョニング中の SQL Server VM ストレージの構成](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

VM が作成されたら、ここで選択した内容に基づいて、次のストレージ構成タスクが実行されます。

* Premium SSD を作成し、仮想マシンに接続する。
* SQL Server にアクセスできるようにデータ ディスクを構成する。
* 指定したサイズとパフォーマンス (IOPS とスループット) 要件に基づいて、データ ディスクを記憶域プールにまとめる。
* 仮想マシンで記憶域プールを新しいドライブに関連付ける。
* 指定したワークロードの種類 (データ ウェアハウス、トランザクション処理、または一般) に基づいて、新しいドライブを最適化する。

Azure によるストレージ設定の構成方法の詳細については、「 [ストレージの構成](#storage-configuration)」を参照してください。 Azure portal で SQL Server VM を作成する詳しい手順については、[プロビジョニング チュートリアル](virtual-machines-windows-portal-sql-server-provision.md)をご覧ください。

### <a name="resource-manage-templates"></a>Resource Manager テンプレート

次の Resource Manager テンプレートを使用する場合、既定では、2 つ Premium データ ディスクが記憶域プール構成なしで接続されます。 ただし、仮想マシンに接続される Premium データ ディスクの数は、次のテンプレートをカスタマイズすることで変更できます。

* [自動バックアップを備えた VM の作成](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
* [自動修正を備えた VM の作成](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
* [AKV 統合を備えた VM の作成](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

## <a name="existing-vms"></a>既存の VM

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

既存の SQL Server VM については、Azure ポータルで一部のストレージ設定を変更できます。 [SQL 仮想マシン リソース](virtual-machines-windows-sql-manage-portal.md#access-sql-virtual-machine-resource)を開き、 **[概要]** を選択します。 SQL Server の概要ページに、VM の現在のストレージ使用量が表示されます。 このグラフには、VM 上のすべてのドライブが示されており、 ドライブごとに、次の 4 つのセクションの記憶域スペースが表示されます。

* SQL データ
* SQL ログ
* その他 (非 SQL ストレージ)
* 使用可能

ストレージ設定を変更するには、 **[設定]** の下で **[構成]** を選択します。 

![既存の SQL Server VM 用のストレージを構成する](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-existing.png)

表示される構成オプションは、この機能を前に使用したことがあるかどうかによって異なります。 初めて使用する場合は、新しいドライブのストレージ要件を指定できます。 この機能をドライブを作成したことがある場合は、そのドライブのストレージを拡張することを選択できます。

### <a name="use-for-the-first-time"></a>初めて使用する

この機能を初めて使用する場合は、新しいドライブのストレージ サイズとパフォーマンス制限を指定できます。 これは、プロビジョニングするときの操作に似ています。 主な違いは、ワークロードの種類を指定できない点です。 この制限により、仮想マシン上の既存の SQL Server 構成が利用できなくなるのを防ぎます。

Azure では、仕様に基いて新しいドライブが作成されます。 このシナリオでは、次のストレージ構成タスクが実行されます。

* Premium Storage データ ディスクを作成し、仮想マシンに接続する。
* SQL Server にアクセスできるようにデータ ディスクを構成する。
* 指定したサイズとパフォーマンス (IOPS とスループット) 要件に基づいて、データ ディスクを記憶域プールにまとめる。
* 仮想マシンで記憶域プールを新しいドライブに関連付ける。

Azure によるストレージ設定の構成方法の詳細については、「 [ストレージの構成](#storage-configuration)」を参照してください。

### <a name="add-a-new-drive"></a>新しいドライブの追加

SQL Server VM に既にストレージを構成してある場合は、そのストレージを拡張すると、新しいオプションが 2 つ表示されます。 1 つ目は新しいドライブを追加するオプションで、VM のパフォーマンス レベルを向上させることができます。

ただし、パフォーマンス向上を実現するには、ドライブを追加した後、手動で構成しなければならない部分がいくつか出てきます。

### <a name="extend-the-drive"></a>ドライブの拡張

もう 1 つはストレージ拡張オプションで、既存のドライブを拡張します。 このオプションでは、ドライブの利用可能なストレージは増えますが、パフォーマンスは向上しません。 記憶域プールを使用している場合、記憶域プールの作成後に、列数を変更することはできません。 データ ディスクにわたってストライピングできる、並列書き込み数は、この列数によって決まります。 したがって、データ ディスクを追加しても、パフォーマンスを高めることはできません。 データが書き込まれるストレージが増えるだけです。 また、この制限は、ドライブを拡張するとき、列数によって、追加できるデータ ディスクの最小数が決まることを意味します。 データ ディスクが 4 つ含まれる記憶域プールを作成した場合は、列数も 4 になります。 ストレージを拡張するときは毎回、少なくとも 4 つのデータ ディスクを追加する必要があります。

![SQL VM のドライブを拡張する](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-extend-a-drive.png)

## <a name="storage-configuration"></a>ストレージの構成

このセクションでは、Azure portal での SQL VM のプロビジョニングまたは構成時に自動的に行われる、ストレージ構成の変更に関するリファレンス情報を提供します。

* VM に対して 2 TB 未満のストレージを選択した場合、記憶域プールは作成されません。
* VM に対して 2 TB 以上のストレージを選択した場合、記憶域プールが構成されます。 このトピックの次のセクションでは、記憶域プールの構成について詳しく説明します。
* 自動ストレージ構成では、必ず [Premium SSD](../disks-types.md) P30 データ ディスクが使用されます。 結果として、選択したテラバイト数と、VM に接続されているデータ ディスク数は 1 対 1 で対応しています。

料金情報については、 [Storage 料金](https://azure.microsoft.com/pricing/details/storage) ページの「 **Disk Storage** 」タブを参照してください。

### <a name="creation-of-the-storage-pool"></a>記憶域プールの作成

Azure では、次の設定を使用して、SQL Server VM で記憶域プールを作成します。

| Setting | 値 |
| --- | --- |
| ストライプ サイズ |256 KB (データ ウェアハウス)、64 KB (トランザクション) |
| ディスク サイズ |各 1 TB |
| キャッシュ |読み取り |
| アロケーション サイズ |64 KB NTFS アロケーション ユニット サイズ |
| ファイルの瞬時初期化 |Enabled |
| メモリ内のページのロック |Enabled |
| 復旧 |シンプルな復元 (回復性なし) |
| 列数の合計 |データ ディスクの数<sup>1</sup> |
| TempDB の場所 |データ ディスクに格納<sup>2</sup> |

<sup>1</sup> 記憶域プールの作成後、その記憶域プールの列数を変更することはできません。

<sup>2</sup> この設定は、ストレージ構成機能を使用して作成した最初のドライブにのみ適用されます。

## <a name="workload-optimization-settings"></a>ワークロード最適化の設定

次の表では、使用可能な 3 つのワークロードの種類のオプションと、対応する最適化について説明します。

| ワークロードの種類 | 説明 | 最適化 |
| --- | --- | --- |
| **全般** |ほとんどのワークロードをサポートする既定の設定 |なし |
| **トランザクション処理** |従来のデータベース OLTP ワークロード用にストレージを最適化します |トレース フラグ 1117<br/>トレース フラグ 1118 |
| **データ ウェアハウス** |分析とレポートのワークロード用にストレージを最適化します |トレース フラグ 610<br/>トレース フラグ 1117 |

> [!NOTE]
> ワークロードの種類を指定するには、SQL 仮想マシンをプロビジョニングしているときに、ストレージの構成手順でそのワークロードの種類を選択します。

## <a name="next-steps"></a>次の手順

Azure VM での SQL Server の実行に関するその他のトピックについては、「 [Azure Virtual Machines における SQL Server](virtual-machines-windows-sql-server-iaas-overview.md)」を参照してください。
