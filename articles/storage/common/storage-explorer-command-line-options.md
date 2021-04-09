---
title: Azure Storage Explorer のコマンド ライン オプション | Microsoft Docs
description: Azure Storage Explorer のスタートアップ コマンドライン オプションのドキュメント
services: storage
author: chuye
ms.service: storage
ms.topic: article
ms.date: 02/24/2021
ms.author: chuye
ms.openlocfilehash: 0d588d85852f798542c5d52658e8dae3b9e57949
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101743717"
---
# <a name="azure-storage-explorer-command-line-options"></a>Azure Storage Explorer のコマンド ライン オプション

Microsoft Azure Storage Explorer には、アプリケーションの起動時に追加できる一連のコマンドライン オプションがあります。 これらのコマンドライン オプションのほとんどは、デバッグまたはトラブルシューティングの目的で使用されます。

## <a name="command-line-options"></a>コマンド ライン オプション
オプション  | 説明
:------- | :-----------
`--debug`/`--prod`  | デバッグ モードまたは運用モードでアプリケーションを起動します。 デバッグ モードでは、ローカルの添付データはアプリケーションのローカル ストレージに格納され、暗号化されません。 一部のリソース ノードの [プロパティ] パネルに、非表示プロパティが表示されます。 ログの詳細レベルは、Storage Explorer の内部セットアップ ロジックを公開するデバッグ メッセージを出力するように設定されます。 既定値は `--prod` です。
`--lang`  | 特定の言語でアプリケーションを起動します。 たとえば、「 `--lang="zh-Hans"` 」のように入力します。
`--disable-gpu` | GPU アクセラレーションを使用せずにアプリケーションを起動します。
`--auto-open-dev-tools` | ブラウザー ウィンドウが表示されたらすぐに開発者ツール ウィンドウが開かれるようにします。 このオプションは、ブラウザー ウィンドウのスタートアップ コード内の特定の行でブレーク ポイントをヒットさせる場合に便利です。
`--verbosity` | Storage Explorer ログの詳細レベルを設定します。 サポートされる詳細レベルには、`debug`、`verbose`、`info`、`warn`、`error`、および `silent` があります。 たとえば、「 `--verbosity=verbose` 」のように入力します。 運用モードで実行する場合、既定の詳細レベルは `info` です。 デバッグ モードで実行する場合、ログ詳細レベルは常に `debug` になります。
`--log-dir` | ログ ファイルを保存するディレクトリを設定します。 たとえば、「 `--log-dir=path_to_a_directory` 」のように入力します。

カスタム コマンドライン オプションを使用して Storage Explorer を開始する例

```shell
./MicrosoftAzureStorageExplorer --lang=en --auto-open-dev-tools
```

> [!NOTE]
> これらのコマンドライン オプションは、新しいバージョンの Storage Explorer で変更される可能性があります。

## <a name="when-to-use-command-line-options"></a>コマンドライン オプションを使用する局面

一部のコマンドライン オプションは、Storage Explorer をカスタマイズするために使用できます。 `--lang` などの、対応するユーザー設定があるオプションについては、 コマンドライン オプションを使用する代わりにユーザー設定を使用することをお勧めします。 

その他のコマンドライン オプションは、デバッグやトラブルシューティングに役立ちます。 Storage Explorer で問題が発生した場合、デバッグ モードで問題を再現すると、調査すべき詳細情報を得るのに役立つことがあります。