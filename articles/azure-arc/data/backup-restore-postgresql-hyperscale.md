---
title: Azure Database for PostgreSQL Hyperscale サーバー グループでのバックアップと復元
description: Azure Database for PostgreSQL Hyperscale サーバー グループでのバックアップと復元
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 12/09/2020
ms.topic: how-to
ms.openlocfilehash: 8b3304c673e8606667246a7d0df9ad8f3be11d9b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101686701"
---
# <a name="back-up-and-restore-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Azure Arc 対応 PostgreSQL Hyperscale サーバー グループのバックアップと復元

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

Azure Arc 対応 PostgreSQL Hyperscale サーバー グループのバックアップまたは復元を行うときには、サーバー グループのすべての PostgreSQL ノードにあるデータベースのセット全体のバックアップや復元が行われます。

## <a name="take-a-manual-full-backup"></a>手動で完全バックアップを行う

サーバー グループのデータ フォルダーとログ フォルダー全体の完全バックアップを行うには、次のコマンドを実行します。
```console
azdata arc postgres backup create [--name <backup name>] --server-name <server group name> [--no-wait] 
```
各値の説明:
- __name__ は、バックアップの名前を示します
- __server-name__ は、サーバー グループを示します
- __no wait__ は、バックアップの完了を待たずに、ユーザーがこのコマンド ライン ウィンドウを継続して使用できるようにすることを示します

このコマンドを使用すると、Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを構成するすべてのノードで、分散完全バックアップが調整されます。 つまり、コーディネーター ノードとワーカー ノードのすべてのデータがバックアップされます。

次に例を示します。

```console
azdata arc postgres backup create --name backup12082020-0250pm --server-name postgres01
```

バックアップが完了すると、バックアップの ID、名前、サイズ、状態、タイムスタンプが返されます。 次に例を示します。
```console
{
  "ID": "8085723fcbae4aafb24798c1458f4bb7",
  "name": "backup12082020-0250pm",
  "size": "9.04 MiB",
  "state": "Done",
  "timestamp": "2020-12-08 22:50:22+00:00"
}
```
`+xx:yy` は、バックアップが行われた時刻のタイムゾーンを示します。 この例では、"+00:00" は UTC 時刻を意味します (UTC + 00 時 00 分)。

> [!NOTE]
> 次のことはまだできません。
> - 自動バックアップのスケジュールを作成する
> - 実行中にバックアップの進行状況を表示する

## <a name="list-backups"></a>バックアップの一覧を表示する

復元に使用できるバックアップの一覧を表示するには、次のコマンドを実行します。

```console
azdata arc postgres backup list --server-name <servergroup name>
```

次に例を示します。

```console
azdata arc postgres backup list --server-name postgres01
```

次のような出力が返されます。

```output
ID                                Name                   Size       State    Timestamp
--------------------------------  ---------------------  ---------  -------  -------------------------
d744303b1b224ef48be9cba4f58c7cb9  backup12072020-0731pm  13.83 MiB  Done     2020-12-08 03:32:09+00:00
c4f964d28da34318a420e6d14374bd36  backup12072020-0819pm  9.04 MiB   Done     2020-12-08 04:19:49+00:00
a304c6ef99694645a2a90ce339e94714  backup12072020-0822pm  9.1 MiB    Done     2020-12-08 04:22:26+00:00
47d1f57ec9014328abb0d8fe56020760  backup12072020-0827pm  9.06 MiB   Done     2020-12-08 04:27:22+00:00
8085723fcbae4aafb24798c1458f4bb7  backup12082020-0250pm  9.04 MiB   Done     2020-12-08 22:50:22+00:00
```

タイムスタンプ列は、バックアップが作成された時点を UTC で示しています。

## <a name="restore-a-backup"></a>バックアップを復元する
このセクションでは、完全復元やポイントインタイム リストアを実行する方法を示します。 完全バックアップを復元するときには、バックアップの内容全体を復元することになります。 ポイントインタイム リストアを実行するときには、指定した時点まで復元することになります。 この時点より後に実行されたトランザクションは一切復元されません。

### <a name="restore-a-full-backup"></a>完全バックアップを復元する
バックアップの内容全体を復元するには、次のコマンドを実行します。
```console
azdata arc postgres backup restore --server-name <target server group name> [--source-server-name <source server group name> --backup-id <backup id>]
or
azdata arc postgres backup restore -sn <target server group name> [-ssn <source server group name> --backup-id <backup id>]
```
<!--To read the general format of restore command, run: azdata arc postgres backup restore --help -->

各値の説明:
- __backup-id__ は、バックアップの一覧表示コマンドで表示される、バックアップの ID です。
これを使用すると、Azure Arc 対応 PostgreSQL Hyperscale サーバー グループを構成するすべてのノードで、分散完全復元が調整されます。 つまり、コーディネーター ノードとワーカー ノードのすべてのデータが復元されます。

#### <a name="examples"></a>例 :

__サーバー グループ postgres01 をそれ自体の上に復元する:__

```console
azdata arc postgres backup restore -sn postgres01 --backup-id d134f51aa87f4044b5fb07cf95cf797f
```

この操作は、PostgreSQL バージョン 12 以降でのみサポートされています。

__サーバー グループ postgres01 を別のサーバー グループ postgres02 に復元する:__

```console
azdata arc postgres backup restore -sn postgres02 -ssn postgres01 --backup-id d134f51aa87f4044b5fb07cf95cf797f
```
この操作は、PostgreSQL バージョン 11 以降のどのバージョンでもサポートされています。 ターゲット サーバー グループは、復元操作の前に作成されている必要があり、同じ構成である必要があります。また、ソース サーバー グループと同じバックアップ PVC を使用している必要があります。

復元操作が完了すると、次のような出力がコマンド ラインに返されます。

```json
{
  "ID": "d134f51aa87f4044b5fb07cf95cf797f",
  "state": "Done"
}
```

> [!NOTE]
> 次のことはまだできません。
> - 名前を指定してバックアップを復元する
> - 復元操作の進行状況を表示する


### <a name="do-a-point-in-time-restore"></a>ポイントインタイム リストアを実行する

サーバー グループを特定の時点まで復元するには、次のコマンドを実行します。
```console
azdata arc postgres backup restore --server-name <target server group name> --source-server-name <source server group name> --time <point in time to restore to>
or
azdata arc postgres backup restore -sn <target server group name> -ssn <source server group name> -t <point in time to restore to>
```

restore コマンドの一般的形式を表示して読むには、`azdata arc postgres backup restore --help` を実行します。

ここで `time` は、復元先の特定の時点です。 タイムスタンプ、または数字とサフィックス (分には `m`、時間には `h`、日には `d`、週には `w`) のいずれかを指定します。 たとえば `1.5h` では、90 分さかのぼります。

#### <a name="examples"></a>例 :
__サーバー グループ postgres01 の、それ自体へのポイントインタイム リストアを実行する:__

サーバー グループの、それ自体へのポイントインタイム リストアを実行することはまだできません。

__サーバー グループ postgres01 から別のサーバー グループ postgres02 への、特定のタイムスタンプまでのポイントインタイム リストアを実行する:__
```console
azdata arc postgres backup restore -sn postgres02 -ssn postgres01 -t "2020-12-08 04:23:48.751326+00"
``` 

この例では、サーバー グループ postgres02 に、2020 年 12 月 8 日 04:23: 48.75 UTC 時点でのサーバー グループ postgres01 の状態が復元されます。 "+00" は、指定した時点のタイムゾーンを示すことに注意してください。 タイムゾーンを指定しないと、復元操作を実行するクライアントのタイムゾーンが使用されます。

次に例を示します。
- `2020-12-08 04:23:48.751326+00` は `2020-12-08 04:23:48.751326` UTC として解釈されます
- 太平洋標準時ゾーン (PST = UTC+08) では、`2020-12-08 04:23:48.751326` は `2020-12-08 12:23:48.751326` UTC として解釈されます。この動作は、PostgreSQL バージョン 11 以降のどのバージョンでもサポートされています。 ターゲット サーバー グループは、復元操作の前に作成されている必要があり、ソース サーバー グループと同じバックアップ PVC を使用している必要があります。


__サーバー グループ postgres01 から別のサーバー グループ postgres02 への、過去の特定の時間までのポイントインタイム リストアを実行する:__
```console
azdata arc postgres backup restore -sn postgres02 -ssn postgres01 -t "22m"
```

この例では、サーバー グループ postgres02 に、22 分前のサーバー グループ postgres01 の状態が復元されます。
この操作は、PostgreSQL バージョン 11 以降のどのバージョンでもサポートされています。 ターゲット サーバー グループは、復元操作の前に作成されている必要があり、ソース サーバー グループと同じバックアップ PVC を使用している必要があります。

> [!NOTE]
> 次のことはまだできません。
> - 復元操作の進行状況を表示する

## <a name="delete-backups"></a>バックアップを削除する

プレビューでは、バックアップの保持を設定することはできません。 ただし、不要なバックアップは手動で削除できます。
バックアップを削除する汎用のコマンドは次のとおりです。

```console
azdata arc postgres backup delete  [--server-name, -sn] {[--name, -n], -id}
```

ここで、
- `--server-name` はユーザーがバックアップを削除するサーバー グループの名前です。
- `--name` は削除するバックアップの名前です。
- `-id` は削除するバックアップの ID です。

> [!NOTE]
> `--name` と `-id` は同時に指定できません。

次に例を示します。

```console
azdata arc postgres backup delete -sn postgres01 -n MyBackup091720200110am
{
  "ID": "5b0481dfc1c94b4cac79dd56a1bb21f4",
  "name": "MyBackup091720200110am",
  "state": "Done"
}
```

前の段落で説明したように、list backup コマンドを実行すると、バックアップの名前と ID を取得できます。

削除コマンドの詳細については、次を実行してください。

```console
azdata arc postgres backup delete --help
```

## <a name="next-steps"></a>次のステップ
- サーバー グループの[スケール アウト (ワーカー ノードの追加)](scale-out-postgresql-hyperscale-server-group.md) について確認する
- サーバー グループの[スケールアップまたはスケール ダウン (メモリ/仮想コアの増減)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md) について確認する
