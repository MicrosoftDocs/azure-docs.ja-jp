---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: yuemlu
ms.service: storage
ms.topic: include
ms.date: 01/08/2019
ms.author: yuemlu
ms.custom: include file
ms.openlocfilehash: ad57d373422e0fc310e51ac31f2a2e76999abf22
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/10/2019
ms.locfileid: "54193416"
---
# <a name="cost-effective-standard-storage-and-unmanaged-and-managed-azure-vm-disks"></a>コスト効率に優れた Standard Storage および Azure VM の非管理対象ディスクと管理ディスク

Azure Standard Storage は、待機時間の影響を受けないワークロードを実行する VM 向けの信頼性の高い低コストのディスク サポートを提供します。 また、BLOB、テーブル、キュー、ファイルもサポートしています。 Standard Storage では、データはハード ディスク ドライブ (HDD) に格納されます。 VM を使用するときに、Dev/Test シナリオや重要度の低いワークロードには Standard SSD および HDD ディスクを使用し、ミッション クリティカルな運用アプリケーションには Premium SSD ディスクを使用できます。 Standard Storage はすべての Azure リージョンで利用できます。 

この記事では、Standard SSD および HDD VM ディスクの使用について説明します。 BLOB、テーブル、キュー、ファイルでのストレージの使用の詳細については、[Storage の概要](../articles/storage/common/storage-introduction.md)に関する記事を参照してください。

## <a name="disk-types"></a>ディスクの種類

Azure VM の Standard ディスクは、次の 2 とおりの方法で作成できます。

**アンマネージド ディスク**: この種類のディスクは、VM ディスクに対応する VHD ファイルの格納に使用するストレージ アカウントをユーザーが管理する本来の方法です。 VHD ファイルは、ストレージ アカウントにページ BLOB として格納されます。 非管理対象ディスクは、主に Premium Storage を使用する VM (DSv2 シリーズや GS シリーズなど) も含め、すべてのサイズの Azure VM に接続できます。 Azure VM には複数の Standard ディスクをアタッチできるので、VM あたり最大 256 TiB のストレージを使用できます。 プレビュー ディスク サイズを使用する場合、VM あたり最大約 2 PiB のストレージを使用できます。

[**Azure Managed Disks**](../articles/virtual-machines/windows/managed-disks-overview.md): この機能は、VM ディスクに使用するストレージ アカウントを管理します。 必要なディスクの種類 (Premium SSD、Standard SSD、または Standard HDD) とサイズを指定すれば、ディスクの作成と管理は Azure によって行われます。 ストレージ アカウントのスケーラビリティの制限を超えないように、複数のストレージ アカウントにディスクを配置することを気に掛ける必要はありません。Azure がこれを管理します。

どちらの種類のディスクも利用できる場合でも、Managed Disks を使用してさまざまな機能を活用することをお勧めします。

Azure Standard Storage を使用するには、[無料試用版](https://azure.microsoft.com/pricing/free-trial/)のページをご覧ください。 

Managed Disks を使用する VM を作成する方法については、次のいずれかの記事を参照してください。

* [Resource Manager と PowerShell を使用して VM を作成する](../articles/virtual-machines/windows/quick-create-powershell.md)
* [Azure CLI を使用して新しく Linux VM を作成する](../articles/virtual-machines/linux/quick-create-cli.md)

## <a name="standard-storage-features"></a>Standard Storage の機能

ここでは、Standard Storage の機能をいくつか紹介します。 詳細については、[Azure Storage の概要](../articles/storage/common/storage-introduction.md)に関する記事をご覧ください。

**Standard Storage**: Azure Standard Storage では、Azure Disks、Azure BLOB、Azure Files、Azure Tables、Azure Queues をサポートしています。 Standard Storage サービスを使用するには、まず、[Azure ストレージ アカウントを作成](../articles/storage/common/storage-quickstart-create-account.md)します。

**Standard SSD ディスク:** Standard SSD ディスクは、Standard HDD ディスクよりも信頼性の高いパフォーマンスを実現しており、現在リリースされています。 Standard SSD ディスクを利用できるリージョンの詳細については、[Standard SSD ディスクを使用できるリージョン](../articles/virtual-machines/windows/faq-for-disks.md#standard-ssds-azure-regions)に関する記事を参照してください。

**Standard HDD ディスク**: Standard HDD ディスクは、Premium Storage で使用されるサイズ シリーズの VM (DSv2 シリーズや GS シリーズなど) も含め、すべての Azure VM に接続できます。 Standard HDD ディスクは、1 台の VM にのみ接続できます。 ただし、1 台の VM に Standard Storage ディスクを 1 つ以上接続できます。その VM サイズで定義されている最大ディスク数まで接続することが可能です。 Standard Storage のスケーラビリティとパフォーマンスのターゲットに関する次のセクションで、仕様についてさらに詳しく説明します。

**Standard ページ BLOB**: Standard ページ BLOB は VM の永続ディスクを保持するために使用されます。他の種類の Azure BLOB と同様に、REST から Standard ページ BLOB に直接アクセスすることもできます。 [ページ BLOB](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs) は、ランダム読み取り/書き込み操作に最適化された 512 バイト ページのコレクションです。 

**ストレージ レプリケーション**: ほとんどのリージョンで、Standard Storage アカウント内のデータはローカルにレプリケートすることも、複数のデータセンター間で geo レプリケートすることもできます。 ローカル冗長ストレージ (LRS)、ゾーン冗長ストレージ (ZRS)、geo 冗長ストレージ (GRS)、読み取りアクセス geo 冗長ストレージ (RA-GRS) の 4 種類のレプリケーションを使用できます。 現在、Standard Storage の Managed Disks では、ローカル冗長ストレージ (LRS) のみをサポートしています。 詳細については、[Storage のレプリケーション](../articles/storage/common/storage-redundancy.md)に関する記事をご覧ください。

## <a name="scalability-and-performance-targets"></a>スケーラビリティとパフォーマンスのターゲット

このセクションでは、Standard Storage を使用する際に考慮する必要のあるスケーラビリティとパフォーマンスのターゲットについて説明します。

### <a name="account-limits--does-not-apply-to-managed-disks"></a>アカウントの制限 - マネージド ディスクは適用外

| **リソース** | **既定の制限** |
|--------------|-------------------|
| ストレージ アカウントあたりの容量 (TB)  | 500 TB |
| ストレージ アカウントあたりの最大受信速度<sup>1</sup>  (米国リージョン) | GRS/ZRS が有効な場合は 10 Gbps、LRS の場合は 20 Gbps |
| ストレージ アカウントあたりの最大送信速度<sup>1</sup>  (米国リージョン) | RA-GRS/GRS/ZRS が有効な場合は 20 Gbps、LRS の場合は 30 Gbps |
| ストレージ アカウントあたりの最大受信速度<sup>1</sup>  (ヨーロッパおよびアジア リージョン) | GRS/ZRS が有効な場合は 5 Gbps、LRS の場合は 10 Gbps |
| ストレージ アカウントあたりの最大送信速度<sup>1</sup>  (ヨーロッパおよびアジア リージョン) | RA-GRS/GRS/ZRS が有効な場合は 10 Gbps、LRS の場合は 15 Gbps |
| ストレージ アカウントあたりの合計要求レート (オブジェクト サイズが 1 KB の場合) | 最大 20,000 の IOPS、エンティティ/秒、またはメッセージ/秒 |

<sup>1</sup>受信とは、ストレージ アカウントに送信されるすべてのデータ (要求) のことです。 送信とは、ストレージ アカウントから受信するすべてのデータ (応答) のことです。

詳細については、「[Azure Storage のスケーラビリティおよびパフォーマンスのターゲット](../articles/storage/common/storage-scalability-targets.md)」をご覧ください。

アプリケーションのニーズが 1 つのストレージ アカウントのスケーラビリティ ターゲットを超えた場合は、複数のストレージ アカウントを使用するようにアプリケーションをビルドし、それらのストレージ アカウント間でデータをパーティション分割します。 また、Azure Managed Disks を使用することもできます。Azure Managed Disks では、データのパーティション分割と配置の管理は Azure が行います。

### <a name="standard-disks-limits"></a>Standard ディスクの制限

Premium ディスクとは異なり、Standard ディスクの IOPS (1 秒あたりの入出力操作) とスループット (帯域幅) はプロビジョニングされません。 Standard ディスクのパフォーマンスは、ディスクの接続先の VM サイズとディスクのサイズによって異なります。

ワークロードで高パフォーマンス、低待機時間のディスク サポートが必要な場合は、Premium Storage を使用することを検討してください。 Premium Storage の利点の詳細については、[高パフォーマンスの Premium Storage と Azure VM ディスク](../articles/virtual-machines/windows/premium-storage.md)に関する記事をご覧ください。

## <a name="snapshots-and-copy-blob"></a>スナップショットと Copy BLOB

Storage サービスでは、VHD ファイルはページ BLOB です。 ページ BLOB のスナップショットを作成し、別の場所 (別のストレージ アカウントなど) にコピーできます。

### <a name="unmanaged-disks"></a>非管理対象ディスク

Standard Storage でスナップショットを使用する場合と同様に、非管理対象 Standard ディスクの[増分スナップショット](../articles/virtual-machines/windows/incremental-snapshots.md)を作成できます。 ソース ディスクがローカル冗長ストレージ アカウント内にある場合は、スナップショットを作成し、それらのスナップショットを geo 冗長 Standard Storage アカウントにコピーすることをお勧めします。 詳細については、「 [Azure Storage 冗長オプション](../articles/storage/common/storage-redundancy.md)」をご覧ください。

ディスクが VM に接続されている場合、ディスクで特定の API 操作を実行できなくなります。 たとえば、ディスクが VM にアタッチされている間は、その BLOB に対して [Copy Blob](/rest/api/storageservices/Copy-Blob) 操作を実行できません。 代わりに、[Snapshot Blob](/rest/api/storageservices/Snapshot-Blob) REST API メソッドを使って BLOB のスナップショットを作成してから、スナップショットの [Copy Blob](/rest/api/storageservices/Copy-Blob) を実行してアタッチされたディスクをコピーします。 または、ディスクの接続を解除してから必要な操作を実行します。

スナップショットの geo 冗長コピーを維持するには、AzCopy または Copy BLOB を使用して、ローカル冗長ストレージ アカウントから geo 冗長 Standard Storage アカウントにスナップショットをコピーします。 詳しくは、「[AzCopy コマンド ライン ユーティリティを使用してデータを転送する](../articles/storage/common/storage-use-azcopy.md)」および「[BLOB のコピー](/rest/api/storageservices/Copy-Blob)」をご覧ください。

Standard Storage アカウントのページ BLOB に対して REST 操作を実行する方法の詳細については、[Azure Storage Services REST API](/rest/api/storageservices/Azure-Storage-Services-REST-API-Reference) に関する記事をご覧ください。

### <a name="managed-disks"></a>マネージド ディスク

マネージド ディスクのスナップショットはマネージド ディスクの読み取り専用コピーであり、Standard マネージド ディスクとして保存されます。 Managed Disks では増分スナップショットは現在サポートされていませんが、今後サポートされる予定です。

マネージド ディスクが VM に接続されている場合、ディスクで特定の API 操作を実行できなくなります。 たとえば、ディスクが VM に接続されているときには、Shared Access Signature (SAS) を生成してコピー操作を実行することはできません。 代わりに、ディスクのスナップショットを作成してから、スナップショットのコピーを実行します。 または、ディスクの接続を解除してから、Shared Access Signature (SAS) を生成してコピー操作を実行します。

## <a name="pricing-and-billing"></a>価格と課金

Standard Storage を使用するときには、課金に関する次の考慮事項が適用されます。

* Standard Storage の非管理対象ディスク/データ サイズ
* Standard マネージド ディスク
* Standard Storage のスナップショット
* 送信データ転送
* トランザクション

**アンマネージド ストレージのデータとディスク サイズ**: アンマネージド ディスクとその他のデータ (BLOB、テーブル、キュー、ファイル) については、使用している領域分に対してのみ課金されます。 たとえば、ページ BLOB が 127 GB としてプロビジョニングされている VM があり、その VM で実際には 10 GB の領域しか使用していない場合、10 GB の領域分に対して課金されます。 最大 8,191 GB の Standard Storage および最大 4,095 GB の Standard 非管理対象ディスクがサポートされます。 

**マネージド ディスク**: Standard マネージド ディスクへの課金は、ディスクのプロビジョニング済みサイズによって異なります。 Azure では、プロビジョニング済みサイズ (切り上げたもの) を、次の表に示す マネージド ディスク オプションの中で最も近いオプションにマップします。 各マネージド ディスクは、サポートされているプロビジョニング済みサイズのいずれかにマップされ、それに応じて課金されます。 たとえば、Standard マネージド ディスクを作成し、200 GiB のプロビジョニング済みサイズを指定した場合、ディスクの種類 S15 の価格に従って課金されます。

アスタリスクで示されるサイズはプレビュー中です。

| **Standard HDD 管理<br>ディスクの種類** | **S4** | **S6** | **S10** | **S15** | **S20** | **S30** | **S40** | **S50** | **S60*** | **S70*** | **S80*** |
|------------------|---------|---------|--------|--------|--------|----------------|----------------|----------------|----------------|----------------|----------------|
| ディスク サイズ        | 32 GiB  | 64 GiB  | 128 GiB | 256 GiB | 512 GiB | 1,024 GiB (1 TiB) | 2,048 GiB (2 TiB) | 4,095 GiB (4 TiB) | 8,192 GiB (8 TiB) | 16,385 GiB (16 TiB) | 32,767 GiB (32 TiB) |


**スナップショット**: Standard ディスクのスナップショットについては、スナップショットで使用された追加の容量に対して課金されます。 スナップショットの詳細については、「 [BLOB のスナップショットの作成](/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob)」をご覧ください。

**送信データ転送**:  [送信データ転送](https://azure.microsoft.com/pricing/details/data-transfers/)  (Azure データ センターから送信されるデータ) では、帯域幅の使用量に対して課金されます。

**トランザクション**: Standard Storage では、100,000 トランザクションあたり 0.0036 ドルが課金されます。 トランザクションには、ストレージに対する読み取り操作と書き込み操作の両方が含まれます。

Standard Storage、Virtual Machines、Managed Disks の価格の詳細については、次のページをご覧ください。

* [Azure Storage の料金](https://azure.microsoft.com/pricing/details/storage/)
* [Virtual Machines の料金](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Managed Disks の価格](https://azure.microsoft.com/pricing/details/managed-disks)

## <a name="azure-backup-service-support"></a>Azure Backup サービスのサポート

非管理対象ディスクを使用する仮想マシンは、Azure Backup を使用してバックアップできます。 [詳細についてはこちらをご覧ください](../articles/backup/backup-azure-vms-first-look-arm.md)。

また、Managed Disks で Azure Backup サービスを使用して、時間ベースのバックアップ、VM の簡易復元、バックアップ リテンション期間ポリシーを適用したバックアップ ジョブを作成することもできます。 詳細については、[Managed Disks を使用する VM での Azure Backup サービスの使用](../articles/backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup)に関するセクションをご覧ください。

## <a name="next-steps"></a>次の手順

* [Azure ストレージの概要](../articles/storage/common/storage-introduction.md)

* [ストレージ アカウントの作成](../articles/storage/common/storage-quickstart-create-account.md)

* [Managed Disks の概要](../articles/virtual-machines/linux/managed-disks-overview.md)

* [Resource Manager と PowerShell を使用して VM を作成する](../articles/virtual-machines/windows/quick-create-powershell.md)

* [Azure CLI を使用して新しく Linux VM を作成する](../articles/virtual-machines/linux/quick-create-cli.md)
