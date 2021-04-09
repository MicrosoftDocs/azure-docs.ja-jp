---
title: azcopy load clfs | Microsoft Docs
titleSuffix: Azure Storage
description: この記事では、azcopy load clfs コマンドに関する参照情報を提供します。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: b36ea25180c31fef199aaacb10e46b3caa20f807
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98878377"
---
# <a name="azcopy-load-clfs"></a>azcopy load clfs

ローカル データをコンテナーに転送し、Microsoft の Avere Cloud FileSystem (CLFS) 形式で格納します。

## <a name="synopsis"></a>概要

この load コマンドでは、データが Azure Blob Storage コンテナーにコピーされ、Microsoft の Avere Cloud FileSystem (CLFS) 形式で格納されます。 Azure HPC Cache と Avere vFXT for Azure 製品では、独自の CLFS 形式が使用されています。

このコマンドを利用するには、pip3 install clfsload~=1.0.23 を使用して必要な拡張機能をインストールします。 パスに CLFSLoad.py が含まれていることを確認します。 この手順の詳細については、[https://aka.ms/azcopy/clfs](https://aka.ms/azcopy/clfs) をご覧ください。

このコマンドは、特定の Microsoft ハイパフォーマンス コンピューティング キャッシュ製品で使用するために、既存のデータをクラウド ストレージに移動するための単純なオプションです。 

これらの製品では、データを管理するために独自のクラウド ファイルシステム形式が使用されるため、ネイティブ コピー コマンドを使用してデータを読み込むことはできません。 

代わりに、キャッシュ製品自体を使用するか、この load コマンドを使用して、適切な独自形式でデータを読み込む必要があります。
このコマンドでは、キャッシュを使用せずにデータを転送できます。 たとえば、キャッシュの負荷を増加させずに、ストレージに事前入力したり、ワーキング セットにファイルを追加したりできます。

ターゲットは、空の Azure Storage コンテナーです。 転送が完了したら、Azure HPC Cache インスタンスまたは Avere vFXT for Azure クラスターでターゲット コンテナーを使用できます。

> [!NOTE] 
> これは、この load コマンドのプレビュー リリースです。 問題がありましたら、AzCopy Github リポジトリに報告してください。

```
azcopy load clfs [local dir] [container URL] [flags]
```

## <a name="related-conceptual-articles"></a>関連する概念に関する記事

- [AzCopy を使ってみる](storage-use-azcopy-v10.md)
- [AzCopy と Blob Storage でデータを転送する](./storage-use-azcopy-v10.md#transfer-data)
- [AzCopy とファイル ストレージでデータを転送する](storage-use-azcopy-files.md)
- [AzCopy の構成、最適化、トラブルシューティング](storage-use-azcopy-configure.md)

## <a name="examples"></a>例

ディレクトリ全体を CLFS 形式で SAS を含むコンテナーに読み込みます。

```azcopy
azcopy load clfs "/path/to/dir" "https://[account].blob.core.windows.net/[container]?[SAS]" --state-path="/path/to/state/path"
```

## <a name="options"></a>Options

**--compression-type** string   転送で使用する圧縮の種類を指定します。 使用できる値は、`DISABLED`、`LZ4` です。 (既定値は `LZ4`)

**--help**    `azcopy load clfs` コマンドのヘルプです。

**--log-level** string   ログ ファイルのログの詳細度を定義します。使用できるレベルは、`DEBUG`、`INFO`、`WARNING`、`ERROR` です。 (既定値は `INFO`)

**--max-errors** uint32   許容される転送エラーの最大数を指定します。 十分なエラーが発生すると、ジョブはただちに停止されます。

**--new-session**   追跡情報が `--state-path` に保持される既存のジョブを続行するのではなく、新しいジョブを開始します。 (既定値は true)

**--preserve-hardlinks**    ハード リンク関係を保持します。

**--state-path** string   ジョブの状態を追跡するためのローカル ディレクトリのパスが必要です。 このパスには、ジョブを再開するための既存のディレクトリを指定する必要があります。 新しいジョブの場合は、空にする必要があります。

## <a name="options-inherited-from-parent-commands"></a>親コマンドから継承されるオプション

|オプション|説明|
|---|---|
|--cap-mbps float|転送速度の上限を設定します (メガビット/秒)。 瞬間的なスループットは、上限と若干異なる場合があります。 このオプションを 0 に設定した場合や省略した場合、スループットは制限されません。|
|--output-type string|コマンドの出力形式。 選択肢には、text、json などがあります。 既定値は "text" です。|
|--trusted-microsoft-suffixes string   | Azure Active Directory ログイン トークンを送信できる追加のドメイン サフィックスを指定します。  既定値は " *.core.windows.net;* .core.chinacloudapi.cn; *.core.cloudapi.de;* .core.usgovcloudapi.net" です。 ここに記載されているすべてが既定値に追加されます。 セキュリティのために、Microsoft Azure のドメインのみをここに入力してください。 複数のエンティティは、セミコロンで区切ります。|

## <a name="see-also"></a>関連項目

- [azcopy](storage-ref-azcopy.md)
