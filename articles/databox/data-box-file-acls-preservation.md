---
title: Azure Data Box を使用したファイルの ACL、属性、およびタイムスタンプの保持
description: SMB による Azure Data Box へのデータのコピー中に保持される ACL、タイムスタンプ、および属性。 Windows および Linux のデータ コピー ツールを使用したメタデータのコピー。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: alkohli
ms.openlocfilehash: e8df77356b6b5b1b40e2abd772e13c2e811413ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91950314"
---
# <a name="preserving-file-acls-attributes-and-timestamps-with-azure-data-box"></a>Azure Data Box を使用したファイルの ACL、属性、およびタイムスタンプの保持

Azure Data Box を使用すると、Azure にデータを送信するときにアクセス制御リスト (ACL)、タイムスタンプ、ファイル属性を保持できます。 この記事では、Azure Files にアップロードするために、サーバー メッセージ ブロック (SMB) を介して Data Box にデータをコピーするときに転送できるメタデータについて説明します。 

Windows および Linux のデータ コピー ツールを使用してメタデータをコピーするための具体的な手順を示します。 Blob Storage にデータを転送する場合、メタデータは保持されません。

この記事では、転送される ACL、タイムスタンプ、ファイル属性を総称して "*メタデータ*" と呼びます。

## <a name="transferred-metadata"></a>転送されるメタデータ

Data Box のデータが Azure Files にアップロードされると、次のメタデータが転送されます。

#### <a name="timestamps"></a>タイムスタンプ

次のタイムスタンプが転送されます。
- CreationTime
- 最終書き込み時刻

次のタイムスタンプは転送されません。
- LastAccessTime
  
#### <a name="file-attributes"></a>ファイル属性

ファイルとディレクトリの両方のファイル属性は、特に記載のない限り転送されます。

次のファイル属性が転送されます。
- FILE_ATTRIBUTE_READONLY (ファイルのみ)
- FILE_ATTRIBUTE_HIDDEN
- FILE_ATTRIBUTE_SYSTEM
- FILE_ATTRIBUTE_DIRECTORY (ディレクトリのみ)
- FILE_ATTRIBUTE_ARCHIVE
- FILE_ATTRIBUTE_TEMPORARY (ファイルのみ)
- FILE_ATTRIBUTE_NO_SCRUB_DATA

次のファイル属性は転送されません。
- FILE_ATTRIBUTE_OFFLINE
- FILE_ATTRIBUTE_NOT_CONTENT_INDEXED
  
ディレクトリの読み取り専用属性は転送されません。

#### <a name="acls"></a>ACL

SMB 経由で Data Box にコピーするディレクトリとファイルのすべての ACL がコピーされ、転送されます。 転送には、随意 ACL (DACL) とシステム ACL (SACL) の両方が含まれます。 Linux では、Windows NT ACL のみが転送されます。

ACL は、ネットワーク ファイル システム (NTS) を介したデータのコピー中や、データ コピー サービスを使用してデータを転送するときは、転送されません。 データ コピー サービスは、共有から直接データを読み取るため、ACL を読み取ることはできません。

データ コピー ツールで ACL がコピーされない場合でも、ディレクトリおよびファイルの既定の ACL は Azure Files に転送されます。 既定の ACL には、Administrator アカウント、SYSTEM アカウント、および Data Box でデータをマウントおよびコピーするために使用された SMB 共有ユーザー アカウントに対するアクセス許可があります。

ACL には、次のプロパティを持つセキュリティ記述子が含まれています。ACL、Owner、Group、SACL。

ACL の転送は既定で有効になっています。 Data Box のローカル Web UI でこの設定を無効にすることができます。 詳しくは、「[ローカル Web UI を使用して Data Box および Data Box Heavy を管理する](./data-box-local-web-ui-admin.md)」をご覧ください。

> [!NOTE]
> ACL が条件付きアクセス制御エントリ (ACE) 文字列を含むファイルはコピーされません。 これは既知の問題です。 この問題を回避するには、共有をマウントしてから、ACL のコピーをサポートするコピー ツールを使用して、これらのファイルを Azure Files 共有に手動でコピーします。

## <a name="copying-data-and-metadata"></a>データとメタデータのコピー

データの ACL、タイムスタンプ、属性を転送するには、次の手順を使用して Data Box にデータをコピーします。 

### <a name="windows-data-copy-tool"></a>Windows データ コピー ツール

SMB 経由で Data Box にデータをコピーするには、`robocopy` などの SMB 互換ファイル コピー ツールを使用します。 次のサンプル コマンドは、すべてのファイルとディレクトリをコピーし、データと共にメタデータを転送します。

`/copyall` または `/dcopy:DAT` の各オプションを使用する場合は、必要なバックアップ オペレーター特権が無効になっていないことを確認してください。 詳しくは、「[ローカル Web UI を使用して Data Box および Data Box Heavy を管理する](./data-box-local-web-ui-admin.md)」をご覧ください。 

```console
robocopy <Source> <Target> * /copyall /e /dcopy:DAT /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /log+:<LogFile>
```

where

|オプション |説明 |
|------------------- | ----- |
|`/copyall` |すべての属性をコピーします。|
|`/e`      |サブディレクトリをコピーします (空のディレクトリを含む)。         |
|`/dcopy:DAT`  |データ、属性、タイムスタンプをコピーします。 注:ディレクトリの `CreationTime` を転送するには、/dcopy:DAT オプションを使用する必要があります。 |
|`/r:3`    |失敗したコピーの 3 回の再試行を指定します。         |
|`/w:60`   |再試行の間の待機時間を 60 秒に指定します。         |
|`/is`     |同じファイルを含めます。         |
|`/nfl`    |ファイル名をログしません。         |
|`/ndl`    |ディレクトリ名をログしません。        |
|`/np`     |コピー操作の進行状況を表示しません。         |
|`/MT:32 or 64`  |32 または 64 スレッドのマルチスレッドを使用します。           |
|`/fft`    |任意のファイル システムのタイムスタンプの細分性を下げます。        |
|`/log+:<LogFile>`  |既存のログ ファイルに出力を追加します。|

これらの `robocopy` パラメーターの詳細については、「[チュートリアル: Azure Data Box に SMB 経由でデータをコピーする](./data-box-deploy-copy-data.md)」をご覧ください

### <a name="linux-data-copy-tool"></a>Linux データ コピー ツール

Linux でのメタデータの転送は、2 段階のプロセスです。 まず、`rsync` などのツールを使用してソース データをコピーします。これによってメタデータはコピーされません。 データをコピーした後、`smbcacls` や `cifsacl` などのツールを使用してメタデータをコピーできます。 

次のサンプル コマンドは、`rsync` を使用してデータをコピーする最初の手順を実行します。 

```console
cp -aR /etc /opt/ 
rsync -avP /etc /opt (-a copies a directory)
```

## <a name="next-steps"></a>次のステップ

- [Azure Data Box に SMB 経由でデータをコピーする](./data-box-deploy-copy-data.md)