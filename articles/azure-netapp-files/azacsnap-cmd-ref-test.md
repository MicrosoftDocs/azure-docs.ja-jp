---
title: Azure NetApp Files 用の Azure アプリケーション整合性スナップショット ツールをテストする | Microsoft Docs
description: Azure NetApp Files で使用できる Azure アプリケーション整合性スナップショット ツールのテスト コマンドを実行する方法について説明します。
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
ms.openlocfilehash: 03ffa93a71ded40e033f2068ea23fc6b994a5bbb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97632159"
---
# <a name="test-azure-application-consistent-snapshot-tool-preview"></a>Azure アプリケーション整合性スナップショット ツールをテストする (プレビュー)

この記事では、Azure NetApp Files で使用できる Azure アプリケーション整合性スナップショット ツールのテスト コマンドを実行する方法について説明します。

## <a name="introduction"></a>はじめに

構成のテストは、`azacsnap -c test` コマンドを実行して行います。

## <a name="command-options"></a>コマンド オプション

`-c test` コマンドには、次のオプションがあります。

- `--test hana` SAP HANA インスタンスへの接続がテストされます。

- `--test storage` 基になるストレージ インターフェイスとの通信がテストされます。構成されているすべての `data` ボリュームで一時的なストレージ スナップショットが作成された後、それらは削除されます。 

- `--test all` `hana` テストと `storage` テストの両方が順番に実行されます。

- `[--configfile <config filename>]` は省略可能なパラメーターであり、カスタム構成ファイル名を使用できます。

## <a name="check-connectivity-with-sap-hana-azacsnap--c-test---test-hana"></a>SAP HANA `azacsnap -c test --test hana` との接続を確認する

このコマンドを実行して、構成ファイル内のすべての HANA インスタンスについて、HANA 接続性を確認します。 HDBuserstore を使用して SYSTEMDB に接続し、SID 情報をフェッチします。

SSL の場合、このコマンドを実行すると、省略可能な次の引数を取得できます。

- `--ssl=` によって、データベースとの暗号化された接続が強制され、`openssl` または `commoncrypto` のいずれかの SAP HANA との通信に使用する暗号化方法が定義されます。 定義されている場合、このコマンドは、同じディレクトリ内で 2 つのファイルを検索することを想定しています。これらのファイルには、対応する SID にちなんだ名前を付ける必要があります。 「[SAP HANA との通信での SSL の使用](azacsnap-installation.md#using-ssl-for-communication-with-sap-hana)」を参照してください。

### <a name="output-of-the-azacsnap--c-test---test-hana-command"></a>`azacsnap -c test --test hana` コマンドの出力

```bash
azacsnap -c test --test hana
```

```output
BEGIN : Test process started for 'hana'
BEGIN : SAP HANA tests
PASSED: Successful connectivity to HANA version 2.00.032.00.1533114046
END   : Test process complete for 'hana'
```

## <a name="check-connectivity-with-storage-azacsnap--c-test---test-storage"></a>ストレージ `azacsnap -c test --test storage` との接続を確認する

`azacsnap` コマンドを実行して、構成されたすべてのデータ ボリュームのスナップショットを取得して、各 SAP HANA インスタンスのボリュームに適切にアクセスできることを確認します。 各ファイル システムのスナップショット アクセスを確認するために、一時スナップショットが各データ ボリュームに対して作成され、その後削除されます。

### <a name="output-of-the-azacsnap--c-test---test-storage-command"></a>`azacsnap -c test --test storage` コマンドの出力

```bash
azacsnap -c test --test storage
```

```output
BEGIN : Test process started for 'storage'
BEGIN : Storage test snapshots on 'data' volumes
BEGIN : 2 task(s) to Test Snapshots for Storage Volume Type 'data'
PASSED: Task#2/2 Storage test successful for Volume
PASSED: Task#1/2 Storage test successful for Volume
END   : Storage tests complete
END   : Test process complete for 'storage'
```

> [!NOTE]
> Azure Large Instance の場合、`azacsnap -c test --test storage` コマンドを実行して、ストレージの世代と HLI SKU を推定します。  この情報に基づいて、"ブート" スナップショットの構成に関するガイダンスが提供されます (`Action:` 出力で始まる行を参照してください)。

```output
SID1   : Generation 4
Storage: ams07-a700s-saphan-1-01v250-client25-nprod
HLI SKU: S96
Action : Configure the 'boot' snapshots on ALL the servers.
```

## <a name="next-steps"></a>次のステップ

- [Azure アプリケーション整合性スナップショットツールを使用したスナップショット バックアップ](azacsnap-cmd-ref-backup.md)
