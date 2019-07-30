---
title: Azure File Sync のデプロイの計画 | Microsoft Docs
description: Azure Files のデプロイを計画するときの考慮事項について説明します。
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 2/7/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: e9e790ac8ac67478a0e7b5143a5b2f1fdd9c790c
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798674"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>Azure File Sync のデプロイの計画
Azure File Sync を使用すると、オンプレミスのファイル サーバーの柔軟性、パフォーマンス、互換性を維持したまま Azure Files で組織のファイル共有を一元化できます。 Azure File Sync により、ご利用の Windows Server が Azure ファイル共有の高速キャッシュに変わります。 SMB、NFS、FTPS など、Windows Server 上で利用できるあらゆるプロトコルを使用して、データにローカルにアクセスできます。 キャッシュは、世界中にいくつでも必要に応じて設置することができます。

この記事では、Azure File Sync をデプロイする際の重要な考慮事項について説明します。 「[Azure Files のデプロイの計画](storage-files-planning.md)」も読むことをお勧めします。 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="azure-file-sync-terminology"></a>Azure File Sync の用語
Azure File Sync のデプロイの計画の詳細な説明に入る前に、用語を理解することが重要です。

### <a name="storage-sync-service"></a>ストレージ同期サービス
ストレージ同期サービスは、Azure File Sync の最上位レベルの Azure リソースです。ストレージ同期サービス リソースは、ストレージ アカウント リソースのピアであり、同じように Azure リソース グループにデプロイできます。 ストレージ アカウント リソースの個別の最上位レベルのリソースが必要です。ストレージ同期サービスは、複数の同期グループを介して複数のストレージ アカウントとの同期関係を作成する可能性があるためです。 1 つのサブスクリプションで、複数の ストレージ同期サービス リソースをデプロイすることができます。

### <a name="sync-group"></a>同期グループ
同期グループは、一連のファイルの同期トポロジを定義します。 同期グループ内のエンドポイントは、相互に同期を維持されます。 たとえば、Azure File Sync で管理するファイル セットが 2 組ある場合、2 つの同期グループを作成し、各同期グループに別のエンドポイントを追加します。 ストレージ同期サービスは、必要に応じて任意の数の同期グループをホストできます。  

### <a name="registered-server"></a>登録済みサーバー
登録済みサーバー オブジェクトは、サーバー (またはクラスター) とストレージ同期サービス間の信頼関係を表します。 ストレージ同期サービス インスタンスには、任意の数のサーバーを登録できます。 ただし、1 つのサーバー (またはクラスター) は、同時に 1 つのストレージ同期サービスのみに登録できます。

### <a name="azure-file-sync-agent"></a>Azure ファイル同期エージェント
Azure File Sync エージェントは、Windows Server を Azure ファイル共有と同期できるようにするダウンロード可能なパッケージです。 Azure File Sync エージェントには、3 つの主要コンポーネントがあります。 
- **FileSyncSvc.exe**:バックグラウンドの Windows サービス。サーバー エンドポイントの変更の監視と、Azure に対する同期セッションの開始を担当します。
- **StorageSync.sys**:Azure File Sync ファイル システム フィルターであり、Azure Files に対するファイルの階層化を担当します (クラウドの階層化が有効な場合)。
- **PowerShell 管理コマンドレット**:Microsoft.StorageSync Azure リソース プロバイダーと対話するために使用する PowerShell コマンドレット。 これらは、次の (既定の) 場所で見つけることができます。
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll

### <a name="server-endpoint"></a>サーバー エンドポイント
サーバー エンドポイントは、登録済みサーバー上の特定の場所を表します。たとえば、サーバー ボリュームのフォルダーなどです。 名前空間が重複していなければ、同じボリューム上に複数のサーバー エンドポイントが存在できます (たとえば `F:\sync1` と `F:\sync2`)。 各サーバー エンドポイントについて、クラウドの階層化ポリシーを個別に構成することができます。 

マウント ポイントを使用してサーバー エンドポイントを作成できます。 サーバー エンドポイント内のマウントポイントはスキップされることに注意してください。  

システム ボリュームでサーバー エンドポイントを作成することはできますが、その場合、次の 2 つの制限があります。
* クラウド階層化を有効にすることはできません。
* 名前空間の迅速な復元 (システムが名前空間全体をすばやく停止した後、起動してコンテンツを呼び戻す) は実行されません。


> [!Note]  
> 非リムーバブル ボリュームのみがサポートされます。  サーバー エンドポイントのパスでは、リモート共有からマップされたドライブはサポートされていません。  また、システム ボリュームでは、クラウド階層経由の Windows システム ボリューム上にあるサーバー エンドポイントはサポートされていません。

既存の一連のファイルを含むサーバーの場所をサーバー エンドポイントとして同期グループに追加すると、それらのファイルは、同期グループの他のエンドポイント上に既にある他のファイルと統合されます。

### <a name="cloud-endpoint"></a>クラウド エンドポイント
クラウド エンドポイントは、同期グループに含まれる Azure ファイル共有です。 Azure ファイル共有の同期全体と 1 つの Azure ファイル共有は、1 つのクラウド エンドポイントのみのメンバーになることができます。 したがって、Azure ファイル共有は、1 つの同期グループのみのメンバーになることができます。 既存の一連のファイルを含む Azure ファイル共有をクラウド エンドポイントとして同期グループに追加すると、既存のファイルは、同期グループの他のエンドポイント上に既にある他のファイルと統合されます。

> [!Important]  
> Azure File Sync は、Azure ファイル共有に対する直接的な変更をサポートします。 ただし、Azure ファイル共有に対して行われた変更は、まず Azure File Sync の変更検出ジョブによって認識される必要があります。 クラウド エンドポイントに対する変更検出ジョブは、24 時間に 1 回のみ起動されます。 さらに、REST プロトコルで Azure ファイル共有に対して行われた変更は、SMB の最終更新時刻を更新するものではなく、同期による変更とは見なされません。詳細については、「[Azure Files についてよく寄せられる質問 (FAQ)](storage-files-faq.md#afs-change-detection)」を参照してください。

### <a name="cloud-tiering"></a>クラウドの階層化 
クラウドの階層化は Azure File Sync のオプション機能です。この機能では、頻繁にアクセスされるファイルがサーバー上にローカルにキャッシュされ、その他のファイルはポリシー設定に基づいて Azure Files に階層化されます。 詳細については、[クラウドの階層化](storage-sync-cloud-tiering.md)に関するページを参照してください。

## <a name="azure-file-sync-system-requirements-and-interoperability"></a>Azure File Sync システムの要件と相互運用性 
このセクションでは、Windows Server の機能とロール、およびサード パーティ ソリューションとの Azure File Sync エージェント システムの要件と相互運用性について説明します。

### <a name="evaluation-cmdlet"></a>評価コマンドレット
Azure File Sync をデプロイする前に、Azure File Sync 評価コマンドレットを使用して、お使いのシステムと互換性があるかどうかを評価する必要があります。 このコマンドレットでは、サポートされていない文字やサポートされていない OS バージョンなど、ファイル システムとデータセットに関する潜在的な問題をチェックします。 そのチェックでは、以下で説明する機能のほとんどがカバーされていますが、すべてではないことに注意してください。デプロイが円滑に進むように、このセクションの残りの部分をよく読むことをお勧めします。 

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

### <a name="system-requirements"></a>システム要件
- Windows Server 2012 R2、Windows Server 2016、または Windows Server 2019 を実行しているサーバー:

    | Version | サポートされている SKU | サポートされているデプロイ オプション |
    |---------|----------------|------------------------------|
    | Windows Server 2019 | Datacenter および Standard | 完全およびコア |
    | Windows Server 2016 | Datacenter および Standard | 完全およびコア |
    | Windows Server 2012 R2 | Datacenter および Standard | 完全およびコア |

    Windows Server の今後のバージョンは、それらがリリースされたときに追加されます。

    > [!Important]  
    > Azure File Sync で使用するすべてのサーバーは、Windows Update の最新の更新プログラムを使用して常に最新の状態を保つことをお勧めします。 

- 2 GiB 以上のメモリを装備しているサーバー。

    > [!Important]  
    > 動的メモリを有効にした仮想マシンでサーバーが実行されている場合は、2048 MiB 以上のメモリで VM を構成する必要があります。
    
- NTFS ファイル システムでフォーマットされたローカルに接続されたボリューム。

### <a name="file-system-features"></a>ファイル システムの機能

| 機能 | サポートの状態 | メモ |
|---------|----------------|-------|
| アクセス制御リスト (ACL) | 完全にサポートされます | Windows ACL は、Azure File Sync に保持され、サーバー エンドポイント上の Windows Server によって適用されます。 クラウドでファイルに直接アクセスする場合、Azure Files は Windows ACL を (まだ) サポートしていません。 |
| ハード リンク | Skipped | |
| シンボリック リンク | Skipped | |
| マウント ポイント | 部分的にサポート | マウント ポイントがサーバー エンドポイントのルートである場合がありますが、マウント ポイントがサーバー エンドポイントの名前空間に含まれている場合、マウント ポイントはスキップされます。 |
| 接合 | Skipped | たとえば、分散ファイル システムの DfrsrPrivate フォルダーや DFSRoots フォルダーなどです。 |
| 再解析ポイント | Skipped | |
| NTFS 圧縮 | 完全にサポートされます | |
| スパース ファイル | 完全にサポートされます | スパース ファイルは同期されます (ブロックされません) が、完全なファイルとしてクラウドと同期されます。 クラウド内 (または他のサーバー上) のファイルの内容が変更され、変更がダウンロードされると、ファイルはスパースではなくなります。 |
| 代替データ ストリーム (ADS) | 保持されますが、同期されません | たとえば、ファイル分類インフラストラクチャによって作成された分類タグは同期されません。 各サーバー エンドポイント上のファイルに既存の分類タグは、変更されません。 |

> [!Note]  
> NTFS ボリュームのみがサポートされます。 ReFS、FAT、FAT32 などのファイル システムはサポートされていません。

### <a name="files-skipped"></a>スキップされるファイル

| ファイル/フォルダー | Note |
|-|-|
| Desktop.ini | システムに固有のファイル |
| ethumbs.db$ | サムネイル用の一時ファイル |
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
**エージェント バージョン 5.0.2.0 以降**   
データ重複除去は、Windows Server 2016 および Windows Server 2019 でクラウドを使った階層化が有効になっているボリュームでサポートされます。 クラウドを使った階層化が有効なボリュームでデータ重複除去を有効にすると、より多くのストレージをプロビジョニングしなくても、より多くのファイルをオンプレミスでキャッシュできます。 

クラウドを使った階層化が有効なボリュームでデータ重複除去が有効になっている場合、サーバー エンドポイントの場所にある重複除去最適化ファイルは、クラウドを使った階層化のポリシー設定に基づいて、通常のファイルと同様に階層化されます。 重複除去最適化ファイルが階層化された後、データ重複除去ガベージ コレクション ジョブが自動的に実行され、ボリューム上の他のファイルから参照されなくなった不要なチャンクを削除することによって、ディスク領域が解放されます。

ボリュームの節約はサーバーにのみ適用されることに注意してください。Azure ファイル共有内のデータは重複除去されません。

**Windows Server 2012 R2 またはそれよりも古いエージェント バージョン**  
クラウドの階層化が有効ではないボリュームでは、Azure File Sync は、そのボリュームでの Windows Server のデータ重複除去の有効化をサポートします。

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
    - 新しいサーバーの OS バージョン用 (Windows Server 2016 または Windows Server 2019) の Azure File Sync エージェントをダウンロードします。
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
Azure File Sync エージェントがインストールされているサーバー上での sysprep の使用はサポートされていません。予期しない結果になる可能性があります。 エージェントのインストールとサーバーの登録は、サーバー イメージの展開と sysprep ミニ セットアップの完了後に行われます。

### <a name="windows-search"></a>Windows Search
クラウドの階層化がサーバー エンドポイントで有効になっている場合、階層化されたファイルはスキップされ、Windows Search によってインデックスが付けられます。 階層化されていないファイルは適切にインデックスが付けられます。

### <a name="antivirus-solutions"></a>ウイルス対策ソリューション
ウイルス対策は、ファイルをスキャンして既知の悪意のあるコードを検索することで機能するので、ウイルス対策製品によって階層化されたファイルの再呼び出しが発生することがあります。 Azure File Sync エージェントのバージョン 4.0 以降では、階層化されたファイルにはセキュリティで保護された Windows 属性 FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS が設定されています。 この属性を設定してオフライン ファイルの読み取りをスキップするようにソリューションを構成する方法について、ソフトウェア ベンダーと相談することをお勧めします (多くの場合は自動実行されます)。 

Microsoft の社内ウイルス対策ソリューションである Windows Defender と System Center Endpoint Protection (SCEP) では、この属性が設定されたファイルの読み取りが自動的にスキップされます。 そのテスト結果として小さな問題点がわかりました。既存の同期グループにサーバーを追加すると、新しいサーバー上で 800 バイト未満のファイルの呼び戻し (ダウンロード) が実行されるという問題です。 このようなファイルは新しいサーバー上に残り、階層化のサイズ要件 (64 KB を超えるサイズ) を満たしていないため、階層化されません。

> [!Note]  
> ウイルス対策ソフトウェア ベンダーは、その製品と Azure File Sync との間の互換性を、[Azure File Sync Antivirus Compatibility Test Suite](https://www.microsoft.com/download/details.aspx?id=58322) を使用して確認することができます。これは、Microsoft ダウンロード センターでダウンロードできます。

### <a name="backup-solutions"></a>バックアップ ソリューション
ウイルス対策ソリューションと同様に、バックアップ ソリューションでも階層化されたファイルの再呼び出しが発生することがあります。 Azure ファイル共有をバックアップするには、オンプレミスのバックアップ製品ではなく、クラウド バックアップ ソリューションを使用することをお勧めします。

オンプレミス バックアップ ソリューションを使用している場合、クラウドの階層化が無効な同期グループ内のサーバーに対してバックアップを実行する必要があります。 復元を実行するときは、ボリューム レベルまたはファイル レベルの復元オプションを使用します。 ファイル レベルの復元オプションを使用して復元されたファイルは、同期グループ内のすべてのエンドポイントに同期され、既存のファイルはバックアップから復元されたバージョンに置き換えられます。  ボリューム レベルの復元では、Azure ファイル共有やその他のサーバー エンドポイントの新しいファイル バージョンに置き換えられません。

> [!Note]  
> ベアメタル (BMR) 復元は予期しない結果が生じることがあるため、現在サポートされていません。

> [!Note]  
> VSS スナップショット ([以前のバージョン] タブを含む) は、クラウドの階層化が有効なボリュームでは現在サポートされていません。 クラウドの階層化が有効になっている場合、Azure ファイル共有のスナップショットを使用して、バックアップからファイルを復元します。

### <a name="encryption-solutions"></a>暗号化ソリューション
暗号化ソリューションのサポートは、実装方法によって変わります。 Azure ファイル同期は次のソリューションと連携することが確認されています。

- BitLocker 暗号化
- Azure Information Protection、Azure Rights Management サービス (Azure RMS)、および Active Directory RMS

Azure ファイル同期は、次のソリューションと連携しないことが確認されています。

- NTFS 暗号化ファイル システム (EFS)

通常、Azure File Sync は、ファイル システムの下に位置する暗号化ソリューション (BitLocker など) と、ファイル形式で実装されるソリューション (Azure Information Protection など) との相互運用性をサポートします。 ファイル システムの上に位置するソリューション (NTFS EFS など) に対する特別な相互運用性はありません。

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>その他の階層型ストレージ管理 (HSM) ソリューション
その他の HSM ソリューションを Azure File Sync で使用することはできません。

## <a name="region-availability"></a>利用可能なリージョン
Azure File Sync は、次のリージョンでのみ利用できます。

| リージョン | データ センターの場所 |
|--------|---------------------|
| オーストラリア東部 | ニュー サウス ウェールズ州 |
| オーストラリア南東部 | ビクトリア州 |
| ブラジル南部 | サンパウロ州 |
| カナダ中部 | トロント |
| カナダ東部 | ケベック シティ |
| インド中部 | プネー |
| 米国中部 | アイオワ州 |
| 東アジア | 香港特別行政区 |
| East US | バージニア州 |
| 米国東部 2 | バージニア州 |
| フランス中部 | パリ |
| 韓国中部| ソウル |
| 韓国南部| 釜山 |
| 東日本 | 東京、埼玉 |
| 西日本 | 大阪 |
| 米国中北部 | イリノイ州 |
| 北ヨーロッパ | アイルランド |
| 米国中南部 | テキサス |
| インド南部 | チェンナイ |
| 東南アジア | シンガポール |
| 英国南部 | ロンドン |
| 英国西部 | カーディフ |
| 米国政府アリゾナ | アリゾナ |
| 米国政府テキサス | テキサス |
| 米国政府バージニア州 | バージニア州 |
| 西ヨーロッパ | オランダ |
| 米国中西部 | ワイオミング |
| 米国西部 | カリフォルニア |
| 米国西部 2 | ワシントン |

Azure File Sync は、ストレージ同期サービスと同じリージョンの Azure ファイル共有との同期のみをサポートしています。

### <a name="azure-disaster-recovery"></a>Azure ディザスター リカバリー
Azure リージョンの損失を防ぐため、Azure File Sync には [geo 冗長ストレージの冗長性](../common/storage-redundancy-grs.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) (GRS) オプションが統合されています。 GRS ストレージは、プライマリ リージョンのストレージ (通常、操作している) と、ペアのセカンダリ リージョンとの間でブロックの非同期レプリケーションを使用することで機能します。 Azure リージョンが一時的または永続的にオフラインになる障害が発生した場合、Microsoft はペアのリージョンにストレージをフェールオーバーします。 

> [!Warning]  
> Azure ファイル共有を GRS ストレージ アカウントのクラウド エンドポイントとして使用している場合は、ストレージ アカウントのフェールオーバーを開始しないでください。 それを行うと、同期の動作が停止し、新しく階層化されたファイルの場合は予期せずデータが失われる可能性があります。 Azure リージョンが失われた場合は、Azure File Sync との互換性のある方法でストレージ アカウントのフェールオーバーがトリガーされます。

geo 冗長ストレージと Azure File Sync との間のフェールオーバーの統合をサポートするため、すべての Azure File Sync リージョンが、ストレージで使用されるセカンダリ リージョンと一致するセカンダリ リージョンとペアになります。 これらのペアは次のとおりです。

| プライマリ リージョン      | ペアのリージョン      |
|---------------------|--------------------|
| オーストラリア東部      | オーストラリア南東部|
| オーストラリア南東部 | オーストラリア東部     |
| ブラジル南部        | 米国中南部   |
| カナダ中部      | カナダ東部        |
| カナダ東部         | カナダ中部     |
| インド中部       | インド南部        |
| 米国中部          | 米国東部 2          |
| 東アジア           | 東南アジア     |
| East US             | 米国西部            |
| 米国東部 2           | 米国中部         |
| フランス中部      | フランス南部       |
| 東日本          | 西日本         |
| 西日本          | 東日本         |
| 韓国中部       | 韓国南部        |
| 韓国南部         | 韓国中部      |
| 北ヨーロッパ        | 西ヨーロッパ        |
| 米国中北部    | 米国中南部   |
| 米国中南部    | 米国中北部   |
| インド南部         | インド中部      |
| 東南アジア      | 東アジア          |
| 英国南部            | 英国西部            |
| 英国西部             | 英国南部           |
| 米国政府アリゾナ      | 米国政府テキサス       |
| US Gov アイオワ         | 米国政府バージニア州    |
| 米国政府バージニア州      | 米国政府テキサス       |
| 西ヨーロッパ         | 北ヨーロッパ       |
| 米国中西部     | 米国西部 2          |
| 米国西部             | East US            |
| 米国西部 2           | 米国中西部    |

## <a name="azure-file-sync-agent-update-policy"></a>Azure ファイル同期エージェントの更新ポリシー
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>次の手順
* [ファイアウォールとプロキシの設定の考慮事項](storage-sync-files-firewall-and-proxy.md)
* [Azure Files のデプロイの計画](storage-files-planning.md)
* [Azure Files をデプロイする](storage-files-deployment-guide.md)
* [Azure File Sync をデプロイする](storage-sync-files-deployment-guide.md)
* [Azure File Sync の監視](storage-sync-files-monitoring.md)
