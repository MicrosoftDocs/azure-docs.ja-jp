---
title: オフライン バックアップの概要
description: オフライン バックアップのコンポーネントについて説明します。 これには、Azure Data Box に基づくオフライン バックアップと Azure Import/Export サービスに基づくオフライン バックアップが含まれます。
ms.topic: conceptual
ms.date: 1/28/2020
ms.custom: references_regions
ms.openlocfilehash: 7c65cf6b36af3057fb06c6a6584fa458b1030c72
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98704137"
---
# <a name="overview-of-offline-backup"></a>オフライン バックアップの概要

この記事では、オフライン バックアップの概要を説明します。

Azure への初回完全バックアップでは、通常、大量のデータがオンラインで転送され、増分変更のみを転送する後続のバックアップと比べて多くのネットワーク帯域幅が必要になります。 特定地域のリモート オフィスやデータセンターには、常に十分なネットワーク帯域幅があるわけではありません。 このため、これらの初期バックアップには数日かかることがあります。 この間、バックアップでは、オンプレミスのデータセンターで実行されているアプリケーション用にプロビジョニングされたのと同じネットワークが継続して使用されます。

Azure Backup では、ネットワーク帯域幅を使用せずに初期バックアップ データをオフラインで転送するオフライン バックアップがサポートされています。 これは、バックアップ データを物理記憶域装置にコピーするメカニズムを提供します。 その後、デバイスは近くの Azure データ センターに発送され、Recovery Services コンテナーにアップロードされます。 このプロセスにより、ネットワーク帯域幅を使用せずにバックアップ データを確実に転送できます。

## <a name="offline-backup-options"></a>オフライン バックアップのオプション

オフライン バックアップは、ストレージ デバイスの所有権に基づいて 2 つのモードで提供されます。

- Azure Data Box に基づくオフライン バックアップ (プレビュー)
- Azure Import/Export サービスに基づくオフライン バックアップ

## <a name="offline-backup-based-on-azure-data-box-preview"></a>Azure Data Box に基づくオフライン バックアップ (プレビュー)

このモードは、現在、Microsoft Azure Recovery Services (MARS) エージェントでサポートされています (プレビュー段階)。 このオプションは、[Azure Data Box](https://azure.microsoft.com/services/databox/) を利用し、USB コネクタを使用して Microsoft 独自の安全で改ざん防止機能を備えた転送アプライアンスをデータセンターやリモート オフィスに転送します。 バックアップ データは、これらのデバイスに直接書き込まれます。 このオプションを使用すると、Azure と互換性のあるディスクやコネクタを自分で調達したり、一時的ストレージをステージング場所としてプロビジョニングしたりするために必要な労力を節約できます。 また、Microsoft では、Azure portal を通じて追跡できるエンドツーエンドの転送ロジスティックも処理します。

このオプションを使用したバックアップ データの移動について説明するアーキテクチャを次に示します。

![Azure Backup Data Box のアーキテクチャ](./media/offline-backup-overview/azure-backup-databox-architecture.png)

アーキテクチャの概要は次のとおりです。

1. Azure Backup は、事前に構成されたこれらのデバイスにバックアップ データを直接コピーします。
2. 次に、ユーザーはそれらのデバイスを Azure データセンターに返送できます。
3. Azure Data Box は、お客様が所有するストレージ アカウントにデータをコピーします。
4. Azure Backup は、ストレージ アカウントから、指定された Recovery Services コンテナーにバックアップ データを自動的にコピーします。 増分オンライン バックアップがスケジュールされます。

Azure Data Box に基づいてオフライン バックアップを使用するには、[Azure Data Box を使用したオフライン バックアップ](offline-backup-azure-data-box.md)に関する記事を参照してください。

## <a name="offline-backup-based-on-the-azure-importexport-service"></a>Azure Import/Export サービスに基づくオフライン バックアップ

このオプションは、Microsoft Azure Backup Server (MABS)、System Center Data Protection Manager (DPM) DPM A、および MARS エージェントでサポートされています。 [Azure Import/Export サービス](../import-export/storage-import-export-service.md)が使用されます。 Azure と互換性のある独自のディスクとコネクタを使用して、Azure に初期バックアップ データを転送できます。 この方法では、ステージング場所と呼ばれる一時的ストレージをプロビジョニングし、事前に構築されたユーティリティを使用してバックアップ データをフォーマットし、お客様が所有するディスクにコピーする必要があります。

このオプションを使用したバックアップ データの移動について説明するアーキテクチャを次に示します。

![Azure Backup Import/Export サービスのアーキテクチャ](./media/offline-backup-overview/azure-backup-import-export.png)

アーキテクチャの概要は次のとおりです。

1. ネットワーク経由でバックアップ データを送信する代わりに、Azure Backup はステージング場所にバックアップ データを書き込みます。
2. ステージング場所のデータは、カスタム ユーティリティを使用して 1 つ以上の SATA ディスクに書き込まれます。
3. ユーティリティは、準備作業の一環として Azure インポート ジョブを作成します。 SATA ドライブが、最も近い Azure データセンターに発送され、インポート ジョブを参照してアクティビティを接続します。
4. Azure データセンターでは、ディスク上のデータが Azure ストレージ アカウントにコピーされます。
5. Azure Backup によってストレージ アカウントから Recovery Services コンテナーにバックアップ データがコピーされます。 増分バックアップがスケジュールされます。

MARS エージェントで Azure Import/Export サービスに基づくオフライン バックアップを使用するには、「[Azure Backup でのオフライン バックアップのワークフロー](./backup-azure-backup-import-export.md)」を参照してください。

MABS または DPM-A と共に同じものを使用するには、「[DPM と Azure Backup Server のオフライン バックアップのワークフロー](./backup-azure-backup-server-import-export.md)」を参照してください。

## <a name="offline-backup-support-summary"></a>オフライン バックアップのサポートの概要

次の表では、シナリオに基づいて適切な選択を行えるように、使用可能な 2 つのオプションを比較しています。

| **考慮事項**                                            | **Azure Data Box に基づくオフライン バックアップ**                     | **Azure Import/Export サービスに基づくオフライン バックアップ**                |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Azure Backup のデプロイ モデル                              | MARS エージェント (プレビュー)                                              | MARS エージェント、MABS、DPM-A                                           |
| サーバー (MARS) 1 台あたり、または保護グループ (MABS、DPM-A) 1 つあたりの最大バックアップ データ | [Azure Data Box Disk](../databox/data-box-disk-overview.md) - 7.2 TB <br> [Azure Data Box](../databox/data-box-overview.md) - 80 TB       | 80 TB (それぞれ 8 TB のディスクが最大 10 個)                          |
| セキュリティ (データ、デバイス、およびサービス)                           | [データ](../databox/data-box-security.md#data-box-data-protection) - AES-256 ビットで暗号化 <br> [デバイス](../databox/data-box-security.md#data-box-device-protection) - 堅牢なケースで、データをコピーするための独自の資格情報ベースのインターフェイス <br> [サービス](../databox/data-box-security.md#data-box-service-protection) - Azure セキュリティ機能によって保護 | データ - BitLocker で暗号化                                 |
| 一時的なステージング場所のプロビジョニング                     | 必要なし                                                | バックアップ データの推定サイズ以上        |
| サポートされているリージョン                                           | [Azure Data Box Disk のリージョン](../databox/data-box-disk-overview.md#region-availability) <br> [Azure Data Box のリージョン](../databox/data-box-disk-overview.md#region-availability) | [Azure Import/Export サービスのリージョン](../import-export/storage-import-export-service.md#region-availability) |
| 国外への発送                                     | サポートされていません  <br>    Azure データセンターの発送元の住所と宛先は同一国/地域内でなければなりません* | サポートされています                                                    |
| 転送ロジスティック (配送、輸送、集荷)           | Microsoft が完全に管理                                     | お客様による管理                                            |
| 価格                                                      | [Azure Data Box の価格](https://azure.microsoft.com/pricing/details/databox/) <br> [Azure Data Box Disk の価格](https://azure.microsoft.com/pricing/details/databox/disk/) | [Azure Import/Export サービスの価格](https://azure.microsoft.com/pricing/details/storage-import-export/) |

*お住まいの国/地域に Azure データセンターがない場合は、別の国/地域の Azure データセンターにディスクを発送する必要があります。

## <a name="next-steps"></a>次のステップ

- [Azure Data Box を使用した Azure Backup のオフライン バックアップ](offline-backup-azure-data-box.md#backup-data-size-and-supported-data-box-skus)
- [Azure Backup でのオフライン バックアップのワークフロー](backup-azure-backup-import-export.md)
- [DPM と Azure Backup Server のオフライン バックアップのワークフロー](backup-azure-backup-server-import-export.md)
