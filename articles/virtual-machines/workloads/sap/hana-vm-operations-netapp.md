---
title: SAP HANA Azure 仮想マシンの ANF 構成 |Microsoft Docs
description: SAP HANA における Azure NetApp Files のストレージに関する推奨事項。
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP, Azure, ANF, HANA, Azure NetApp Files, スナップショット
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/23/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: de8af71928ad6a83d4930e4e6e0b8dd257148111
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101666611"
---
# <a name="nfs-v41-volumes-on-azure-netapp-files-for-sap-hana"></a>SAP HANA 用 Azure NetApp Files 上の NFS v4.1 ボリューム

Azure NetApp Files には、 **/hana/shared**、 **/hana/data**、 **/hana/log** の各ボリュームに使用できるネイティブ NFS 共有が用意されています。 **/hana/data** および **/hana/log** のボリュームに ANF ベースの NFS 共有を使用するには、v4.1 NFS プロトコルを使用する必要があります。 共有が ANF ベースの場合、 **/hana/data** および **/hana/log** ボリュームに NFS プロトコル v3 を使用することはできません。 


> [!IMPORTANT]
> Azure NetApp Files に実装されている NFS v3 プロトコルの使用は、 **/hana/data** および **/hana/log** ではサポートされて **いません**。 機能的観点から、 **/hana/data** および **/hana/log** ボリュームには、NFS 4.1 の使用が必須となります。 一方、 **/hana/shared** ボリュームの場合、機能的観点から NFS v3 または NFS v4.1 プロトコルを使用できます。

## <a name="important-considerations"></a>重要な考慮事項

SAP Netweaver および SAP HANA 用に Azure NetApp Files を検討するときは、以下の重要な考慮事項に注意してください。

- 最小容量プールは 4 TiB です  
- 最小ボリューム サイズは 100 GiB です。
- Azure NetApp Files と、Azure NetApp Files のボリュームがマウントされるすべての仮想マシンは、同じ Azure 仮想ネットワーク内、または同じリージョン内の[ピアリングされた仮想ネットワーク](../../../virtual-network/virtual-network-peering-overview.md)内に存在する必要があります
- 待ち時間を短縮するには、仮想マシンを Azure NetApp ストレージに近い場所にデプロイすることが重要です。  
- 選択した仮想ネットワークには、Azure NetApp Files に委任されているサブネットがある必要があります
- データベース サーバーから ANF ボリュームへの待機時間が計測されており、1 ミリ秒未満であることを確認します
- Azure NetApp ボリュームのスループットは、「[Azure NetApp Files のサービス レベル](../../../azure-netapp-files/azure-netapp-files-service-levels.md)」に記載されているように、ボリューム クォータとサービス レベルの機能です。 HANA Azure NetApp ボリュームのサイズを設定するときは、そのスループットが HANA システム要件を満たしていることを確認してください
- 大きなボリュームでより高いパフォーマンスを実現するために、可能であれば、/sapmnt、/usr/sap/trans などに対して 1 つのボリュームを使用するなど、ボリュームを "統合" してみてください。 可能な場合  
- Azure NetApp Files の[エクスポート ポリシー](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)では、ユーザーが制御できるのは、許可されたクライアント、アクセスの種類 (読み取りおよび書き込み、読み取り専用など) です。 
- Azure NetApp Files 機能は、ゾーンにはまだ対応していません。 現在、Azure NetApp Files 機能は、Azure リージョン内のすべての可用性ゾーンにはデプロイされていません。 Azure リージョンによっては、待ち時間が発生する可能性があることに注意してください。   
- 仮想マシン上の <b>sid</b>adm のユーザー ID と `sapsys` のグループ ID は、Azure NetApp Files の構成と一致している必要があります。 

> [!IMPORTANT]
> SAP HANA ワークロードにとって、待ち時間の短縮は重要です。 Microsoft の担当者と協力して、仮想マシンと Azure NetApp Files ボリュームが確実に近接してデプロイされるようにします。  

> [!IMPORTANT]
> 仮想マシンと Azure NetApp の構成の間で、<b>sid</b>adm のユーザー ID と、`sapsys` のグループ ID が一致しない場合は、VM にマウントされた Azure NetApp ボリューム上のファイルのアクセス許可が `nobody` として表示されます。 Azure NetApp Files に[新しいシステムをオンボード](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u)するときに、正しい <b>sid</b>adm のユーザー ID と `sapsys` のグループ ID を指定していることを確認してください。


## <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Azure NetApp Files 上の HANA データベースのサイズ指定

Azure NetApp ボリュームのスループットは、「[Azure NetApp Files のサービス レベル](../../../azure-netapp-files/azure-netapp-files-service-levels.md)」に記載されているように、ボリューム サイズとサービス レベルの機能です。 

重要なのは、サイズに対応するパフォーマンスと、SVM (ストレージ仮想マシン) の LIF (論理インターフェイス) に物理的な制限があるということを理解することです。

次の表は、バックアップを格納するには大規模な "Standard" ボリュームを作成するのが理にかなっていること、そして 12 TB を超える "Ultra" ボリュームを作成すると 1 つの LIF の物理的な帯域幅の容量を超えてしまうため、理にかなっていないことを示しています。 

LIF と 1 つの Linux セッションの最大スループットは、1.2 から 1.4 GB/秒です。 /hana/data のスループットを向上させる必要がある場合は、SAP HANA データ ボリュームのパーティション分割を使用して、データの再読み込み時または HANA セーブポイントの I/O アクティビティを、複数の NFS 共有に配置されている複数の HANA データ ファイル間でストライピングできます。 HANA データ ボリュームのストライピングの詳細については、これらの記事をご覧ください。

- [HANA 管理者ガイド](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.05/en-US/40b2b2a880ec4df7bac16eae3daef756.html?q=hana%20data%20volume%20partitioning)
- [SAP HANA に関するブログ - データ ボリュームのパーティション分割](https://blogs.sap.com/2020/10/07/sap-hana-partitioning-data-volumes/)
- [SAP ノート #2400005](https://launchpad.support.sap.com/#/notes/2400005)
- [SAP ノート #2700123](https://launchpad.support.sap.com/#/notes/2700123)


| サイズ  | スループット (Standard) | スループット (Premium) | スループット (Ultra) |
| --- | --- | --- | --- |
| 1 TB (テラバイト) | 16 MB/秒 | 64 MB/秒 | 128 MB/秒 |
| 2 TB | 32 MB/秒 | 128 MB/秒 | 256 MB/秒 |
| 4 TB | 64 MB/秒 | 256 MB/秒 | 512 MB/秒 |
| 10 TB | 160 MB/秒 | 640 MB/秒 | 1,280 MB/秒 |
| 15 TB | 240 MB/秒 | 960 MB/秒 | 1,400 MB/秒 |
| 20 TB | 320 MB/秒 | 1,280 MB/秒 | 1,400 MB/秒 |
| 40 TB | 640 MB/秒 | 1,400 MB/秒 | 1,400 MB/秒 |

データは、ストレージ バックエンドの同じ SSD に書き込まれていることを理解しておくことが重要です。 容量プールからのパフォーマンス クォータは、環境を管理できるように作成されています。
ストレージ KPI は、すべての HANA データベース サイズで同じです。 ほとんどの場合、この想定は現実と顧客の期待を反映していません。 HANA システムのサイズは、必ずしも小規模なシステムでは必要なストレージのスループットが低く、大規模なシステムでは必要なストレージのスループットが高いことを意味しません。 しかし、一般的には、より大きな HANA データベース インスタンスでは、より高いスループットの要件があると考えられます。 基となるハードウェアに対する SAP のサイズ設定ルールにより、このような大規模な HANA インスタンスでは、インスタンスの再起動後にデータをロードするようなタスクでも、より多くの CPU リソースと、より多くの並列処理を提供しています。 そのため、ボリュームのサイズは顧客の期待と要件に合わせて採用する必要があります。 純粋な容量の要件だけで判断すべきではありません。

Azure で SAP のインフラストラクチャを設計する際には、SAP による (運用システムの) 最小ストレージ スループット要件に注意する必要があります。これは、最小スループット特性につながります。

| ボリュームの種類と I/O の種類 | SAP で要求される最小 KPI | Premium サービス レベル | Ultra サービス レベル |
| --- | --- | --- | --- |
| ログ ボリュームの書き込み | 250 MB/秒 | 4 TB | 2 TB |
| データ ボリュームの書き込み | 250 MB/秒 | 4 TB | 2 TB |
| データ ボリュームの読み取り | 400 MB/秒 | 6.3 TB | 3.2 TB |

3 つのすべての KPI が要求されるため、読み取りの最小要件を満たすには、 **/hana/data** のボリュームを、より大きな容量にサイズ調整する必要があります。

高帯域幅を必要としない HANA システムでは、ANF ボリュームのサイズを小さくすることができます。 また、HANA システムのスループットを向上させる必要がある場合は、容量をオンラインでサイズ変更することでボリュームを調整できます。 KPI は、バックアップ ボリュームに対しては定義されていません。 ただし、高パフォーマンスの環境には、バックアップ ボリュームのスループットが不可欠です。 ログ、およびデータ ボリュームのパフォーマンスは、顧客の期待に合わせて設計する必要があります。

> [!IMPORTANT]
> 1 つの NFS ボリュームにデプロイする容量に関係なく、スループットは、仮想マシンでコンシューマーによって活用される毎秒 1.2 から 1.4 GB までの帯域幅範囲にとどまるものと予想されます。 これは、ANF プランの基礎アーキテクチャと NFS 関連の Linux セッション上限に関係があります。 「[Azure NetApp Files のパフォーマンス ベンチマークのテスト結果](../../../azure-netapp-files/performance-benchmarks-linux.md)」という記事に記載されているパフォーマンスとスループットの数値は、1 つの共有 NFS ボリュームと複数のクライアント VM (結果として複数のセッション) に対して行われたものです。 このシナリオは、SAP で測定するシナリオとは異なります。 その場合、ANF でホストされている NFS ボリュームに対して 1 つの VM からの スループットを測定します。

データとログの SAP 最小スループット要件を満たすため、および **/hana/shared** のガイドラインに従うと、推奨されるサイズは次のようになります。

| ボリューム | サイズ<br /> Premium ストレージ層 | サイズ<br /> Ultra ストレージ層 | サポートされる NFS プロトコル |
| --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | v4.1 |
| /hana/data | 6.3 TiB | 3.2 TiB | v4.1 |
| /hana/shared scale-up | 最小 (1 TB、1 x RAM)  | 最小 (1 TB、1 x RAM) | v3 または v4.1 |
| /hana/shared scale-out | 1 x ワーカー ノードの RAM<br /> 4 ワーカー ノードあたり  | 1 x ワーカー ノードの RAM<br /> 4 ワーカー ノードあたり  | v3 または v4.1 |
| /hana/logbackup | 3 x RAM  | 3 x RAM | v3 または v4.1 |
| /hana/backup | 2 x RAM  | 2 x RAM | v3 または v4.1 |

すべてのボリュームで、NFS v4.1 を強くお勧めします

バックアップ ボリュームのサイズは推定値です。 正確な要件は、ワークロードと運用プロセスに基づいて定義する必要があります。 バックアップの場合は、異なる SAP HANA インスタンスの複数のボリュームを 1 つ (または 2 つ) の大きなボリュームに統合できます。これにより、ANF のサービス レベルが低くなる可能性があります。

> [!NOTE]
> このドキュメントで説明されている Azure NetApp Files のサイズ設定の推奨事項は、SAP がそのインフラストラクチャ プロバイダーに表明している最小要件を対象としています。 実際の顧客のデプロイとワークロードのシナリオでは、これらは十分ではない場合があります。 これらの推奨事項は開始点として利用し、ご自分の固有のワークロードの要件に合わせて調整してください。  

そのため、Ultra Disk Storage 用に記載されているのと同様の ANF ボリューム用のスループットをデプロイすることを検討してください。 また、Ultra ディスク テーブルの場合と同じように、さまざまな VM SKU のボリュームに対して記載されているサイズも考慮してください。

> [!TIP]
> Azure NetApp Files ボリュームは、ボリュームを `unmount` したり、仮想マシンを停止したり、SAP HANA を停止したりすることなく、動的にサイズ変更することができます。 これにより、予想されるスループット要求と予期しないスループット要求の両方を柔軟に満たすことができます。

ANF でホストされる NFS v4.1 ボリュームを使用して、スタンバイ ノードを含む SAP HANA スケールアウト構成をデプロイする方法については、[SUSE Linux Enterprise Server 上に Azure VM のスタンバイ ノードと Azure NetApp Files を使用して SAP HANA をスケールアウトする方法](./sap-hana-scale-out-standby-netapp-files-suse.md)に関するドキュメントを参照してください。


## <a name="availability"></a>可用性
ANF システムの更新とアップグレードは、お客様の環境に影響を与えることなく適用されます。 定義された [SLA は 99.99%](https://azure.microsoft.com/support/legal/sla/netapp/) です。


## <a name="volumes-and-ip-addresses-and-capacity-pools"></a>ボリュームと IP アドレスと容量プール
ANF を使用する際は、基になるインフラストラクチャどのように構築されているかを理解することが重要です。 容量プールは、ANF の課金モデルを簡単に作成することができる、単なる構造です。 容量プールには、基になるインフラストラクチャとの物理的な関係はありません。 容量プールを作成すると、課金可能なシェルのみが作成され、それ以上は作成されません。 ボリュームを作成すると、いくつかの NetApp システムのクラスター上に最初の SVM (ストレージ仮想マシン) が作成されます。 ボリュームにアクセスするために、この SVM に対して 1 つの IP が作成されます。 複数のボリュームを作成した場合、すべてのボリュームがこのマルチコントローラー NetApp クラスター上のこの SVM に分散されます。 IP を 1 つだけ取得した場合でも、データは複数のコントローラーに分散されます。 ANF には、構成されたストレージのボリュームまたは容量が内部で事前に定義されたレベルに達した際に、顧客のワークロードを自動的に分散するロジックが備わっています。 ボリュームにアクセスするための新しい IP アドレスが割り当てられるため、このようなケースに気づくことがあるかもしれません。

##<a name="log-volume-and-log-backup-volume"></a>ログ ボリュームとログ バックアップ ボリューム
"ログ ボリューム" ( **/hana/log**) は、オンライン REDO ログの書き込みに使用されます。 そのため、このボリュームにはオープンなファイルが存在し、このボリュームのスナップショットを作成しても意味がありません。 オンラインの redo ログ ファイルがいっぱいになるか、redo ログ バックアップが実行されると、オンライン redo ログ ファイルは、ログ バックアップ ボリュームにアーカイブまたはバックアップされます。 適切なバックアップ パフォーマンスを実現するには、ログ バックアップ ボリュームに十分なスループットが必要です。 ストレージ コストを最適化するには、複数の HANA インスタンスのログ バックアップ ボリュームを統合することが有効です。 これにより、複数の HANA インスタンスで同じボリュームが活用され、異なるディレクトリにバックアップが書き込まれます。 このような統合を使用すると、ボリュームを若干大きくする必要があるため、スループットを向上させることができます。 

これは、HANA データベースの完全バックアップの書き込みに使用するボリュームにも当てはまります。  
 

## <a name="backup"></a>バックアップ
「[Azure Virtual Machines 上の SAP HANA のバックアップ ガイド](./sap-hana-backup-guide.md)」の説明にある SAP HANA データベースをバックアップするストリーミング バックアップと Azure Back サービスに加えて、Azure NetApp Files により、ストレージベースのスナップショット バックアップを実行することができるようになります。 

SAP HANA では以下がサポートされます。

- ストレージベースのスナップショット バックアップ (SAP HANA 1.0 SPS7 以降)
- マルチ データベース コンテナー (MDC) HANA 環境でのストレージベースのスナップショット バックアップのサポート (SAP HANA 2.0 SPS4 以降)


ストレージベースのスナップショット バックアップの作成は、簡単な 4 つの手順で行います。 
1. HANA (内部) データベース スナップショットを作成する - これは、ユーザーまたはツールで実行する必要があるアクティビティです 
1. SAP HANA によってデータがデータファイルに書き込まれることで、ストレージに一貫性のある状態が作り出される - この手順は、HANA スナップショットの作成後に実行されます
1. ストレージ上に **/hana/data** ボリュームのスナップショットを作成する - これは、ユーザーまたはツールで実行する必要があるステップです。 **/hana/log** ボリュームのスナップショットを実行する必要はありません
1. HANA (内部) データベース スナップショットを削除して通常の操作を再開する - これは、ユーザーまたはツールで実行する必要があるステップです

> [!WARNING]
> 最後のステップを行わなかった場合、または最後のステップを実行できなかった場合、SAP HANA のメモリ需要に深刻な影響を及ぼし、SAP HANA が停止する可能性があります

```
BACKUP DATA FOR FULL SYSTEM CREATE SNAPSHOT COMMENT 'SNAPSHOT-2019-03-18:11:00';
```

![SAP HANA の ANF スナップショット バックアップ](media/hana-vm-operations-netapp/storage-snapshot-scenario.png)

```
az netappfiles snapshot create -g mygroup --account-name myaccname --pool-name mypoolname --volume-name myvolname --name mysnapname 
```

![SAP HANA の ANF スナップショット バックアップ パート 2](media/hana-vm-operations-netapp/storage-snapshot.png)

```
BACKUP DATA FOR FULL SYSTEM CLOSE SNAPSHOT BACKUP_ID 47110815 SUCCESSFUL SNAPSHOT-2020-08-18:11:00';
```

このスナップショット バックアップ手順は、さまざまなツールを使用してさまざまな方法で管理できます。 1 つの例として、GitHub ([https://github.com/netapp/ntaphana](https://github.com/netapp/ntaphana)) にある python スクリプト “ntaphana_azure.py” があります。これは、メンテナンスやサポートのない、"現状有姿" で提供されるサンプル コードです。



> [!CAUTION]
> スナップショット自体は、スナップショットを作成したボリュームと同じ物理ストレージに配置されているため、保護されたバックアップではありません。 1 日あたり少なくとも 1 つのスナップショットを、別の場所に "保護" する必要があります。 これは、同じ環境でも、リモートの Azure リージョンでも、Azure Blob Storage 上でも可能です。


Commvault バックアップ製品のユーザーの場合、2 つ目のオプションは Commvault IntelliSnap V.11.21 以降です。 このバージョンまたはそれ以降のバージョンの Commvault では Azure NetApp Files がサポートされています。 詳細については、[Commvault IntelliSnap 11.21](https://documentation.commvault.com/11.21/essential/116350_getting_started_with_backup_and_restore_operations_for_azure_netapp_file_services_smb_shares_and_nfs_shares.html) に関する記事を参照してください。


### <a name="back-up-the-snapshot-using-azure-blob-storage"></a>Azure Blob Storage を使用してスナップショットをバックアップする
Azure Blob Storage へのバックアップは、ANF ベースの HANA データベース ストレージのスナップショット バックアップを保存する費用対効果の高い高速な方法です。 スナップショットを Azure Blob Storage に保存するには、azcopy ツールを使用することをお勧めします。 このツールの最新版をダウンロードして、GitHub の python スクリプトがインストールされている bin ディレクトリなどにインストールします。
次のように、最新の azcopy ツールをダウンロードします。

```
root # wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1
Saving to: ‘azcopy_v10.tar.gz’
```

最も高度な機能は、同期オプションです。 同期オプションを使用すると、azcopy によってソースと同期先ディレクトリが常に同期されます。 パラメーター **--delete-destination** を使用することは重要です。 このパラメーターを指定しないと、azcopy によって同期先のサイトのファイルが削除されず、同期先の領域使用率が増加します。 Azure ストレージ アカウントでブロック BLOB コンテナーを作成します。 次に、BLOB コンテナーの SAS キーを作成し、スナップショット フォルダーを Azure Blob コンテナーに同期します。

たとえば、データを保護するために、毎日のスナップショットを Azure Blob コンテナーに同期する必要があるとします。 その 1 つのスナップショットのみを保持する必要がある場合は、以下のコマンドを使用できます。

```
root # > azcopy sync '/hana/data/SID/mnt00001/.snapshot' 'https://azacsnaptmytestblob01.blob.core.windows.net/abc?sv=2021-02-02&ss=bfqt&srt=sco&sp=rwdlacup&se=2021-02-04T08:25:26Z&st=2021-02-04T00:25:26Z&spr=https&sig=abcdefghijklmnopqrstuvwxyz' --recursive=true --delete-destination=true
```

## <a name="next-steps"></a>次のステップ
以下の記事を参照してください。

- [Azure 仮想マシンの SAP HANA の高可用性](./sap-hana-availability-overview.md)