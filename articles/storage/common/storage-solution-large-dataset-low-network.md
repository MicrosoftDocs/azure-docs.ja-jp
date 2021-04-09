---
title: ネットワーク帯域幅が低速またはない場合の、大規模なデータセットの Azure データ転送のオプション | Microsoft Docs
description: ご利用の環境が、ネットワーク帯域幅がない状態に制限されていて、大規模なデータセットを転送する予定の場合に、データ転送に関する Azure ソリューションを選択する方法について説明します。
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: 9b83ba106b35a0a3abd035e85f60c4c39bbadd3b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98704642"
---
# <a name="data-transfer-for-large-datasets-with-low-or-no-network-bandwidth"></a>ネットワーク帯域幅が低速またはない場合の大規模なデータセットのデータ転送
 
この記事では、ご利用の環境が、ネットワーク帯域幅がない状態に制限されていて、大規模なデータセットを転送する予定の場合の、データ転送ソリューションの概要を示します。 また、この記事では、推奨されるデータ転送と、このシナリオのそれぞれの主要な機能のマトリックスについても説明します。

利用可能なすべてのデータ転送オプションの概要を理解するには、[Azure データ転送ソリューションの選択](storage-choose-data-transfer-solution.md)に関するページに移動します。

## <a name="offline-transfer-or-network-transfer"></a>オフライン転送またはネットワーク転送

大規模なデータセットであれば、必ず数 TB から数 PB のデータが含まれています。 ネットワーク帯域幅がなしに制限されているか、ネットワークが低速であるか、またはネットワークを信頼できません。 また、次の点にも注目してください。

- インターネット サービス プロバイダー (ISP) から、ネットワーク転送のコストによる制限を受けている。
- 機密データを扱う場合、セキュリティまたは組織のポリシーで発信接続は許可されていない。

上のどの場合も、物理デバイスを使用して、1 回限りの一括データ転送を行います。 Microsoft によって供給される Data Box Disk、Data Box、Data Box Heavy デバイスから選択します。または、独自のディスクを使用して Import/Export を利用します。

物理デバイスが適切なオプションがかどうかを確認するには、次の表を使用します。 ここには、さまざまな使用可能帯域幅の場合の、ネットワーク データ転送の推定時間が示されています (90% の使用率と仮定)。 ネットワーク転送が低速すぎると推定される場合は、物理デバイスを使用する必要があります。  

![ネットワーク転送またはオフライン転送](media/storage-solution-large-dataset-low-network/storage-network-or-offline-transfer.png)

## <a name="recommended-options"></a>推奨されるオプション

このシナリオで使用できるオプションは、Azure Data Box のオフライン転送または Azure Import/Export 用のデバイスです。

- **オフライン転送用の Azure Data Box ファミリ** – 時間、ネットワークの可用性、またはコストが限られている場合は、Microsoft が供給する Data Box デバイスのデバイスを使用して、大量のデータを Azure に移動します。 Robocopy などのツールを使って、オンプレミスのデータをコピーします。 転送対象のデータのサイズに応じて、Data Box Disk、Data Box、Data Box Heavy から選択できます。
- **Azure Import/Export** – 大量のデータを安全に Azure Blob Storage と Azure Files にインポートするために、独自のディスク ドライブを送付して Azure Import/Export サービスを使用します。 また、このサービスでは、Azure Blob Storage からディスク ドライブにデータを転送し、オンプレミスのサイトに送付できます。

## <a name="comparison-of-key-capabilities"></a>主な機能の比較

次の表は、主な機能における相違点をまとめたものです。

|                                     |    Data Box Disk      |    Data Box                                      |    Data Box Heavy              |    インポート/エクスポート                       |
|-------------------------------------|---------------------------------|--------------------------------------------------|------------------------------------------|----------------------------------------|
|    **データ サイズ**                    |    最大 35 TB                 |    デバイスあたり最大 80 TB                       |    デバイスあたり最大 800 TB               |    変数                            |
|    **データの種類**                    |    Azure BLOB                  |    Azure BLOB<br>Azure Files                    |    Azure BLOB<br>Azure Files            |    Azure BLOB<br>Azure Files          |
|    **フォーム ファクター**                  |    注文ごとに 5 つの SSD             |    1 X 50 lbs。 1 回の注文ごとにデスクトップ サイズのデバイス    |    1 X 最大 500-lbs。 1 回の注文ごとに大容量のデバイス    |    1 回の注文ごとに最大 10 台の HDD/SSD        |
|    **初期セットアップ時間**           |    低 <br>(15 分)            |    低から中 <br> (30 分未満)               |    中<br>(1 ～ 2 時間)               |    中から難<br>(可変) |
|    **Azure にデータを送信する**           |    はい                          |    はい                                           |    はい                                   |    はい                                 |
|    **Azure からデータをエクスポートする**       |    いいえ                           |    いいえ                                            |    いいえ                                    |    はい                                 |
|    **暗号化**                   |    AES 128 ビット                  |    AES 256 ビット                                   |    AES 256 ビット                           |    AES 128 ビット                         |
|    **ハードウェア**                     |     Microsoft による提供          |    Microsoft による提供                            |    Microsoft による提供                    |    お客様による提供                   |
|    **ネットワーク インターフェイス**            |    USB 3.1/SATA                 |    RJ 45、SFP+                                   |    RJ45、QSFP+                           |    SATA II/SATA III                    |
|    **パートナー統合**          |    一部                         |    [高](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureExpressPod)                                          |    [高](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureExpressPod)                                  |    一部                                |
|    **送付**                     |    Microsoft による管理            |    Microsoft による管理                             |    Microsoft による管理                     |    お客様による管理                    |
| **データの移動時に使用する**     |コマース境界内|コマース境界内|コマース境界内|地理的境界を越える (US から EU など)|
|    **料金**                      |    [料金](https://azure.microsoft.com/pricing/details/databox/disk/)                    |   [料金](https://azure.microsoft.com/pricing/details/storage/databox/)                                      |  [料金](https://azure.microsoft.com/pricing/details/storage/databox/heavy/)                               |   [料金](https://azure.microsoft.com/pricing/details/storage-import-export/)                            |


## <a name="next-steps"></a>次のステップ

- 以下の方法を理解します

    - [Data Box Edge を使用してデータを転送する](../../databox/data-box-disk-quickstart-portal.md)。
    - [Data Box を使用してデータを転送する](../../databox/data-box-quickstart-portal.md)。
    - [Import/Export を使用してデータを転送する](../../import-export/storage-import-export-data-to-blobs.md)。