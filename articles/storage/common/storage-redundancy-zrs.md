---
title: ゾーン冗長ストレージ (ZRS) に高可用 Azure Storage アプリケーションを構築する | Microsoft Docs
description: ゾーン冗長ストレージ (ZRS) を利用すると、高可用アプリケーションを簡単に構築できます。 ZRS は、データセンターのハードウェア障害だけでなく、一部の地域の災害からも保護できます。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/28/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: f4e36edf86823453e663ed875c7d5e4ffdc2e524
ms.sourcegitcommit: df7942ba1f28903ff7bef640ecef894e95f7f335
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2019
ms.locfileid: "69016424"
---
# <a name="zone-redundant-storage-zrs-for-building-highly-available-azure-storage-applications"></a>高可用 Azure Storage アプリケーションを構築するためのゾーン冗長ストレージ (ZRS)

[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-zrs.md)]

## <a name="support-coverage-and-regional-availability"></a>サポート範囲とリージョンの可用性

現在、ZRS は標準の汎用 v2 のアカウントの種類をサポートしています。 ストレージ アカウントの種類の詳細については、[Azure Storage アカウントの概要](storage-account-overview.md)に関するページを参照してください。

ZRS は、ブロック BLOB、非ディスク ページ BLOB、ファイル、テーブル、およびキューに使用できます。

ZRS は、次のリージョンで一般公開されています。

- 東南アジア
- 西ヨーロッパ
- 北ヨーロッパ
- フランス中部
- 東日本
- 英国南部
- 米国中部
- 米国東部
- 米国東部 2
- 米国西部 2

Microsoft は、今後も ZRS が有効な Azure リージョンを増やす予定です。 新しいリージョンの情報については、[Azure サービスの更新情報](https://azure.microsoft.com/updates/)に関するページを定期的に参照してください。

**既知の制限事項**

- アーカイブ層は、ZRS アカウントでは現在サポートされていません。 詳細については、「[Azure Blob Storage: ホット、クール、アーカイブ ストレージ層](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers)」を参照してください。
- マネージド ディスクでは ZRS はサポートされていません。 Standard SSD Managed Disks のスナップショットとイメージを、Standard HDD ストレージに保存できます。また、[LRS オプションと ZRS オプションから選択](https://azure.microsoft.com/pricing/details/managed-disks/)できます。

## <a name="what-happens-when-a-zone-becomes-unavailable"></a>ゾーンが利用不可になった場合

データは、ゾーンが使用できなくなった場合でも読み取り操作と書き込み操作の両方にアクセスできます。 一時的な障害の処理の方法に従うことをお勧めします。 これらの方法には、指数バックオフを使用した再試行ポリシーの実行などがあります。

ゾーンが利用不可になると、Azure は DNS の再指定などのネットワークの更新を実行します。 このような更新は、更新が完了する前にデータにアクセスしている場合、アプリケーションに影響を与える可能性があります。

ZRS は、複数のゾーンが永続的に影響を受けるリージョンの災害からデータを保護することはできませんが、 データが一時的に利用できなくなった場合は、そのデータの回復性を提供します。 リージョンの災害から保護するには、geo 冗長ストレージ (GRS) を使用することをお勧めします。 GRS について詳しくは、「[geo 冗長ストレージ (GRS):Azure Storage のリージョン間レプリケーション](storage-redundancy-grs.md)」をご覧ください。

## <a name="converting-to-zrs-replication"></a>ZRS レプリケーションへの変換

LRS、GRS、および RA-GRS 間の移行は簡単です。 アカウントの冗長性の種類を変更するには、Azure portal またはストレージ リソース プロバイダー API を使用します。 その後 Azure は、データを適宜レプリケートします。 

ZRS へのデータ移行には別の戦略が必要です。 ZRS 移行には、リージョン内の 1 つのストレージ スタンプから複数のスタンプへのデータの物理的移動が含まれます。

ZRS に移行するとき、主に 2 つの選択肢があります。 

- 既存のアカウントから新しい ZRS アカウントにデータを手動でコピーまたは移動する。
- ライブ マイグレーションを要求する。

特定の日付までに移行を完了する必要がある場合は、手動の移行を実行することを検討してください。 手動の移行はライブ マイグレーションよりも高い柔軟性を備えています。 手動の移行では、タイミングを制御できます。

手動の移行を実行する場合は、次の選択肢があります。
- AzCopy のような既存のツール、Azure Storage クライアント ライブラリのいずれか、 または信頼できるサード パーティ製ツールを使用します。
- Hadoop または HDInsight に詳しい場合は、ソースと宛先 (ZRS) の両方のアカウントをクラスターにアタッチします。 次に、DistCp などのツールを使用してデータ コピー処理を並列化します。
- Azure Storage クライアント ライブラリのいずれかを使用して、独自のツールをビルドします。

手動の移行ではアプリケーションのダウンタイムが発生することがあります。 アプリケーションが高可用性を必要とする場合、Microsoft はライブ マイグレーションのオプションも提供します。 ライブ マイグレーションは、ダウンタイムのないインプレース移行です。 

ライブ マイグレーション中は、データがソースと宛先のストレージ スタンプ間で移行中にストレージ アカウントを使用できます。 移行プロセス中は、通常と同じレベルの持続性と可用性の SLA を維持できます。

ライブ マイグレーションでは次の制限に注意してください。

- Microsoft はお客様のライブ マイグレーションの要求に速やかに対応しますが、ライブ マイグレーションがいつ完了するかについての保証はありません。 データを特定の日付までに ZRS に移行する必要がある場合は、手動の移行を実行することをお勧めします。 一般的に、アカウントで保存しているデータが多いほど、データの移行には時間がかかります。 
- ライブ マイグレーションは、LRS または GRS のレプリケーションを使用するストレージ アカウントについてのみサポートされます。 自分のアカウントが RA-GRS を使用している場合は、続行する前に、まず自分のアカウントのレプリケーションの種類を LRS または GRS のいずれかに変更する必要があります。 この中間の手順により、移行前に RA GRS によって提供される読み取り専用のセカンダリ エンドポイントが削除されます。
- アカウントにはデータが含まれている必要があります。
- 同じリージョン内のデータのみ移行できます。 ソース アカウントとは異なるリージョンにある ZRS アカウントにデータを移行する場合は、手動の移行を実行する必要があります。
- Standard ストレージ アカウントの種類のみがライブ マイグレーションをサポートします。 Premium ストレージ アカウントは手動で移行する必要があります。
- ZRS から LRS、GRS、RA-GRS にライブ マイグレーションすることはできません。 新しいストレージ アカウントか既存のストレージ アカウントにデータを手動で移動する必要があります。
- マネージド ディスクは LRS にのみ使用できます。ZRS に移行することはできません。 Standard SSD Managed Disks のスナップショットとイメージを、Standard HDD ストレージに保存できます。また、[LRS オプションと ZRS オプションから選択](https://azure.microsoft.com/pricing/details/managed-disks/)できます。 可用性セットとの統合については、[Azure マネージド ディスクの概要](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#integration-with-availability-sets)に関する記事を参照してください。
- アーカイブ データを含む LRS アカウントまたは GRS アカウントは ZRS に移行できません。

ライブ マイグレーションは [Azure サポート ポータル](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)から要求できます。 ポータルから、ZRS に変換するストレージ アカウントを選択します。
1. **[新しいサポート要求]** を選択します
2. アカウント情報に基づき **[基本]** に入力します。 **[サービス]** セクションで、 **[Storage Account Management]\(ストレージ アカウントの管理)** と ZRS に変換するリソースを選択します。 
3. **[次へ]** を選択します。 
4. **[Problem]\(問題)** セクションで以下の値を指定します。 
    - **[重大度]** :既定値をそのまま使用します。
    - **[問題の種類]** : **[データ移行]** を選択します。
    - **[カテゴリ]** : **[Migrate to ZRS within a region]\(リージョン内の ZRS への移行\)** を選択します。
    - **[タイトル]** :**ZRS アカウント移行**などのわかりやすいタイトルを入力します。
    - **[詳細]** : **[詳細]** ボックスには、たとえば、\_\_ リージョンで [LRS、GRS] から ZRS に移行するなどの詳細情報を入力します。 
5. **[次へ]** を選択します。
6. **[連絡先情報]** ブレードの連絡先情報が正しいことを確認します。
7. **作成** を選択します。

サポート担当者はお客様に連絡し、必要なサポートを提供します。

## <a name="live-migration-to-zrs-faq"></a>ZRS のライブ マイグレーションについてよくあるご質問

**移行中はダウンタイムを予定するべきですか。**

移行によってダウンタイムが発生することはありません。 ライブ マイグレーション中、移行元と移行先のストレージ スタンプの間でデータが移行されているとき、引き続きストレージ アカウントを使用できます。 移行プロセス中は、通常と同じレベルの持続性と可用性の SLA を維持できます。

**移行でデータが失われることはありますか。**

移行でデータが失われることはありません。 移行プロセス中は、通常と同じレベルの持続性と可用性の SLA を維持できます。

**移行が完了したら、アプリケーションを更新する必要はありますか。**

移行が完了すると、アカウントのレプリケーション タイプが "ゾーン冗長ストレージ (ZRS)" に変わります。 サービス エンドポイント、アクセス キー、SAS、その他のアカウント構成オプションは変更されません。

**汎用 v1 アカウントを ZRS にライブ マイグレーションするように依頼できますか。**

ZRS は汎用 v2 アカウントにのみ対応しています。そのため、ZRS へのライブ マイグレーションを依頼する前に、必ずアカウントを汎用 v2 にアップグレードしてください。 詳細については、「[Azure ストレージ アカウントの概要](https://docs.microsoft.com/azure/storage/common/storage-account-overview)」と「[汎用 v2 ストレージ アカウントにアップグレードする](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade)」を参照してください。

**読み取りアクセス geo 冗長ストレージ (RA-GRS) アカウントを ZRS にライブ マイグレーションするように依頼できますか。**

ZRS へのライブ マイグレーションを依頼する前に、アプリケーションとワークロードで読み取り専用セカンダリ エンドポイントにアクセスする必要がないことを確認し、ストレージ アカウントのレプリケーション タイプを geo 冗長ストレージ (GRS) に変更してください。 詳細については、「[レプリケーション戦略の変更](https://docs.microsoft.com/azure/storage/common/storage-redundancy#changing-replication-strategy)」を参照してください。

**別のリージョンにある ZRS にストレージ アカウントをライブ マイグレーションするように依頼できますか。**

ソース アカウントのリージョンとは異なるリージョンにある ZRS アカウントにデータを移行する場合は、手動で移行する必要があります。

## <a name="zrs-classic-a-legacy-option-for-block-blobs-redundancy"></a>ZRS クラシック:BLOB の冗長性をブロックするレガシー オプション
> [!NOTE]
> Microsoft は、2021 年 3 月 31 日に ZRS クラシック アカウントを非推奨にして移行します。 ZRS クラシックのお客様には、非推奨になる前に詳細をご連絡します。 
>
> リージョンで ZRS が[一般公開](#support-coverage-and-regional-availability)されると、そのリージョンのポータルからは ZRS クラシック アカウントを作成できなくなります。 Microsoft PowerShell と Azure CLI を使用した ZRS クラシック アカウントの作成は、ZRS クラシックが非推奨になるまでのオプションです。

ZRS クラシックでは、1 つから 2 つのリージョン内の複数のデータセンターに、データが非同期的にレプリケートされます。 Microsoft がセカンダリへのフェールオーバーを開始していないと、レプリケートされたデータを利用できないことがあります。 ZRS クラシック アカウントと LRS、GRS、または RA-GRS を相互に変換することはできません。 また、ZRS クラシック アカウントは、メトリックまたはログをサポートしていません。

ZRS クラシックを利用できるのは、汎用 V1 (GPv1) ストレージ アカウントの**ブロック BLOB** に限られます。 ストレージ アカウントについて詳しくは、「[Azure ストレージ アカウントの概要](storage-account-overview.md)」をご覧ください。

LRS、ZRS クラシック、GRS、または RA-GRS アカウントとの間で ZRS アカウント データを手動で移行するには、次のいずれかのツールを使用します:AzCopy、Azure Storage Explorer、Azure PowerShell、または Azure CLI。 また、Azure Storage クライアント ライブラリのいずれかを使用して、独自の移行ソリューションを構築することもできます。

また、ZRS が利用できるリージョンであれば、ポータルで、あるいは Azure PowerShell または Azure CLI を使用し、ZRS Classic アカウントを ZRS にアップグレードできます。 Azure portal で ZRS にアップグレードするには、アカウントの **[構成]** セクションに移動し、 **[アップグレードする]** を選択します。

![ポータルで ZRS Classic を ZRS にアップグレードする](media/storage-redundancy-zrs/portal-zrs-classic-upgrade.png)

PowerShell を利用して ZRS にアップグレードするには、次のコマンドを呼び出します。
```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> -AccountName <storage_account> -UpgradeToStorageV2
```

CLI を利用して ZRS にアップグレードするには、次のコマンドを呼び出します。
```cli
az storage account update -g <resource_group> -n <storage_account> --set kind=StorageV2
```

## <a name="see-also"></a>関連項目
- [Azure Storage のレプリケーション](storage-redundancy.md)
- [ローカル冗長ストレージ (LRS):Azure Storage の低コストのデータ冗長性](storage-redundancy-lrs.md)
- [geo 冗長ストレージ (GRS):Azure Storage のリージョン間レプリケーション](storage-redundancy-grs.md)
