---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/08/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 509568b143c9fbbf236139ca83cb55b0ef39beb0
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86145948"
---
次の表では、Azure の汎用 v1、v2、BLOB ストレージ、ブロック BLOB ストレージのアカウントに対する既定の制限について説明します。 "*受信*" 制限は、ストレージ アカウントに送信されるすべてのデータを指します。 *送信*制限は、ストレージ アカウントから受信するすべてのデータを指します。

| リソース | 制限 |
| --- | --- |
| サブスクリプションあたりの各リージョンのストレージ アカウント数 (Standard および Premium のストレージ アカウントを含む)。| 250 |
| ストレージ アカウントの最大容量 | 5 PiB <sup>1</sup>|
| ストレージ アカウントあたりの BLOB コンテナー、BLOB、ファイル共有、テーブル、キュー、エンティティ、メッセージの最大数 | 制限なし |
| ストレージ アカウントあたりの最大要求レート<sup>1</sup> | 1 秒あたり 20,000 要求 |
| ストレージ アカウントあたりの最大イングレス<sup>1</sup> (米国、ヨーロッパ リージョン) | 10 Gbps |
| ストレージ アカウントあたりの最大イングレス<sup>1</sup> (米国とヨーロッパ以外のリージョン) | RA-GRS/GRS が有効な場合は 5 Gbps、LRS/ZRS<sup>2</sup> の場合は 10 Gbps |
| 汎用 v2 および BLOB ストレージ アカウントの最大送信速度 (すべてのリージョン) | 50 Gbps |
| 汎用 v1 ストレージ アカウントの最大送信速度 (米国リージョン) | RA-GRS/GRS が有効な場合は 20 Gbps、LRS/ZRS<sup>2</sup> の場合は 30 Gbps |
| 汎用 v1 ストレージ アカウントの最大送信速度 (米国以外のリージョン) | RA-GRS/GRS が有効な場合は 10 Gbps、LRS/ZRS<sup>2</sup> の場合は 15 Gbps |
| ストレージアカウントごとの仮想ネットワーク規則の最大数 | 200 |
| ストレージアカウントごとの IP アドレス規則の最大数 | 200 |

<sup>1</sup> Azure Storage 標準アカウントは、要求によるより高い容量制限とより高いイングレス制限をサポートします。 アカウント制限の引き上げを希望する場合は、[Azure サポートにお問い合わせください](https://azure.microsoft.com/support/faq/)。

<sup>2</sup> お使いのストレージ アカウントの読み取りアクセスで geo 冗長ストレージ (RA-GRS) または geo ゾーン冗長ストレージ (RA-GZRS) を有効にしている場合、第 2 の場所のエグレス ターゲットが第 1 の場所のそれと同じになります。 詳細については、「[Azure Storage のレプリケーション](../articles/storage/common/storage-redundancy.md)」をご覧ください。

> [!NOTE]
> Microsoft では、ほとんどのシナリオで汎用 v2 ストレージ アカウントを使用することをお勧めしています。 汎用 v1 または Azure BLOB ストレージ アカウントは汎用 v2 アカウントに簡単にアップグレードできます。その際にダウンタイムは発生せず、データをコピーする必要はありません。 詳細については、「[汎用 v2 ストレージ アカウントにアップグレードする](../articles/storage/common/storage-account-upgrade.md)」を参照してください。

アプリケーションで必要とされるスケーラビリティが、単一ストレージ アカウントあたりのスケーラビリティ ターゲットを超えている場合は、複数のストレージ アカウントを使用するようにアプリケーションを構築できます。 その後、それらのストレージ アカウント間でデータをパーティション分割できます。 ボリューム価格については、「 [Azure Storage 料金 ](https://azure.microsoft.com/pricing/details/storage/) 」を参照してください。

すべてのストレージ アカウントは、いつ作成されたかに関係なく、フラット ネットワーク トポロジ上で実行されます。 Azure Storage フラット ネットワークのアーキテクチャとスケーラビリティの詳細については、[Microsoft Azure Storage: 強力な一貫性を備えた高可用性クラウド ストレージ サービス](https://docs.microsoft.com/archive/blogs/hanuk/windows-azures-flat-network-storage-to-enable-higher-scalability-targets)を参照してください。 
