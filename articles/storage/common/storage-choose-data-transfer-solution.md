---
title: データ転送用の Azure ソリューションを選択する | Microsoft Docs
description: データ サイズと環境内で使用可能なネットワーク帯域幅に基づいてデータ転送用の Azure ソリューションを選択する方法について学習します。
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: alkohli
ms.openlocfilehash: 11ea9c759bdb4bb2b837028407ce6e83f6e25a8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92784050"
---
# <a name="choose-an-azure-solution-for-data-transfer"></a>データ転送用の Azure ソリューションを選択する

この記事では、いくつかの一般的な Azure データ転送ソリューションの概要を示します。 また、環境内のネットワーク帯域幅と転送するデータのサイズに応じて推奨されるオプションへのリンクも記載されています。

## <a name="types-of-data-movement"></a>データ移動の種類

データ転送は、オフライン、またはネットワーク接続経由で行うことができます。 次に応じて、ソリューションを選択します。

- **データ サイズ** - 転送用のデータのサイズ。
- **転送頻度** - 1 回限り、または定期的なデータ インジェスト。
- **ネットワーク** – 環境内でデータ転送に使用可能な帯域幅。

次の種類のデータ移動が可能です。

- **配送可能なデバイスを使用したオフライン転送** - オフラインの 1 回限りの一括データ転送を実行するときに、配送可能な物理的なデバイスを使用します。 Microsoft では、ディスク (セキュリティで保護された特殊なデバイス) を発送しています。 または、お客様ご自身でディスクを購入し、発送することもできます。 デバイスにデータをコピーし、Azure に発送すると、そこでデータがアップロードされます。  この場合に使用できるオプションは、Data Box Disk、Data Box、Data Box Heavy、および Import/Export (ご自身のディスクを使用) です。

- **ネットワーク転送** - ネットワーク接続経由で Azure にデータを転送します。 これは、さまざまな方法で実行できます。

    - **グラフィカル インターフェイス** - 少数のファイルを時折転送するだけで、データ転送を自動化する必要がない場合は、Azure Storage Explorer や、Azure portal の Web ベースの探索ツールなど、グラフィカル インターフェイス ツールを選択できます。
    - **スクリプトまたはプログラムによる転送** - 提供されている最適化されたソフトウェア ツールを使用するか、REST API/SDK を直接呼び出すことができます。 使用可能かつスクリプト実行可能なツールは、AzCopy、Azure PowerShell、および Azure CLI です。 プログラム インターフェイスでは、.NET、Java、Python、Node/JS、C++、Go、PHP、または Ruby のいずれかの SDK を使用します。
    - **オンプレミス デバイス** - データセンター内に存在し、ネットワーク経由のデータ転送を最適化する、物理または仮想デバイスが提供されています。 これらのデバイスは、頻繁に使用されるファイルのローカル キャッシュも提供します。 物理デバイスは Azure Stack Edge、仮想デバイスは Data Box Gateway です。 両方ともオンプレミスで永続的に実行され、ネットワーク経由で Azure に接続されます。
    - **マネージド データ パイプライン** - クラウド パイプラインを設定して、複数の Azure サービス間またはオンプレミスで、あるいは 2 つを組み合わせて、ファイルを定期的に転送することができます。 データ パイプラインを設定および管理したり、分析用にデータを移動および変換するには、Azure Data Factory を使用します。

次の図は、転送に使用可能なネットワーク帯域幅、転送用のデータのサイズ、および転送の頻度に応じて、さまざまな Azure データ転送ツールを選択するためのガイドラインを示しています。

![Azure データ転送ツール](media/storage-choose-data-transfer-solution/azure-data-transfer-options-3.png)

**オフライン転送デバイスの上限 - Data Box Disk、Data Box、および Data Box Heavy は、複数の種類のデバイスを発注することによって拡張できます。*

## <a name="selecting-a-data-transfer-solution"></a>データ転送ソリューションの選択

次の質問に答えて、データ転送ソリューションの選択に役立ててください。

- 使用可能なネットワーク帯域幅が制限されているか存在せず、かつ大規模なデータセットを転送しますか。
  
    "はい" の場合は、[シナリオ 1: ネットワーク帯域幅が低速または存在しない場合の大規模なデータセットの転送](storage-solution-large-dataset-low-network.md)に関するページを参照してください。
- 大規模なデータセットをネットワーク経由で転送し、かつ中程度から高いネットワーク帯域幅が存在しますか。

    "はい" の場合は、[シナリオ 2: 中程度から高速のネットワーク帯域幅での大規模なデータセットの転送](storage-solution-large-dataset-moderate-high-network.md)に関するページを参照してください。
- ネットワーク経由で少数のファイルを時折転送するだけですか。

    "はい" の場合は、[シナリオ 3: ネットワーク帯域幅が制限されているか中程度の場合の小規模なデータセットの転送](storage-solution-small-dataset-low-moderate-network.md)に関するページを参照してください。
- 一定間隔で特定の時点のデータ転送を探していますか。

    "はい" の場合は、[シナリオ 4: 定期的なデータの転送](storage-solution-periodic-data-transfer.md)に関するページに記載されているスクリプト/プログラム オプションを使用します。
- 進行中の継続的なデータ転送を探していますか。

    "はい" の場合は、[シナリオ 4: 定期的なデータの転送](storage-solution-periodic-data-transfer.md)に関するページに記載されているスクリプト/プログラム オプションを使用します。

## <a name="data-transfer-feature-in-azure-portal"></a>Azure portal のデータ転送機能

Azure portal で Azure Storage アカウントにアクセスして、 **[データ転送]** 機能を選択することもできます。 環境内のネットワーク帯域幅、転送するデータのサイズ、およびデータ転送の頻度を指定します。 指定した情報に対応する最適なデータ転送ソリューションが表示されます。 

## <a name="next-steps"></a>次のステップ

- [Azure Storage Explorer の概要を理解する](https://azure.microsoft.com/resources/videos/introduction-to-microsoft-azure-storage-explorer/)
- [AzCopy の概要を理解する](./storage-use-azcopy-v10.md)
- [クイック スタート: PowerShell を使用して BLOB をアップロード、ダウンロード、および一覧表示する](../blobs/storage-quickstart-blobs-powershell.md)
- [クイック スタート: Azure CLI を使用して BLOB を作成、ダウンロード、一覧表示する](../blobs/storage-quickstart-blobs-cli.md)
- 内容は次のとおりです。

    - [オフライン転送用の Azure Data Box、Azure Data Box Disk、および Azure Data Box Heavy](../../databox/index.yml)
    - [オンライン転送用の Azure Data Box Gateway と Azure Stack Edge](../../databox-online/index.yml)
- [Azure Data Factory の概要を理解する](../../data-factory/copy-activity-overview.md)
- REST API を使用してデータ転送する

    - [.NET の場合](/dotnet/api/overview/azure/storage)
    - [Java の場合](/java/api/overview/azure/storage)