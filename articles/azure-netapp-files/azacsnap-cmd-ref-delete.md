---
title: Azure NetApp Files 用の Azure アプリケーション整合性スナップショット ツールを使用して削除する | Microsoft Docs
description: Azure NetApp Files で使用できる Azure アプリケーション整合性スナップショット ツールの delete コマンドを実行するためのガイドを提供します。
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
ms.openlocfilehash: 1f2c767d45bb08e25a057c7db1f380ceb250f607
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864909"
---
# <a name="delete-using-azure-application-consistent-snapshot-tool-preview"></a>Azure アプリケーション整合性スナップショット ツールを使用して削除する (プレビュー)

この記事では、Azure NetApp Files で使用できる Azure アプリケーション整合性スナップショット ツールの delete コマンドを実行するためのガイドを提供します。

## <a name="introduction"></a>はじめに

`azacsnap -c delete` コマンドを使用して、ボリューム スナップショットとデータベース カタログ エントリを削除できます。

> [!IMPORTANT]
> このコマンドを実行する前の 10 分以内に作成されたスナップショットは、スナップショットのレプリケーションと干渉する可能性があるため、削除しないでください。

## <a name="command-options"></a>コマンド オプション

`-c delete` コマンドには、次のオプションがあります。

- `--delete hana` をオプション `--dbsid <SID>` および `--hanabackupid <HANA backup id>` と共に使用すると、条件に一致するエントリが SAP HANA バックアップ カタログから削除されます。

- `--delete storage` をオプション `--snapshot <snapshot name>` と共に使用すると、スナップショットがバックエンド ストレージ システムから削除されます。

- `--delete sync` をオプション `--dbsid <SID>` および `--hanabackupid <HANA backup id>` と共に使用すると、`<HANA backup id>` のバックアップ カタログからストレージ スナップショット名が取得され、名前付きスナップショットを含むすべてのボリュームから、バックアップ カタログ "_および_" スナップショットのエントリが削除されます。

- `--delete sync` を `--snapshot <snapshot name>` と共に使用すると、`<snapshot name>` のバックアップ カタログ内のすべてのエントリが確認され、SAP HANA バックアップ ID が取得され、名前付きスナップショットを含むすべてのボリュームから、バックアップ カタログ "_および_" スナップショットの両方のエントリが削除されます。

- `[--force]` (省略可能) *注意して使用してください*。  この操作を行うと、確認が求められず、強制的に削除されます。

- `[--configfile <config filename>]` は省略可能なパラメーターであり、カスタム構成ファイル名を使用できます。

### <a name="delete-a-snapshot-using-sync-option"></a>`sync` オプションを使用してスナップショットを削除する`

```bash
azacsnap -c delete --delete sync --dbsid H80 --hanabackupid 157979797979
```

> [!NOTE]
> SAP HANA バックアップ ID 157979797979 のバックアップ カタログに含まれるすべてのエントリを確認し、ストレージ スナップショット名を取得して、名前付きスナップショットを含むすべてのボリュームからバックアップ カタログとスナップショットの両方のエントリを削除します。

```bash
azacsnap -c delete --delete sync --snapshot hana_hourly.2020-01-22_2358
```

> [!NOTE]
> hana_hourly.2020-01-22_2358 という名前のスナップショットのバックアップ カタログに含まれるすべてのエントリを確認し、SAP HANA バックアップ ID を取得して、名前付きスナップショットを含むすべてのボリュームからバックアップ カタログとスナップショットの両方のエントリを削除します。

### <a name="delete-a-snapshot-using-hana-option"></a>`hana` オプションを使用してスナップショットを削除する`

```bash
azacsnap -c delete --delete hana --dbsid H80 --hanabackupid 157979797979
```

> [!NOTE]
> SID H80 のバックアップ カタログから SAP HANA バックアップ ID 157979797979 を削除します。

### <a name="delete-a-snapshot-using-storage-option"></a>`storage` オプションを使用してスナップショットを削除する`

```bash
azacsnap -c delete --delete storage --snapshot hana_hourly.2020-01-22_2358
```

> [!NOTE]
> hana_hourly.2020-01-22_2358 という名前のスナップショットを含むすべてのボリュームからスナップショットを削除します。

**`--delete storage` オプションを使用して出力する**

削除の確認を求めるメッセージがユーザーに表示されます。

```bash
azacsnap -c delete --delete storage --snapshot azacsnap-hsr-ha.2020-07-02T221702.2535255Z
```

```output
Processing delete request for snapshot 'azacsnap-hsr-ha.2020-07-02T221702.2535255Z'.
Are you sure you want to permanently delete the snapshot 'azacsnap-hsr-ha.2020-07-02T221702.2535255Z' from all storage volumes.? (y/n) [n]: y
Snapshot deletion completed
```

省略可能な `--force` パラメーターを次のように使用することで、ユーザーの確認を回避できます。

```bash
azacsnap -c delete --delete storage --snapshot azacsnap-hsr-ha.2020-07-02T222201.4988618Z --force
```

```output
Processing delete request for snapshot 'azacsnap-hsr-ha.2020-07-02T222201.4988618Z'.
Snapshot deletion completed
```

## <a name="next-steps"></a>次のステップ

- [スナップショットの詳細を取得する](azacsnap-cmd-ref-details.md)
- [バックアップを取得する](azacsnap-cmd-ref-backup.md)
