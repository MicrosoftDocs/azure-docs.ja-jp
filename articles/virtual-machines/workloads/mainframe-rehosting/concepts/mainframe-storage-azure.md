---
title: メインフレーム ストレージを Azure Storage に移動する
description: 非常にスケーラブルな Azure ストレージ リソースにより、メインフレーム ベースの組織は IBM z14 アプリケーションを移行して最新化できます。
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
ms.openlocfilehash: 27f625e7fced5ff710a6fa0e8b2045a8c5434258
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "104954362"
---
# <a name="move-mainframe-storage-to-azure"></a>メインフレーム ストレージを Azure に移動する

Microsoft Azure でメインフレームのワークロードを実行するには、メインフレームの機能の Azure との比較を把握する必要があります。 非常にスケーラブルなストレージ リソースにより、組織は依存しているアプリケーションを破棄することなく最新化を図ることができます。

Azure は、IBM z14 ベースのシステム (この記事の執筆時点で最新モデル) に匹敵するメインフレーム的機能とストレージ容量を提供します。 この記事では、Azure で同等の結果を得る方法について説明します。

## <a name="mainframe-storage-at-a-glance"></a>メインフレーム ストレージの概要

IBM メインフレームのストレージには 2 つの特徴があります。 まず、直接アクセス ストレージ デバイス (DASD) です。 2 番目はシーケンシャル ストレージです。 ストレージを管理するために、メインフレームはデータ ファシリティ ストレージ管理サブシステム (DFSMS) を提供します。 それは、さまざまなストレージ デバイスへのデータ アクセスを管理します。

[DASD](https://en.wikipedia.org/wiki/Direct-access_storage_device) とは、(インメモリでない) 二次記憶装置用の独立したデバイスのことであり、一意のアドレスを使用してデータに直接アクセスすることを可能にします。 もともと、DASD という用語はスピン ディスク、磁気ドラム、またはデータ セルに適用されていました。 しかし、この用語は今では、ソリッドステート ストレージ デバイス (SSD)、ストレージ エリア ネットワーク (SAN)、ネットワーク接続ストレージ (NAS)、および光学式ドライブにも適用できます。 このドキュメントの目的上、DASD はスピン ディスク、SAN、および SSD を指します。

DASD ストレージとは対照的に、メインフレーム上のシーケンシャル ストレージは、開始点からデータがアクセスされた後、1 行ずつ読み取りまたは書き込みが行われるテープ ドライブのようなデバイスを指します。

ストレージ デバイスは通常、ファイバー接続 (FICON) を使用して接続されるか、またはメインフレームの IO バス上で直接アクセスされます。後者では、サーバー上のパーティション間でハイパーバイザーを使用して高速通信を行うための IBM のテクノロジーである [HiperSockets](https://www.ibm.com/support/knowledgecenter/zosbasics/com.ibm.zos.znetwork/znetwork_85.htm) が使用されます。

ほとんどのメインフレーム システムでは、ストレージを 2 つのタイプに分けます。

- *オンライン ストレージ* (ホット ストレージとも呼ばれます) は日常業務に必要です。 通常、この目的には DASD ストレージが使用されます。 ただし、毎日のテープ バックアップ (論理または物理) などのシーケンシャル ストレージもこの目的に使用できます。

- *アーカイブ ストレージ*(コールド ストレージとも呼ばれます) は、特定の時点でマウントされていることの保証がありません。 代わりに、必要に応じてマウントおよびアクセスされます。 アーカイブ ストレージは、多くの場合、ストレージ用のシーケンシャル テープ バックアップ (論理または物理) を使用して実装されます。

## <a name="mainframe-versus-io-latency-and-iops"></a>メインフレームと IO 待機時間および IOPS

メインフレームは多くの場合、ハイ パフォーマンス IO と短い IO 待機時間が求められるアプリケーションに使用されます。 これは、IO デバイスへの FICON 接続と HiperSockets を使用して行うことができます。 HiperSockets を使用してアプリケーションおよびデバイスをメインフレームの IO チャネルに直接接続すると、マイクロ秒単位の待機時間を実現できます。

## <a name="azure-storage-at-a-glance"></a>Azure ストレージの概要

Azure の [IaaS](https://azure.microsoft.com/overview/what-is-iaas/) (サービスとしてのインフラストラクチャ) ストレージ オプションは、メインフレームに匹敵する容量を提供します。

マイクロソフトは、Azure でホストされるアプリケーションのためにペタバイト相当のストレージを提供し、お客様には複数のストレージ オプションがあります。 これらは、ハイ パフォーマンスのための SSD ストレージから、大容量ストレージおよびアーカイブのための低コスト BLOB ストレージまで、多岐にわたります。 また Azure は、ストレージのデータ冗長性オプションを提供しますが、これはメインフレーム環境でのセットアップにはさらに手間がかかるものです。

次の表にまとめるように、Azure ストレージは [Azure Disks](../../../managed-disks-overview.md)、[Azure Files](../../../../storage/files/storage-files-introduction.md)、および [Azure Blobs](../../../../storage/blobs/storage-blobs-overview.md) として提供されます。 [それぞれを使用する状況](../../../../storage/common/storage-introduction.md)について詳しく説明します。

<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>Type</th><th>説明</th><th>使用目的:</th></tr>
</thead>
<tbody>
<tr><td>Azure Files
</td>
<td>
格納されているファイルにどこからでもアクセスできる <a href="/rest/api/storageservices/file-service-rest-api">REST</a> インターフェイス、SMB インターフェイス、クライアント ライブラリを備えています。
</td>
<td><ul>
<li>アプリケーションでネイティブ ファイル システム API を使用して、Azure で稼働している他のアプリケーションとの間でデータを共有している場合に、アプリケーションをクラウドにリフト アンド シフトする。</li>
<li>多くの VM からアクセスする必要がある開発ツールやデバッグ ツールを格納する。</li>
</ul>
</td>
</tr>
<tr><td>Azure BLOB
</td>
<td>大規模な非構造化データをブロック BLOB に格納してアクセスできる <a href="/rest/api/storageservices/blob-service-rest-api">REST</a> インターフェイスとクライアント ライブラリを備えています。 エンタープライズ ビッグ データ分析ソリューション用の <a href="/azure/storage/blobs/data-lake-storage-introduction">Azure Data Lake Storage Gen2</a> もサポートされています。
</td>
<td><ul>
<li>アプリケーションでストリーミングとランダム アクセスのシナリオに対応する。</li>
<li>どこからでもアプリケーション データにアクセスする。</li>
<li>Azure 上にエンタープライズ データ レイクを構築し、ビッグ データ分析を実行する。</li>
</ul></td>
</tr>
<tr><td>Azure ディスク
</td>
<td>アタッチされた仮想ハード ディスクにデータを永続的に格納し、アクセスできる <a href="/rest/api/compute/disks">REST</a> インターフェイスとクライアント ライブラリを備えています。
</td>
<td><ul>
<li>永続ディスクに対しネイティブ ファイル システム API を使用してデータの読み取りと書き込みを行うアプリケーションをリフト アンド シフトする。</li>
<li>外部 (ディスクがアタッチされている VM の外) からのアクセスが不要なデータを格納する。</li>
</ul></td>
</tr>
</tbody>
</table>
<!-- markdownlint-enable MD033 -->

## <a name="azure-hot-online-and-cold-archive-storage"></a>Azure のホット (オンライン) ストレージとコールド (アーカイブ) ストレージ

特定のシステムのストレージのタイプは、ストレージ サイズ、スループット、IOPS など、システムの要件によって異なります。 メインフレーム上の DASD タイプのストレージの場合、Azure 上のアプリケーションは通常、Azure Disks ドライブ ストレージを代わりに使用します。 メインフレームのアーカイブ ストレージの場合、Azure では BLOB ストレージが使用されます。

SSD は Azure で最高のストレージ パフォーマンスを提供します。 (このドキュメントの執筆時点では) 次のオプションが利用できます。

| Type         | サイズ           | IOPS                  |
|--------------|----------------|-----------------------|
| Ultra SSD    | 4 GB ～ 64 TB  | 1,200 ～ 160,000 IOPS |
| Premium SSD  | 32 GB ～ 32 TB | 12 ～ 15,000 IOPS     |
| Standard SSD | 32 GB ～ 32 TB | 12 ～ 2,000 IOPS      |

BLOB ストレージは、Azure 上で最大のストレージ容量を提供します。 ストレージ サイズに加えて、Azure はマネージド ストレージとアンマネージド ストレージの両方を提供します。 マネージド ストレージでは、基になるストレージ アカウントの管理は Azure が受け持ちます。 アンマネージド ストレージでは、ユーザーの責任で、ストレージ要件を満たす適切なサイズの Azure ストレージ アカウントをセットアップします。

## <a name="next-steps"></a>次のステップ

- [メインフレーム移行](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Azure 仮想マシンでのメインフレーム リホスト](../overview.md)
- [メインフレーム コンピューティングを Azure に移行する](mainframe-compute-Azure.md)
- [Azure BLOB、Azure Files、Azure ディスクの使い分け](../../../../storage/common/storage-introduction.md)
- [Azure VM ワークロード向けの Standard SSD マネージド ディスク](../../../disks-types.md#standard-ssd)

### <a name="ibm-resources"></a>IBM リソース

- [IBM Z 上の Parallel Sysplex](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS and the Coupling Facility: Beyond the Basics](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Db2 pureScale 機能のインストールに必要なユーザーを作成する](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt - インスタンス コマンドを作成する](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Db2 pureScale クラスター化データベース ソリューション](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [メインフレーム アプリケーション用の Microsoft Azure Government クラウド](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft と FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>その他の移行リソース

- [Azure 仮想データ センターのリフト アンド シフト ガイド](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://glusterdocs.readthedocs.io/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)