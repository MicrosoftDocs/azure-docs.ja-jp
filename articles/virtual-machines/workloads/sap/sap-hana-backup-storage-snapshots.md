---
title: ストレージ スナップショットに基づいた SAP HANA Azure バックアップ | Microsoft Docs
description: Azure 仮想マシン上の SAP HANA をバックアップする方法は主に 2 つあります。この記事では、ストレージ スナップショットに基づいた SAP HANA のバックアップについて説明します
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: rclaus
ms.openlocfilehash: 875060a59cf70d295534c3a4f56136010a560e74
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709926"
---
# <a name="sap-hana-backup-based-on-storage-snapshots"></a>ストレージ スナップショットに基づいた SAP HANA のバックアップ

## <a name="introduction"></a>はじめに

この記事は、SAP HANA のバックアップに関する 3 部構成の記事の 1 つです。 「[Azure Virtual Machines 上の SAP HANA のバックアップ ガイド](sap-hana-backup-guide.md)」には、概要と基本的な情報が記載されており、「[ファイル レベルの SAP HANA Azure バックアップ](sap-hana-backup-file-level.md)」には、ファイルベースのバックアップ オプションについての説明があります。

オールインワンの単一インスタンス デモ システムで VM バックアップ機能を使用する場合、OS レベルで HANA バックアップを管理するのではなく、VM バックアップを実行することを検討する必要があります。 別の方法としては、Azure BLOB スナップショットを作成して、仮想マシンに接続されている個別の仮想ディスクのコピーを作成し、HANA データ ファイルを保持する方法があります。 しかし、システムの稼働中に VM バックアップまたはディスク スナップショットを作成する場合、アプリの整合性が重要な問題になります。 関連記事「[Azure Virtual Machines 上の SAP HANA のバックアップ ガイド](sap-hana-backup-guide.md)」の「_ストレージ スナップショットを作成する際の SAP HANA データの整合性_」を参照してください。 SAP HANA には、これらの種類のストレージ スナップショットに対応する機能が備わっています。

## <a name="sap-hana-snapshots-as-central-part-of-application-consistent-backups"></a>アプリケーションの整合バックアップの中心部としての SAP HANA スナップショット

SAP HANA には、ストレージのスナップショットを作成する機能があります。 単一コンテナー システムの制限があります。 複数のテナントで SAP HANA MCS を使用するシナリオでは、この種の SAP HANA データベース スナップショットをサポートしていません (「[Create a Storage Snapshot (SAP HANA Studio)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a0/3f8f08501e44d89115db3c5aa08e3f/content.htm)」 (ストレージ スナップショットの作成 (SAP HANA Studio)) を参照)。

手順は次のようになります。

- SAP HANA スナップショットを開始して、ストレージ スナップショットの準備を行う
- ストレージ スナップショットを実行する (例: Azure BLOB スナップショップ)
- SAP HANA スナップショットを確認する

![このスクリーンショットには、SQL ステートメントを使用して SAP HANA データ スナップショットを作成できることが示されています](media/sap-hana-backup-storage-snapshots/image011.png)

このスクリーンショットには、SQL ステートメントを使用して SAP HANA データ スナップショットを作成できることが示されています。

![スナップショットは、SAP HANA Studio のバックアップ カタログにも表示されます](media/sap-hana-backup-storage-snapshots/image012.png)

スナップショットは、SAP HANA Studio のバックアップ カタログにも表示されます。

![ディスクでは、スナップショットは SAP HANA データ ディレクトリ内に表示されます](media/sap-hana-backup-storage-snapshots/image013.png)

ディスクでは、スナップショットは SAP HANA データ ディレクトリ内に表示されます。

SAP HANA がスナップショット準備モードの間、ストレージ スナップショットを実行する前にファイル システムの整合性も確保されるようにする必要があります。 関連記事「[Azure Virtual Machines 上の SAP HANA のバックアップ ガイド](sap-hana-backup-guide.md)」の「_ストレージ スナップショットを作成する際の SAP HANA データの整合性_」を参照してください。

ストレージ スナップショット完了後の SAP HANA スナップショットの確認は、非常に重要です。 BACKUP DATA CLOSE SNAPSHOT という、実行すべき対応する SQL ステートメントがあります ([BACKUP DATA CLOSE SNAPSHOT ステートメント (バックアップと復旧)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/9739966f7f4bd5818769ad4ce6a7f8/content.htm) に関するページを参照)。

> [!IMPORTANT]
> HANA スナップショットを確認してください。 &quot;コピーオンライト&quot; 方式のため、スナップショット準備モードの SAP HANA に追加のディスク領域が必要でない場合があります。また、SAP HANA スナップショットを確認するまで、新しいバックアップを開始することはできません。

## <a name="hana-vm-backup-via-azure-backup-service"></a>Azure Backup サービスによる HANA VM バックアップ

Linux VM では Azure Backup サービスの Backup エージェントは使用できません。 また、Linux には Windows の VSS と同様な機能はありません。  ファイル/ディレクトリ レベルの Azure バックアップを使用するには、SAP HANA バックアップ ファイルを Windows VM にコピーしたうえで、Backup エージェントを使用します。 

それ以外では、Azure Backup サービスによる Linux VM の完全バックアップのみが可能です。 詳細については、「[Azure Backup の各機能の概要](../../../backup/backup-introduction-to-azure-backup.md)」を参照してください。

Azure Backup サービスには、VM をバックアップして復元するオプションが用意されています。 このサービスの詳細としくみについては、記事「[Azure における VM バックアップ インフラストラクチャの計画を立てる](../../../backup/backup-azure-vms-introduction.md)」を参照してください。

この記事によると、重要な考慮事項が 2 つあります。

_&quot;Linux には VSS に相当するプラットフォームが存在しないため、Linux 仮想マシンに関しては、ファイル整合バックアップのみの対応となります。&quot;_

_&quot;アプリケーションでは、復元されたデータに対する独自の "&quot;修正&quot;" メカニズムを実装する必要があります。&quot;_

このため、バックアップ開始時にディスク上で SAP HANA の整合性を確保する必要があります。 前述の「_SAP HANA スナップショット_」を参照してください。 しかし、SAP HANAがこのスナップショット準備モードのままになっていると、潜在的な問題があります。 詳細については、「[Create a Storage Snapshot (SAP HANA Studio) (ストレージ スナップショットの作成 (SAP HANA Studio))](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a0/3f8f08501e44d89115db3c5aa08e3f/content.htm)」を参照してください。

この記事では次のように述べられています。

_&quot;作成後のストレージ スナップショットは、できるだけ早く確認または破棄することを強くお勧めします。ストレージ スナップショットの準備中または作成中、スナップショット関連データはフリーズしています。スナップショット関連データがフリーズしている間も、データベースに対する変更は可能なままです。そのような変更によって、フリーズ中のスナップショット関連データが変更されることはありません。その代わり、変更は、データ領域内にある、ストレージ スナップショットとは別の場所に書き込まれます。また、変更はログにも書き込まれます。しかし、スナップショット関連データのフリーズ時間の長さに比例して、データ ボリュームは大きくなる可能性があります。&quot;_

Azure Backup では、ファイル システムの整合性の確保は Azure VM 拡張機能によって行われます。 これらの拡張機能はスタンドアロンでは使用できず、必ず Azure Backup サービスと共に使用する必要があります。 それでも、SAP HANA スナップショットを作成および削除するスクリプトを提供してアプリ整合性を確保することは必須です。

Azure Backup には主に次の 4 つのフェーズがあります。

- 準備スクリプトの実行: スクリプトで SAP HANA スナップショットが作成される必要があります
- スナップショットの作成
- ポストスナップショット スクリプトの実行: スクリプトで準備スクリプトによって作成された SAP HANA が削除される必要があります
- コンテナーへのデータ転送

これらのスクリプトをコピーする場所と Azure Backup の正確な動作の詳細については、次の記事を参照してください。

- [Azure における VM バックアップ インフラストラクチャの計画を立てる](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction)
- [Azure Linux VM のアプリケーション整合性バックアップ](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent)



Microsoft では現時点では、まだ SAP HANA の準備スクリプトおよびポストスナップショット スクリプトは公開していません。 これらのスクリプトは、お客様自身またはシステム インテグレーターが作成して、上記のドキュメントに従って手順を構成する必要があります。


## <a name="restore-from-application-consistent-backup-against-a-vm"></a>VM に対するアプリケーション整合性バックアップからの復元
アプリケーション整合性バックアップで Azure Backup から復元する手順については、「[Azure 仮想マシンのバックアップからファイルを回復する](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm)」の記事を参照してください。 

> [!IMPORTANT]
> 「[Azure 仮想マシンのバックアップからファイルを回復する](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm)」の記事には、ディスクのストライプ セットを使用するときの例外と手順があります。 SAP HANA の VM は、通常ディスクがストライピングされ構成されています。 つまり、記事を読み、記事に記載されているようなケースの復元手順をテストすることが不可欠です。 



## <a name="hana-license-key-and-vm-restore-via-azure-backup-service"></a>HANA ライセンス キーと Azure Backup サービスによる VM の復元

Azure Backup サービスは、復元中に新しい VM を作成するよう設計されています。 現在、既存の Azure VM の &quot;インプレース&quot; 復元を実行する計画はありません。

![この図は、Azure Portal 内の Azure サービスの復元オプションを示しています](media/sap-hana-backup-storage-snapshots/image019.png)

この図は、Azure Portal 内の Azure サービスの復元オプションを示しています。 復元中の VM の作成とディスクの復元のいずれかを選択できます。 ディスクを復元した後、新しい VM をその上に作成する必要があります。 Azure で新しい VM が作成されると、一意の VM ID が変更されます (「[Accessing and Using Azure VM Unique ID (一意の Azure VM ID へのアクセスと使用)](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/)」を参照)。

![この図は、Azure Backup サービスによる復元前後の一意の Azure VM ID を示しています](media/sap-hana-backup-storage-snapshots/image020.png)

この図は、Azure Backup サービスによる復元前後の一意の Azure VM ID を示しています。 SAP ライセンスに使用される SAP ハードウェア キーでは、この一意の VM ID が使用されています。 そのため、VM の復元後には新しい SAP ライセンスをインストールする必要があります。

このバックアップ ガイドの執筆中に、新しい Azure Backup 機能がプレビュー モードで公開されました。 この機能を使用すると、VM バックアップ用に作成した VM スナップショットに基づいてファイル レベルの復元を行えます。 新しい VM のデプロイが不要になるため、一意の VM ID は変わらず、新しい SAP HANA ライセンス キーは必要ありません。 この機能に関する詳細なドキュメントについては、テストが完全に完了した後に公開される予定です。

Azure Backup では最終的に個別の Azure 仮想ディスクのバックアップが可能になるほか、VM 内部からのファイルとディレクトリのバックアップが可能になる予定です。 Azure Backup の大きな利点は、すべてのバックアップが Azure Backup によって管理されるため、お客様が管理せずに済む点です。 復元が必要になった場合、Azure Backup によって使用に適したバックアップが選択されます。

## <a name="sap-hana-vm-backup-via-manual-disk-snapshot"></a>手動のディスク スナップショットによる SAP HANA VM バックアップ

Azure Backup サービスを使用するのではなく、PowerShell を使用して Azure VHD の BLOB スナップショットを手動で作成することで、個別のバックアップ ソリューションを構成することができます。 この手順の説明については、「[Using blob snapshots with PowerShell (PowerShell による BLOB スナップショットの使用)](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)」を参照してください。

柔軟性は高まりますが、このドキュメントで先ほど説明した問題が解決されるわけではありません。

- SAP HANA スナップショットを作成して SAP HANA の整合性を確保する必要があります。
- リースの存在を示すエラーが原因で VM の割り当てが解除された場合でも、OS ディスクは上書きできません。 OS ディスクは VM の削除後にのみ動作します。これは、新しい一意の VM ID と新しい SAP ライセンスのインストールが必要であることを意味します。

![復元できるのは Azure VM のデータ ディスクのみです](media/sap-hana-backup-storage-snapshots/image021.png)

新しい一意の VM ID の取得 (つまり SAP ライセンスの無効化) の問題を回避しながら復元できるのは、Azure VM のデータ ディスクのみです。

- テストでは、1 つの VM に 2 つの Azure データ ディスクを接続し、それらのディスクの上にソフトウェア RAID を定義しました 
- SAP HANA スナップショット機能によって SAP HANA の整合性が確保されていることを確認しました
- ファイル システムをフリーズしました (関連記事「[Azure Virtual Machines 上の SAP HANA のバックアップ ガイド](sap-hana-backup-guide.md)」の「_ストレージ スナップショットを作成する際の SAP HANA データの整合性_」を参照)
- 両方のデータ ディスクから BLOB スナップショットを作成
- ファイル システムのフリーズを解除
- SAP HANA スナップショットを確認
- データ ディスクを復元するために、VM をシャットダウンして両方のディスクの接続を解除
- 接続を解除した後、先ほどの BLOB スナップショットを使用してディスクを上書き
- 次に、復元した仮想ディスクを VM に再接続
- VM の起動後、ソフトウェア RAID 上のすべてが正常に動作し、BLOB スナップショットの時点まで復元
- HANA は HANA スナップショットの状態に復元

もし BLOB スナップショットの前に SAP HANA をシャットダウンできるのであれば、手順の複雑さは減少します。 その場合 HANA スナップショットをスキップできます。また、システム内で他に何も行われない場合、ファイル システムのフリーズもスキップできます。 すべてが稼働している間にスナップショットを実行する必要がある場合、手順の複雑さは増加します。 関連記事「[Azure Virtual Machines 上の SAP HANA のバックアップ ガイド](sap-hana-backup-guide.md)」の「_ストレージ スナップショットを作成する際の SAP HANA データの整合性_」を参照してください。

## <a name="next-steps"></a>次の手順
* 「[Azure Virtual Machines 上の SAP HANA のバックアップ ガイド](sap-hana-backup-guide.md)」には、概要と基本的な情報が記載されています。
* 「[ファイル レベルの SAP HANA Azure バックアップ](sap-hana-backup-file-level.md)」では、ファイルベースのバックアップ オプションについて説明しています。
* SAP HANA on Azure (L インスタンス) の高可用性を確保し、ディザスター リカバリーを計画する方法を確認するには、「[Azure での SAP HANA (L インスタンス) の高可用性とディザスター リカバリー](hana-overview-high-availability-disaster-recovery.md)」を参照してください。
