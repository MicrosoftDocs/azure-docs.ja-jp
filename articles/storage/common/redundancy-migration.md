---
title: ストレージ アカウントがレプリケートされる方法を変更する
titleSuffix: Azure Storage
description: 既存ストレージ アカウントのデータがレプリケートされる方法を変更する方法について説明します。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/19/2021
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 412e5ac661761d5fda1d375c59511c053a6354a6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101714784"
---
# <a name="change-how-a-storage-account-is-replicated"></a>ストレージ アカウントがレプリケートされる方法を変更する

Azure Storage では、計画されたイベントや計画外のイベント (一時的なハードウェア障害、ネットワークの停止や停電、大規模な自然災害など) から保護するため、常にデータの複数のコピーが格納されます。 冗長性により、障害が発生しても、ストレージ アカウントが [Azure Storage のサービス レベル アグリーメント (SLA)](https://azure.microsoft.com/support/legal/sla/storage/) を満たすことが保証されます。

Azure Storage には、以下の種類のレプリケーションが用意されています。

- ローカル冗長ストレージ (LRS)
- ゾーン冗長ストレージ (ZRS)
- geo 冗長ストレージ (GRS) または読み取りアクセス geo 冗長ストレージ (RA-GRS)
- geo ゾーン冗長ストレージ (GZRS) または読み取りアクセス geo ゾーン冗長ストレージ (RA-GZRS)

これらの各オプションの概要については、「[Azure Storage の冗長性](storage-redundancy.md)」を参照してください。

## <a name="switch-between-types-of-replication"></a>レプリケーションの種類を切り替える

ストレージ アカウントのレプリケーションは、ある種類から他のどの種類にも切り替え可能ですが、一部のシナリオは他のシナリオよりも簡単です。 セカンダリ リージョンに対する geo レプリケーションまたは読み取りアクセスを追加または削除する場合は、Azure portal、PowerShell、または Azure CLI を使用してレプリケーション設定を更新できます。 ただし、プライマリ リージョンでデータがレプリケートされる方法を変更する場合は、LRS から ZRS に移行するか、その逆を行ってから、手動での移行を実行する必要があります。

次の表に、各種類のレプリケーションを別の種類に切り替える方法の概要を示します。

| 切り替え | 先: LRS | 先: GRS/RA-GRS | 先: ZRS | 先: GZRS/RA-GZRS |
|--------------------|----------------------------------------------------|---------------------------------------------------------------------|----------------------------------------------------|---------------------------------------------------------------------|
| <b>元: LRS</b> | 該当なし | Azure portal、PowerShell、または CLI を使用してレプリケーション設定を変更する<sup>1、2</sup> | 手動の移行を実行する <br /><br /> OR <br /><br /> ライブ マイグレーションを要求する | 手動の移行を実行する <br /><br /> OR <br /><br /> まず GRS/RA-GRS に切り替えてから、ライブ マイグレーションを要求する<sup>1</sup> |
| <b>元: GRS/RA-GRS</b> | Azure portal、PowerShell、または CLI を使用してレプリケーション設定を変更する | 該当なし | 手動の移行を実行する <br /><br /> OR <br /><br /> まず LRS に切り替えてから、ライブ マイグレーションを要求する | 手動の移行を実行する <br /><br /> OR <br /><br /> ライブ マイグレーションを要求する |
| <b>元: ZRS</b> | 手動の移行を実行する | 手動の移行を実行する | 該当なし | ライブ マイグレーションを要求する |
| <b>元: GZRS/RA-GZRS</b> | 手動の移行を実行する | 手動の移行を実行する | Azure portal、PowerShell、または CLI を使用してレプリケーション設定を変更する | 該当なし |

<sup>1</sup> 1 回限りのエグレス料金が発生します。<br />
<sup>2</sup> ストレージ アカウントにアーカイブ層の BLOB が含まれる場合、LRS から GRS への移行はサポートされません。<br />
<sup>3</sup> ZRS から GZRS/RA-GZRS への変換またはその逆の変換は、次のリージョンではサポートされていません: 米国東部 2、米国東部、西ヨーロッパ。

> [!CAUTION]
> (RA-)GRS または (RA-)GZRS アカウントに対して[アカウントのフェールオーバー](storage-disaster-recovery-guidance.md)を実行した場合、アカウントはフェールオーバー後の新しいプライマリ リージョンでローカル冗長 (LRS) になります。 フェールオーバーによって作成される LRS アカウントに対する ZRS または GZRS へのライブ マイグレーションは、サポートされていません。 これは、いわゆるフェールバック操作の場合にも当てはまります。 たとえば、RA-GZRS からセカンダリ リージョンの LRS へのアカウントのフェールオーバーを実行してから、それをもう一度 RA-GRS に構成し、元のプライマリ リージョンへの別のアカウントのフェイルオーバーを実行した場合、プライマリ リージョンの RA-GZRS への元のライブ マイグレーションについて、サポートに問い合わせることはできません。 代わりに、ZRS または GZRS への手動移行を実行する必要があります。

## <a name="change-the-replication-setting"></a>レプリケーション設定を変更する

プライマリ リージョンでデータがレプリケートされる方法を変更しない限り、Azure portal、PowerShell、または Azure CLI を使用してストレージ アカウントのレプリケーション設定を変更できます。 プライマリ リージョンの LRS からプライマリ リージョンの ZRS に移行する場合、またはその逆の場合は、手動での移行またはライブ マイグレーションを実行する必要があります。

ストレージ アカウントがレプリケートされる方法を変更しても、アプリケーションのダウン タイムは発生しません。

# <a name="portal"></a>[ポータル](#tab/portal)

Azure portal でストレージ アカウントの冗長オプションを変更するには、以下の手順に従います。

1. Azure Portal のストレージ アカウントに移動します。
1. **[構成]** 設定を選択します。
1. **[レプリケーション]** 設定を更新します。

![ポータルでレプリケーション オプションを変更する方法を示すスクリーンショット](media/redundancy-migration/change-replication-option.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell を使用してストレージ アカウントの冗長オプションを変更するには、[Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) コマンドを呼び出して、`-SkuName` パラメーターを指定します。

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage_account> `
    -SkuName <sku>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用してストレージ アカウントの冗長オプションを変更するには、[az storage account update](/cli/azure/storage/account#az-storage-account-update) コマンドを呼び出して、`--sku` パラメーターを指定します。

```azurecli-interactive
az storage account update \
    --name <storage-account>
    --resource-group <resource_group> \
    --sku <sku>
```

---

## <a name="perform-a-manual-migration-to-zrs-gzrs-or-ra-gzrs"></a>ZRS、GZRS、または RA-GZRS への手動移行を実行する

プライマリ リージョンでストレージ アカウント内のデータがレプリケートされる方法を変更する場合は、LRS から ZRS に移行するか、その逆を行ってから、手動での移行を実行するように選択する必要があります。 手動の移行はライブ マイグレーションよりも高い柔軟性を備えています。 手動での移行のタイミングを制御します。そのため、特定の日付までに移行を完了する必要がある場合は、このオプションを使用します。

プライマリ リージョンで LRS から ZRS への手動の移行を実行する場合、またはその逆を実行する場合、移行先ストレージ アカウントは geo 冗長にすることができ、セカンダリ リージョンに対する読み取りアクセス用に構成することもできます。 たとえば、1 つの手順で LRS アカウントを GZRS または RA-GZRS アカウントに移行できます。

手動の移行ではアプリケーションのダウンタイムが発生することがあります。 アプリケーションが高可用性を必要とする場合、Microsoft はライブ マイグレーションのオプションも提供します。 ライブ マイグレーションは、ダウンタイムのないインプレース移行です。

手動の移行では、既存のストレージ アカウントから、プライマリ リージョン内の ZRS を使用する新しいストレージ アカウントに、データをコピーします。 手動の移行を実行する場合、次のいずれかのオプションを使用できます。

- AzCopy のような既存のツール、Azure Storage クライアント ライブラリのいずれか、または信頼できるサード パーティ製ツールを使用して、データをコピーします。
- Hadoop や HDInsight を使い慣れている場合は、ソース ストレージ アカウントとターゲット ストレージ アカウントの両方をクラスターにアタッチできます。 次に、DistCp などのツールを使用してデータ コピー処理を並列化します。

## <a name="request-a-live-migration-to-zrs-gzrs-or-ra-gzrs"></a>ZRS、GZRS、または RA-GZRS へのライブ マイグレーションを要求する

アプリケーションのダウンタイムなしで LRS からプライマリ リージョンの ZRS にストレージ アカウントを移行する必要がある場合は、Microsoft からのライブ マイグレーションを要求できます。 LRS から GZRS または RA-GZRS に移行するには、最初に GRS または RA-GRS に切り替えてから、ライブ マイグレーションを要求します。 同様に、GRS または RA-GRS から GZRS または RA-GZRS へのライブ マイグレーションを要求できます。 GRS または RA-GRS から ZRS に移行するには、まず LRS に切り替え、ライブ マイグレーションを要求します。

ライブ マイグレーション中にはストレージ アカウント内のデータにアクセスでき、持続性や可用性が失われることはありません。 移行プロセスの間、Azure Storage の SLA は維持されます。 ライブ マイグレーションに関連するデータ損失はありません。 サービス エンドポイント、アクセス キー、Shared Access Signature、およびその他のアカウント オプションは、移行後も変更されません。

ZRS は汎用 v2 アカウントにのみ対応しているため、ZRS へのライブ マイグレーションの要求を送信する前に、必ずストレージ アカウントをアップグレードしてください。 詳細については、「[汎用 v2 ストレージ アカウントにアップグレードする](storage-account-upgrade.md)」を参照してください。 ストレージ アカウントには、ライブ マイグレーションによって移行するデータが含まれている必要があります。

ライブ マイグレーションは、LRS または GRS のレプリケーションを使用するストレージ アカウントについてのみサポートされます。 自分のアカウントが RA-GRS を使用している場合は、続行する前に、まず自分のアカウントのレプリケーションの種類を LRS または GRS のいずれかに変更する必要があります。 この中間の手順により、移行前に RA GRS によって提供される読み取り専用のセカンダリ エンドポイントが削除されます。

Microsoft はお客様のライブ マイグレーションの要求に速やかに対応しますが、ライブ マイグレーションがいつ完了するかについての保証はありません。 データを特定の日付までに ZRS に移行する必要がある場合は、手動の移行を実行することをお勧めします。 一般的に、アカウントで保存しているデータが多いほど、データの移行には時間がかかります。

以下の場合には手動の移行を実行する必要があります。

- ソース アカウントとは異なるリージョンにある ZRS ストレージ アカウントにデータを移行したい。
- ストレージ アカウントは、Premium ページ BLOB またはブロック BLOB アカウントである。
- ZRS から LRS、GRS、または RA-GRS にデータを移行したい。
- ストレージ アカウントにアーカイブ層のデータが含まれている。

ライブ マイグレーションは [Azure サポート ポータル](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)から要求できます。 ポータルから、ZRS に変換するストレージ アカウントを選択します。

1. **[新しいサポート要求]** を選択します。
2. アカウント情報に基づき **[基本]** に入力します。 
    - **問題の種類**: **[Technical]\(テクニカル\)** を選択します。
    - **サービス**: **[使用中のサービス]** と **[Storage Account Management]\(ストレージ アカウントの管理\)** を選択します。
    - **リソース**:ZRS に変換するリソースを選択します。
3. **[次へ]** を選択します。
4. **[Problem]\(問題)** セクションで以下の値を指定します。
    - **[重大度]** :既定値をそのまま使用します。
    - **[問題の種類]** : **[データ移行]** を選択します。
    - **カテゴリ**: **[Migrate to ZRS]\(ZRS へ移行\)** を選択します。
    - **[タイトル]** :**ZRS アカウント移行** などのわかりやすいタイトルを入力します。
    - **[詳細]** : **[詳細]** ボックスには、たとえば、\_\_ リージョンで [LRS、GRS] から ZRS に移行するなどの詳細情報を入力します。
5. **[次へ]** を選択します。
6. **[連絡先情報]** ブレードの連絡先情報が正しいことを確認します。
7. **［作成］** を選択します

サポート担当者はお客様に連絡し、必要なサポートを提供します。

> [!NOTE]
> Premium ファイル共有 (FileStorage アカウント) は、LRS と ZRS でのみ使用できます。
>
> GZRS ストレージ アカウントでは、現在アーカイブ層はサポートされていません。 詳細については、「[Azure Blob Storage: ホット、クール、アーカイブ ストレージ層](../blobs/storage-blob-storage-tiers.md)」を参照してください。
>
> マネージド ディスクは LRS にのみ使用できます。ZRS に移行することはできません。 Standard SSD マネージド ディスクのスナップショットとイメージを、Standard HDD ストレージに格納できます。また、[LRS オプションと ZRS オプションから選択](https://azure.microsoft.com/pricing/details/managed-disks/)できます。 可用性セットとの統合については、「[Azure マネージド ディスクの概要](../../virtual-machines/managed-disks-overview.md#integration-with-availability-sets)」を参照してください。

## <a name="switch-from-zrs-classic"></a>ZRS クラシックから切り替える

> [!IMPORTANT]
> Microsoft は、2021 年 3 月 31 日に ZRS クラシック アカウントを非推奨にして移行します。 ZRS クラシックのお客様には、非推奨になる前に詳細をご連絡します。
>
> 特定のリージョンで ZRS が一般公開になった後は、そのリージョンの Azure portal からは ZRS クラシック アカウントを作成できなくなります。 Microsoft PowerShell と Azure CLI を使用した ZRS クラシック アカウントの作成は、ZRS クラシックが非推奨になるまでのオプションです。 ZRS を使用できる場所については、「[Azure Storage の冗長性](storage-redundancy.md)」を参照してください。

ZRS クラシックでは、1 つから 2 つのリージョン内の複数のデータセンターに、データが非同期的にレプリケートされます。 Microsoft がセカンダリへのフェールオーバーを開始していないと、レプリケートされたデータを利用できないことがあります。 ZRS クラシック アカウントと LRS、GRS、または RA-GRS を相互に変換することはできません。 また、ZRS クラシック アカウントは、メトリックまたはログをサポートしていません。

ZRS クラシックを利用できるのは、汎用 V1 (GPv1) ストレージ アカウントの **ブロック BLOB** に限られます。 ストレージ アカウントについて詳しくは、「[Azure ストレージ アカウントの概要](storage-account-overview.md)」をご覧ください。

LRS、GRS、RA-GRS、または ZRS クラシック アカウントとの間で ZRS アカウントのデータを手動で移行するには、次のいずれかのツールを使用します。AzCopy、Azure Storage Explorer、PowerShell、または Azure CLI です。 また、Azure Storage クライアント ライブラリのいずれかを使用して、独自の移行ソリューションを構築することもできます。

ZRS を使用できるリージョンでは、Azure portal、PowerShell、または Azure CLI を使用し、ZRS クラシック ストレージ アカウントを ZRS にアップグレードすることもできます。

# <a name="portal"></a>[ポータル](#tab/portal)

Azure portal で ZRS にアップグレードするには、アカウントの **[構成]** 設定に移動し、 **[アップグレードする]** を選択します。

![ポータルで ZRS Classic を ZRS にアップグレードする](media/redundancy-migration/portal-zrs-classic-upgrade.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell を使用して ZRS にアップグレードするには、次のコマンドを呼び出します。

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> -AccountName <storage_account> -UpgradeToStorageV2
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用して ZRS にアップグレードするには、次のコマンドを呼び出します。

```cli
az storage account update -g <resource_group> -n <storage_account> --set kind=StorageV2
```

---

## <a name="costs-associated-with-changing-how-data-is-replicated"></a>データがレプリケートされる方法の変更に関連するコスト

データがレプリケートされる方法の変更に関連するコストは、変換のパスによって異なります。 Azure Storage の冗長性のオファリングは、コストが最小から最大の順に、LRS、ZRS、GRS、RA-GRS、GZRS、RA-GZRS となっています。

たとえば、LRS "*から*" 他の種類のレプリケーションに移行する場合、より高度な冗長性レベルに移行するので、追加料金が発生します。 GRS または RA-GRS "*に*" 移行する場合、(プライマリ リージョン内の) データがリモートのセカンダリ リージョンにレプリケートされているため、エグレス帯域幅の料金がかかります。 この料金は初回セットアップ時に 1 回だけかかる料金です。 データがコピーされた後に、追加の移行料金はかかりません。 帯域幅の料金の詳細については、[Azure Storage の料金に関するページ](https://azure.microsoft.com/pricing/details/storage/blobs/)をご覧ください。

ストレージ アカウントを GRS から LRS に移行した場合、追加のコストは発生しませんが、レプリケートされたデータはセカンダリ ロケーションから削除されます。

> [!IMPORTANT]
> ストレージ アカウントを RA-GRS から GRS または LRS に移行した場合、そのアカウントは変換日からさらに 30 日間、RA-GRS として課金されることにご注意ください。

## <a name="see-also"></a>関連項目

- [Azure Storage の冗長性](storage-redundancy.md)
- [ストレージ アカウントの最終同期時刻プロパティを確認する](last-sync-time-get.md)
- [geo 冗長性を使用する高可用性アプリケーションの設計](geo-redundant-design.md)
