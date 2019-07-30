---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/22/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4abf50e11070f2060309ae9b9cd045c874a2c52e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133452"
---
# <a name="what-disk-types-are-available-in-azure"></a>Azure で利用できるディスクの種類

現在、Azure のマネージド ディスクでは 4 種類のディスクが提供されており、そのうちの 3 つは一般提供 (GA) 済み、もう 1 つはプレビュー段階です。 これら 4 種類のディスクのそれぞれに、固有の適切なターゲット顧客シナリオがあります。

## <a name="disk-comparison"></a>ディスクの比較

次の表では、使用するものを決定するときの参考に、マネージド ディスクの Ultra SSD (ソリッドステート ドライブ) (プレビュー)、Premium SSD、Standard SSD、および Standard HDD (ハード ディスク ドライブ) を比較します。

|   | Ultra SSD (プレビュー)   | Premium SSD   | Standard SSD   | Standard HDD   |
|---------|---------|---------|---------|---------|
|ディスクの種類   |SSD   |SSD   |SSD   |HDD   |
|シナリオ   |SAP HANA やトップ レベルのデータベース (たとえば SQL や Oracle) などの I/O 集約型のワークロードと、その他のトランザクションが多いワークロード。   |運用環境のワークロードやパフォーマンスに影響されやすいワークロード   |Web サーバー、あまり使用されていないエンタープライズ アプリケーション、および開発/テスト   |バックアップ、重要ではない、不定期に起こるアクセス   |
|ディスク サイズ   |65,536 ギビバイト (GiB) (プレビュー)   |32,767 GiB    |32,767 GiB   |32,767 GiB   |
|最大スループット   |2,000 MiB/秒 (プレビュー)   |900 MiB/秒   |750 MiB/秒   |500 MiB/秒   |
|最大 IOPS   |160,000 (プレビュー)   |20,000   |6,000   |2,000   |

## <a name="ultra-ssd-preview"></a>Ultra SSD (プレビュー)

Azure Ultra SSD (プレビュー) は、Azure IaaS VM 用に高スループット、高 IOPS、一貫性のある低待機時間のディスク ストレージを提供します。 Ultra SSD のその他のメリットとして、仮想マシンを再起動する必要なしに、ワークロードに合わせてディスクのパフォーマンスを動的に変更できます。 Ultra SSD は、SAP HANA、最上位層のデータベース、トランザクションの多いワークロードなど、データ集中型のワークロードに適しています。 Ultra SSD は、データ ディスクとしてのみ使用できます。 OS ディスクとしては Premium SSD を使用することをお勧めします。

### <a name="performance"></a>パフォーマンス

Ultra ディスクをプロビジョニングするときは、ディスクの容量とパフォーマンスを個別に構成することができます。 Ultra SSD は、4 GiB から最大 64 TiB までの複数の固定されたサイズで提供されます。IOPS とスループットを個別に構成できる柔軟なパフォーマンス構成モデルであることが特徴です。

Ultra SSD の主な機能は次のとおりです。

- ディスク容量:Ultra SSD の容量の範囲は 4 GiB から最大 64 TiB です。
- ディスク IOPS: Ultra SSD では、300 IOPS/GiB の IOPS 制限と、ディスクあたり最大 160 K の IOPS がサポートされます。 プロビジョニングした IOPS を達成するには、選択したディスクの IOPS が VM の IOPS 未満であることを確認してください。 最小ディスク IOPS は 100 IOPS です。
- ディスク スループット: Ultra SSD では、1 つのディスクのスループットは、プロビジョニングされた IOPS ごとに 256 KiB/秒に制限され、ディスクあたり最大 2000 MBps に制限されます (MBps = 秒あたり 10^6 バイト)。 最小ディスク スループットは 1 MiB です。
- Ultra SSD では、実行時に、ディスクをデタッチすることなく、仮想マシンから、ディスク パフォーマンス属性 (IOPS とスループット) を調整できます。 ディスクでディスク パフォーマンス サイズ変更操作を実行した場合、変更が実際に有効になるまでに最大で 1 時間かかることがあります。

### <a name="disk-size"></a>ディスク サイズ

|ディスク サイズ (GiB)  |IOPS の上限  |スループットの上限 (MBps)  |
|---------|---------|---------|
|4     |1,200         |300         |
|8     |2,400         |600         |
|16     |4,800         |1,200         |
|32     |9,600         |2,000         |
|64     |19,200         |2,000         |
|128     |38,400         |2,000         |
|256     |76,800         |2,000         |
|512     |80,000         |2,000         |
|1,024 - 65,536 (この範囲内のサイズは 1 TiB ずつ増えます)     |160,000         |2,000         |

### <a name="transactions"></a>トランザクション

Ultra SSD では、スループットが 256 KiB 以下の I/O 操作は、それぞれ単一の I/O 操作とみなされます。 スループットが 256 KiB を超える I/O 操作は、サイズが 256 KiB の複数の I/O とみなされます。

### <a name="preview-scope-and-limitations"></a>プレビューの範囲と制限事項

プレビューの間の Ultra SSD は次のとおりです。

- 米国東部 2 の単一可用性ゾーンでサポートされます  
- 可用性ゾーンでのみ使用できます (ゾーン外の可用性セットと単一の VM デプロイでは、Ultra ディスクを接続できません)
- ES/DS v3 VM でのみサポートされています
- データ ディスクとしてのみ使用可能であり、4k 物理セクター サイズのみをサポートしています  
- 空のディスクとしてのみ作成できます  
- 現在、デプロイには Azure Resource Manager テンプレート、CLI、PowerShell、および Python SDK のみを使用できます。
- Azure portal を使用してのデプロイは (まだ) できません。
- ディスク スナップショット、VM イメージ、可用性セット、仮想マシン スケール セット、Azure Disk Encryption はサポートされていません。
- Azure Backup または Azure Site Recovery との統合はまだサポートされていません。
-  [ほとんどのプレビュー](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)と同様に、一般提供 (GA) されるまではこの機能を運用環境のワークロードに使用しないでください。
