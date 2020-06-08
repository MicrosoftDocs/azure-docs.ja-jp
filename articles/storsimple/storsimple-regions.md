---
title: StorSimple の利用可能なリージョン
description: 多様な StorSimple デバイス モデルを使用できる Azure リージョンについて説明します。
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 11/16/2017
ms.author: alkohli
ms.openlocfilehash: a2f04d6faa7770eef7768437ae0e624b76713f98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "76275109"
---
# <a name="available-regions-for-your-storsimple"></a>StorSimple に使用できるリージョン

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>概要

Azure データセンターは、お客様のパフォーマンスの要望、要件、およびデータの場所に関する希望を満たすために世界中の複数の地域で運用されています。 Azure の geo とは、少なくとも 1 つの Azure リージョンを含む、世界の定義済みの地域です。 Azure リージョンは、geo に含まれる領域で、1 つ以上のデータセンターが含まれます。

Azure リージョンの選択はとても重要であり、リージョンの選択は、データの保存地域と統治権、サービスの可用性、パフォーマンス、コスト、冗長性などの要因に影響を受けます。 リージョンの詳細な選択方法については、「[適切な Azure リージョンの選択](https://azure.microsoft.com/overview/datacenters/how-to-choose/)」を参照してください。

StorSimple ソリューションのリージョンの選択は、具体的には次の要因によって決まります。

- StorSimple デバイス マネージャー サービスを使用できるリージョン。
- StorSimple の物理、クラウド、または仮想デバイスを使用できる国/リージョン。
- 最適なパフォーマンスを実現できる StorSimple データを格納するストレージ アカウントのリージョン。

このチュートリアルでは、StorSimple デバイス マネージャー サービス、オンプレミスの物理デバイスとクラウド デバイスに使用できるリージョンについて説明します。 この記事に記載されている内容は、StorSimple 8000 および 1200 デバイスに適用されます。

## <a name="region-availability-for-storsimple-device-manager-service"></a>StorSimple デバイス マネージャー サービスに使用できるリージョン

現在、StorSimple デバイス マネージャー サービスは、12 個のパブリック リージョンと 2 個の Azure Government リージョンでサポートされています。

リージョン (場所) は、StorSimple デバイス マネージャー サービスを初めて作成するときに定義します。 一般的に、デバイスをデプロイする地域に最も近い場所を選択します。 ただし、デバイスとサービスを別の場所にデプロイすることもできます。

次の図は、Azure パブリック クラウドで StorSimple デバイス マネージャー サービスを使用可能でデプロイすることができるリージョンの一覧です。

![storsimple-device-manager-service-regions](./media/storsimple-region/storsimple-device-manager-service-regions.png)

Azure Government クラウドの場合、StorSimple デバイス マネージャー サービスは US Gov アイオワおよび米国政府バージニアのデータセンターで使用できます。

## <a name="region-availability-for-data-stored-in-storsimple"></a>StorSimple に格納されているデータに使用できるリージョン

StorSimple データは Azure ストレージ アカウントに物理的に格納されており、Azure ストレージ アカウントはすべての Azure リージョンで使用できます。 Azure ストレージ アカウントを作成すると、ストレージ アカウントの主な拠点が選択され、データが保存されるリージョンが決まります。

StorSimple デバイス マネージャー サービスを初めて作成し、ストレージ アカウントを関連付けるときに、StorSimple デバイス マネージャー サービスと Azure ストレージを 2 つの別の場所に分けることができます。 その場合、StorSimple デバイス マネージャーと Azure ストレージ アカウントを別々に作成する必要があります。

一般的に、ストレージ アカウントのサービスに最も近いリージョンを選択します。 ただし、最も近い Microsoft Azure リージョンが、実際には待機時間が最短のリージョンではないことがあります。 ネットワーク サービスのパフォーマンスに影響し、結果としてソリューションのパフォーマンスにも影響するのが待機時間です。 そのため、別の地域のストレージ アカウントを選択する場合、使用するサービスと、ストレージ アカウントに関連付けられているリージョン間の待機時間を把握することが重要です。

StorSimple Cloud Appliance を使用している場合、サービスとそれに関連するストレージ アカウントを同じリージョンにすることをお勧めします。 ストレージ アカウントが異なるリージョンに存在すると、十分なパフォーマンスが得られない可能性があります。

## <a name="availability-of-storsimple-device"></a>StorSimple デバイスの可用性

StorSimple デバイスは、モデルによって使用できる地域や国/リージョンが異なります。

### <a name="storsimple-physical-device-models-81008600"></a>StorSimple 物理デバイス (モデル 8100/8600)

StorSimple 8100 または 8600 物理デバイスを使用している場合、デバイスは次の国/リージョンで使用できます。

| #  | 国/リージョン        | #  | 国/リージョン     | #  | 国/リージョン      | #  | 国/リージョン             |
|----|-----------------------|----|--------------------|----|---------------------|----|----------------------------|
| 1  | オーストラリア             | 16 | 香港特別行政区      | 31 | ニュージーランド         | 46 | 南アフリカ               |
| 2  | オーストリア               | 17 | ハンガリー            | 32 | ナイジェリア             | 47 | 韓国                |
| 3  | バーレーン               | 18 | アイスランド            | 33 | ノルウェー              | 48 | スペイン                      |
| 4  | ベルギー               | 19 | インド              | 34 | ペルー                | 49 | スリランカ                  |
| 5  | ブラジル                | 20 | インドネシア          | 35 | フィリピン         | 50 | スウェーデン                     |
| 6  | カナダ                | 21 | アイルランド            | 36 | ポーランド              | 51 | スイス                |
| 7  | チリ                 | 22 | イスラエル             | 37 | ポルトガル            | 52 | 台湾                     |
| 8  | コロンビア              | 23 | イタリア              | 38 | プエルトリコ         | 53 | タイ                   |
| 9  | チェコ共和国        | 24 | 日本              | 39 | カタール               | 54 | トルコ                     |
| 10 | デンマーク               | 25 | ケニア              | 40 | ルーマニア             | 55 | ウクライナ                    |
| 11 | エジプト                 | 26 | クウェート             | 41 | ロシア              | 56 | アラブ首長国連邦       |
| 12 | フィンランド               | 27 | 中華人民共和国マカオ特別行政区          | 42 | サウジアラビア        | 57 | イギリス             |
| 13 | フランス                | 28 | マレーシア           | 43 | シンガポール           | 58 | United States              |
| 14 | ドイツ               | 29 | メキシコ             | 44 | スロバキア            | 59 | ベトナム                    |
| 15 | ギリシャ                | 30 | オランダ        | 45 | スロベニア            | 60 | クロアチア                    |

国/リージョンの追加に応じてこの一覧は変更されます。 地域の最新の一覧については、「[Product terms](https://www.microsoft.com/en-us/licensing/product-licensing/products)」(製品条項) のストレージ アレイ条件に関する付録を参照してください。

Microsoft は、前述の一覧に記載されている地域に物理ハードウェアを出荷し、StorSimple 用のハードウェア交換部品を提供することができます。

> [!IMPORTANT]
> StorSimple がサポートされていないリージョンには StorSimple 物理デバイスを配置しないでください。 Microsoft は、StorSimple がサポートされていない国/リージョンに交換部品を出荷しません。

### <a name="storsimple-cloud-appliance-models-80108020"></a>StorSimple Cloud Appliance (モデル 8010/8020)

StorSimple Cloud Appliance 8010 または 8020 を使用している場合、基になる VM がサポートされているすべてのリージョンでデバイスがサポートされ、使用できます。 8010 は、すべての Azure リージョンでサポートされている _Standard_A3_ VM を使用しています。

8020 は、Premium Storage と _Standard_DS3_ VM を使用してクラウド アプライアンスを作成しています。 8020 は、Premium Storage と _Standard_DS3_ Azure VM をサポートする Azure リージョンでサポートされています。 [こちらの表](https://azure.microsoft.com/regions/services/)を参照し、該当するリージョンで **「Virtual Machines」の「DS シリーズ」** と **「Storage」の「Disk Storage」** の両方が利用できるかどうかを確認してください。

### <a name="storsimple-virtual-array-model-1200"></a>StorSimple Virtual Array (モデル 1200)

1200 シリーズの StorSimple Virtual Array を使用している場合、仮想ディスク イメージはすべての Azure リージョンでサポートされています。

## <a name="next-steps"></a>次のステップ

* [さまざまな StorSimple モデルの価格](https://azure.microsoft.com/pricing/calculator/#storsimple2)について学びます。
* [StorSimple ストレージ アカウントの管理の詳細](storsimple-8000-manage-storage-accounts.md)
* [StorSimple デバイス マネージャー サービスを使用した StorSimple デバイスの管理方法](storsimple-8000-manager-service-administration.md)
