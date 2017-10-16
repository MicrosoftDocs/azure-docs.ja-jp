---
title: "Azure Storage のデータ レプリケーション | Microsoft Docs"
description: "Microsoft Azure Storage アカウント内のデータは、持続性と高可用性を保証するため、レプリケートされます。 レプリケーション オプションには、ローカル冗長ストレージ (LRS)、ゾーン冗長ストレージ (ZRS)、geo 冗長ストレージ (GRS)、読み取りアクセス geo 冗長ストレージ (RA-GRS) などがあります。"
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 86bdb6d4-da59-4337-8375-2527b6bdf73f
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: tamram
ms.openlocfilehash: f16692d1241082dddb9f8687d18f6bf1c1aba2eb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-storage-replication"></a>Azure Storage のレプリケーション
Microsoft Azure ストレージ アカウント内のデータは、持続性と高可用性を保証するため、常にレプリケートされています。 レプリケーションによりデータは同じデータ センター内か、2 番目のデータ センターにコピーされます。このコピー先は、選んだレプリケーション オプションによって変わります。 レプリケーションにより、データが保護され、一時的なハードウェアの障害が発生した際にアプリケーションのアップタイムが維持されます。 データが 2 番目のデータ センターにレプリケートされた場合、データは 1 次拠点で発生した致命的なエラーから保護されます。

レプリケーションにより、障害が発生しても、ストレージ アカウントは[ストレージのサービス レベル アグリーメント (SLA)](https://azure.microsoft.com/support/legal/sla/storage/) を満たすことができます。 Azure Storage の持続性と可用性の保証については、SLA をご覧ください。

ストレージ アカウントを作成するときは、次のレプリケーション オプションのいずれかを選択できます。

* [ローカル冗長ストレージ (LRS)](#locally-redundant-storage)
* [ゾーン冗長ストレージ (ZRS)](#zone-redundant-storage)
* [geo 冗長ストレージ (GRS)](#geo-redundant-storage)
* [読み取りアクセス geo 冗長ストレージ (RA-GRS)](#read-access-geo-redundant-storage)

読み取りアクセス geo 冗長ストレージ (RA-GRS) は、ストレージ アカウントを作成するときの既定のオプションです。

次の表は、LRS、ZRS、GRS、RA-GRS の違いについて、簡単に概要を示したものです。以降のセクションでは、それぞれのレプリケーションの種類について詳細に説明します。

| レプリケーションの方法 | LRS | ZRS | GRS | RA-GRS |
|:--- |:--- |:--- |:--- |:--- |
| 複数のデータセンター間でのデータのレプリケート |いいえ |可能  |あり |あり |
| 1 次拠点に加えて 2 次拠点からもデータの読み取り可能 |いいえ |いいえ |いいえ |はい |
| 個別のノードで保持されるデータ コピーの数 |3 |3 |6 |6 |

さまざまな冗長オプションの料金情報については、「 [Azure Storage 料金](https://azure.microsoft.com/pricing/details/storage/) 」をご覧ください。

> [!NOTE]
> Premium Storage でサポートされるのは、ローカル冗長ストレージ (LRS) だけです。 Premium Storage については、「 [Premium Storage: Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ](../storage-premium-storage.md)」をご覧ください。
>

## <a name="locally-redundant-storage"></a>ローカル冗長ストレージ
[!INCLUDE [storage-common-redundancy-LRS](../../../includes/storage-common-redundancy-LRS.md)]

## <a name="zone-redundant-storage"></a>ゾーン冗長ストレージ
ゾーン冗長ストレージ (ZRS) は、LRS と同様に 3 つのレプリカを格納するだけでなく、1 つまたは 2 つのリージョン内のデータセンター間で非同期的にデータをレプリケートするので、LRS よりも高い持続性を提供します。 ZRS に格納されたデータは、プライマリ データセンターが使用不能または回復不能な場合でも、持続可能です。
ZRS を使用する予定のお客様は、次の点に注意してください。

* ZRS は、汎用ストレージ アカウントのブロック BLOB に対してのみ使うことができ、バージョン 2014-02-14 以降のストレージ サービスでのみサポートされます。
* 非同期レプリケーションには遅延が伴うため、地域的な災害が発生した場合にデータをプライマリから復旧できないと、セカンダリにまだレプリケートされていない変更は失われる可能性があります。
* Microsoft がセカンダリへのフェールオーバーを開始するまで、レプリカは利用できない場合があります。
* ZRS アカウントを後で LRS または GRS に変換することはできません。 同様に、既存の LRS または GRS アカウントを ZRS アカウントに変換することはできません。
* ZRS アカウントには、メトリックまたはログ機能はありません。

## <a name="geo-redundant-storage"></a>geo 冗長ストレージ
[!INCLUDE [storage-common-redundancy-GRS](../../../includes/storage-common-redundancy-GRS.md)]

## <a name="read-access-geo-redundant-storage"></a>読み取りアクセス geo 冗長ストレージ
読み取りアクセス geo 冗長ストレージ (RA-GRS) では、GRS が提供する 2 つのリージョンにまたがるレプリケーションに加えて、2 次拠点のデータにも読み取り専用アクセスを提供することで、ストレージ アカウントの可用性が最大限に発揮されます。

セカンダリ リージョンのデータに対して読み取り専用アクセスを有効にすると、ストレージ アカウントのプライマリ エンドポイントだけでなく、セカンダリ エンドポイントのデータも使用できます。 セカンダリ エンドポイントはプライマリ エンドポイントと似ていますが、アカウント名に `–secondary` というサフィックスが追加されます。 たとえば、Blob service のプライマリ エンドポイントが `myaccount.blob.core.windows.net` の場合、セカンダリ エンドポイントは `myaccount-secondary.blob.core.windows.net` になります。 ストレージ アカウントのアクセス キーは、プライマリ エンドポイントとセカンダリ エンドポイントの両方で同じです。

考慮事項:

* RA-GRS を使うときは、通信対象のエンドポイントをアプリケーションで管理する必要があります。
* 非同期レプリケーションには遅延が伴うため、地域的な災害が発生した場合にデータをプライマリ リージョンから復旧できないと、セカンダリ リージョンにまだレプリケートされていない変更は失われます。
* Microsoft がセカンダリ リージョンへのフェールオーバーを開始した場合、フェールオーバーの完了後、そのデータへの読み取り/書き込みアクセスができるようになります。 詳細については、「[障害復旧のガイダンス](../storage-disaster-recovery-guidance.md)」を参照してください。 
* RA-GRS は高可用性を目的として作られています。 スケーラビリティのガイダンスについては、[パフォーマンス チェックリスト](../storage-performance-checklist.md)をご覧ください。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

<a id="howtochange"></a>
#### <a name="1-how-can-i-change-the-geo-replication-type-of-my-storage-account"></a>1.ストレージ アカウントの geo レプリケーションの種類を変更するにはどうすればよいですか。

   ストレージ アカウントの geo レプリケーションは、LRS、GRS、RA-GRS のいずれかの種類に変更できます。変更は、[Azure Portal](https://portal.azure.com/) または [Azure PowerShell](storage-powershell-guide-full.md) を使用して行うことができるほか、多数存在するストレージ クライアント ライブラリのいずれかを使用してプログラムで行うことができます。 ZRS アカウントを LRS または GRS に変換することはできないことに注意してください。 同様に、既存の LRS または GRS アカウントを ZRS アカウントに変換することはできません。

<a id="changedowntime"></a>
#### <a name="2-will-there-be-any-down-time-if-i-change-the-replication-type-of-my-storage-account"></a>2.ストレージ アカウントのレプリケーションの種類を変更した場合、ダウン タイムは発生しますか。

   いいえ。ダウン タイムは一切発生しません。

<a id="changecost"></a>
#### <a name="3-will-there-be-any-additional-cost-if-i-change-the-replication-type-of-my-storage-account"></a>手順 3.ストレージ アカウントのレプリケーションの種類を変更した場合、追加のコストは発生しますか。

   はい。 ストレージ アカウントを LRS から GRS (または RA-GRS) に変更した場合、プライマリ ロケーションの既存のデータをセカンダリ ロケーションにコピーする際のエグレス トラフィックについて追加料金が発生します。 初回データ コピー後は、プライマリ ロケーションからセカンダリ ロケーションへのデータの geo レプリケーションに関してそれ以上のエグレス料金が発生することはありません。 帯域幅使用料の詳細については、[Azure Storage の価格に関するページ](https://azure.microsoft.com/pricing/details/storage/blobs/)を参照してください。 GRS から LRS に変更した場合、追加のコストは発生しませんが、セカンダリ ロケーションからデータが削除されます。

<a id="ragrsbenefits"></a>
#### <a name="4-how-can-ra-grs-help-me"></a>4.RA-GRS の利点を教えてください。
   
   GRS ストレージでは、プライマリ リージョンのデータが、数百マイル離れたセカンダリ リージョンにレプリケートされます。 これにより、地域的な停電やプライマリ リージョンが復旧できない災害が発生しても、データは保持されます。 RA-GRS ストレージでは、それに加えてセカンダリ ロケーションのデータを読み取る機能が追加されます。 この機能の活用方法について詳しくは、[RA-GRS ストレージを使用した高可用性アプリケーションの設計](../storage-designing-ha-apps-with-ragrs.md)に関するページを参照してください。 

<a id="lastsynctime"></a>
#### <a name="5-is-there-a-way-for-me-to-figure-out-how-long-it-takes-to-replicate-my-data-from-the-primary-to-the-secondary-region"></a>5.プライマリ リージョンからセカンダリ リージョンにデータをレプリケートするのにかかる時間を把握する方法はありますか。
   
   RA-GRS ストレージをご利用の場合、ストレージ アカウントの最後の同期時刻を確認できます。 最後の同期時刻は、GMT の日付/時刻値です。この時刻より前にプライマリに対して書き込まれたデータはすべて、セカンダリ ロケーションに対して正常に書き込まれています。つまり、これらのデータは、セカンダリ ロケーションから読み取ることができます。 最後の同期時刻より後にプライマリに対して書き込まれたデータに関しては、読み取りできる場合とできない場合とがあります。 この値は、[Azure Portal](https://portal.azure.com/) または [Azure PowerShell](storage-powershell-guide-full.md) で照会できるほか、プログラムで REST API またはいずれかのストレージ クライアント ライブラリを使用して照会することができます。 

<a id="outage"></a>
#### <a name="6-how-can-i-switch-to-the-secondary-region-if-there-is-an-outage-in-the-primary-region"></a>6.プライマリ リージョンで障害が発生した場合、セカンダリ リージョンに切り替えるにはどうすればよいですか。
   
   詳細については、「[Azure Storage の停止が発生した場合の対処方法](../storage-disaster-recovery-guidance.md)」の記事を参照してください。

<a id="rpo-rto"></a>
#### <a name="7-what-is-the-rpo-and-rto-with-grs"></a>7.GRS の RPO や RTO とは何ですか。
   
   Recover Point Objective (RPO: 回復ポイントの目標): GRS と RA-GRS のストレージ サービスでは、プライマリ ロケーションのデータがセカンダリ ロケーションに対して非同期的に geo レプリケーションされます。 特定の地域で大規模な災害が発生してフェールオーバーが必要となった場合、geo レプリケーションが済んでいない直近の差分変更は失われる可能性があります。 データ消失が生じる可能性のある時間 (分) を RPO といいます (つまりデータを過去のどの時点まで復旧できるかを表します)。 現在、geo レプリケーションの所要時間を規定する SLA はありませんが、RPO は 15 分未満とするのが一般的です。

   Recovery Time Objective (RTO: 目標復旧時間): フェールオーバーが必要となった場合に、それを実行してストレージ アカウントをオンラインに戻すまでにかかる時間の指標です。 フェールオーバーの所要時間には、次の内容が含まれます。
    * プライマリ ロケーションのデータを復旧できるか、またはフェールオーバーが必要かを Microsoft が調査して判断するまでの時間。
    * アカウントをフェールオーバーする (プライマリ DNS エントリの参照先をセカンダリ ロケーションに変更する) のにかかる時間。

   Microsoft はお客様のデータを保全する重大な責任を果たすために、データを復旧できる可能性がわずかでもあれば、フェールオーバーを先送りしてでも、プライマリ ロケーションのデータを復旧することに専念します。 将来的には、お客様がアカウント レベルでフェールオーバーをトリガーできる API を提供する予定です。そうなれば、お客様が個々に RTO を制御することも可能ですが、現時点ではまだその機能はご利用いただけません。
   
## <a name="next-steps"></a>次のステップ
* [RA-GRS ストレージを使用した高可用性アプリケーションの設計](../storage-designing-ha-apps-with-ragrs.md)
* [Azure Storage の料金](https://azure.microsoft.com/pricing/details/storage/)
* [Azure ストレージ アカウントについて](../storage-create-storage-account.md)
* [Azure Storage のスケーラビリティおよびパフォーマンスのターゲット](storage-scalability-targets.md)
* [Microsoft Azure Storage 冗長オプションと読み取りアクセス geo 冗長ストレージ ](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
* [SOSP ペーパー - Azure Storage: 強力な整合性を備えた高可用クラウド ストレージ サービス](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

