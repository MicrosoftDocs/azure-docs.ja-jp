---
title: "Azure ファイル同期 (プレビュー) のデプロイの計画 | Microsoft Docs"
description: "Azure Files のデプロイを計画するときの考慮事項について説明します。"
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: wgries
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 3c003d498600a2cfd12ef2adfb7c16f9dfaddb37
ms.contentlocale: ja-jp
ms.lasthandoff: 09/25/2017

---

# <a name="planning-for-an-azure-file-sync-preview-deployment"></a>Azure ファイル同期 (プレビュー) のデプロイの計画
Azure ファイル同期 (プレビュー) を使用すると、オンプレミスまたは Azure の Windows Server に共有をレプリケートできます。 管理者とユーザーは、SMB や NFS 共有などを使って Windows Server 経由でファイル共有にアクセスします。 この方法は、ブランチ オフィスなどの Azure データ センターから離れた場所にあるデータにアクセスして変更する場合に特に便利です。 複数のブランチ オフィス間など、複数の Windows Server エンドポイント間でデータをレプリケートできます。 

このガイドでは、Azure ファイル同期をデプロイするときに考慮する事項について説明します。「[Planning for an Azure Files deployment](storage-files-planning.md)」(Azure Files デプロイの計画) ガイドのテストについて読むことをお勧めします。 

## <a name="understanding-azure-file-sync-terminology"></a>Azure ファイル同期の用語
Azure ファイル同期 (AFS) の詳細な説明を読む前に、用語を理解しておく必要があります。

### <a name="storage-sync-service"></a>ストレージ同期サービス
ストレージ同期サービスは、Azure ファイル同期を表す最上位レベルの Azure リソースです。ストレージ同期サービス リソースは、ストレージ アカウント リソースのピアであり、同じように Azure リソース グループに展開できます。 ストレージ アカウント リソースの個別の最上位レベルのリソースが必要です。ストレージ同期サービスは、複数の同期グループを介して複数のストレージ アカウントとの同期関係を作成する可能性があるためです。 1 つのサブスクリプションで、複数の ストレージ同期サービス リソースをデプロイすることができます。

### <a name="sync-group"></a>同期グループ
同期グループは、一連のファイルの同期トポロジを定義します。 同期グループ内のエンドポイントは、相互に同期状態が維持されます。 たとえば、AFS で管理する一連のファイルが 2 組ある場合、2 つの同期グループを作成し、それぞれに別のエンドポイントを追加します。 ストレージ同期サービスは、必要に応じて任意の数の同期グループをホストできます。  

### <a name="registered-server"></a>登録済みサーバー
登録済みサーバー オブジェクトは、サーバー (またはクラスター) とストレージ同期サービス間の信頼関係を表します。 ストレージ同期サービス インスタンスには、任意の数のサーバーを登録できます。 ただし、1 つのサーバー (またはクラスター) は、同時に 1 つのストレージ同期サービスにのみ登録できます。

### <a name="azure-file-sync-agent"></a>Azure ファイル同期エージェント
Azure ファイル同期エージェントは、Windows Server を Azure ファイル共有と同期できるようにするダウンロード可能なパッケージです。 Azure ファイル同期エージェントは、主に 3 つのコンポーネントから構成されます。 
- **FileSyncSvc.exe**: バックグラウンドの Windows サービス。サーバー エンドポイントの変更の監視と、Azure に対する同期セッションの開始を担当します。
- **StorageSync.sys**: Azure ファイル同期のファイル システム フィルターです。(クラウドの階層化が有効な場合) Azure Files に対するファイルの階層化を担当します。
- **PowerShell 管理コマンドレット**: Microsoft.StorageSync Azure Resource Provider と対話するための PowerShell コマンドレット。 これらのファイルは (既定で) 次の場所にあります。
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll

### <a name="cloud-endpoint"></a>クラウド エンドポイント
クラウド エンドポイントは、同期グループに含まれる Azure ファイル共有です。 Azure ファイル共有同期と Azure ファイル共有の全体が、1 つのクラウド エンドポイントの単なるメンバーであり、ひいては 1 つの同期グループのメンバーである可能性があります。 既存の一連のファイルを含む Azure ファイル共有をクラウド エンドポイントとして同期グループに追加すると、それらのファイルは、同期グループの他のエンドポイント上に既にある他のファイルと統合されます。

### <a name="server-endpoint"></a>サーバー エンドポイント
サーバー エンドポイントは、登録済みサーバー上の特定の場所を表します。たとえば、サーバー ボリュームのフォルダーや、ボリュームのルートなどです。 名前空間が重複していなければ、同じボリューム上に複数のサーバー エンドポイントが存在する可能性があります (たとえば、F:\sync1 と F:\sync2 など)。 各サーバー エンドポイントについて、クラウドの階層化ポリシーを個別に構成することができます。 既存の一連のファイルを含むサーバーの場所をサーバー エンドポイントとして同期グループに追加すると、それらのファイルは、同期グループの他のエンドポイント上に既にある他のファイルと統合されます。

### <a name="cloud-tiering"></a>クラウドの階層化 
クラウドの階層化は、Azure ファイル同期のオプション機能です。頻繁に使用またはアクセスされるファイルを Azure Files に階層化することができます。 ファイルを階層化すると、Azure ファイル同期ファイル システム フィルター (StorageSync.sys) はローカルでファイルをポインターと置き換えるか、ポイントを再解析して Azure Files 内のファイルの URL を示します。 NTFS 内で階層化されたファイルには、"オフライン" 属性が設定されるので、サード パーティ アプリケーションは階層化されたファイルを特定できます。 ユーザーが階層化されたファイルを開くと、Azure ファイル同期は、Azure Files のファイル データをシームレスに回収します。ファイルがシステムにローカルに保存されていないことをユーザーが知っている必要はありません。 この機能は、階層型ストレージ管理 (HSM) とも呼ばれます。

## <a name="azure-file-sync-interoperability"></a>Azure ファイル同期の相互運用性 
ここでは、Azure ファイル同期と、Windows Server の機能およびロールとサードパーティ ソリューションとの相互運用性について説明します。

### <a name="supported-versions-of-windows-server"></a>サポートされている Windows Server のバージョン
現在のところ、Azure ファイル同期では次の Windows Server バージョンがサポートされています。

| バージョン | サポートされている SKU | サポートされているデプロイ オプション |
|---------|----------------|------------------------------|
| Windows Server 2016 | Datacenter および Standard | フル (UI ありのサーバー) |
| Windows Server 2012 R2 | Datacenter および Standard | フル (UI ありのサーバー) |

今後の Windows Server バージョンがリリースされた場合は追加される予定です。また、ユーザーのフィードバックに応じて古いバージョンの Windows が追加される可能性があります。

> [!Important]  
> Azure ファイル同期に使用するすべての Windows Server は、Windows Update の最新の更新プログラムを使用して常に最新の状態を保つことをお勧めします。 

### <a name="file-system-features"></a>ファイル システムの機能
| 機能 | サポートの状態 | メモ |
|---------|----------------|-------|
| アクセス制御リスト (ACL) | 完全にサポートされます | Windows ACL は、Azure ファイル同期に保持され、サーバー エンドポイント上の Windows Server によって適用されます。 クラウド内のファイルに直接アクセスする場合、Azure Files は Windows ACL を (まだ) サポートしていません。 |
| ハード リンク | Skipped | |
| シンボリック リンク | Skipped | |
| マウント ポイント | 部分的にサポート | マウント ポイントは、サーバー エンドポイントのルートの場合がありますが、サーバー エンドポイントの名前空間に含まれている場合はスキップされます。 |
| 接合 | Skipped | |
| 再解析ポイント | Skipped | |
| NTFS 圧縮 | 完全にサポートされます | |
| スパース ファイル | 完全にサポートされます | スパース ファイルは同期されます (ブロックされません) が、完全なファイルとしてクラウドと同期されます。 クラウド内 (または他のサーバー上) のファイルの内容が変更され、変更がダウンロードされると、ファイルはスパースではなくなります |
| 代替データ ストリーム (ADS) | 保持されますが、同期されません | |

> [!Note]  
> NTFS ボリュームのみがサポートされます。

### <a name="failover-clustering"></a>フェールオーバー クラスタリング
Windows Server フェールオーバー クラスタリングは、Azure ファイル同期の "汎用ファイル サーバー" デプロイ オプションでサポートされています。 フェールオーバー クラスタリングは、"アプリケーション データ用のスケールアウト ファイル サーバー" (SOSF) またはクラスター共有ボリューム (CSV) ではサポートされていません。

> [!Note]  
> 同期が適切に機能するには、フェールオーバー クラスタリングのすべてのノードに Azure ファイル同期エージェントをインストールする必要があります。

### <a name="windows-server-data-deduplication"></a>Windows Server のデータ重複除去
クラウドの階層化が有効ではないボリュームの場合、Azure ファイル同期は、そのボリュームのデータ重複除去の有効化をサポートします。 現在のところ、クラウドの階層化を有効にした Azure ファイル同期とデータ重複除去間の相互運用性はサポートしていません。

### <a name="anti-virus-solutions"></a>ウイルス対策ソリューション
ウイルス対策は、ファイルをスキャンして既知の悪意のあるコードを検索することで機能するので、ウイルス対策によって、階層化されたファイルの回収が発生する可能性があります。 階層化されたファイルには "オフライン" 属性が設定されているため、オフライン ファイルの読み取りをスキップするようにソリューションを構成する方法について、ソフトウェア ベンダーに相談することをお勧めします。 

次のソリューションは、オフライン ファイルのスキップをサポートすることが確認されています。

- [Symantec Endpoint Protection](https://support.symantec.com/en_US/article.tech173752.html)
- [McAfee EndPoint Security](https://kc.mcafee.com/resources/sites/MCAFEE/content/live/PRODUCT_DOCUMENTATION/26000/PD26799/en_US/ens_1050_help_0-00_en-us.pdf) (ページ 90 の「Scan only what you need to」(必要なファイルのみをスキャンする) セクションを参照してください)。
- [Kaspersky Anti-Virus](https://support.kaspersky.com/4684)
- [Sophos Endpoint Protection](https://community.sophos.com/kb/en-us/40102)
- [TrendMicro OfficeScan](https://success.trendmicro.com/solution/1114377-preventing-performance-or-backup-and-restore-issues-when-using-commvault-software-with-osce-11-0#collapseTwo) 

### <a name="backup-solutions"></a>バックアップ ソリューション
ウイルス対策ソリューションと同様に、バックアップ ソリューションによって階層化されたファイルの回収が発生する可能性があります。 Azure ファイル共有をバックアップするには、オンプレミス バックアップ製品ではなく、クラウド バックアップ ソリューションを使用することをお勧めします。

### <a name="encryption-solutions"></a>暗号化ソリューション
暗号化ソリューションのサポートは、実装方法によって変わります。 Azure ファイル同期は次のソリューションと連携することが確認されています。

- BitLocker 暗号化
- Azure RMS (および従来の Active Directory RMS)

Azure ファイル同期は、次のソリューションと連携しないことが確認されています。

- NTFS 暗号化ファイル システム (EFS)

一般的に、Azure ファイル同期は、BitLocker などのファイル システムの下位にある暗号化ソリューションや、BitLocker などのファイル形式で実装されているソリューションと相互運用することができます。ただし、ファイル システム (NTFS 暗号化ファイル システムなど) の上位にあるソリューションと相互引用できる特殊な相互運用はありません。

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>その他の階層型ストレージ管理 (HSM) ソリューション
Azure ファイル同期との併用が推奨される他の階層型ストレージ管理ソリューションはありません。

## <a name="region-availability"></a>利用可能なリージョン
Azure ファイル同期は、次のリージョンでプレビューとして公開されています。

| リージョン | データ センターの場所 |
|--------|---------------------|
| 米国西部 | カリフォルニア州 (米国) |
| 西ヨーロッパ | オランダ |
| 東南アジア | シンガポール |
| オーストラリア東部 | ニュー サウス ウェールズ州 (オーストラリア) |

プレビューでは、ストレージ同期サービスと同じリージョンの Azure ファイル共有との同期のみをサポートしています。

## <a name="azure-file-sync-agent-update-policy"></a>Azure ファイル同期エージェントの更新ポリシー
Azure ファイル同期エージェントの更新プログラムは、新機能を追加し、見つかった問題を解決するために定期的にリリースされます。 Azure ファイル同期エージェントのすべての更新プログラムがリリースされ次第入手できるように Microsoft Update を有効にすることをお勧めします。 それでも、組織によっては更新プログラムを厳格に制御したい事情があるのではないでしょうか。 古いバージョンの Azure ファイル同期エージェントを使用するデプロイの場合:

- ストレージ同期サービスは、新しいメジャー バージョンの初回のリリースから 3 か月間は、前のメジャー バージョンをサポートする予定です。 たとえば、ストレージ同期サービスは、バージョン 2.\* のリリースから 3 か月間は、バージョン 1.\* をサポートします。
- 3 か月間が経過すると、ストレージ同期サービスは、同期グループとの同期から、期限切れしたバージョンを使用する登録済みサーバーをブロックする処理を開始します。
- 前のメジャー バージョンがサポートされる 3 か月間は、最新のメジャー バージョンに対してのみバグ修正が行われます。

## <a name="next-steps"></a>次のステップ
* [Azure Files のデプロイの計画](storage-files-planning.md)
* [Azure Files のデプロイ方法](storage-files-deployment-guide.md)
* [Azure ファイル同期のデプロイ方法](storage-sync-files-deployment-guide.md)

