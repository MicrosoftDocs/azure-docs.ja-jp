---
title: Azure File Sync のデプロイの計画 | Microsoft Docs
description: Azure Files のデプロイを計画するときの考慮事項について説明します。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: a079f42f63e232c21a52bd108b34c3b022dcee5b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/27/2020
ms.locfileid: "82176092"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>Azure File Sync のデプロイの計画

:::row:::
    :::column:::
        [![Azure File Sync を紹介するインタビューとデモ- クリックして再生](./media/storage-sync-files-planning/azure-file-sync-interview-video-snapshot.png)](https://www.youtube.com/watch?v=nfWLO7F52-s)
    :::column-end:::
    :::column:::
        Azure File Sync は、オンプレミスの Windows Server またはクラウド VM に多数の Azure ファイル共有をキャッシュできるサービスです。 
        
        この記事では、Azure File Sync の概念と機能を紹介します。 Azure File Sync について理解したら、[Azure File Sync デプロイ ガイド](storage-sync-files-deployment-guide.md)に従って、このサービスを試してみることを検討してください。        
    :::column-end:::
:::row-end:::

ファイルは [Azure ファイル共有](storage-files-introduction.md)のクラウドに格納されます。 Azure ファイル共有は 2 つの方法で使用できます。これらのサーバーレスの Azure ファイル共有 (SMB) を直接マウントするか、Azure File Sync を使用してオンプレミスで Azure ファイル共有をキャッシュします。選択するデプロイ オプションによって、デプロイを計画するときに考慮する必要がある側面が変わります。 

- **Azure ファイル共有を直接マウントする**:Azure Files では SMB アクセスが提供されるため、Windows、macOS、および Linux で使用可能な標準的な SMB クライアントを使用して、オンプレミスまたはクラウドで Azure ファイル共有をマウントすることができます。 Azure ファイル共有はサーバーレスであるため、運用環境でデプロイするシナリオでは、ファイル サーバーや NAS デバイスを管理する必要ありません。 つまり、ソフトウェアの修正プログラムを適用したり、物理ディスクを交換したりする必要はありません。 

- **Azure File Sync を使用したオンプレミスでの Azure ファイル共有のキャッシュ**:Azure File Sync を使用すると、オンプレミスのファイル サーバーの柔軟性、パフォーマンス、互換性を維持しながら、Azure Files で組織のファイル共有を一元化できます。 Azure File Sync によって、オンプレミス (またはクラウド) の Windows Server が Azure ファイル共有の高速キャッシュに変換されます。 

## <a name="management-concepts"></a>管理の概念
Azure File Sync のデプロイには、次の 3 つの基本的な管理オブジェクトがあります。

- **Azure ファイル共有**: Azure ファイル共有はサーバーレス クラウド ファイル共有であり、Azure File Sync の同期関係の*クラウド エンドポイント*を提供します。 Azure ファイル共有内のファイルには、SMB または FileREST プロトコルを使用して直接アクセスできます。ただし、Azure ファイル共有が Azure File Sync で使用されている場合は、主に Windows Server キャッシュを介してファイルにアクセスすることをお勧めします。これは、現在 Azure Files には Windows Server のような効率的な変更検出メカニズムがないため、Azure ファイル共有に直接加えた変更がサーバー エンドポイントに反映されるまでに時間がかかるためです。
- **サーバー エンドポイント**:Azure ファイル共有に同期されている Windows Server 上のパス。 これには、ボリューム上の特定のフォルダー、またはボリュームのルートを指定できます。 名前空間が重複していなければ、同じボリューム上に複数のサーバー エンドポイントが存在できます。
- **同期グループ**:**クラウド エンドポイント**、Azure ファイル共有、およびサーバー エンドポイント間の同期関係を定義するオブジェクト。 同期グループ内のエンドポイントは、相互に同期を維持されます。 たとえば、Azure File Sync で管理するファイル セットが 2 組ある場合、2 つの同期グループを作成し、各同期グループに別のエンドポイントを追加します。

### <a name="azure-file-share-management-concepts"></a>Azure ファイル共有の管理の概念
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

### <a name="azure-file-sync-management-concepts"></a>Azure File Sync の管理の概念
同期グループは、**ストレージ同期サービス**にデプロイされます。これは、Azure File Sync で使用するサーバーを登録する最上位のオブジェクトで、同期グループのリレーションシップが含まれています。 ストレージ同期サービス リソースは、ストレージ アカウント リソースのピアであり、同じように Azure リソース グループにデプロイできます。 ストレージ同期サービスでは、複数のストレージ アカウントと複数の登録済み Windows サーバー間で Azure ファイル共有を含む同期グループを作成できます。

ストレージ同期サービスで同期グループを作成する前に、まずストレージ同期サービスで Windows Server を登録する必要があります。 これにより、**登録済みサーバー** オブジェクトが作成され、これはサーバー (またはクラスター) とストレージ同期サービス間の信頼関係を表します。 ストレージ同期サービスを登録するには、まずサーバーに Azure File Sync エージェントをインストールする必要があります。 個々のサーバーまたはクラスターは、同時に 1 つのストレージ同期サービスのみに登録できます。

同期グループには、1 つのクラウド エンドポイント (Azure ファイル共有) と 1 つ以上のサーバー エンドポイントが含まれます。 サーバー エンドポイント オブジェクトには、**クラウドを使った階層化**機能を構成する設定が含まれています。これにより Azure File Sync のキャッシュ機能が提供されます。Azure ファイル共有と同期するには、Azure ファイル共有を含むストレージ アカウントが、ストレージ同期サービスと同じ Azure リージョンに存在している必要があります。

### <a name="management-guidance"></a>管理ガイダンス
Azure File Sync をデプロイする場合は、次のことをお勧めします。

- Windows ファイル共有で Azure ファイル共有 1:1 をデプロイします。 サーバー エンドポイント オブジェクトを使用すると、同期関係のサーバー側で同期トポロジを設定する方法において、高い柔軟性を発揮します。 管理を簡略化するには、サーバー エンドポイントのパスを Windows ファイル共有のパスと一致させます。 

- 使用するストレージ同期サービスは、出来る限り最小限に抑えます。 これにより、Windows Server は一度に 1 つのストレージ同期サービスにしか登録できないため、複数のサーバー エンドポイントを含む同期グループがある場合の管理が簡単になります。 

- Azure ファイル共有をデプロイする際には、ストレージ アカウントの IOPS 制限に注意してください。 ファイル共有をストレージ アカウントに 1:1 でマップするのが理想的ですが、これは、組織と Azure の両方からのさまざまな制限や制約により、実現できない場合もあります。 1 つのストレージ アカウントに 1 つのファイル共有のみをデプロイすることができない場合は、使用頻度が高い共有と低い共有を考慮し、最もホットなファイル共有が同じストレージ アカウントに一緒に配置されないようにしてください。

## <a name="windows-file-server-considerations"></a>Windows ファイル サーバーに関する考慮事項
Windows Server で同期機能を有効にするには、Azure File Sync のダウンロード可能なエージェントをインストールする必要があります。 Azure File Sync エージェントには、2 つの主な構成要素があります。`FileSyncSvc.exe` は、サーバー エンドポイントの変更の監視と同期セッションの開始を担当するバックグラウンド Windows サービスであり、`StorageSync.sys` はクラウドの階層化と迅速なディザスター リカバリーを可能にするファイル システム フィルターです。  

### <a name="operating-system-requirements"></a>オペレーティング システムの要件
Azure File Sync は、次のバージョンの Windows Server でサポートされています。

| Version | サポートされている SKU | サポートされているデプロイ オプション |
|---------|----------------|------------------------------|
| Windows Server 2019 | Datacenter、Standard、および IoT | 完全およびコア |
| Windows Server 2016 | Datacenter、Standard、および Storage Server | 完全およびコア |
| Windows Server 2012 R2 | Datacenter、Standard、および Storage Server | 完全およびコア |

Windows Server の今後のバージョンは、それらがリリースされたときに追加されます。

> [!Important]  
> Azure File Sync で使用するすべてのサーバーは、Windows Update の最新の更新プログラムを使用して常に最新の状態を保つことをお勧めします。 

### <a name="minimum-system-resources"></a>最小システム リソース
Azure File Sync には、少なくとも 1 つの CPU と最低 2 GiB のメモリを備えたサーバー (物理または仮想) が必要です。

> [!Important]  
> 動的メモリを有効にした仮想マシンでサーバーが実行されている場合は、2048 MiB 以上のメモリで VM を構成する必要があります。

ほとんどの運用環境のワークロードでは、最小要件のみを使用して Azure File Sync 同期サーバーを構成することはお勧めしません。 詳細については、「[推奨されるシステム リソース](#recommended-system-resources)」を参照してください。

### <a name="recommended-system-resources"></a>推奨されるシステム リソース
サーバーの機能やアプリケーションと同様に、Azure File Sync のシステム リソース要件は、デプロイの規模によって決まります。サーバー上の大規模なデプロイでは、より多くのシステム リソースが必要になります。 Azure File Sync の場合、スケールは、サーバー エンドポイント全体のオブジェクト数とデータセットのチャーンによって決まります。 1 台のサーバーは複数の同期グループにサーバー エンドポイントを持つことができ、次の表に示すオブジェクトの数では、サーバーが接続されている完全な名前空間が考慮されています。 

たとえば、サーバー エンドポイント A (1000 万オブジェクト) + サーバー エンドポイント B (1000 万オブジェクト) = 2000 万オブジェクトです。 その例のデプロイでは、8 個の CPU と、安定状態の場合は 16 GiB メモリ、初期移行の場合は (可能であれば) 48 GiB のメモリが推奨されます。
 
名前空間データは、パフォーマンス上の理由からメモリに格納されます。 そのため、よいパフォーマンスを維持するには、名前空間が大きいほど多くのメモリが必要であり、チャーンが多いほど処理に必要な CPU が増えます。 
 
次の表では、名前空間のサイズと、一般的な汎用ファイル共有の容量への変換の両方を示してあります。ファイルの平均サイズは 512 KiB です。 ファイル サイズが小さい場合は、同じ容量になるようにメモリを追加することを検討してください。 名前空間のサイズに基づいてメモリを構成します。

| 名前空間のサイズ - ファイルとディレクトリ (百万)  | 一般的な容量 (TiB)  | CPU コア  | 推奨されるメモリ (GiB) |
|---------|---------|---------|---------|
| 3        | 1.4     | 2        | 8 (初期同期)/2 (通常のチャーン)      |
| 5        | 2.3     | 2        | 16 (初期同期)/4 (通常のチャーン)    |
| 10       | 4.7     | 4        | 32 (初期同期)/8 (通常のチャーン)   |
| 30       | 14.0    | 8        | 48 (初期同期)/16 (通常のチャーン)   |
| 50       | 23.3    | 16       | 64 (初期同期)/32 (通常のチャーン)  |
| 100*     | 46.6    | 32       | 128 (初期同期)/32 (通常のチャーン)  |

\*現時点では、1 億を超えるファイルとディレクトリを同期することは推奨されていません。 これは、テストされたしきい値に基づくソフト制限です。 詳細については、「[Azure Files のスケーラビリティおよびパフォーマンスのターゲット](storage-files-scale-targets.md#azure-file-sync-scale-targets)」を参照してください。

> [!TIP]
> 名前空間の初期同期は集中的操作であるため、初期同期が完了するまでは、より多くのメモリを割り当てることをお勧めします。 これは必須ではありませんが、初期同期の時間が短くなる場合があります。 
> 
> 通常のチャーンは、1 日に変更される名前空間の 0.5% です。 チャーンのレベルがそれより高い場合は、CPU を追加することを検討してください。 

- NTFS ファイル システムでフォーマットされたローカルに接続されたボリューム。

### <a name="evaluation-cmdlet"></a>評価コマンドレット
Azure File Sync をデプロイする前に、Azure File Sync 評価コマンドレットを使用して、お使いのシステムと互換性があるかどうかを評価する必要があります。 このコマンドレットでは、サポートされていない文字やサポートされていないオペレーティング システム バージョンなど、ファイル システムとデータセットに関する潜在的な問題をチェックします。 そのチェックでは、以下で説明する機能のほとんどがカバーされていますが、すべてではありません。デプロイが円滑に進むように、このセクションの残りの部分をよく読むことをお勧めします。 

評価コマンドレットをインストールするには、Az PowerShell モジュールをインストールします。これは、次の手順に従ってインストールできます。[Azure PowerShell をインストールして構成](https://docs.microsoft.com/powershell/azure/install-Az-ps)します。

#### <a name="usage"></a>使用法  
複数の方法で評価ツールを呼び出すことができます。システム チェックとデータセット チェックのどちらか一方または両方を行うことができます。 システム チェックとデータセット チェックの両方を実行するには: 

```powershell
Invoke-AzStorageSyncCompatibilityCheck -Path <path>
```

データセットのみをテストするには:
```powershell
Invoke-AzStorageSyncCompatibilityCheck -Path <path> -SkipSystemChecks
```
 
システム要件のみをテストするには:
```powershell
Invoke-AzStorageSyncCompatibilityCheck -ComputerName <computer name>
```
 
CSV で結果を表示するには:
```powershell
$errors = Invoke-AzStorageSyncCompatibilityCheck […]
$errors | Select-Object -Property Type, Path, Level, Description | Export-Csv -Path <csv path>
```

### <a name="file-system-compatibility"></a>ファイル システムの互換性
Azure File Sync は、直接接続された NTFS ボリュームでのみサポートされています。 Windows Server のダイレクト アタッチド ストレージ (DAS) は、Windows Server オペレーティング システムがファイル システムを所有していることを意味します。 DAS は、ディスクをファイル サーバーに物理的に接続すること、仮想ディスクをファイル サーバー VM (Hyper-V によってホストされる VM など) に接続すること、または ISCSI を使用して提供されます。

NTFS ボリュームのみがサポートされます。ReFS、FAT、FAT32 およびその他のファイル システムはサポートされていません。

次の表に、NTFS ファイル システムの機能の相互運用の状態を示します。 

| 機能 | サポートの状態 | Notes |
|---------|----------------|-------|
| アクセス制御リスト (ACL) | 完全にサポートされています | Windows スタイルの随意アクセス制御リストは Azure File Sync に保持され、サーバー エンドポイント上の Windows Server によって適用されます。 Azure ファイル共有を直接マウントするときに ACL を適用することもできますが、これには追加の構成が必要です。 詳細については、[ID に関するセクション](#identity)を参照してください。 |
| ハード リンク | スキップ | |
| シンボリック リンク | スキップ | |
| マウント ポイント | 部分的にサポートされています。 | マウント ポイントがサーバー エンドポイントのルートである場合がありますが、マウント ポイントがサーバー エンドポイントの名前空間に含まれている場合、マウント ポイントはスキップされます。 |
| 接合 | スキップ | たとえば、分散ファイル システムの DfrsrPrivate フォルダーや DFSRoots フォルダーなどです。 |
| 再解析ポイント | スキップ | |
| NTFS 圧縮 | 完全にサポートされています | |
| スパース ファイル | 完全にサポートされています | スパース ファイルは同期されます (ブロックされません) が、完全なファイルとしてクラウドと同期されます。 クラウド内 (または他のサーバー上) のファイルの内容が変更され、変更がダウンロードされると、ファイルはスパースではなくなります。 |
| 代替データ ストリーム (ADS) | 保持されますが、同期されません | たとえば、ファイル分類インフラストラクチャによって作成された分類タグは同期されません。 各サーバー エンドポイント上のファイルに既存の分類タグは、変更されません。 |

<a id="files-skipped"></a>Azure File Sync は、次のような特定の一時ファイルとシステム フォルダーもスキップします。

| ファイル/フォルダー | Note |
|-|-|
| pagefile.sys | システムに固有のファイル |
| Desktop.ini | システムに固有のファイル |
| thumbs.db | サムネイル用の一時ファイル |
| ehthumbs.db | メディア サムネイル用の一時ファイル |
| ~$\*.\* | Office の一時ファイル |
| \*.tmp | 一時ファイル |
| \*.laccdb | Access データベースのロック ファイル|
| 635D02A9D91C401B97884B82B3BCDAEA.* | 内部の同期ファイル|
| \\System Volume Information | ボリュームに固有のフォルダー |
| $RECYCLE.BIN| Folder |
| \\SyncShareState | 同期用のフォルダー |

### <a name="failover-clustering"></a>フェールオーバー クラスタリング
Windows Server フェールオーバー クラスタリングは、Azure ファイル同期の "汎用ファイル サーバー" デプロイ オプションでサポートされています。 フェールオーバー クラスタリングは、"アプリケーション データ用のスケールアウト ファイル サーバー" (SOFS) またはクラスター共有ボリューム (CSV) ではサポートされていません。

> [!Note]  
> 同期が適切に機能するには、フェールオーバー クラスターのすべてのノードに Azure File Sync エージェントをインストールする必要があります。

### <a name="data-deduplication"></a>データ重複除去
**Windows Server 2016 および Windows Server 2019**   
データ重複除去は、Windows Server 2016 および Windows Server 2019 でクラウドを使った階層化が有効になっているボリュームでサポートされます。 クラウドを使った階層化が有効なボリュームでデータ重複除去を有効にすると、より多くのストレージをプロビジョニングしなくても、より多くのファイルをオンプレミスでキャッシュできます。 

クラウドを使った階層化が有効なボリュームでデータ重複除去が有効になっている場合、サーバー エンドポイントの場所にある重複除去最適化ファイルは、クラウドを使った階層化のポリシー設定に基づいて、通常のファイルと同様に階層化されます。 重複除去最適化ファイルが階層化された後、データ重複除去ガベージ コレクション ジョブが自動的に実行され、ボリューム上の他のファイルから参照されなくなった不要なチャンクを削除することによって、ディスク領域が解放されます。

ボリュームの節約はサーバーにのみ適用されることに注意してください。Azure ファイル共有内のデータは重複除去されません。

> [!Note]  
> Windows Server 2019 でクラウドを使った階層化が有効になっているボリュームでデータ重複除去をサポートするには、Windows 更新プログラム [KB4520062](https://support.microsoft.com/help/4520062) をインストールし、Azure File Sync エージェント バージョン 9.0.0.0 以降が必要です。

**Windows Server 2012 R2**  
Azure File Sync については、データ重複除去とクラウドを使った階層化は、Windows Server 2012 R2 の同じボリューム上ではサポートされていません。 ボリュームでデータ重複除去が有効になっている場合は、クラウドを使った階層化を無効にする必要があります。 

**メモ**
- Azure File Sync エージェントをインストールする前にデータ重複除去がインストールされている場合、同じボリューム上でのデータ重複除去およびクラウドを使った階層化をサポートするには再起動が必要です。
- クラウドを使った階層化を有効にした後にボリューム上のデータ重複除去を有効にする場合、最初の重複除去最適化ジョブで、そのボリューム上のまだ階層化されていないファイルが最適化され、クラウドを使った階層化に次のような影響を与えます。
    - 空き領域ポリシーに従い、ボリューム上の空き領域に応じて、ヒートマップを使用してファイルが継続的に階層化されます。
    - 重複除去最適化ジョブがファイルにアクセスしているために、本来ならば階層化の対象となっていたかもしれないファイルの階層化が日付ポリシーによってスキップされます。
- 進行中の重複除去最適化ジョブについては、ファイルがまだ階層化されていない場合は、データ ポリシーでのクラウドを使った階層化が、データ重複除去の [MinimumFileAgeDays](https://docs.microsoft.com/powershell/module/deduplication/set-dedupvolume?view=win10-ps) 設定により遅延します。 
    - 例:MinimumFileAgeDays 設定が 7 日、クラウドを使った階層化の日付ポリシーが 30 日の場合、日付ポリシーによって 37 日後にファイルが階層化されます。
    - 注:Azure File Sync によってファイルが階層化されると、重複除去最適化ジョブによってそのファイルはスキップされます。
- インストールされている Azure File Sync エージェントを使用して Windows Server 2012 R2 を実行しているサーバーが、Windows Server 2016 または Windows Server 2019 にアップグレードされる場合、同じボリューム上でのデータ重複除去およびクラウドを使った階層化がサポートされるには次の手順を行う必要があります。  
    - Windows Server 2012 R2 用の Azure File Sync エージェントをアンインストールして、サーバーを再起動します。
    - 新しいサーバーのオペレーティング システム バージョン用 (Windows Server 2016 または Windows Server 2019) の Azure File Sync エージェントをダウンロードします。
    - Azure File Sync エージェントをインストールして、サーバーを再起動します。  
    
    注:サーバー上の Azure File Sync 構成設定は、エージェントがアンインストールされ再インストールされるときに保持されます。

### <a name="distributed-file-system-dfs"></a>分散ファイル システム (DFS)
Azure File Sync は、DFS 名前空間 (DFS-N) および DFS レプリケーション (DFS-R) との相互運用をサポートします。

**DFS 名前空間 (DFS-N)** :Azure File Sync は DFS-N サーバーで完全にサポートされます。 1 つまたは複数の DFS-N メンバーに Azure File Sync をインストールして、サーバー エンドポイントとクラウド エンドポイントの間でデータを同期できます。 詳しくは、「[DFS 名前空間の概要](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview)」をご覧ください。
 
**DFS レプリケーション (DFS-R)** :DFS-R と Azure File Sync はどちらもレプリケーション ソリューションなので、ほとんどの場合は、DFS-R を Azure File Sync に置き換えることをお勧めします。ただし、DFS-R と Azure File Sync を併用するのが望ましいシナリオがいくつかあります。

- DFS-R のデプロイから Azure File Sync のデプロイに移行しているとき。 詳しくは、「[DFS レプリケーション (DFS-R) のデプロイを Azure File Sync に移行する](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync)」をご覧ください。
- ファイル データのコピーが必要なオンプレミスのサーバーの中に、インターネットに直接接続することができないものが含まれる場合。
- ブランチ サーバーが単一のハブ サーバーにデータを統合しており、それに対して Azure File Sync を使いたい場合。

Azure File Sync と DFS-R を併用するには、次のことが必要です。

1. DFS-R でレプリケートされるフォルダーを含むボリュームで、Azure File Sync のクラウドの階層化を無効にする必要があります。
2. DFS-R の読み取り専用レプリケーション フォルダーには、サーバー エンドポイントを構成しないようにする必要があります。

詳しくは、[DFS レプリケーションの概要](https://technet.microsoft.com/library/jj127250)に関するページをご覧ください。

### <a name="sysprep"></a>Sysprep
Azure File Sync エージェントがインストールされているサーバー上で sysprep を使用することはサポートされていません。予期しない結果になる可能性があります。 エージェントのインストールとサーバーの登録は、サーバー イメージの展開と sysprep ミニ セットアップの完了後に行われます。

### <a name="windows-search"></a>Windows Search
クラウドの階層化がサーバー エンドポイントで有効になっている場合、階層化されたファイルはスキップされ、Windows Search によってインデックスが付けられます。 階層化されていないファイルは適切にインデックスが付けられます。

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>その他の階層型ストレージ管理 (HSM) ソリューション
その他の HSM ソリューションを Azure File Sync で使用することはできません。

## <a name="identity"></a>ID
Azure File Sync は、同期のセットアップ以外に特別な設定を行わなくても、標準の AD ベースの ID で動作します。Azure File Sync を使用する場合に一般的に想定されるのは、ほとんどのアクセスが Azure ファイル共有ではなく、Azure File Sync キャッシュ サーバーを経由することです。 サーバー エンドポイントは Windows Server 上にあり、Windows Server では AD と Windows スタイルの ACL が長い間サポートされてきたため、ストレージ同期サービスに登録されている Windows ファイル サーバーがドメインに参加していることを確認する以外は何も必要ありません。 Azure File Sync では、Azure ファイル共有内のファイルに ACL が格納され、それらはすべてのサーバー エンドポイントにレプリケートされます。

Azure ファイル共有に直接加えられた変更は、同期グループ内のサーバー エンドポイントに同期するのに時間がかかる場合がありますが、ファイル共有に対する AD アクセス許可をクラウドで直接適用できることも確認する必要があります。 これを行うには、Windows ファイル サーバーがドメイン参加しているのと同じように、ストレージ アカウントをオンプレミスの AD にドメイン参加させる必要があります。 お客様が所有する Active Directory へのストレージ アカウントのドメイン参加について詳しくは、[Azure Files Active Directory の概要](storage-files-active-directory-overview.md)に関するページを参照してください。

> [!Important]  
> Azure File Sync を正常にデプロイするために、ストレージ アカウントを Active Directory にドメイン参加させる必要はありません。これは、ユーザーが Azure ファイル共有を直接マウントするときにオンプレミスの ACL を適用することを Azure ファイル共有に許可する、厳密には省略可能な手順です。

## <a name="networking"></a>ネットワーク
Azure File Sync エージェントは、Azure File Sync REST プロトコルと FileREST プロトコルを使用して、ストレージ同期サービスと Azure ファイル共有と通信します。どちらも、常にポート443 経由で HTTPS を使用します。 SMB は、Windows Server と Azure ファイル共有の間でデータをアップロードまたはダウンロードするために使用されることはありません。 ほとんどの組織では、ほぼすべての Web サイトにアクセスするための要件として、ポート 443 経由の HTTPS トラフィックを許可しているため、通常、Azure File Sync をデプロイするために特別なネットワーク構成は必要ありません。

組織のポリシーまたは独自の規制要件に基づいて、Azure とのより制限的な通信が必要になる可能性があります。そのため、Azure File Sync は、ネットワークを構成するためのいくつかのメカニズムを提供します。 要件に応じて、次のことができます。

- ExpressRoute または Azure VPN 経由のトンネル同期とファイルのアップロードまたはダウンロード トラフィック。 
- Azure Files と Azure のネットワーク機能 (サービス エンドポイント、プライベート エンドポイントなど) の使用。
- お使いの環境でプロキシをサポートするよう Azure File Sync を構成。
- Azure File Sync からネットワーク アクティビティを調整。

Azure File Sync のネットワーク機能の構成の詳細については、以下を参照してください。
- [Azure File Sync のプロキシとファイアウォールの設定](storage-sync-files-firewall-and-proxy.md)
- [データセンターで Azure File Sync が良好なネイバーであることを確認する](storage-sync-files-server-registration.md)

## <a name="encryption"></a>暗号化
Azure File Sync を使用する場合、Windows Server のストレージの保存時の暗号化、Azure File Sync エージェントと Azure 間での暗号化、Azure ファイル共有のデータの暗号化という、3 つの異なるレベルの暗号化を考慮する必要があります。 

### <a name="windows-server-encryption-at-rest"></a>Windows Server の保存時の 暗号化 
一般的に Azure File Sync で機能する Windows Server 上のデータを暗号化する方法には、ファイル システムとそれに書き込まれたすべてのデータが暗号化されるファイル システムにおける暗号化と、ファイル形式自体での暗号化という、2 つの方法があります。 これらのメソッドは相互に排他的ではありません。暗号化の目的が異なるため、必要に応じて組み合わせて使用できます。

そのファイル システムにおける暗号化機能を提供するために、Windows Server は BitLocker 受信トレイを提供します。 BitLocker は Azure File Sync に対して完全に透過的です。BitLocker などの暗号化メカニズムを使用する主な理由は、ディスクの盗難によるオンプレミスのデータセンターからの物理的なデータの流出を防ぎ、権限のない OS によってデータへの不正読み取り、または不正書き込みが行われるのを防ぐためです。 BitLocker の詳細については、「[BitLocker の概要](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview)」を参照してください。

NTFS ボリューム下に配置されるという点で BitLocker と同様に機能するサード パーティ製品は、Azure File Sync で同様に完全に透過的に機能するはずです。 

データを暗号化するもう 1 つの主な方法は、アプリケーションがファイルを保存するときにファイルのデータ ストリームを暗号化することです。 アプリケーションによっては、これがネイティブに実行されることもありますが、一般的にはそうではありません。 ファイルのデータ ストリームを暗号化する方法の例としては、Azure Information Protection (AIP)/Azure Rights Management Services (Azure RMS)/Active Directory RMS があります。 AIP/RMS などの暗号化メカニズムを使用する主な理由は、ファイル共有のデータを、誰かがフラッシュ ドライブなどの別の場所にコピーしたり、承認されていないユーザーに電子メールで送信したりすることによって、データがファイル共有から流出することを防ぐためです。 ファイルのデータ ストリームがファイル形式の一部として暗号化されている場合、このファイルは引き続き Azure ファイル共有で暗号化されます。 

Azure File Sync は、ファイル システムより上に位置し、ファイルのデータ ストリームの下にある NTFS 暗号化ファイル システム (NTFS EFS) やサード パーティの暗号化ソリューションと相互運用できません。 

### <a name="encryption-in-transit"></a>転送中の暗号化

> [!NOTE]
> Azure File Sync サービスは、2020 年 8 月に TLS 1.0 と 1.1 のサポートを削除します。 サポートされているすべての Azure File Sync エージェントのバージョンは、既に TLS 1.2 を既定で使用しています。 TLS 1.2 がサーバーで無効になっているか、プロキシが使用されている場合は、以前のバージョンの TLS が使用される可能性があります。 プロキシを使用している場合は、プロキシの構成を確認することをお勧めします。 2020 年 5 月 1 日より後に追加された Azure File Sync サービスのリージョンでは TLS 1.2 のみがサポートされます。TLS 1.0 と 1.1 のサポートは、2020 年 8 月に既存のリージョンから削除されます。  詳細については、[トラブルシューティング ガイド](storage-sync-files-troubleshoot.md#tls-12-required-for-azure-file-sync)を参照してください。

Azure File Sync エージェントは、Azure File Sync REST プロトコルと FileREST プロトコルを使用して、ストレージ同期サービスと Azure ファイル共有と通信します。どちらも、常にポート443 経由で HTTPS を使用します。 Azure File Sync では、暗号化されていない要求が HTTP 経由で送信されることはありません。 

Azure ストレージ アカウントには、転送中の暗号化を要求するスイッチが含まれています。これは既定で有効になっています。 ストレージ アカウント レベルでスイッチが無効になっており、Azure ファイル共有への暗号化されていない接続が可能な場合でも、Azure File Sync は暗号化されたチャネルのみを使用してファイル共有にアクセスします。

ストレージ アカウントの転送中の暗号化を無効にする主な理由は、古いオペレーティング システム (Windows Server 2008 R2 や古い Linux ディストリビューションなど) 上で実行する必要のある、Azure ファイル共有と直接通信するレガシ アプリケーションをサポートするためです。 レガシ アプリケーションとファイル共有の Windows Server キャッシュが通信する場合、この設定を切り替えても効果はありません。 

転送中のデータの暗号化が有効になっていることを確認することを強くお勧めします。

転送中の暗号化の詳細については、「[Azure Storage で安全な転送が必要](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)」を参照してください。

### <a name="azure-file-share-encryption-at-rest"></a>Azure ファイル共有の保存時の暗号化
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="storage-tiers"></a>ストレージ層
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>標準ファイル共有を最大 100 TiB にまたげるようにする
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="regional-availability"></a>リージョン別の提供状況
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="azure-file-sync-region-availability"></a>Azure File Sync の利用可能なリージョン
Azure File Sync は、次のリージョンで利用できます。

| Azure cloud | 地理的リージョン | Azure リージョン | リージョン コード |
|-------------|-------------------|--------------|-------------|
| パブリック | アジア | 東アジア | `eastasia` |
| パブリック | アジア | 東南アジア | `southeastasia` |
| パブリック | オーストラリア | オーストラリア東部 | `australiaeast` |
| パブリック | オーストラリア | オーストラリア南東部 | `australiasoutheast` |
| パブリック | ブラジル | ブラジル南部 | `brazilsouth` |
| パブリック | カナダ | カナダ中部 | `canadacentral` |
| パブリック | カナダ | カナダ東部 | `canadaeast` |
| パブリック | ヨーロッパ | 北ヨーロッパ | `northeurope` |
| パブリック | ヨーロッパ | 西ヨーロッパ | `westeurope` |
| パブリック | フランス | フランス中部 | `francecentral` |
| パブリック | フランス | フランス南部* | `francesouth` |
| パブリック | インド | インド中部 | `centralindia` |
| パブリック | インド | インド南部 | `southindia` |
| パブリック | 日本 | 東日本 | `japaneast` |
| パブリック | 日本 | 西日本 | `japanwest` |
| パブリック | 韓国 | 韓国中部 | `koreacentral` |
| パブリック | 韓国 | 韓国南部 | `koreasouth` |
| パブリック | 南アフリカ | 南アフリカ北部 | `southafricanorth` |
| パブリック | 南アフリカ | 南アフリカ西部* | `southafricawest` |
| パブリック | UAE | アラブ首長国連邦中部* | `uaecentral` |
| パブリック | UAE | アラブ首長国連邦北部 | `uaenorth` |
| パブリック | 英国 | 英国南部 | `uksouth` |
| パブリック | 英国 | 英国西部 | `ukwest` |
| パブリック | US | 米国中部 | `centralus` |
| パブリック | US | 米国東部 | `eastus` |
| パブリック | US | 米国東部 2 | `eastus2` |
| パブリック | US | 米国中北部 | `northcentralus` |
| パブリック | US | 米国中南部 | `southcentralus` |
| パブリック | US | 米国中西部 | `westcentralus` |
| パブリック | US | 米国西部 | `westus` |
| パブリック | US | 米国西部 2 | `westus2` |
| US Gov | US | US Gov アリゾナ | `usgovarizona` |
| US Gov | US | US Gov テキサス | `usgovtexas` |
| US Gov | US | US Gov バージニア州 | `usgovvirginia` |

Azure File Sync は、ストレージ同期サービスと同じリージョンの Azure ファイル共有との同期のみをサポートしています。

アスタリスクが付いているリージョンでは、Azure サポートに連絡して、これらのリージョンの Azure Storage へのアクセス権を要求する必要があります。 このプロセスについては、[このドキュメント](https://azure.microsoft.com/global-infrastructure/geographies/)で概説されています。

## <a name="redundancy"></a>冗長性
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

> [!Important]  
> Geo 冗長ストレージおよび Geo ゾーン冗長ストレージには、セカンダリ リージョンに手動でストレージをフェールオーバーする機能があります。 データ損失の可能性が高くなるため、Azure File Sync を使用している場合は、障害が発生していない場合にこの操作を行わないことをお勧めします。 ストレージの手動フェールオーバーを開始する必要がある障害が発生した場合は、Azure File Sync でセカンダリ エンドポイントとの同期が再開されるよう、Microsoft のサポートケースを開く必要があります。

## <a name="migration"></a>移行
既存の Windows ファイル サーバーがある場合は、新しいサーバーにデータを移動しなくても、Azure File Sync を直接インストールすることができます。 Azure File Sync の導入の一環として新しい Windows ファイル サーバーへの移行を計画している場合は、データを移動する方法がいくつかあります。

- 古いファイル共有と新しいファイル共有のサーバー エンドポイントを作成し、Azure File Sync を使用してサーバー エンドポイント間でデータを同期させます。 この方法の利点は、クラウドを使った階層化に対応している Azure File Sync であるため、新しいファイル サーバーのストレージをオーバーサブスクライブすることが非常に簡単になることです。 準備ができたら、エンド ユーザーを新しいサーバー上のファイル共有に切り替え、古いファイル共有のサーバー エンドポイントを削除します。

- 新しいファイル サーバーにのみサーバー エンドポイントを作成し、`robocopy` を使用して古いファイル共有からデータをコピーします。 新しいサーバー上のファイル共有のトポロジ (各ボリュームで保持されている共有の数、各ボリュームの空き領域など) に応じて、追加のストレージを一時的にプロビジョニングする必要があります。これは、オンプレミスのデータセンター内の古いサーバーから新しいサーバーへの `robocopy` は、Azure File Sync がデータを Azure に移動するよりも短時間で完了するためです。

Data Box を使用して、Azure File Sync のデプロイにデータを移行することもできます。 ほとんどの場合、お客様がデータを取り込むために Data Box を使用するのは、デプロイの速度が向上すると考えたか、または制限された帯域幅シナリオで役立つことがあると考えたためです。 Azure File Sync のデプロイへデータを取り込むために Data Box を使用すると、帯域幅の使用率が低下することは事実ですが、ほとんどのシナリオでは、上記の方法のいずれかを使用してオンライン データのアップロードを実行する方が短時間で済みます。 Data Box を使用して Azure File Sync のデプロイにデータを取り込む方法の詳細については、「[Azure Data Box を使用して Azure File Sync にデータを移行する](storage-sync-offline-data-transfer.md)」を参照してください。

新しい Azure File Sync のデプロイにデータを移行する際によくある間違いは、Windows ファイル サーバーではなく Azure ファイル共有にデータを直接コピーしてしまうことです。 Azure File Sync は Azure ファイル共有上のすべての新しいファイルを識別し、それらを Windows ファイル共有に同期しますが、通常、これは Windows ファイル サーバーを使用してデータを読み込む場合よりもはるかに低速です。 AzCopy などの多くの Azure のコピー ツールには、さらにタイムスタンプや ACL などのファイルの重要なメタデータをすべてコピーしないという欠点があります。

## <a name="antivirus"></a>ウイルス対策
ウイルス対策は、ファイルをスキャンして既知の悪意のあるコードを検索することで機能するので、ウイルス対策製品によって階層化されたファイルの再呼び出しが発生することがあります。 Azure File Sync エージェントのバージョン 4.0 以降では、階層化されたファイルにはセキュリティで保護された Windows 属性 FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS が設定されています。 この属性を設定してオフライン ファイルの読み取りをスキップするようにソリューションを構成する方法について、ソフトウェア ベンダーと相談することをお勧めします (多くの場合は自動実行されます)。 

Microsoft の社内ウイルス対策ソリューションである Windows Defender と System Center Endpoint Protection (SCEP) では、この属性が設定されたファイルの読み取りが自動的にスキップされます。 そのテスト結果として小さな問題点がわかりました。既存の同期グループにサーバーを追加すると、新しいサーバー上で 800 バイト未満のファイルの呼び戻し (ダウンロード) が実行されるという問題です。 このようなファイルは新しいサーバー上に残り、階層化のサイズ要件 (64 KB を超えるサイズ) を満たしていないため、階層化されません。

> [!Note]  
> ウイルス対策ソフトウェア ベンダーは、その製品と Azure File Sync との間の互換性を、[Azure File Sync Antivirus Compatibility Test Suite](https://www.microsoft.com/download/details.aspx?id=58322) を使用して確認することができます。これは、Microsoft ダウンロード センターでダウンロードできます。

## <a name="backup"></a>バックアップ 
ウイルス対策ソリューションと同様に、バックアップ ソリューションでも階層化されたファイルの再呼び出しが発生することがあります。 Azure ファイル共有をバックアップするには、オンプレミスのバックアップ製品ではなく、クラウド バックアップ ソリューションを使用することをお勧めします。

オンプレミス バックアップ ソリューションを使用している場合、クラウドの階層化が無効な同期グループ内のサーバーに対してバックアップを実行する必要があります。 復元を実行するときは、ボリューム レベルまたはファイル レベルの復元オプションを使用します。 ファイル レベルの復元オプションを使用して復元されたファイルは、同期グループ内のすべてのエンドポイントに同期され、既存のファイルはバックアップから復元されたバージョンに置き換えられます。  ボリューム レベルの復元では、Azure ファイル共有やその他のサーバー エンドポイントの新しいファイル バージョンに置き換えられません。

> [!Note]  
> ベアメタル (BMR) 復元は予期しない結果が生じることがあるため、現在サポートされていません。

> [!Note]  
> Azure File Sync エージェントのバージョン 9 では、VSS スナップショット ([以前のバージョン] タブを含む) が、クラウドの階層化が有効なボリュームでサポートされるようになりました。 ただし、PowerShell を使用して以前のバージョンの互換性を有効にする必要があります。 方法については、[こちら](storage-files-deployment-guide.md)をご覧ください。

## <a name="azure-file-sync-agent-update-policy"></a>Azure ファイル同期エージェントの更新ポリシー
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>次のステップ
* [ファイアウォールとプロキシの設定の考慮事項](storage-sync-files-firewall-and-proxy.md)
* [Azure Files のデプロイの計画](storage-files-planning.md)
* [Azure Files をデプロイする](storage-files-deployment-guide.md)
* [Azure File Sync をデプロイする](storage-sync-files-deployment-guide.md)
* [Azure File Sync の監視](storage-sync-files-monitoring.md)