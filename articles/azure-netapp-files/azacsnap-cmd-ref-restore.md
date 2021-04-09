---
title: Azure NetApp Files 用の Azure アプリケーション整合性スナップショット ツールを使用して復元する | Microsoft Docs
description: Azure NetApp Files で使用できる Azure アプリケーション整合性スナップショット ツールの restore コマンドを実行するためのガイドを提供します。
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
ms.topic: reference
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 793b4da8fcf46ba4d5618f8ada86f9c3c8026ffd
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104865266"
---
# <a name="restore-using-azure-application-consistent-snapshot-tool-preview"></a>Azure アプリケーション整合性スナップショット ツールを使用して復元する (プレビュー)

この記事では、Azure NetApp Files で使用できる Azure アプリケーション整合性スナップショット ツールの restore コマンドを実行する方法について説明します。

## <a name="introduction"></a>はじめに

スナップショットからのボリュームの復元は、`azacsnap -c restore` コマンドを使用して行います。

> [!IMPORTANT]
> これにより、データベースの復旧は実行されず、以下の各オプションで説明するように、ボリュームの復元のみが行われます。

## <a name="command-options"></a>コマンド オプション

`-c restore` コマンドには、次のオプションがあります。

- `--restore snaptovol` ターゲット ボリュームの最新のスナップショットに基づいて、新しいボリュームを作成します。 このコマンドを使用すると、新しいボリュームを作成するベースとして最新のボリューム スナップショットが使用され、構成されたターゲット ボリュームに基づいて新しい "複製" ボリュームが作成されます。  このコマンドにより、プライマリからセカンダリへのストレージのレプリケーションが中断されることはありません。 代わりに、利用可能な最新のスナップショットの複製が DR サイトに作成され、複製されたボリュームの推奨されるファイルシステム マウントポイントが提示されます。 このコマンドは、**DR リージョン** の Azure Large Instance システムで実行する必要があります (つまり、ターゲット フェールオーバー システム)。

- `--restore revertvolume` 最新のスナップショットに基づいて、ターゲット ボリュームを前の状態に戻します。  対になっている DR リージョンへの DR フェールオーバーの一部としてこのコマンドを使用します。 このコマンドを実行すると、プライマリ サイトからセカンダリ サイトへのストレージ レプリケーションが **停止** され、回復された DR ボリュームに対して推奨されるファイルシステム マウントポイントと共に、DR ボリュームで使用可能な最新のスナップショットに、ターゲットの DR ボリュームが戻されます。 このコマンドは、**DR リージョン** の Azure Large Instance システムで実行する必要があります (つまり、ターゲット フェールオーバー システム)。
    > [!NOTE]
    > サブコマンド (`--restore revertvolume`) は、Azure Large Instance でのみ使用でき、Azure NetApp Files では使用できません。
- `--dbsid <SAP HANA SID>` は、ボリューム復元コマンドの適用対象の SAP HANA SID であり、構成ファイルから選択されます。

- `[--configfile <config filename>]` は省略可能なパラメーターであり、カスタム構成ファイル名を使用できます。

## <a name="perform-a-test-dr-failover-azacsnap--c-restore---restore-snaptovol"></a>DR フェールオーバーのテストを実行する `azacsnap -c restore --restore snaptovol`

このコマンドは "完全な" DR フェールオーバー コマンド (`--restore restorevolume`) に似ていますが、プライマリ サイトとディザスター リカバリー サイトの間のレプリケーションは中断されず、ディザスター リカバリー ボリュームから複製ボリュームが作成されます。これにより、DR サイトの最新のスナップショットを復元できます。 その後、お客様は、それらの複製されたボリュームを使用してディザスター リカバリーをテストすることができ、プライマリ サイトとディザスター リカバリー サイトの間のレプリケーション契約が切れる HANA 環境の完全なフェールオーバーを実行する必要はありません。

- この方法を使用すると、複数の異なる復元ポイントを、それぞれに独自の復元ポイントを使用してテストできます。
- 複製は、コマンドが実行されたときのタイムスタンプによって指定され、実行時に使用可能な最新のデータとその他のスナップショットを表します。

> [!IMPORTANT]
> この操作は、Azure Large Instance にのみ適用されます。
>
> - このコマンドを実行するときは、4 週間後に複製が削除される前に、連携のため運用にメールで連絡する必要があります。
> - このコマンドを実行するたびに新しい複製が作成され、テストが完了したら Microsoft の運用チームがそれを削除する必要があります。
> - 作成された複製ボリュームは、4 週間後に自動的に削除されます。

構成ファイル (`DR.json` など) には DR ボリュームのみを含め、運用ボリュームは含めないようにする必要があります。そうしないと、運用ボリュームに複製が作成される可能性があります。

### <a name="output-of-the-azacsnap--c-restore---restore-snaptovol-command-for-single-node-scenario"></a>`azacsnap -c restore --restore snaptovol` コマンドの出力 (単一ノード シナリオの場合)

```output
> azacsnap --configfile DR.json -c restore --restore snaptovol --dbsid H80
* This program is designed for those customers who have previously installed the
  Production HANA instance in the Disaster Recovery Location either as a
  stand-alone instance or as part of a multi-purpose environment.
* This program should be executed from the Disaster Recovery location otherwise
  unintended consequences may result.
* This program is intended to allow the customer to simulate a Disaster Recovery
  failover without actually requiring a failover and subsequent failback.
* Any other restore points must be handled by Microsoft Operations.
* As part of the process, a clone is created of the each of the 'data' and 'other'
  volumes per the configuration file.

Do you wish to continue? (y/n) [n]: y

About to create clones of volumes based on the latest snapshot, these will be
kept for 4 weeks before being automatically deleted by Microsoft Operations.
Enter an email address to contact when deleting clones: <b>person@nowhere.com</b>
Checking state of HLI volumes for SID 'PEW'
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Displaying Mount Points by Volume as follows:
10.50.251.34:/hana_data_h80_sapprdhdb80_mnt00001_t020_xdp_rwclone_20200916_0256  /hana/data/H80/mnt00001 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
10.50.251.36:/hana_log_backups_h80_sapprdhdb80_t020_xdp_rwclone_20200916_0256  /hana/log_backups/H80/01 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
*******************  HANA Test DR Restore Steps  ******************************
* Complete the following steps to recover your HANA database:           *
* 1. Ensure ALL the target mount points exist to mount the snapshot clones.    *
*    e.g. mkdir /hana/logbackups/H99_SOURCE                                    *
* 2. Add Mount Point Details from 'Displaying Mount Points by Volume' as       *
*    output above into /etc/fstab of DR Server.                                *
* 3. Mount newly added filesystems.                                            *
* 4. Perform HANA Snapshot Recovery using HANA Studio.                         *
********************************************************************************
*  These snapshot copies (clones) are kept for 4 weeks before                  *
*  being automatically removed.                                                *
*  Please contact Microsoft Operations to delete them earlier.                 *
********************************************************************************
```

> [!IMPORTANT]
> "Displaying Mount Points by Volume" の出力は、シナリオによって異なります。

## <a name="perform-full-dr-failover-azacsnap--c-restore---restore-revertvolume"></a>完全な DR フェールオーバーを実行する `azacsnap -c restore --restore revertvolume`

このコマンドを使用すると、プライマリ サイトからセカンダリ サイトへのストレージのレプリケーションが **停止** され、DR ボリュームで最新のスナップショットが復元されて、DR ボリュームのマウントポイントが提供されます。

このコマンドは、DR ボリュームのみが含まれる構成ファイル (`DR.json` など) を使用して、DR サーバーで実行する必要があります。

コマンド `azacsnap -c restore --restore revertvolume` を実行して、DR サイトへのフェールオーバーを実行します。  このコマンドを使用するには、パラメーターとして SID を追加する必要があります。 これは、DR サイトで復旧する必要がある HANA インスタンスの SID です。

> [!IMPORTANT]
> DR 演習またはテストの実行を計画している場合にのみ、このコマンドを実行してください。 このコマンドを実行すると、レプリケーションが中断されます。 レプリケーションを再度有効にするには、Microsoft の運用チームに連絡する必要があります。

DR フェールオーバーを実行する手順の概要は次のとおりです。

- **プライマリ** サイトで HANA インスタンスをシャットダウンする必要があります。 この操作は、データの不整合を避けるために DR サイトへのフェールオーバーを実際に行っている場合にのみ必要です。
- 運用 SID の DR ノードで HANA インスタンスをシャットダウンします。
- 復旧する SID を使用して、DR ノードでコマンド `azacsnap -c restore --restore revertvolume` を実行します。
  - このコマンドを実行すると、プライマリから DR サイトへのストレージ レプリケーション リンクが中断されます
  - このコマンドを使用すると、構成に従って、"データ" ボリュームと "その他" のボリュームが復元されます。  通常、この操作は、`/hana/data` および `/hana/logbackups` ファイルシステムのボリュームに対して行います。  `/hana/shared` ファイルシステムは復旧されず、DR の場所にある SID に対する既存の `/hana/shared` が使用されます。
  - `/hana/data` ボリュームと `/hana/logbackups` ボリュームをマウントします。それらを確実に `/etc/fstab` ファイルに追加します
- HANA SYSTEMDB スナップショットを復元します。 HANA Studio には、スナップショット コマンド `azacsnap -c restore --restore revertvolume` の実行の一部として復元されたストレージ スナップショットで使用できる最新の HANA スナップショットのみが表示されます。
- テナント データベースを復旧します。
- 運用 SID の DR サイトで HANA インスタンスを起動します (例: この場合は H80)。
- データベースのテストを実行します。

## <a name="next-steps"></a>次のステップ

- [バックアップを取得する](azacsnap-cmd-ref-backup.md)
- [スナップショットの詳細を取得する](azacsnap-cmd-ref-details.md)
