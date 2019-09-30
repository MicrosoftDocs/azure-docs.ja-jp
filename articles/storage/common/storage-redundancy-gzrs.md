---
title: geo ゾーン冗長ストレージ (GZRS) (プレビュー) を使用して高可用性 Azure Storage アプリケーションを構築する | Microsoft Docs
description: geo ゾーン冗長ストレージ (GZRS) では、ゾーン冗長ストレージ (ZRS) の高可用性と、geo 冗長ストレージ (GRS) によって提供されるリージョン障害からの保護を融合させます。 GZRS ストレージ アカウント内のデータは、プライマリ リージョンの Azure 可用性ゾーン間でレプリケートされ、リージョンの災害から保護するためにセカンダリ地理的リージョンにもレプリケートされます。
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/13/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 321866279e076bfa77d1892e64deaf4b16c08366
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300647"
---
# <a name="build-highly-available-azure-storage-applications-with-geo-zone-redundant-storage-gzrs-preview"></a>geo ゾーン冗長ストレージ (GZRS) (プレビュー) を使用して高可用性 Azure Storage アプリケーションを構築する

geo ゾーン冗長ストレージ (GZRS) (プレビュー) では、[ゾーン冗長ストレージ (ZRS)](storage-redundancy-zrs.md) の高可用性と、[geo 冗長ストレージ (GRS)](storage-redundancy-grs.md) によって提供されるリージョン障害からの保護を融合させます。 GZRS ストレージ アカウント内のデータは、プライマリ リージョンの 3 つの [Azure 可用性ゾーン](../../availability-zones/az-overview.md)間でレプリケートされ、リージョンの災害から保護するためにセカンダリ リージョンにもレプリケートされます。 各 Azure リージョンは、同じ geo 内の別のリージョンと組み合わせて、リージョン ペアにして使用します。 詳細と例外については、[ドキュメント](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)をご覧ください。

GZRS ストレージ アカウントを使用すると、可用性ゾーンが使用できなくなったり、回復できなくなった場合に、引き続きデータの読み取りと書き込みを行うことができます。 さらに、リージョン全体の障害が発生した場合や、プライマリ リージョンを回復できない災害が発生した場合にも、データは保持されます。 GZRS は、指定された 1 年間にわたって、オブジェクトの 99.99999999999999% (シックスティーン ナイン) 以上の持続性を提供するように設計されています。 GZRS では、LRS、ZRS、GRS、または RA-GRS と同じ [スケーラビリティ ターゲット](storage-scalability-targets.md)も提供されます。 プライマリ リージョンで災害が発生した場合にアプリケーションでデータを読み取れるようにする必要がある場合は、読み取りアクセス geo ゾーン冗長ストレージ (RA-GZRS) を使用して、必要に応じてセカンダリ リージョンのデータに対する読み取りアクセスを有効にすることができます。

一貫性、持続性、高可用性、優れたパフォーマンス、災害復旧のための回復性を必要とするアプリケーションに対しては、Microsoft では GZRS を使用することをお勧めします。 リージョンの災害が発生した場合のセカンダリ リージョンへの読み取りアクセスのセキュリティを強化するには、ストレージ アカウントに対して RA-GZRS を有効にします。

## <a name="about-the-preview"></a>プレビューについて

GZRS と RA-GZRS は、汎用 v2 ストレージ アカウントでのみサポートされます。 ストレージ アカウントの種類の詳細については、「 [Azure Storage アカウントの概要](storage-account-overview.md)」を参照してください。 GZRS と RA-GZRS では、ブロック BLOB、ページ BLOB (VHD ディスクではない)、ファイル、テーブル、およびキューがサポートされます。

GZRS と RA-GZRS は現在、次のリージョンにおいてプレビューで利用できます。

- 北ヨーロッパ
- 西ヨーロッパ
- 米国東部
- 米国東部 2
- 米国中部

Microsoft では引き続き、他の Azure リージョンでも GZRS と RA-GZRS を有効にしていきます。 サポートされているリージョンについては、 [Azure サービスの更新情報](https://azure.microsoft.com/updates/) に関するページを定期的に確認してください。

プレビュー価格については、 [BLOB](https://azure.microsoft.com/pricing/details/storage/blobs)、 [Files](https://azure.microsoft.com/pricing/details/storage/files/)、 [Queues](https://azure.microsoft.com/pricing/details/storage/queues/)、 [Tables](https://azure.microsoft.com/pricing/details/storage/tables/)の GZRS プレビュー価格に関する説明を参照してください。

> [!IMPORTANT]
> Microsoft では、運用環境ワークロードにはプレビュー機能を使用しないことをお勧めします。

## <a name="how-gzrs-and-ra-gzrs-work"></a>GZRS と RA-GZRS のしくみ

GZRS または RA-GZRS が有効になっているストレージ アカウントにデータが書き込まれると、そのデータはまず、プライマリ リージョンの 3 つの可用性ゾーン間で同期的にレプリケートされます。 その後、データは、数百マイル離れた 2 番目のリージョンに非同期的にレプリケートされます。 データは、セカンダリ リージョンに書き込まれると、[ローカル冗長ストレージ (LRS)](storage-redundancy-lrs.md) を使用して、そのリージョン内でさらに 3 回、同期的にレプリケートされます。

> [!IMPORTANT]
> 非同期レプリケーションでは、データがプライマリ リージョンに書き込まれる時間から、それがセカンダリ リージョンにレプリケートされるまでの遅延が伴います。 地域的な災害が発生した場合に、データをプライマリ リージョンから復旧できないと、セカンダリ リージョンにまだレプリケートされていない変更が失われる可能性があります。

ストレージ アカウントを作成するときは、そのアカウントのデータをレプリケートする方法を指定し、そのアカウントのプライマリ リージョンも指定します。 geo レプリケートされたアカウントのペアになっているセカンダリ リージョンは、プライマリ リージョンに基づいて決定され、変更することはできません。 Azure でサポートされているリージョンに関する最新情報については、「 [ビジネス継続性とディザスター リカバリー (BCDR): Azure のペアになっているリージョン](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)」を参照してください。 GZRS または RA-GZRS を使用したストレージ アカウントの作成については、「[ストレージアカウントの作成](storage-quickstart-create-account.md)」を参照してください。

### <a name="use-ra-gzrs-for-high-availability"></a>高可用性を確保するために RA-GZRS を使用する

ストレージ アカウントに対して RA-GZRS を有効にすると、ストレージ アカウントのプライマリ エンドポイントからだけでなく、セカンダリ エンドポイントからもデータを読み取ることができます。 セカンダリ エンドポイントでは、アカウント名にサフィックス *–secondary* が追加されます。 たとえば、Blob service のプライマリ エンドポイントが `myaccount.blob.core.windows.net` の場合、セカンダリ エンドポイントは `myaccount-secondary.blob.core.windows.net` になります。 ストレージ アカウントのアクセス キーは、プライマリ エンドポイントとセカンダリ エンドポイントの両方で同じです。

リージョン障害が発生した場合に RA-GZRS を利用するには、このシナリオを処理するようにアプリケーションを事前に設計する必要があります。 アプリケーションではプライマリ エンドポイントに対する読み取りと書き込みを行う必要がありますが、プライマリ リージョンが使用できなくなった場合は、セカンダリ エンドポイントを使用するように切り替える必要があります。 RA-GRS を使用した高可用性を確保するための設計に関するガイダンスについては、 [RA-GZRS または RA-GRS を使用した高可用性アプリケーションの設計](https://docs.microsoft.com/en-us/azure/storage/common/storage-designing-ha-apps-with-ragrs)に関するページを参照してください。

データはセカンダリ リージョンに非同期的にレプリケートされるため、セカンダリ リージョンは多くの場合、プライマリ リージョンより遅れています。 セカンダリ リージョンにレプリケートされた書き込み操作を特定するために、アプリケーションでは、ストレージ アカウントの最終同期時刻が確認されます。 最終同期時刻より前にプライマリ リージョンに書き込まれたすべての書き込み操作は、セカンダリ リージョンに正常にレプリケートされています。つまり、セカンダリ リージョンからの読み取りが可能です。 最終同期時刻の後にプライマリ リージョンに書き込まれた書き込み操作は、セカンダリ リージョンにレプリケートされている場合とそうでない場合があります。つまり、読み取り操作で使用できない場合があります。

**[最終同期時刻]** プロパティの値は、Azure PowerShell、Azure CLI、または Azure Storage クライアント ライブラリのいずれかを使用してクエリできます。 **[最終同期時刻]** プロパティは、GMT 日付/時刻の値です。

RA-GZRS を使用したパフォーマンスとスケーラビリティに関するその他のガイダンスについては、「[Microsoft Azure Storage のパフォーマンスとスケーラビリティに対するチェック リスト](storage-performance-checklist.md)」を参照してください。

### <a name="availability-zone-outages"></a>可用性ゾーンの障害

プライマリ リージョンの可用性ゾーンに影響を与える障害が発生した場合、アプリケーションでは引き続き、そのリージョンの他の可用性ゾーンを使用して、ストレージ アカウントに対する読み取りと書き込みをシームレスに行うことができます。 Microsoft では、GZRS または ZRS を使用する場合は、引き続き一時的な障害の処理の方法に従うことをお勧めします。 これらの方法には、指数バックオフを使用した再試行ポリシーの実行などがあります。

可用性ゾーンが使用できなくなった場合、Azure では DNS の再指定などのネットワークの更新を行います。 このような更新は、更新が完了する前にデータにアクセスしている場合、アプリケーションに影響を与える可能性があります。

### <a name="regional-outages"></a>リージョン障害

障害がプライマリ リージョン全体に影響を与える場合、Microsoft ではまず、プライマリ リージョンの復元を試みます。 復元できない場合、Microsoft では、セカンダリ リージョンが新しいプライマリ リージョンになるように、セカンダリ リージョンにフェールオーバーします。 ストレージ アカウントで RA-GZRS が有効になっている場合、このシナリオ用に設計されたアプリケーションでは、フェールオーバーを待機している間に、セカンダリ リージョンから読み取ることができます。 ストレージ アカウントで RA-GZRS が有効になっていない場合、フェールオーバーが完了するまで、アプリケーションではセカンダリから読み取ることはできません。

> [!NOTE]
> GZRS と RA-GZRS は現在、米国東部リージョンでのみ、プレビュー段階にあります。 米国東部 2 ではお客様が管理するアカウントのフェールオーバー (プレビュー) はまだご利用いただけません。そのため、現在、お客様は GZRS および RA-GZRS アカウントを使用してアカウントのフェールオーバー イベントを管理することはできません。 プレビュー期間中は、GZRS および RA-GZRS アカウントに影響するフェールオーバー イベントはすべて Microsoft によって管理されます。

データはセカンダリ リージョンに非同期的にレプリケートされるため、プライマリ リージョンに影響を与える障害が発生すると、プライマリ リージョンを復旧できない場合にデータが失われる可能性があります。 プライマリ リージョンへの最新の書き込みと、セカンダリ リージョンへの最後の書き込みとの間隔は、回復ポイントの目標 (RPO) と呼ばれます。 RPO は、データを復旧できる対象の時点を示します。 現在、データをセカンダリ リージョンにレプリケートするのにかかる時間に関する SLA はありませんが、Azure Storage では通常、RPO は 15 分未満となります。

回復時刻の目標 (RTO) は、フェールオーバーを実行し、ストレージ アカウントをオンラインに戻すのにかかる時間の測定値です。 この測定値は、セカンダリの場所を指すようにプライマリ DNS エントリを変更することによって、フェールオーバーを実行するために Azure で必要な時間を示します。

## <a name="migrate-a-storage-account-to-gzrs-or-ra-gzrs"></a>ストレージ アカウントを GZRS または RA-GZRS に移行する

既存のストレージ アカウントを GZRS または RA-GZRS に移行することができます。 既存の ZRS アカウントから GZRS または RA-GZRS への移行は簡単ですが、LRS、GRS、または RA-GRS アカウントからの移行はもう少し複雑です。 次のセクションでは、両方の場合の移行方法について説明します。

### <a name="migrating-from-a-zrs-account"></a>ZRS アカウントからの移行

既存の ZRS アカウントを RA-GZRS に変換するには、[Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) コマンドレットを使用してアカウントの SKU を変更します。 プレースホルダー値をお客様独自の値に置き換えてください。

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group> -AccountName <storage-account> -SkuName "Standard_RAGZRS"
```

### <a name="migrating-from-an-lrs-grs-or-ra-grs-account"></a>LRS、GRS、または RA-GRS アカウントからの移行

LRS、GRS、または RA-GRS アカウントから GZRS または RA-GZRS に移行する場合、次の 2 つのオプションがあります。

- 既存のアカウントから新しい GZRS または RA-GZRS アカウントにデータを手動でコピーまたは移動することができます。
- ライブ マイグレーションを要求することができます。

#### <a name="perform-a-manual-migration"></a>手動の移行を実行する

特定の日付までに移行を完了する必要がある場合は、手動の移行を実行することを検討してください。 手動の移行はライブ マイグレーションよりも高い柔軟性を備えています。 手動の移行では、タイミングを制御できます。

既存のアカウントから GZRS または RA-GZRS アカウントにデータを手動で移行するには、データを効率的にコピーできるツールを使用します。 次に例をいくつか示します。

- AzCopy などのユーティリティや信頼できるサードパーティ製のツールを使用します。 Azcopy については、「[AzCopy を使ってみる](storage-use-azcopy-v10.md)」を参照してください。
- Hadoop や HDInsight を使い慣れている場合は、ソースとターゲットの両方のストレージ アカウントをクラスターにアタッチします。 次に、DistCp などのツールを使用してデータ コピー プロセスを並列化します。
- Azure Storage クライアント ライブラリのいずれかを使用して、独自のツールをビルドします。

#### <a name="perform-a-live-migration"></a>ライブ マイグレーションを実行する

手動の移行ではアプリケーションのダウンタイムが発生することがあります。 アプリケーションが高可用性を必要とする場合、Microsoft はライブ マイグレーションのオプションも提供します。 ライブ マイグレーションは、ダウンタイムのないインプレース移行です。

ライブ マイグレーション中は、ソースとターゲットのストレージ アカウント間でのデータの移行時にストレージ アカウントを使用できます。 ライブ マイグレーション プロセス中は、アカウントで引き続き、持続性と可用性の SLA が満たされます。 ライブ マイグレーションによってダウンタイムやデータ損失が発生することはありません。

GZRS/RA-GZRS がサポートされるのは、汎用 v2 アカウントでのみです。そのため、GZRS/RA-GZRS へのライブ マイグレーションの要求を送信する前に、アカウントを汎用 v2 にアップグレードする必要があります。 詳細については、「 [Azure ストレージ アカウントの概要](https://docs.microsoft.com/azure/storage/common/storage-account-overview) 」と「 [汎用 v2 ストレージ アカウントにアップグレードする](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade)」を参照してください。

移行が完了すると、ストレージ アカウントのレプリケーション設定が、**geo ゾーン冗長ストレージ (GZRS)** または**読み取りアクセス geo ゾーン冗長ストレージ (RA-GZRS)** に更新されます。 サービス エンドポイント、アクセス キー、Shared Access Signatures (SAS)、およびその他のアカウント構成オプションは変更されず、そのままです。

ライブ マイグレーションでは次の制限に注意してください。

- Microsoft はお客様のライブ マイグレーションの要求に速やかに対応しますが、ライブ マイグレーションがいつ完了するかについての保証はありません。 データを特定の日付までに GZRS または RA-GZRS に移行する必要がある場合、Microsoft では代わりに手動の移行を実行することをお勧めします。 一般的に、アカウントで保存しているデータが多いほど、データの移行には時間がかかります。
- アカウントにはデータが含まれている必要があります。
- 同じリージョン内のデータのみ移行できます。
- Standard ストレージ アカウントの種類のみがライブ マイグレーションをサポートします。 Premium ストレージ アカウントは手動で移行する必要があります。
- GZRS または RA-GZRS アカウントから LRS、GRS、または RA-GRS アカウントへのライブ マイグレーションはサポートされていません。 新しいストレージ アカウントか既存のストレージ アカウントにデータを手動で移動する必要があります。
- RA-GRS から RA-GZRS へのライブ マイグレーションを要求できます。 しかし、RA-GRS から GZRS への移行はサポートされていません。 この場合は、RA-GZRS へのライブ マイグレーションを要求してから、GZRS を使用するようにストレージ アカウントを手動で変換する必要があります。
- マネージド ディスクでは LRS のみがサポートされており、GZRS や RA-GZRS に移行することはできません。 可用性セットとの統合については、「 [Azure マネージド ディスクの概要](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#integration-with-availability-sets)」を参照してください。
- Standard SSD マネージド ディスクのスナップショットとイメージを、Standard HDD ストレージに格納できます。また、 [LRS、ZRS、GZRS、および RA-GZRS オプションから選択](https://azure.microsoft.com/pricing/details/managed-disks/)できます。
- 大きなファイル共有を含むアカウントは、GZRS ではサポートされていません。

ライブ マイグレーションを要求するには、 [Azure portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) を使用します。 ポータルから、GZRS または RA-GZRS に移行するストレージ アカウントを選択し、次の手順に従います。

1.  **[新しいサポート リクエスト]** を選択します。
2. アカウント情報に基づいて、 **[基本]**  に入力します。  **[サービス] ** セクションで、 **[Storage Account Management]\(ストレージ アカウントの管理\)**  を選択し、移行するアカウントを指定します。
3.  **[次へ]** を選択します。
4.  **[問題]**  セクションで、以下の値を指定します。
    - **[重大度]** :既定値をそのまま使用します。
    - **[問題の種類]** : **[データ移行]** を選択します。
    - **[カテゴリ]** : **[Migrate to (RA-)GZRS within a region]\(リージョン内の (RA-)GZRS への移行\)** を選択します。
    - **[タイトル]** : **(RA-)GZRS アカウント移行**などのわかりやすいタイトルを入力します。
    - **[詳細]** : **[詳細]**  ボックスには、「\_\_ リージョンで [LRS、GRS] から GZRS に移行する」などの詳細情報を入力します。 または、「\_\_ リージョンで [LRS、RA-GRS] から RA-GZRS に移行する」と入力します。
5.  **[次へ]** を選択します。
6.  **[連絡先情報]**  ブレードの連絡先情報が正しいことを確認します。
7.  **[作成]** を選択します。

サポート担当者から連絡があり、サポートを受けることができます。

## <a name="see-also"></a>関連項目

- [Azure Storage のレプリケーション](https://docs.microsoft.com/azure/storage/common/storage-redundancy)
- [ローカル冗長ストレージ (LRS):Azure Storage の低コストのデータ冗長性](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs)
- [ゾーン冗長ストレージ (ZRS): 高可用 Azure Storage アプリケーション](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) 
