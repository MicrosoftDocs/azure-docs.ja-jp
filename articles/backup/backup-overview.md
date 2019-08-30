---
title: Azure Backup とは
description: Azure Backup サービスの概要を紹介し、このサービスがお客様の事業継続とディザスター リカバリー (BCDR) 戦略にどのように寄与するかについて説明します。
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: overview
ms.date: 04/24/2019
ms.author: dacurwin
ms.custom: mvc
ms.openlocfilehash: 526c60916854d4918607a1fd1b887ac9d27cd1c7
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950026"
---
# <a name="what-is-the-azure-backup-service"></a>Azure Backup サービスとは

Azure Backup サービスでは、Microsoft Azure クラウドにデータをバックアップします。 オンプレミスのマシンとワークロード、および Azure 仮想マシン (VM) をバックアップできます。


## <a name="why-use-azure-backup"></a>Azure Backup を使用する理由

Azure Backup には、以下のような主な利点があります。

- **オンプレミス バックアップのオフロード**: Azure Backup では、お客様のオンプレミス リソースをクラウドにバックアップするためのシンプルなソリューションが提供されます。 複雑なオンプレミス バックアップ ソリューションをデプロイせずに、短期および長期のバックアップを行えます。
- **Azure IaaS VM のバックアップ**: Azure Backup では、元のデータが誤って破壊されることを防ぐために、独立して分離されたバックアップを提供しています。 バックアップは、組み込みのマネージド復旧ポイントを備えた Recovery Services コンテナーに格納されます。 構成とスケーラビリティは単純で、バックアップは最適化され、必要に応じて簡単に復元することができます。
- **容易なスケーリング** - Azure Backup では、Azure クラウドの基盤となる機能と無制限のスケールを使用して、高可用性を実現します。その際、保守と監視のオーバーヘッドは発生しません。
- **無制限のデータ転送が可能**: Azure Backup では、転送する受信データまたは送信データの量に制限がなく、転送されるデータに料金はかかりません。
    - 送信データとは、復元操作中に Recovery Services コンテナーから転送されるデータを指します。
    - Azure Import/Export サービスを使用してオフライン初期バックアップを実行し、大量のデータをインポートした場合は、受信データに対してコストがかかります。  [詳細情報](backup-azure-backup-import-export.md)。
- **データの安全性の確保**: Azure Backup には、転送中のデータと保存データを保護するためのソリューションがあります。
- **アプリ整合性のあるバックアップの取得**: アプリケーション整合性バックアップは、バックアップ コピーを復元するために必要なすべてのデータが復旧ポイントにあることを意味します。 Azure Backup は、アプリケーション整合性バックアップを提供することで、追加の修正なしでデータを復元できるようにします。 アプリケーション整合性データの復元により復元時間が短縮され、迅速に実行状態に戻ることができます。
- **短期および長期のデータの保持**:短期および長期のデータ保持のために、Recovery Services コンテナーを使用することができます。 Azure では、Recovery Services コンテナーにデータを保持する時間に制限はありません。 任意の期間、データを保持することができます。 Azure Backup では、保護されているインスタンスごとの復旧ポイントが 9,999 個に制限されます。 
- **ストレージ管理の自動化** - ハイブリッド環境では、多くの場合、異種混在のストレージが必要です。つまり、ストレージの一部はオンプレミスに、一部はクラウドに存在していなければなりません。 Azure Backup では、オンプレミスのストレージ デバイスを使用するためのコストはありません。 Azure Backup では、バックアップ ストレージが自動的に割り当てられて管理され、従量制課金モデルが使用されているので、使用したストレージについてのみ料金が発生します。 価格に関して詳しくは、[こちら](https://azure.microsoft.com/pricing/details/backup)をご覧ください。
- **複数のストレージ オプション** - Azure Backup では、ストレージ/データの高可用性を維持するため、2 種類のレプリケーションが提供されています。
    - [ローカル冗長ストレージ (LRS)](../storage/common/storage-redundancy-lrs.md) では、データセンターのストレージ スケール ユニットにデータが 3 回レプリケートされます (データのコピーが 3 つ作成されます)。 データのすべてのコピーは、同じリージョン内に存在します。 LRS は、ローカル ハードウェアの障害からデータを保護するための低コストのオプションです。
    - [geo 冗長ストレージ (GRS)](../storage/common/storage-redundancy-grs.md) は、既定の推奨レプリケーション オプションです。 GRS では、セカンダリ リージョン (ソース データのプライマリの場所から数百マイル離れた場所) にデータがレプリケートされます。 GRS は LRS よりもコストがかかりますが、地域的な障害が発生しても、より高いレベルのデータ持続性が確保されます。


## <a name="whats-the-difference-between-azure-backup-and-azure-site-recovery"></a>Azure Backup と Azure Site Recovery の違い

Azure Backup と Azure Site Recovery は、どちらもお客様のビジネスにおける事業継続とディザスター リカバリー (BCDR) 戦略に役立ちます。 BCDR には 2 つの大きな目的があります。

- 障害発生時に、お客様のビジネス データを安全かつ回復可能な状態で保持する。
- 計画されたダウンタイムおよび計画されていないダウンタイムの際中に、お客様のアプリとワークロードを稼働させ続ける。

両方のサービスによって、互いに異なる補完的な機能が提供されます。

- **Azure Site Recovery**:Site Recovery では、オンプレミス マシン用および Azure VM 用のディザスター リカバリー ソリューションが提供されます。 プライマリ ロケーションからセカンダリにマシンをレプリケートします。 障害が発生したときは、マシンをセカンダリ ロケーションにフェールオーバーし、そこからそれらにアクセスします。 すべてが再び正常に動作するようになったら、マシンをフェールバックしてプライマリ サイトに復旧します。
- **Azure Backup**:Azure Backup サービスでは、オンプレミス マシンおよび Azure VM からデータをバックアップします。 データは、きめ細かいレベルでバックアップして復旧できます (ファイル、フォルダー、マシン システム状態のバックアップ、アプリ対応のデータ バックアップなど)。 Azure Backup では、Site Recovery よりも細かいレベルでデータが扱われます。 たとえば、お客様のノート PC のプレゼンテーションが破損した場合、Azure Backup を使用してプレゼンテーションを復元できます。 VM の構成とデータを安全でアクセス可能な状態に保ちたい場合は、Site Recovery を使用できます。  

お客様の BCDR のニーズを把握するために、表の項目をお役立てください。

**目標** | **詳細** | **比較**
--- | --- | ---
**データのバックアップおよび保有** | バックアップ データは、コンプライアンスの観点からの必要に応じて、数日、数か月、さらには数年にわたって保有および格納できます。 | Azure Backup のようなバックアップ ソリューションでは、バックアップしたいデータを細かく選択し、バックアップ ポリシーと保有ポリシーを微調整できます。<br/><br/> Site Recovery では、同じ細かな調整はできません。
**目標復旧時点 (RPO)** | 復旧を行う必要がある場合に許容されるデータ損失の量です。 | バックアップでは、RPO がより可変的です。<br/><br/> VM バックアップの RPO は通常 1 日であるのに対し、データベース バックアップの RPO は最低 15 分です。<br/><br/> Site Recovery では、低い RPO が提供されます。これは、ソースとレプリカ コピーとの差分が小さくなるように、レプリケーションが継続的または頻繁に行われるためです。
**目標復旧時間 (RTO)** |復旧または復元の完了に要する時間です。 | RPO が大きくなるほど、一般的にはバックアップ ソリューションで処理が必要なデータ量が増えるため、RTO は長くなります。 たとえば、オフサイトの場所からテープを輸送するのにかかる時間によっては、テープからのデータの復元に日単位の時間を要する場合があります。

## <a name="what-backup-scenarios-are-supported"></a>サポートされているバックアップ シナリオ

Azure Backup では、オンプレミス マシンと Azure VM の両方をバックアップできます。

**マシン** | **バックアップ シナリオ**
--- | ---
**オンプレミスのバックアップ** |  1) オンプレミスの Windows マシンで Azure Backup Microsoft Azure Recovery Services (MARS) エージェントを実行して、個々のファイルとシステムの状態をバックアップします。 <br/><br/>2) オンプレミスのマシンをバックアップ サーバー (System Center Data Protection Manager (DPM) または Microsoft Azure Backup Server (MABS)) にバックアップした後、Azure の Azure Backup Recovery Services コンテナーにバックアップするようにバックアップ サーバーを構成します。
**Azure VM** | 1) 個々の Azure VM のバックアップを有効にします。 バックアップを有効にすると、Azure Backup によって、VM で実行されている Azure VM エージェントに拡張機能がインストールされます。 エージェントによって、VM 全体がバックアップされます。<br/><br/> 2) Azure VM で MARS エージェントを実行します。 これは、VM 上の個々のファイルとフォルダーをバックアップする場合に役立ちます。<br/><br/> 


## <a name="why-use-a-backup-server"></a>バックアップ サーバーを使用する理由
マシンやアプリを MABS/DPM ストレージにバックアップした後、DPM/MABS ストレージをコンテナーにバックアップする利点は、次のとおりです。

- MABS/DPM へのバックアップでは、ファイル/フォルダー/ボリュームのバックアップに加えて、一般的なアプリ (SQL Server、Exchange、SharePoint など) 用に最適化されたアプリ対応のバックアップと、マシンの状態のバックアップ (ベアメタル、システム状態) が提供されます。
- オンプレミスのマシンでは、バックアップ対象の各マシンに MARS エージェントをインストールする必要はありません。 各マシンでは DPM/MABS 保護エージェントが実行され、MARS エージェントは MABS/DPM 上でのみ実行されます。
- バックアップを実行するための、より柔軟できめ細かいスケジューリング オプションがあります。
- 保護グループとしてまとめる複数のマシンのバックアップを、単一のコンソールで管理できます。 これは、アプリが複数のマシンにわたって階層化されていて、それらをまとめてバックアップする場合に特に便利です。

バックアップ サーバーを使用するときの[バックアップのしくみ](backup-architecture.md#architecture-back-up-to-dpmmabs)と、バックアップ サーバーの[サポート要件](backup-support-matrix-mabs-dpm.md)の詳細をご覧ください。

## <a name="what-can-i-back-up"></a>バックアップできるデータについて

**マシン** | **バックアップ方法** | **バックアップ**
--- | --- | ---
**オンプレミスの Windows VM** | MARS エージェントを実行する | ファイル、フォルダー、システム状態をバックアップします。<br/><br/> Linux マシンはサポートされていません。
**オンプレミスのマシン** | DPM/MABS へのバックアップ | ファイル、フォルダー、共有、ボリューム、アプリ固有のデータなど、[DPM](backup-support-matrix-mabs-dpm.md#supported-backups-to-dpm) または [MABS](backup-support-matrix-mabs-dpm.md#supported-backups-to-mabs) によって保護されるものがすべてバックアップされます。
**Azure VM** | Azure VM エージェント バックアップ拡張機能を実行する | VM 全体がバックアップされます
**Azure VM** | MARS エージェントを実行する | ファイル、フォルダー、システム状態をバックアップします。<br/><br/> Linux マシンはサポートされていません。
**Azure VM** | Azure で実行されている MABS/DPM にバックアップする | ファイル、フォルダー、共有、ボリューム、アプリ固有のデータなど、[MABS](backup-support-matrix-mabs-dpm.md#supported-backups-to-mabs) または [DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807) によって保護されるものがすべてバックアップされます。

## <a name="what-backup-agents-do-i-need"></a>必要なバックアップ エージェント

**シナリオ** | **エージェント**
--- | ---
**Azure VM のバックアップ** | エージェントは必要ありません。 Azure VM のバックアップを初めて実行するときに、バックアップ用の Azure VM 拡張機能が Azure VM にインストールされます。<br/><br/> Windows と Linux がサポートされます。
**オンプレミスの Windows マシンのバックアップ** | マシン上で直接 MARS エージェントをダウンロード、インストール、実行します。
**MARS エージェントでの Azure VM のバックアップ** | マシン上で直接 MARS エージェントをダウンロード、インストール、実行します。 バックアップ拡張機能と共に、MARS エージェントを実行できます。
**DPM/MABS へのオンプレミス マシンと Azure VM のバックアップ** | DPM または MABS 保護エージェントは、保護対象のマシンで実行されます。 MARS エージェントは、Azure にバックアップするために DPM サーバー/MABS で実行されます。

## <a name="which-backup-agent-should-i-use"></a>使用が推奨されるバックアップ エージェント

**Backup** | **ソリューション** | **制限事項**
--- | --- | ---
**Azure VM 全体をバックアップしたい** | VM のバックアップを有効にします。 バックアップ拡張機能は、Windows または Linux の Azure VM 上で自動的に構成されます。 | VM 全体がバックアップされます <br/><br/> Windows VM の場合、バックアップにはアプリ整合性があります。 Linux の場合、バックアップにはファイル整合性があります。 Linux VM のアプリ対応が必要な場合は、カスタム スクリプトを使用してこれを構成する必要があります。
**Azure VM 上の特定のファイル/フォルダーをバックアップしたい** | MARS エージェントを VM にデプロイします。
**オンプレミスの Windows マシンを直接バックアップしたい** | MARS エージェントをマシンにインストールします。 | ファイル、フォルダー、およびシステム状態を Azure にバックアップできます。 バックアップは、アプリ対応ではありません。
**オンプレミスの Linux マシンを直接バックアップしたい** | Azure にバックアップするには、DPM または MABS をデプロイする必要があります。 | Linux ホストのバックアップはサポートされていません。Hyper-V または VMware でホストされている Linux ゲスト マシンのみをバックアップできます。
**オンプレミスで実行されているアプリをバックアップしたい** | アプリ対応バックアップの場合、マシンは DPM または MABS によって保護されている必要があります。
**Azure VM のバックアップと復旧をきめ細かく柔軟に設定したい** | バックアップ スケジューリングのための追加の柔軟性と、ファイル、フォルダー、ボリューム、アプリ、およびシステム状態を保護および復元するための完全な柔軟性を利用するには、Azure で実行されている MABS/DPM を使用して Azure VM を保護します。

## <a name="backup-and-retention"></a>バックアップと保持

Azure Backup には、*保護されているインスタンス*につき復旧ポイント (バックアップ コピーまたはスナップショットとも呼ばれます) が 9,999 個という制限があります。

- 保護されているインスタンスとは、データを Azure にバックアップするように構成されているコンピューター、サーバー (物理または仮想)、またはワークロードです。 インスタンスは、データのバックアップ コピーが保存されると保護されます。
- データのバックアップ コピーは、保護することです。 ソース データが失われた場合や破損した場合は、バックアップ コピーによってソース データを復元できることがあります。

次の表は、コンポーネントごとの最大バックアップ頻度を示します。 バックアップ ポリシーの構成では、復旧ポイントを使用する頻度を決定します。 たとえば、日ごとに復旧ポイントを作成すると、期限切れになるまで 27 年間復旧ポイントを保持できます。月ごとの復旧ポイントを取得すると、期限切れになるまで 833 年間復旧ポイントを保持できます。Backup サービスによって、復旧ポイントに有効期限が設定されることはありません。

|  | Azure Backup エージェント | System Center DPM | Azure Backup Server | Azure IaaS VM のバックアップ |
| --- | --- | --- | --- | --- |
| バックアップ頻度<br/> (対 Recovery Services コンテナー) |3 バックアップ/日 |2 バックアップ/日 |2 バックアップ/日 |1 バックアップ/日 |
| バックアップ頻度<br/> (対ディスク) |適用不可 |SQL Server の場合は 15 分ごと<br/><br/> 他のワークロードの場合は 1 時間ごと |SQL Server の場合は 15 分ごと<br/><br/> 他のワークロードの場合は 1 時間ごと |適用不可 |
| 保持オプション |毎日、毎週、毎月、毎年 |毎日、毎週、毎月、毎年 |毎日、毎週、毎月、毎年 |毎日、毎週、毎月、毎年 |
| 保護されているインスタンスあたりの復旧ポイントの最大数 |9999|9999|9999|9999|
| 最大保有期間 |バックアップ頻度次第 |バックアップ頻度次第 |バックアップ頻度次第 |バックアップ頻度次第 |
| ローカル ディスクの回復ポイント |適用不可 | ファイル サーバーの場合 64 個<br/><br/> アプリケーション サーバーの場合 448 個 | ファイル サーバーの場合 64 個<br/><br/> アプリケーション サーバーの場合 448 個 |適用不可 |
| テープの回復ポイント |適用不可 |無制限 |適用不可 |適用不可 |

## <a name="how-does-azure-backup-work-with-encryption"></a>Azure Backup における暗号化の処理

**暗号化** | **オンプレミスでのバックアップ** | **Azure VM のバックアップ** | **Azure VM 上の SQL のバックアップ**
--- | --- | --- | ---
保存時の暗号化<br/> (永続化/格納データの暗号化) | ユーザー指定のパスフレーズを使用してデータが暗号化される | Azure [Storage Service Encryption (SSE)](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) を使用して、コンテナー内の格納データが暗号化されます。<br/><br/> Backup では、データを保存する前に自動的に暗号化します。 Azure Storage では、取得前にデータを暗号化解除します。 SSE にカスタマー マネージド キーを使用することは、現在サポートされません。<br/><br/> バックアップできるのは、OS ディスクとデータ ディスクの暗号化に [Azure Disk Encryption (ADE)](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) を使用する VM です。 Azure Backup では、BEK のみで暗号化された VM、および BEK と [KEK](https://blogs.msdn.microsoft.com/cclayton/2017/01/03/creating-a-key-encrypting-key-kek/) の両方で暗号化された VM がサポートされます。 [制限事項](backup-azure-vms-encryption.md#encryption-support)を確認してください。 | Azure Backup では、[TDE](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) が有効になったサーバーまたは SQL Server データベースのバックアップがサポートされます。 キーが Azure によって管理される TDE と、キーがユーザーによって管理される (BYOK) TDE がサポートされます。<br/><br/> Backup によるバックアップ プロセスの過程で SQL 暗号化は実行されません。
転送中の暗号化<br/> (別の場所に移動中のデータの暗号化) | データは AES256 を使用して暗号化され、HTTPS で Azure 内のコンテナーに送信されます。 | Azure 内で、Azure Storage とコンテナーとの間でやり取りされるデータは HTTPS によって保護されます。 このデータは、Azure バックボーン ネットワークにとどまります。<br/><br/> ファイルの回復については、コンテナーと Azure VM との間で転送されるデータが iSCSI によって保護されます。 iSCSI チャネルは、安全なトンネリングによって保護されます。 | Azure 内で、Azure Storage とコンテナーとの間でやり取りされるデータは HTTPS によって保護されます。<br/><br/> SQL は、ファイルの回復の対象外となります。

## <a name="next-steps"></a>次の手順

- さまざまなバックアップ シナリオのアーキテクチャとコンポーネントを[確認](backup-architecture.md)します。
- バックアップおよび [Azure VM バックアップ](backup-support-matrix-iaas.md)に関するサポート要件と制限を[確認](backup-support-matrix.md)します。

[green]: ./media/backup-introduction-to-azure-backup/green.png
[yellow]: ./media/backup-introduction-to-azure-backup/yellow.png
[red]: ./media/backup-introduction-to-azure-backup/red.png
