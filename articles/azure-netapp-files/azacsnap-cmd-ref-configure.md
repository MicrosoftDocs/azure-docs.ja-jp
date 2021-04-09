---
title: Azure NetApp Files 用の Azure アプリケーション整合性スナップショット ツールを構成する | Microsoft Docs
description: Azure NetApp Files で使用できる Azure アプリケーション整合性スナップショット ツールの構成コマンドを実行するためのガイドを提供します。
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
ms.openlocfilehash: 0875aae8bb9049fc96377c1c98efa7391211d08f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97632192"
---
# <a name="configure-azure-application-consistent-snapshot-tool-preview"></a>Azure アプリケーション整合性スナップショット ツールを構成する (プレビュー)

この記事では、Azure NetApp Files で使用できる Azure アプリケーション整合性スナップショット ツールの構成コマンドを実行するためのガイドを提供します。

## <a name="introduction"></a>はじめに

構成ファイルは、`azacsnap -c configure` コマンドを使用して作成または編集できます。

## <a name="command-options"></a>コマンド オプション

`-c configure` コマンドには、次のオプションがあります

- 新しい構成ファイルを作成するには: `--configuration new`。

- 既存の構成ファイルを編集するには: `--configuration edit`。

- `[--configfile <config filename>]` は、カスタム構成ファイル名を許可する省略可能なパラメーターです。

## <a name="configuration-file-for-snapshot-tools"></a>スナップショット ツールの構成ファイル

構成ファイルを作成するには `azacsnap -c configure --configuration new` を実行します。  既定の構成ファイル名は `azacsnap.json` です。  カスタム ファイル名は、`--configfile=` パラメーターと共に使用できます (例: `--configfile=<customname>.json`)。次の例は、Azure Large Instance の構成用です。

```bash
azacsnap -c configure --configuration new
```

```output
Building new config file
Add comment to config file (blank entry to exit adding comments):This is a new config file for `azacsnap`
Add comment to config file (blank entry to exit adding comments):
Add database to config? (y/n) [n]: y
HANA SID (for example, H80): H80
HANA Instance Number (for example, 00): 00
HANA HDB User Store Key (for example, `hdbuserstore List`): AZACSNAP
HANA Server's Address (hostname or IP address): testing01
Add ANF Storage to database section? (y/n) [n]:
Add HLI Storage to database section? (y/n) [n]: y
Add DATA Volume to HLI Storage section of Database section? (y/n) [n]: y
Storage User Name (for example, clbackup25): clt1h80backup
Storage IP Address (for example, 192.168.1.30): 172.18.18.11
Storage Volume Name (for example, hana_data_h80_testing01_mnt00001_t250_vol): hana_data_h80_testing01_mnt00001_t020_vol
Add DATA Volume to HLI Storage section of Database section? (y/n) [n]:
Add OTHER Volume to HLI Storage section of Database section? (y/n) [n]:
Add HLI Storage to database section? (y/n) [n]:
Add database to config? (y/n) [n]:
Editing configuration complete, writing output to 'azacsnap.json'
```

## <a name="details-of-required-values"></a>必須な値の詳細

次のセクションでは、構成ファイルに必要なさまざまな値について詳しく説明します。

### <a name="sap-hana-values"></a>SAP HANA 値

構成に "*データベース*" を追加する場合は、次の値が必要です。

- **HANA サーバーのアドレス** = SAP HANA サーバーのホスト名または IP アドレス。
- **HANA SID** = SAP HANA のシステム ID。
- **HANA インスタンス番号** = SAP HANA のインスタンス番号。
- **HANA HDB ユーザー ストア キー** = データベース バックアップを実行するアクセス許可が構成されている SAP HANA ユーザー。

- 単一ノード:ノードの IP とホスト名
- STONITH が有効な HSR:ノードの IP とホスト名
- スケールアウト (N+N、N+M):現在のマスター ノードの IP とホスト名
- STONITH が無効な HSR:ノードの IP とホスト名
- 単一ノードの複数 SID:これらの SID をホストしているノードのホスト名と IP

### <a name="azure-large-instance-hli-storage-values"></a>Azure Large Instance (HLI) ストレージの値

*HLI ストレージ* をデータベース セクションに追加する場合は、次の値が必要です。

- **ストレージ ユーザー名** = この値は、ストレージへの SSH 接続を確立するために使用されるユーザー名です。
- **ストレージ IP アドレス** = ストレージ システムのアドレス。
- **ストレージ ボリューム名** = スナップショットするボリューム名。  この値は複数の方法で指定できます。おそらく、最も簡単なのは、次のシェル コマンドを使用する方法です。

    ```bash
    grep nfs /etc/fstab | cut -f2 -d"/" | sort | uniq
    ```

    ```output
    hana_data_p40_soldub41_mnt00001_t020_vol
    hana_log_backups_p40_soldub41_t020_vol
    hana_log_p40_soldub41_mnt00001_t020_vol
    hana_shared_p40_soldub41_t020_vol
    ```

### <a name="azure-netapp-files-anf-storage-values"></a>Azure NetApp Files (ANF) ストレージの値

*ANF ストレージ* をデータベース セクションに追加する場合は、次の値が必要です。

- **サービス プリンシパル認証ファイル名** = Azure NetApp Files ストレージとの通信を構成するときに Cloud Shell に生成される `authfile.json` ファイルです。
- **ANF ストレージ ボリュームの完全なリソース ID** = スナップショットされるボリュームの完全なリソース ID。  これは、以下から取得できます。[Azure portal] –> [ANF] –> [ボリューム] –> [設定/プロパティ] –> [リソース ID]

## <a name="configuration-file-overview-azacsnapjson"></a>構成ファイルの概要 (`azacsnap.json`)

次の例では、`azacsnap.json` が 1 つの SID で構成されています。

パラメーター値は、顧客固有の SAP HANA 環境に設定する必要があります。
**Azure Large Instance** システムの場合、この情報はオンボードの通話または引き継ぎの通話の際に Microsoft サービス管理から提供され、引き継ぎ時に提供される Excel ファイルに記載されています。 この情報を再度要求する場合は、サービス要求を開いてください。

以下はあくまでも一例です。値はすべて適宜更新してください。

```bash
cat azacsnap.json
```

```output
{
  "version": "5.0 Preview",
  "logPath": "./logs",
  "securityPath": "./security",
  "comments": [],
  "database": [
    {
      "hana": {
        "serverAddress": "sapprdhdb80",
        "sid": "H80",
        "instanceNumber": "00",
        "hdbUserStoreName": "SCADMIN",
        "savePointAbortWaitSeconds": 600,
        "hliStorage": [
          {
            "dataVolume": [
              {
                "backupName": "clt1h80backup",
                "ipAddress": "172.18.18.11",
                "volume": "hana_data_h80_azsollabbl20a31_mnt00001_t210_vol"
              },
              {
                "backupName": "clt1h80backup",
                "ipAddress": "172.18.18.11",
                "volume": "hana_shared_h80_azsollabbl20a31_t210_vol"
              }
            ],
            "otherVolume": [
              {
                "backupName": "clt1h80backup",
                "ipAddress": "172.18.18.11",
                "volume": "hana_log_backups_h80_azsollabbl20a31_t210_vol"
              }
            ]
          }
        ],
        "anfStorage": []
      }
    }
  ]
}
```

> [!NOTE]
> DR サイトでバックアップを実行する DR シナリオでは、DR サイトの DR 構成ファイルで構成されている HANA サーバー名 (`DR.json` など) は、運用サーバー名と同じである必要があります。

> [!NOTE]
> Azure Large Instance の場合、ストレージ IP アドレスはサーバー プールと同じサブネットに存在する必要があります。 たとえば、この例のサーバー プールのサブネットは 172 です。 18.  18 .0/24 と割り当てられたストレージ IP は 172.18.18.11 です。

## <a name="next-steps"></a>次のステップ

- [Azure アプリケーション整合性スナップショット ツールをテストする](azacsnap-cmd-ref-test.md)
