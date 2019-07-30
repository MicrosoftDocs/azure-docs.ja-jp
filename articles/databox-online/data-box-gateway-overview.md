---
title: Microsoft Azure Data Box Gateway の概要 | Microsoft Docs
description: データを Azure に転送できる仮想アプライアンス ストレージ ソリューションである Azure Data Box Gateway について説明します
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: overview
ms.date: 07/16/2019
ms.author: alkohli
ms.openlocfilehash: 1b749df7c5b3badbc6e7eccd885cb953ab3d0afa
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68277382"
---
# <a name="what-is-azure-data-box-gateway"></a>Azure Data Box Gateway とは

Azure Data Box Gateway は、Azure にシームレスにデータを送信できるストレージ ソリューションです。 この記事では、Azure Data Box Gateway ソリューションの概要、利点、主な機能、このデバイスを配置できるシナリオについて説明します。

Data Box Gateway は、仮想化環境またはハイパーバイザーにプロビジョニングされた仮想マシンに基づく仮想デバイスです。 仮想デバイスはオンプレミスに存在し、NFS および SMB プロトコルを使用して仮想デバイスに対するデータを記述します。 デバイスはその後、Azure ブロック blob、ページ blob、または Azure Files にデータを転送します。

## <a name="use-cases"></a>ユース ケース

Data Box Gateway は、クラウド アーカイブやディザスター リカバリーなどでクラウドにデータを転送したり、クラウド スケールでデータを処理する必要がある場合に活用できます。 Data Box Gateway は、以下のようなさまざまなシナリオで、データ転送に使用できます。

- **クラウド アーカイブ** - Data Box Gateway を使用して、セキュリティで保護された効率的な方法で、数百 TB のデータを Azure storage にコピーします。 データはアーカイブのシナリオに合わせて 1 回で取り込んだり継続的に取り込んだりすることができます。

- **継続的なデータ インジェスト** - データ サイズに関係なく、データをデバイスに継続的に取り込んでクラウドにコピーします。 ゲートウェイ デバイスにデータが書き込まれると、デバイスによってデータが Azure Storage にアップロードされます。  

- **増分転送に先立つ初期一括転送** - オフライン モードでの一括転送 (初期シード) に Data Box を使用し、ネットワーク経由の増分転送 (継続的なフィード) に Data Box Gateway を使用します。

詳細については、[Azure Data Box Gateway のユース ケース](data-box-gateway-use-cases.md)に関するページを参照してください。

## <a name="benefits"></a>メリット

Data Box Gateway には次の利点があります。

- **簡単なデータ転送**- ローカル ネットワーク共有で操作するのと同じくらい簡単に、Azure ストレージとの間でデータを転送できます。  
- **高パフォーマンス** - Azure との間でパフォーマンスの高い転送を実現することにより、ネットワーク データ転送の困難さを解消します。
- **営業時間中の高速アクセスと高速データ インジェスト** - Data Box Gateway は、仮想デバイスのプロビジョニング時にローカル容量サイズとして定義されるローカル キャッシュを搭載しています。 データ ディスクのサイズは、[仮想デバイスの最低要件](data-box-gateway-system-requirements.md#specifications-for-the-virtual-device)に従って指定する必要があります。 ローカル キャッシュには、次の利点があります。
    - ローカル キャッシュを使用することで、データ インジェストを高速に行うことができます。 ピーク営業時間帯に大量のデータを取り込む際は、このキャッシュにデータを保持して、クラウドにアップロードすることができます。
    - 特定のしきい値に達するまでは、ローカル キャッシュにより高速な読み取りアクセスが実現されます。 デバイスの使用率が 50% から 60% に達するまでは、そのデバイスからの読み取りアクセスがすべてキャッシュから行われるため、読み取りが高速化されます。 デバイス上の使用領域がこのしきい値を超えると、ローカル ファイルを削除する処理が開始されます。
 
- **制限された帯域幅の使用** - 営業時間のピーク時にネットワークの使用が制限されるように調整されている場合でも、Azure にデータを書き込むことができます。  

## <a name="key-capabilities"></a>主な機能

Data Box Gateway には次の機能があります。

|機能 |説明  |
|---------|---------|
|速度     | 完全に自動化され、高度に最適化されたデータ転送と帯域幅。|
|サポートされるプロトコル     | データ インジェストのために、標準の SMB プロトコルと NFS プロトコルをサポートします。 <br> サポート対象のバージョンについては、「[Data Box Gateway のシステム要件](data-box-gateway-system-requirements.md)」を参照してください。|
|データ アクセス     | デバイスによってデータがクラウドに送信された後は、クラウド API にアクセスすることで直接そのデータに変更を加えることができます。|
|高速アクセス     | 最近使用されたファイルを高速にアクセスするために、デバイス上でローカル キャッシュを行います。|
|オフライン アップロード     | 切断モードでは、オフライン アップロードのシナリオに対応します。|
|データ更新     | ローカル ファイルをクラウドから最新の情報に更新する機能。|
|暗号化    | ローカルでデータを暗号化し、クラウドへのデータ転送を *https* 経由で保護する BitLocker のサポート       |
|回復性     | 組み込まれたネットワークの回復性        |


## <a name="specifications"></a>仕様

Data Box Gateway 仮想デバイスの仕様は次のとおりです。

| 仕様                                          | 説明              |
|---------------------------------------------------------|--------------------------|
| 仮想プロセッサ (コア)   | 最低 4 |
| メモリ  |8 GB 以上|
| 可用性|単一ノード|
| ディスク|OS ディスク:250 GB <br> データ ディスク:最小 2 TB、シン プロビジョニング、SSD を使用する必要があります|
| ネットワーク インターフェイス |1 つ以上の仮想ネットワーク インターフェイス|
| ネイティブのファイル共有プロトコル|SMB および NFS  |
| セキュリティ|デバイスとデータへのアクセスのロックを解除する認証 <br> AES 256 ビット暗号化を使用した移動中のデータの暗号化|
| 管理|ローカル Web UI - 初期セットアップ、診断、およびデバイスの電源管理 <br> Azure portal - Data Box Gateway デバイスの日々 の管理       |

## <a name="components"></a>コンポーネント

Data Box Gateway ソリューションは、Data Box Gateway リソース、Data Box Gateway 仮想デバイス、ローカル Web UI で構成されています。

- **Data Box Gateway 仮想デバイス**: 仮想化環境またはハイパーバイザーにプロビジョニングされた仮想マシンに基づくデバイスで、Azure にデータを送信できます。
    
- **Data Box Gateway リソース** - さまざまな地理的な場所からアクセスできる Web インターフェイスから、Data Box Gateway デバイスを管理できる、Azure portal でのリソース。 デバイス、共有、ユーザー、アラートの表示と管理は、Data Box Gateway リソースを使用して行います。 詳細については、[Azure portal を使用した管理](data-box-gateway-manage-shares.md)の方法に関するページを参照してください。

- **Data Box ローカル Web UI** - ローカル Web UI を使用して、診断の実行、デバイスのシャットダウンと再起動、サポート パッケージの生成、Microsoft サポートへの連絡とサービス要求の提出を行います。 詳細については、[ローカル Web UI を使用した管理](data-box-gateway-manage-access-power-connectivity-mode.md)の方法に関するページを参照してください。

## <a name="region-availability"></a>利用可能なリージョン

Data Box Gateway 物理デバイス、Azure リソース、データ転送先のターゲット ストレージ アカウントは、すべて同じ地域にある必要はありません。

- **リソースの使用可能性** - Data Box Edge リソースを使用できるすべてのリージョンの一覧については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=databox)」をご覧ください。 Data Box Gateway は、Azure Government クラウドにデプロイすることもできます。 詳細については、「[What is Azure Government? (Azure Government とは)](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)」を参照してください。

- **転送先ストレージ アカウント** - データを格納するストレージ アカウントは、すべての Azure 地域で利用できます。

    最適なパフォーマンスを得るには、ストレージ アカウントが Data Box データを格納する地域は、デバイスが配置されている場所の近くでなければなりません。 デバイスから離れた場所にあるストレージ アカウントは、待ち時間が長くなり、パフォーマンスが低下します。


## <a name="next-steps"></a>次の手順

- [Data Box Gateway のシステム要件](data-box-gateway-system-requirements.md)を確認する。
- [Data Box Gateway の制限事項](data-box-gateway-limits.md)を理解する。
- Azure portal で [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) を配置する。

