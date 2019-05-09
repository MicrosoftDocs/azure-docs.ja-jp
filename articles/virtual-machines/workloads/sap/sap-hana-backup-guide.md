---
title: Azure Virtual Machines 上の SAP HANA のバックアップ ガイド | Microsoft Docs
description: SAP HANA のバックアップ ガイドでは、Azure 仮想マシン上の SAP HANA をバックアップする 2 つの主要な方法について説明します
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: rclaus
ms.openlocfilehash: 89896fab7b1c359007ed23d4f9d9771e366ca68a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58013347"
---
# <a name="backup-guide-for-sap-hana-on-azure-virtual-machines"></a>Azure Virtual Machines 上の SAP HANA のバックアップ ガイド

## <a name="getting-started"></a>Getting Started (概要)

Azure Virtual Machines で実行されている SAP HANA のバックアップ ガイドでは、Azure 固有のトピックのみを取り上げます。 SAP HANA のバックアップに関する一般的事項については、SAP HANA のドキュメントを参照してください (この記事の後半の「_SAP HANA のバックアップに関するドキュメント_」を参照)。

この記事では、Azure 仮想マシン上の SAP HANA をバックアップする 2 つの主要な方法を中心に説明します。

- Azure Linux 仮想マシンのファイル システムに HANA をバックアップする (「[ファイル レベルの SAP HANA Azure バックアップ](sap-hana-backup-file-level.md)」を参照)
- 手動の Azure ストレージ BLOB スナップショット機能または Azure Backup サービスを使用して、ストレージ スナップショットに基づいて HANA をバックアップする (「[ストレージ スナップショットに基づいた SAP HANA のバックアップ](sap-hana-backup-storage-snapshots.md)」を参照)

SAP HANA には、サードパーティ製のバックアップ ツールを SAP HANA に直接統合できるバックアップ API が用意されています  (この API についてはこの記事で取り上げません)。この API に基づいた Azure Backup サービスとの SAP HANA の直接統合は、現時点では使用できません。

SAP HANA は、Azure M シリーズなどのさまざまな Azure VM で正式にサポートされています。 SAP HANA が認定されている Azure VM の完全な一覧については、「[Find Certified IaaS Platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)」(認定 IaaS プラットフォームの検索) を参照してください。 この記事は、SAP HANA on Azure の新しいサービスが公開されたら更新される予定です。

Azure で使用できる SAP HANA ハイブリッド ソリューションもあります。この場合、仮想化は行われず、SAP HANA は物理サーバー上で実行されます。 ただし、この SAP HANA Azure バックアップ ガイドでは、Azure VM 内で SAP HANA が実行される純粋な Azure 環境について取り上げます。&quot;L インスタンス&quot; で実行されている SAP HANA については説明しません。&quot;L インスタンス&quot; を対象とする、ストレージ スナップショットに基づいたこのバックアップ ソリューションの詳細については、「[SAP HANA (large instances) overview and architecture on Azure (SAP HANA on Azure (L インスタンス) の概要とアーキテクチャ)](hana-overview-architecture.md)」を参照してください。

Azure でサポートされている SAP 製品の一般的な情報については、[SAP ノート 1928533](https://launchpad.support.sap.com/#/notes/1928533) を参照してください。

次の 3 つの図には、Azure のネイティブな機能を使用した現在の SAP HANA バックアップ オプションの概要が示されているほか、実現の可能性がある 3 つのバックアップ シナリオが示されています。 関連記事である「[ファイル レベルの SAP HANA Azure バックアップ](sap-hana-backup-file-level.md)」と「[ストレージ スナップショットに基づいた SAP HANA のバックアップ](sap-hana-backup-storage-snapshots.md)」では、サイズが数テラバイトの SAP HANA バックアップに関するサイズとパフォーマンスの考慮事項など、これらのオプションについて詳細に説明されています。

![この図は、現在の VM の状態を保存する 2 つの方法を示しています](media/sap-hana-backup-guide/image001.png)

この図は、Azure Backup サービスと VM ディスクの手動スナップショットのいずれかを使用して現在の VM の状態を保存する方法を示しています。 この手法を使用すると、SAP HANA バックアップを管理する必要がありません。 ディスク スナップショットのシナリオでは、ファイル システムの整合性のほか、アプリケーションで一貫したディスクの状態を確保することが課題になります。 整合性のトピックについては、この記事の後半にあるセクション「_ストレージ スナップショットを作成する際の SAP HANA データの整合性_」で説明します。 SAP HANA バックアップに関する Azure Backup サービスの機能と制限も、この記事の後半で説明します。

![この図は、VM 内の SAP HANA ファイル バックアップを作成する方法について示しています](media/sap-hana-backup-guide/image002.png)

この図は、VM 内の SAP HANA ファイル バックアップを作成したうえで、各種ツールを使用してその HANA バックアップ ファイルを別の場所に格納する方法を示しています。 HANA バックアップの作成には、スナップショットベースのバックアップ ソリューションよりも時間がかかりますが、整合性と一貫性の点でメリットがあります。 詳しくはこの記事の後半で説明します。

![この図は、実現の可能性がある SAP HANA バックアップ シナリオを示しています](media/sap-hana-backup-guide/image003.png)

この図は、実現の可能性がある SAP HANA バックアップ シナリオを示しています。 もし SAP HANA でレプリケーションのセカンダリからバックアップを作成できるようであれば、バックアップ戦略にもう 1 つ選択肢が加わります。 SAP HANA Wiki の次の投稿によると、現時点ではセカンダリからバックアップを作成することはできません。

_&quot;セカンダリ側からバックアップを作成することはできますか。_

_いいえ。現在データとログのバックアップを作成できるのはプライマリ側だけです。自動ログ バックアップが有効になっている場合、セカンダリ側への引き継ぎ後、ログ バックアップがそのセカンダリ側に自動的に書き込まれます。&quot;_

## <a name="sap-resources-for-hana-backup"></a>HANA バックアップ関連の SAP リソース

### <a name="sap-hana-backup-documentation"></a>SAP HANA のバックアップに関するドキュメント

- [SAP HANA 管理の概要](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US)
- [バックアップと回復の戦略の計画](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)
- [ABAP DBACOCKPIT を使用した HANA バックアップのスケジュール設定](https://www.hanatutorials.com/p/schedule-hana-backup-using-abap.html)
- [データ バックアップのスケジュール設定 (SAP HANA Cockpit)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/6d/385fa14ef64a6bab2c97a3d3e40292/frameset.htm)
- [SAP ノート 1642148](https://launchpad.support.sap.com/#/notes/1642148) に記載されている SAP HANA バックアップに関する FAQ
- [SAP ノート 2039883](https://launchpad.support.sap.com/#/notes/2039883) に記載されている、SAP HANA のデータベース スナップショットとストレージ スナップショットに関する FAQ
- [SAP ノート 1820529](https://launchpad.support.sap.com/#/notes/1820529) に記載されている、バックアップと回復に適していないネットワーク ファイル システム

### <a name="why-sap-hana-backup"></a>SAP HANA バックアップが必要な理由

Azure Storage には、高可用性と信頼性が最初から備わっています (Azure Storage の詳細については「[Microsoft Azure Storage の概要](../../../storage/common/storage-introduction.md)」を参照)。

最小限の &quot;バックアップ&quot; は、Azure の SLA を利用して、SAP HANA サーバー VM に接続された Azure VHD 上にある SAP HANA のデータ ファイルとログ ファイルを保持する処理です。 この手法では、VM の障害には対応できるものの、SAP HANA のデータ ファイルとログファイルが破損するリスクや、データまたはファイルの意図しない削除などの論理エラーに対応できません。 さらに、コンプライアンス上または法律上の理由からもバックアップが求められます。 このため、SAP HANA バックアップは常に必要になります。

### <a name="how-to-verify-correctness-of-sap-hana-backup"></a>SAP HANA バックアップの正確性を確認する方法
ストレージ スナップショットを使用する場合、別のシステムで復元のテストを実行することをお勧めします。 そうすることで、バックアップが正しいことと、バックアップと復元の内部処理が正常に機能することを確かめられます。 この確認はオンプレミスでは非常に困難ですが、目的に合わせて必要なリソースを一時的に増やせるクラウドでは、はるかに簡単に実行できます。

簡単な復元を行って HANA の稼働を確認するだけでは不十分であることに留意してください。 テーブルの整合性チェックを実行して、復元されたデータベースに問題がないようにするのが理想的です。 SAP HANA には数種類の整合性チェックが用意されており、これらは [SAP ノート 1977584](https://launchpad.support.sap.com/#/notes/1977584) で説明されています。

テーブルの整合性チェックに関する情報は、SAP Web サイトの「[Table and Catalog Consistency Checks (テーブルとカタログの整合性チェック)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/25/84ec2e324d44529edc8221956359ea/content.htm#loio9357bf52c7324bee9567dca417ad9f8b)」にもあります。

標準的なファイル バックアップでは、復元のテストは必要ありません。 復元に使用できるバックアップをチェックするには、hdbbackupdiag と hdbbackupcheck の 2 つの SAP HANA ツールが役立ちます。 これらのツールの詳細については、「[Manually Checking Whether a Recovery is Possible (回復が可能かどうかを手動でチェックする)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/77/522ef1e3cb4d799bab33e0aeb9c93b/content.htm)」を参照してください。

### <a name="pros-and-cons-of-hana-backup-versus-storage-snapshot"></a>HANA バックアップとストレージ スナップショットの長所と短所

HANA バックアップとストレージ スナップショットについては、いずれかが SAP によって推奨されているということはありません。 状況と使用できるストレージ テクノロジに応じてどちらを使用するかを判断できるよう、双方の長所と短所の一覧が SAP によって作成されています (「[Planning Your Backup and Recovery Strategy (バックアップと回復の戦略の計画)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)」を参照)。

Azure では、Azure BLOB スナップショット機能を使ってもファイル システムの整合性が確保されるわけでない点に注意してください ([PowerShell による BLOB スナップショットの使用](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)に関するページを参照)。 次のセクション「_ストレージ スナップショットを作成する際の SAP HANA データの整合性_」では、この機能に関するいくつかの考慮事項について説明します。

さらに、BLOB スナップショットを頻繁に使用する場合、次の記事で説明されているとおり、課金への影響を把握する必要があります: 「[Understanding How Snapshots Accrue Charges (スナップショットの課金方法について)](/rest/api/storageservices/understanding-how-snapshots-accrue-charges)」。これは Azure 仮想ディスクの使用よりもわかりにくくなっています。

### <a name="sap-hana-data-consistency-when-taking-storage-snapshots"></a>ストレージ スナップショットを作成する際の SAP HANA データの整合性

ストレージ スナップショットを作成する際には、ファイル システムとアプリケーションの整合性が厄介な問題になります。 問題を回避するための方法として、SAP HANA (場合によっては仮想マシン全体) をシャットダウンするのが最も簡単です。 デモまたはプロトタイプ、あるいは開発システムでもシャットダウンを実行できる場合がありますが、運用システムでは実行できません。

Azure では、Azure BLOB スナップショット機能を使ってもファイル システムの整合性が確保されるわけではない点に留意してください。 対象の仮想ディスクが 1 つのみであれば、SAP HANA スナップショット機能を使用することができます。 ただし、ディスクが 1 つであっても追加の項目はチェックする必要があります。 [SAP ノート 2039883](https://launchpad.support.sap.com/#/notes/2039883) には、ストレージ スナップショットを使用した SAP HANA バックアップに関する重要な情報が記載されています。 たとえばこの SAP ノートによると、XFS ファイル システムで整合性を確保するには、ストレージ スナップショットを開始する前に **xfs\_freeze** を実行する必要があります (**xfs\_freeze** の詳細については「[xfs\_freeze(8) - Linux man page](https://linux.die.net/man/8/xfs_freeze) (freeze(8) - Linux man ページ)」を参照)。

整合性の問題は、単一のファイル システムが複数のディスク/ボリュームにまたがっている場合にいっそう難しくなります。 たとえば、mdadm または LVM を使用してストライピングを行う場合です。 前述の SAP ノートでは、次のように述べられています。

_&quot;しかし、SAP HANA のデータ ボリュームごとにストレージ スナップショットを作成している間、ストレージ システムで I/O 整合性を確保する必要があることに注意してください。言い換えると、SAP HANA サービス固有のデータ ボリュームのスナップショット作成は、アトミックな操作である必要があります。&quot;_

XFS ファイル システムが 4 つの Azure 仮想ディスクにまたがっていると仮定すると、HANA のデータ領域を表す一貫したスナップショットを作成する手順は、次のようになります。

- HANA スナップショットを準備する
- ファイル システムをフリーズする (たとえば、**xfs\_freeze** を使用する)
- Azure で必要な BLOB スナップショットをすべて作成する
- ファイル システムのフリーズを解除する
- HANA スナップショットを確認する

ファイル システムの種類に関わらず、大事を取ってすべてのケースで上記の手順を使用することをお勧めします。 これは、ファイル システムに単一のディスクを使用している場合でも、mdadm または LVM を使って複数のディスクでストライピングを行っている場合でも推奨されます。

HANA スナップショットの確認は重要です。 &quot;コピーオンライト&quot; 方式のため、このスナップショット準備モードの間、追加のディスク領域が SAP HANA に必要でない場合があります。 さらに、SAP HANA スナップショットを確認するまで、新しいバックアップを開始することもできません。

Azure Backup サービスでは、ファイル システムの整合性の確保は Azure VM 拡張機能を使用して行われます。 これらの VM 拡張機能はスタンドアロンでは使用できません。 依然として SAP HANA の整合性を管理する必要があります。 詳細については、関連記事「[ファイル レベルの SAP HANA Azure バックアップ](sap-hana-backup-file-level.md)」を参照してください。

### <a name="sap-hana-backup-scheduling-strategy"></a>SAP HANA バックアップのスケジュール戦略

SAP HANA の記事「[Planning Your Backup and Recovery Strategy (バックアップと回復の戦略の計画)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)」には、バックアップを実行する基本的な計画が記載されています。

- ストレージ スナップショット (毎日)
- ファイルまたは backint 形式を使用した完全なデータ バックアップ (毎週)
- 自動ログ バックアップ

必要に応じて、ストレージ スナップショットなしで済ませることができます。この場合、増分バックアップまたは差分バックアップのように、HANA 差分バックアップを代わりに使用できます ([差分バックアップ](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/bb7e33bb571014a03eeabba4e37541/content.htm)に関する記事を参照)。

HANA 管理ガイドには例の一覧があります。 管理ガイドによると、次の一連のバックアップを使用して、SAP HANA を特定の時点に回復するよう推奨されます。

1. 完全なデータ バックアップ
2. 差分バックアップ
3. 増分バックアップ 1
4. 増分バックアップ 2
5. ログ バックアップ

特定のタイプのバックアップを行う適切なタイミングと頻度の正確なスケジュールについて、一般的なガイドラインはありません。これは、スケジュールがお客様固有のものであり、システム内で行われるデータ変更の回数に依存するためです。 SAP は HANA の完全バックアップを週一回行うことを基本として推奨しており、これを一般的なガイダンスと見なすことができます。
ログ バックアップについては、SAP HANA ドキュメント「[Log Backups (ログ バックアップ)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/bb7e33bb571014a03eeabba4e37541/content.htm)」を参照してください。

SAP は、バックアップ カタログが無限に増加するのを防ぐためにハウスキープ処理を実行することも推奨しています (「[Housekeeping for Backup Catalog and Backup Storage (バックアップ カタログとバックアップ ストレージのハウスキープ処理)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ca/c903c28b0e4301b39814ef41dbf568/content.htm)」を参照)。

### <a name="sap-hana-configuration-files"></a>SAP HANA 構成ファイル

[SAP ノート 1642148](https://launchpad.support.sap.com/#/notes/1642148) の FAQ で述べられているように、SAP HANA 構成ファイルは標準の HANA バックアップに含まれません。 これらの構成ファイルはシステムの復元に必須ではありません。 HANA の構成は復元後に手動で変更できます。 復元プロセス中に同じカスタム構成を取得したい場合、HANA 構成ファイルを別途バックアップする必要があります。

標準の HANA バックアップを専用 HANA バックアップ ファイル システムに保存する場合、構成ファイルを同一のバックアップ ファイル システムにコピーしたうえで、クール BLOB ストレージなどの最終的な保存先にまとめてコピーすることもできます。

### <a name="sap-hana-cockpit"></a>SAP HANA Cockpit

SAP HANA Cockpit を使用すると、ブラウザーで SAP HANA の監視と管理を行えます。 SAP HANA バックアップの作業も行えるので、SAP HANA Studio と ABAP DBACOCKPIT の代わりとして使用できます (詳細については、「[SAP HANA Cockpit](https://help.sap.com/saphelp_hanaplatform/helpdata/en/73/c37822444344f3973e0e976b77958e/content.htm)」を参照)。

![この図は、SAP HANA Cockpit のデータベース管理画面を示しています](media/sap-hana-backup-guide/image004.png)

この図は、SAP HANA Cockpit のデータベース管理画面を示しており、左側にバックアップ タイルがあります。 バックアップ タイルを表示するには、ログイン アカウントに適切なユーザー アクセス許可が必要です。

![実行中のバックアップは SAP HANA Cockpit で監視できます](media/sap-hana-backup-guide/image005.png)

実行中のバックアップは SAP HANA Cockpit で監視でき、完了後はバックアップのすべての詳細を表示できます。

![Azure SLES 12 VM 上の Gnome デスクトップで Firefox を使用した例](media/sap-hana-backup-guide/image006.png)

上のスクリーンショットは Azure Windows VM から作成しました。 このスクリーンショットは、Azure SLES 12 VM 上の Gnome デスクトップで Firefox を使用した例です。 ここには、SAP HANA Cockpit で SAP HANA バックアップのスケジュールを定義するオプションが表示されています。 ご覧のとおり、バックアップ ファイルのプレフィックスとして日付/時刻が推奨されています。 SAP HANA Studio では、ファイルの完全バックアップを行う際の既定のプレフィックスは &quot;COMPLETE\_DATA\_BACKUP&quot; です。 一意のプレフィックスを使用することをお勧めします。

### <a name="sap-hana-backup-encryption"></a>SAP HANA バックアップの暗号化

SAP HANA ではデータとログの暗号化を行えます。 SAP HANA のデータとログが暗号化されない場合、バックアップも暗号化されません。 SAP HANA バックアップの暗号化に何らかのサードパーティ製ソリューションを使用するかどうかは、お客様次第です。 SAP HANA 暗号化の詳細については、「[Data and Log Volume Encryption (データとログのボリューム暗号化)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/01f36fbb5710148b668201a6e95cf2/content.htm)」を参照してください。

Microsoft Azure では、IaaS VM 暗号化機能を使用して暗号化できます。 たとえば、VM に接続された専用データ ディスクに SAP HANA バックアップを格納してから、それらのディスクのコピーを取得することができます。

Azure Backup サービスでは、暗号化された VM/ディスクを処理することができます (「[暗号化された仮想マシンを Azure Backup でバックアップおよび復元する方法](../../../backup/backup-azure-vms-encryption.md)」を参照)。

このほか、暗号化を行わずに SAP HANA VM とそのディスクを保持し、暗号化が有効になっているストレージ アカウントに SAP HANA バックアップ ファイルを格納する方法があります (「[Azure Storage Service Encryption for Data at Rest (保存データ向け Azure Storage Service Encryption)](../../../storage/common/storage-service-encryption.md)」を参照)。

## <a name="test-setup"></a>テストの設定

### <a name="test-virtual-machine-on-azure"></a>テストの Azure 仮想マシン

テストを実行するために、次のバックアップ/復元テストでは、SAP HANA は Azure GS5 VM にインストールされています。 原則は、M シリーズ VM の場合と同様です。

![この図は、HANA テスト VM に関する Azure Portal の概要の一部を示しています](media/sap-hana-backup-guide/image007.png)

この図は、HANA テスト VM に関する Azure Portal の概要の一部を示しています。

### <a name="test-backup-size"></a>テストのバックアップ サイズ

![HANA Studio のバックアップ コンソールから取得したこの図は、HANA インデックス サーバーのバックアップ ファイル サイズが 229 GB であることを示しています](media/sap-hana-backup-guide/image008.png)

現実的なパフォーマンス データが得られるように、ダミーのテーブルには、データ バックアップ サイズの合計が 200 GB を超えるデータが格納されています。 HANA Studio のバックアップ コンソールから取得した上の図は、HANA インデックス サーバーのバックアップ ファイル サイズが 229 GB であることを示しています。 テストでは、SAP HANA Studio の既定のバックアップ プレフィックス "COMPLETE_DATA_BACKUP" が使用されました。 実際の運用システムでは、より有用なプレフィックスを定義する必要があります。 SAP HANA Cockpit では日付/時刻が推奨されます。

### <a name="test-tool-to-copy-files-directly-to-azure-storage"></a>ファイルを Azure Storage に直接コピーするためにテストで使用したツール

Azure Blob Storage (または Azure ファイル共有) への SAP HANA バックアップ ファイルの直接転送には、blobxfer ツールを使用しました。このツールはどちらの転送先にも対応しているうえ、コマンド ライン インターフェイスで使用できるので自動化スクリプトに簡単に統合できるためです。 blobxfer ツールは [GitHub](https://github.com/Azure/blobxfer) で入手できます。

### <a name="test-backup-size-estimation"></a>テストのバックアップ サイズの見積もり

SAP HANA のバックアップ サイズの見積もりは重要です。 この見積もりはパフォーマンスの向上に役立ちます。多数のバックアップ ファイルの最大サイズを定義することで、ファイル コピー中の並列処理が可能になるためです  (詳細についてはこの記事内で後述します)。また、完全バックアップと差分バックアップ (増分または差分) のどちらを実行するかを決定する必要もあります。

さいわい、単純な SQL ステートメント **select \* from M\_BACKUP\_SIZE\_ESTIMATIONS** を使って、バックアップ ファイルのサイズを見積もることができます (「[Estimate the Space Needed in the File System for a Data Backup (データ バックアップのためにファイル システムに必要な領域の見積もり)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/7d/46337b7a9c4c708d965b65bc0f343c/content.htm)」を参照)。

![この SQL ステートメントの出力は、ディスク上にある完全なデータ バックアップの実際のサイズにほぼ一致しています](media/sap-hana-backup-guide/image009.png)

テスト システムでは、この SQL ステートメントの出力は、ディスク上にある完全なデータ バックアップの実際のサイズにほぼ一致しています。

### <a name="test-hana-backup-file-size"></a>テストの HANA バックアップ ファイル サイズ

![HANA Studio バックアップ コンソールでは、HANA バックアップ ファイルの最大ファイル サイズを制限できます](media/sap-hana-backup-guide/image010.png)

HANA Studio バックアップ コンソールでは、HANA バックアップ ファイルの最大ファイル サイズを制限できます。 サンプル環境でこの機能を使用すると、230 GB のバックアップ ファイルを 1 つ取得する代わりに、小さなバックアップ ファイルを多数取得することができます。 ファイル サイズを小さくするとパフォーマンスに大きな影響があります (関連記事「[ファイル レベルの SAP HANA Azure バックアップ](sap-hana-backup-file-level.md)」を参照)。

## <a name="summary"></a>まとめ

次の表に、テスト結果に基づいて、Azure 仮想マシンで実行されている SAP HANA データベースのバックアップ ソリューションの長所と短所が示されています。

**SAP HANA をファイル システムにバックアップしたうえで、バックアップ ファイルを最終的なバックアップ先にコピーする**

|解決策                                           |長所                                 |短所                                  |
|---------------------------------------------------|-------------------------------------|--------------------------------------|
|VM ディスク上の HANA バックアップの保持                      |追加の管理の手間が不要     |ローカル VM ディスクの領域の浪費           |
|バックアップ ファイルを BLOB ストレージにコピーする blobxfer ツール |複数のファイルをコピーする並列処理、クール BLOB ストレージを使用可能 | 追加のツール管理とカスタム スクリプト作成 | 
|PowerShell または CLI を使用した BLOB コピー                    |追加のツールが不要、Azure PowerShell または CLI で実行可能 |手動のプロセス、お客様によるスクリプト作成作業と復元用にコピーされた BLOB の管理作業が必要|
|NFS 共有へのコピー                                  |HANA サーバーに影響を与えることなく、他の VM でバックアップ ファイルを後処理|コピー プロセスが低速|
|Azure ファイル サービスへの blobxfer コピー                |ローカル VM ディスクの領域の節約|HANA バックアップによる直接書き込みのサポートなし、ファイル共有のサイズ制限が現時点で 5 TB|
|Azure Backup エージェント                                 | 推奨されるソリューション         | Linux では現在使用不可    |



**ストレージ スナップショットに基づいて SAP HANA をバックアップする**

|解決策                                           |長所                                 |短所                                  |
|---------------------------------------------------|-------------------------------------|--------------------------------------|
|Azure Backup サービス                               | BLOB スナップショットに基づいた VM のバックアップが可能 | ファイル レベルの復元を使用しない場合、復元プロセスで新しい VM の作成が必要 (つまり、新しい SAP HANA ライセンス キーが必要)|
|手動の BLOB スナップショット                              | 一意の VM ID を変更することなく特定の VM ディスクを作成して復元できる柔軟性|すべての作業がお客様による手動操作|

## <a name="next-steps"></a>次の手順
* 「[ファイル レベルの SAP HANA Azure バックアップ](sap-hana-backup-file-level.md)」では、ファイルベースのバックアップ方法について説明されています。
* 「[ストレージ スナップショットに基づいた SAP HANA のバックアップ](sap-hana-backup-storage-snapshots.md)」では、ストレージ スナップショットベースのバックアップ方法について説明されています。
* SAP HANA on Azure (L インスタンス) の高可用性を確保し、ディザスター リカバリーを計画する方法を確認するには、「[Azure での SAP HANA (L インスタンス) の高可用性とディザスター リカバリー](hana-overview-high-availability-disaster-recovery.md)」を参照してください。
