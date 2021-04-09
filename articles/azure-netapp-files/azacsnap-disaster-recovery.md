---
title: Azure NetApp Files 用の Azure アプリケーション整合性スナップショット ツールを使用したディザスター リカバリー | Microsoft Docs
description: Azure NetApp Files で使用できる Azure アプリケーション整合性スナップショット ツールを使用しているときのディザスター リカバリーの実行方法について説明します。
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 554fa394179e7cfc5b86a2b50eb754547d137a44
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104870392"
---
# <a name="disaster-recovery-using-azure-application-consistent-snapshot-tool-preview"></a>Azure アプリケーション整合性スナップショット ツールを使用したディザスター リカバリー (プレビュー)

この記事では、Azure NetApp Files で使用できる Azure アプリケーション整合性スナップショット ツールを使用しているときのディザスター リカバリーの実行方法について説明します。

> [!IMPORTANT]
> この操作は、**Azure Large Instance** にのみ適用されます。

## <a name="introduction"></a>はじめに

Azure Large Instance プラットフォームには、ストレージ ボリューム スナップショットをレプリケートできるディザスター リカバリー サイトを構成することもできます。  そのようなセットアップでスナップショットが正しく構成されている場合は、このサイトでディザスター リカバリーを実行できます。  このドキュメントは、このセットアップでディザスター リカバリーを実行するためのガイドとして使用することを目的としています。

## <a name="prerequisites-for-disaster-recovery-setup"></a>ディザスター リカバリーのセットアップの前提条件

ディザスター リカバリー フェールオーバーを計画する前に、次の前提条件が満たされている必要があります。

- DR サイトに DR ノードがプロビジョニングされていること。 DR には 2 つのオプションがあります。 1 つは通常の DR で、もう 1 つは多目的 DR です。
- ストレージ レプリケーションが動作していること。 ストレージ レプリケーションのセットアップは、DR の自動プロビジョニング時に、Microsoft 運用チームによって実行されます。 ユーザーは、DR サイトで `azacsnap -c details --details replication` コマンドを使用してストレージ レプリケーションを監視できます。
- プライマリ ロケーションにストレージ スナップショットが設定および構成されていること。
- プライマリ インスタンスと同じ SID で、プライマリに対する DR サイトに HANA インスタンスがインストールされていること。
- 「[Azure での SAP HANA L インスタンスの高可用性とディザスター リカバリー](../virtual-machines/workloads/sap/hana-failover-procedure.md)」で説明されている DR フェールオーバーの手順を読んで理解していること
- DR の場所にストレージ スナップショットが設定および構成されていること。
- DR ストレージ ボリュームと、DR サーバーの関連情報を使用して、構成ファイル (`DR.json` など) が作成されていること。
- DR サイトで次の手順が完了していること。
  - ストレージとの通信を有効にします。
  - SAP HANA との通信を有効にします。

## <a name="set-up-disaster-recovery"></a>ディザスター リカバリーを設定する

Microsoft では、DR 復旧に対するストレージ レベルのレプリケーションがサポートされています。 DR を設定するには 2 つの方法があります。

1 つは **通常** で、もう 1 つは **多目的** です。 **通常** の DR の場合は、フェールオーバー用の DR の場所に専用のインスタンスがあります。 **多目的** の DR シナリオの場合は、DR サイトの HANA Large Instance ユニットで別の QA または開発 HANA インスタンスが実行されています。 ただし、ユーザーは、非アクティブで、その HANA Large Instance ユニットにフェールオーバーする HANA インスタンスと同じ SID を持つ、事前インストール済み HANA インスタンスをインストールしてあります。 Microsoft 運用チームは、オンボード時に、サービス要求フォーム (SRF) で提供された入力に基づいて、ストレージ レプリケーションを含む環境を設定します。

> [!IMPORTANT]
> DR セットアップのすべての前提条件が満たされていることを確実にします。

## <a name="monitor-data-replication-from-primary-to-dr-site"></a>プライマリから DR サイトへのデータ レプリケーションを監視する

Microsoft 運用チームは、プライマリ サイトから DR サイトへの DR リンクを既に管理および監視しています。
ユーザーは、スナップショット コマンド `azacsnap -c details --details replication` を使用して、プライマリ サーバーから DR サーバーへのデータ レプリケーションを監視できます。

## <a name="perform-a-failover-to-dr-site"></a>DR サイトへのフェールオーバーを実行する

DR サイトでフェールオーバー コマンドを実行します (`azacsnap -c restore --restore revertvolume`)。

> [!IMPORTANT]
> `azacsnap -c restore --restore revertvolume` コマンドを実行すると、運用サイトから DR サイトへのストレージ レプリケーションが中断されます。 レプリケーションをもう一度設定するには、Microsoft 運用チームに連絡する必要があります。 レプリケーションが再度有効にされると、この SID の DR ストレージにあるすべてのデータが初期化されます。 フェールオーバーを実行するコマンドにより、最後にレプリケートされたストレージ スナップショットが使用できるようになります。 古いスナップショットに復元して戻す必要がある場合は、サポート リクエストを開き、DR サイトに復元される以前のスナップショットを運用チームが提供できるようにします。

DR フェールオーバーの大まかな手順は次のとおりです。

- **プライマリ** サイトで HANA インスタンスをシャットダウンする必要があります。 この操作は、データの不整合がないように DR サイトへのフェールオーバーを実際に行っている場合にのみ必要です。
- 運用 SID の DR ノードで HANA インスタンスをシャットダウンします。
- 復旧する SID を使用して、DR ノードでコマンド `azacsnap -c restore --restore revertvolume` を実行します
  - このコマンドを実行すると、プライマリから DR サイトへのストレージ レプリケーション リンクが中断されます
  - このコマンドを使用すると、/data および /logbackups ボリュームのみが復元されます。/shared ボリュームは復旧されず、DR の場所の SID に対する既存の /shared が使用されます。
  - /data および /logbackups ボリュームをマウントし、それが fstab ファイルに追加されるようにします
- HANA SYSTEMDB スナップショットを復元します。 HANA Studio には、コマンド `azacsnap -c restore --restore revertvolume` の実行の一部として復元されたストレージ スナップショットで使用できる最新の HANA スナップショットのみが表示されます。
- テナント データベースを復旧します。
- 運用 SID の DR サイトで HANA インスタンスを起動します (例: この場合は H80)。
- テストを実行します。

### <a name="example-performing-disaster-recovery"></a>ディザスター リカバリーの実行例

このサブセクションでは、ディザスター リカバリー サイトへのフェールオーバーの詳細な手順について説明します。

#### <a name="step-1-get-the-volume-details-of-the-dr-node"></a>手順 1:DR ノードのボリュームの詳細を取得する

`df –h` コマンドを実行して、ファイルシステムおよび関連付けられているボリュームの一覧を表示し、フェールオーバー後に参照できるようにします。

```bash
df -h
```

```output
Filesystem Size Used Avail Use% Mounted on
devtmpfs 378G 8.0K 378G 1% /dev
tmpfs 569G 0 569G 0%
/dev/shm
tmpfs 378G 18M 378G 1% /run
tmpfs 378G 0 378G 0%
/sys/fs/cgroup
/dev/mapper/3600a098038304445622b4b584c575a66-part2 47G 20G 28G 42% /
/dev/mapper/3600a098038304445622b4b584c575a66-part1 979M 57M 856M 7% /boot
172.18.20.241:/hana_log_h80_mnt00003_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00001_t020_vol 512G 5.5G 507G 2% /hana/log/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00003_t020_vol 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00002_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00002_t020_vol 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00001_t020_vol 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_shared_h80_t020_vol/usr_sap_node1 2.7T 11G 2.7T 1% /usr/sap/H80
tmpfs 76G 0 76G 0% /run/user/0
172.18.20.241:/hana_shared_h80_t020_vol 2.7T 11G 2.7T 1% /hana/shared
172.18.20.241:/hana_data_h80_mnt00001_t020_xdp 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00002_t020_xdp 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00003_t020_xdp 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_backups_h80_t020_xdp 512G 15G 498G 3% /hana/logbackups/H80_T250
```

#### <a name="step-2-shut-down-hana-on-the-primary-site"></a>手順 2:プライマリ サイトで HANA をシャットダウンする

運用ワークロードの完全なフェールオーバーを実行していて、プライマリ運用サイトに接続できる場合は、DR にフェールオーバーされる SAP HANA インスタンスをシャットダウンします。

たとえば、root としてログインした場合は、次の例で示す方法により SAP HANA をシャットダウンできます。  <sid> は SAP HANA SID に置き換えます。

```bash
su - <sid>adm
HDB stop
```

#### <a name="step-3-shut-down-hana-on-dr-site"></a>手順 3: DR サイトで HANA をシャットダウンする

ボリュームを復元する前に、DR サイトで SAP HANA をシャットダウンすることが重要です。

たとえば、root としてログインした場合は、次の例で示す方法により SAP HANA をシャットダウンできます。  <sid> は SAP HANA SID に置き換えます。

```bash
su - <sid>adm
HDB stop
```

> [!IMPORTANT]
> ボリュームを復元する前に、DR サイトの HANA インスタンスがオフラインになっていることを確認します。

#### <a name="step-4-restore-the-volumes"></a>手順 4:ボリュームを復元する

```bash
azacsnap -c restore --restore revertvolume --dbsid H80
```

**_DR フェールオーバー コマンドの出力_**。

```bash
azacsnap --configfile DR.json -c restore --restore revertvolume --dbsid H80
```

```output
* This program is designed for those customers who have previously installed the
  Production HANA instance in the Disaster Recovery Location either as a
  stand-alone instance or as part of a multi-purpose environment.
* This program should be executed from the Disaster Recovery location otherwise
  unintended consequences may result.
* This program is intended to allow the customer to complete a Disaster Recovery
  failover.
* Any other restore points must be handled by Microsoft Operations.
* All volumes ('data' and 'other') are reverted to their most recent snapshot.
* The SnapMirror replication relationship between Prod and DR will be broken.

  CAUTION: a failback will be required after running this command and failback
   might not be a quick process and will require multiple steps in coordination
   with Microsoft Operations.

Do you wish to continue? (y/n) [n]: y
Checking state of HLI volumes for SID 'H80'
Configured volumes (Data and Other) are not quiesced for revert, will retry in 00:00:10 seconds
Volumes All Ok to Revert = True
Reverting volume 'hana_data_h80_mnt00001_t020_xdp' to snapshot 'H80_HANA_DATA_30MIN.2020-09-16_0330.0'
DR.json Data Volume #1 'hana_data_h80_mnt00001_t020_xdp' assigning to mountpoint 'mnt00001'
Reverting volume 'hana_log_backups_h80_t020_xdp01' to snapshot 'H80_HANA_LOGS_3MIN_X9.2020-09-16_0339.recent'
DR.json Other Volume #1 'hana_log_backups_h80_t020_xdp01' assigning to mountpoint '01'
HLI Volume revert completed for SID 'H80'
Displaying Mount Points by Volume as follows:
10.50.251.34:/hana_data_h80_mnt00001_t020_xdp  /hana/data/H80/mnt00001 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
10.50.251.36:/hana_log_backups_h80_t020_xdp01  /hana/log_backups/H80/01 nfs rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
*********************  HANA DR Restore Steps  **********************************
* Please complete the following steps to recover your HANA database:           *
* 1. Ensure ALL the target mount points exist to mount the snapshot clones.    *
*    e.g. mkdir /hana/logbackups/H99_SOURCE                                    *
* 2. Add Mount Point Details from 'Displaying Mount Points by Volume' as       *
*    output above into /etc/fstab of DR Server.                                *
* 3. Mount newly added filesystems.                                            *
* 4. Perform HANA Snapshot Recovery using HANA Studio.                         *
********************************************************************************
```

> [!NOTE]
> DR フェールオーバー用のストレージの準備を完了するには、コンソール表示の最後にある手順を行う必要があります。

#### <a name="step-5-unmount-unnecessary-filesystems"></a>手順 5:不要なファイルシステムのマウントを解除する

`umount` コマンドを実行して、必要のないファイルシステムやボリュームのマウントを解除します。

```bash
umount <Mount point>
```

データとログのバックアップ マウントポイントをマウント解除します。 スケールアウト シナリオの場合、複数のデータ マウントポイントが存在することがあります。

#### <a name="step-6-configure-the-mount-points"></a>手順 6:マウント ポイントを構成する

`/etc/fstab` ファイルを変更して、プライマリ SID (この例では SID = H80) のデータとログのバックアップ エントリをコメント化し、プライマリ サイトの DR ボリュームから作成された新しいマウント ポイント エントリを追加します。 新しいマウント ポイント エントリは、コマンドの出力で提供されます。

- DR サイトで実行されている既存のマウント ポイントを、`#` 文字を使用してコメント化します。

  ```output
  #172.18.20.241:/hana_data_h80_mnt00001_t020_vol /hana/data/H80/mnt00001 nfs     rw,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  #172.18.20.241:/hana_log_backups_h80_t020 /hana/logbackups/H80 nfs rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  ```

- 次の行を `/etc/fstab` に追加します
  > コマンドからの出力はこれと同じになる必要があります

  ```output
  10.50.251.34:/hana_data_h80_mnt00001_t020_xdp  /hana/data/H80/mnt00001 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  10.50.251.36:/hana_log_backups_h80_t020_xdp01  /hana/log_backups/H80/01 nfs rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  ```

#### <a name="step-7-mount-the-recovery-volumes"></a>手順 7: 復旧ボリュームをマウントする

`mount –a` コマンドを実行して、すべてのマウント ポイントをマウントします。

```bash
mount -a
```

ここで、`df –h` を実行すると、`*_dp` ボリュームがマウントされているはずです。

```bash
df -h
```

```output
Filesystem Size Used Avail Use% Mounted on
devtmpfs 378G 8.0K 378G 1% /dev
tmpfs 569G 0 569G 0% /dev/shm
tmpfs 378G 18M 378G 1% /run
tmpfs 378G 0 378G 0% /sys/fs/cgroup
/dev/mapper/3600a098038304445622b4b584c575a66-part2 47G 20G 28G 42% /
/dev/mapper/3600a098038304445622b4b584c575a66-part1 979M 57M 856M 7% /boot
172.18.20.241:/hana_log_h80_mnt00003_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00001_t020_vol 512G 5.5G 507G 2% /hana/log/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00003_t020_vol 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00002_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00002_t020_vol 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00001_t020_vol 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_shared_h80_t020_vol/usr_sap_node1 2.7T 11G 2.7T 1% /usr/sap/H80
tmpfs 76G 0 76G 0% /run/user/0
172.18.20.241:/hana_shared_h80_t020_vol 2.7T 11G 2.7T 1% /hana/shared
172.18.20.241:/hana_data_h80_mnt00001_t020_xdp 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00002_t020_xdp 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00003_t020_xdp 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_backups_h80_t020_xdp 512G 15G 498G 3% /hana/logbackups/H80_T250
```

#### <a name="step-8-recover-the-systemdb"></a>手順 8:SYSTEMDB を復旧する

HANA Studio で、SYSTEMDB インスタンスを右クリックして、[Backup and Recovery]\(バックアップと復旧\) を選択してから、[Recover System Database]\(システム データベースの復旧\) を選択します。

スナップショット (具体的には SYSTEMDB) からのデータベースの復旧については、ガイドを参照してください。

#### <a name="step-9-recover-the-tenant-database"></a>手順 9:テナント データベースを復旧する

HANA Studio で、SYSTEMDB インスタンスを右クリックして、[Backup and Recovery]\(バックアップと復旧\) を選択してから、[Recover Tenant Database]\(テナント データベースの復旧\) を選択します。

スナップショット (具体的には TENANT データベース) からのデータベースの復旧については、ガイドを参照してください。

### <a name="run-azacsnap--c-backup-at-the-dr-site"></a>DR サイトで `azacsnap -c backup` を実行する

DR サイトでスナップショット ベースのバックアップを実行している場合、DR サイトの `azacsnap` 構成ファイルで構成されている HANA サーバー名は、運用サーバー名と同じである必要があります。

> [!IMPORTANT]
> `azacsnap -c backup` を実行すると、DR サイトにストレージ スナップショットを作成できます。これらが別のサイトに自動的にレプリケートされることはありません。  元の運用サイトにファイルまたはデータを戻す方法の詳細については、Microsoft 運用チームにお問い合わせください。

## <a name="next-steps"></a>次のステップ

- [スナップショットの詳細を取得する](azacsnap-cmd-ref-details.md)
- [バックアップを取得する](azacsnap-cmd-ref-backup.md)
