---
title: Azure Storage でリージョン間持久性を実現する geo 冗長ストレージ (GRS) | Microsoft Docs
description: geo 冗長ストレージ (GRS) によって、何百キロも離れた 2 つのリージョン間でデータがレプリケートされます。 GRS は、データセンター内のハードウェア障害だけでなく地域の災害からも保護できます。
services: storage
author: tolandmike
ms.service: storage
ms.topic: article
ms.date: 10/20/2018
ms.author: jeking
ms.subservice: common
ms.openlocfilehash: 2dc409743ce94ecb73e351b839a5a2fb09eadab2
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2019
ms.locfileid: "55512110"
---
# <a name="geo-redundant-storage-grs-cross-regional-replication-for-azure-storage"></a>geo 冗長ストレージ (GRS): Azure Storage のリージョン間レプリケーション
[!INCLUDE [storage-common-redundancy-GRS](../../../includes/storage-common-redundancy-grs.md)]

## <a name="read-access-geo-redundant-storage"></a>読み取りアクセス geo 冗長ストレージ
読み取りアクセス geo 冗長ストレージ (RA-GRS) は、ストレージ アカウントの可用性を最大化します。 RA-GRS は、2 つのリージョンにまたがる geo レプリケーションだけでなく、セカンダリ ロケーションにあるデータに対する読み取り専用アクセスを提供します。

セカンダリ リージョンのデータに対して読み取り専用アクセスを有効にすると、ご利用のストレージ アカウントのプライマリ エンドポイントだけでなく、セカンダリ エンドポイントのデータも使用できます。 セカンダリ エンドポイントはプライマリ エンドポイントと似ていますが、アカウント名に `–secondary` というサフィックスが追加されます。 たとえば、Blob service のプライマリ エンドポイントが `myaccount.blob.core.windows.net` の場合、セカンダリ エンドポイントは `myaccount-secondary.blob.core.windows.net` になります。 ストレージ アカウントのアクセス キーは、プライマリ エンドポイントとセカンダリ エンドポイントの両方で同じです。

RA-GRS 使用時の考慮事項:

* RA-GRS を使うときは、通信対象のエンドポイントをアプリケーションで管理する必要があります。
* 非同期レプリケーションには遅延が伴うため、データをプライマリ リージョンから復旧できないと、セカンダリ リージョンにまだレプリケートされていない変更は失われる可能性があります。
* ストレージ アカウントの最後の同期時刻を確認できます。 最後の同期時刻は、GMT 日付/時刻の値です。 最後の同期時刻より前にプライマリに対して書き込まれたデータはすべて、セカンダリ ロケーションに対して正常に書き込まれています。つまり、これらのデータは、セカンダリ ロケーションから読み取ることができます。 最後の同期時刻より後にプライマリに対して書き込まれたデータに関しては、読み取りできる場合とできない場合とがあります。 この値を、[Azure Portal](https://portal.azure.com/)、[Azure PowerShell](storage-powershell-guide-full.md)、または Azure Storage クライアント ライブラリの 1 つを使用して、クエリできます。
* セカンダリ リージョンへの GRS または RA-GRS アカウントのアカウント フェールオーバー (プレビュー) を開始する場合、そのアカウントへの書き込みアクセスは、フェールオーバーが完了した後に復元されます。 詳細については、「[ディザスター リカバリーとストレージ アカウントのフェールオーバー (プレビュー)](storage-disaster-recovery-guidance.md)」を参照してください。
* RA-GRS は高可用性を目的として作られています。 スケーラビリティのガイダンスについては、[パフォーマンス チェックリスト](storage-performance-checklist.md)をご覧ください。
* RA-GRS を使用して高可用性を確保するためのお勧めの設計方法については、「[RA-GRS を使用した高可用性アプリケーションの設計](storage-designing-ha-apps-with-ragrs.md)」を参照してください。

## <a name="what-is-the-rpo-and-rto-with-grs"></a>GRS の RPO や RTO とは何ですか。

**Recovery Point Objective (RPO: 目標復旧ポイント):** GRS と RA-GRS のストレージ サービスでは、データがプライマリ ロケーションからセカンダリ ロケーションに非同期的に geo レプリケートされます。 プライマリ リージョンが使用不能になった場合は、セカンダリ リージョンへのアカウント フェールオーバー (プレビュー) を実行できます。 フェールオーバーを開始すると、geo レプリケートされていない最近の変更が失われる可能性があります。 データが失われる可能性がある分数は、RPO と呼ばれます。 RPO は、データを復旧できる対象の時点を示します。 現在、geo レプリケーションの所要時間を規定する SLA はありませんが、Azure Storage では RPO を 15 分未満とするのが一般的です。

**Recovery Time Objective (RTO: 目標復旧時間):** RTO は、フェールオーバーを実行してストレージ アカウントをオンラインに戻すまでにかかる時間の指標です。 フェールオーバーの所要時間には、次のアクションが含まれます。

   * プライマリからセカンダリ リージョンへのストレージ アカウントのフェールオーバーが開始されるまでの時間。
   * Azure がプライマリ DNS エントリの参照先をセカンダリ ロケーションに変更してフェールオーバーを実行するのに必要な時間。

## <a name="paired-regions"></a>ペアになっているリージョン 
ストレージ アカウントの作成時に、アカウントのプライマリ リージョンを選択します。 ペアのセカンダリ リージョンはプライマリ リージョンに基づいて決定され、変更することはできません。 Azure でサポートされているリージョンに関する最新の情報については、「[ビジネス継続性とディザスター リカバリー (BCDR): Azure のペアになっているリージョン](../../best-practices-availability-paired-regions.md)」を参照してください。

## <a name="see-also"></a>関連項目
- [Azure Storage のレプリケーション](storage-redundancy.md)
- [ローカル冗長ストレージ (LRS):Azure Storage の低コストのデータ冗長性](storage-redundancy-lrs.md)
- [ゾーン冗長ストレージ (ZRS): 高可用 Azure Storage アプリケーション](storage-redundancy-zrs.md)
