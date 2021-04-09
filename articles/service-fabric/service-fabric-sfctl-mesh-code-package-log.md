---
title: Azure Service Fabric CLI- sfctl mesh code-package-log
description: Azure Service Fabric のコマンド ライン インターフェイスである sfctl について説明します。 指定したコード パッケージのログを取得するためのコマンドの一覧が含まれています。
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 9ac1d85a1a498f9f6fcd0a03f8f819d1cdfcac33
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86257302"
---
# <a name="sfctl-mesh-code-package-log"></a>sfctl mesh code-package-log
特定のサービス レプリカの指定されたコード パッケージのコンテナーのログを取得します。

## <a name="commands"></a>コマンド

|コマンド|説明|
| --- | --- |
| get | コンテナーからログを取得します。 |

## <a name="sfctl-mesh-code-package-log-get"></a>sfctl mesh code-package-log get
コンテナーからログを取得します。

サービス レプリカの指定されたコード パッケージのコンテナーのログを取得します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --app-name --application-name [必須] | アプリケーションの名前です。 |
| --code-package-name           [必須] | サービスのコード パッケージの名前。 |
| --replica-name                [必須] | Service Fabric レプリカ名。 |
| --service-name                [必須] | サービスの名前。 |
| --tail | ログの末尾から表示する行数。 既定値は 100 です。 ログ全体を表示する場合は "all" です。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。 |


## <a name="next-steps"></a>次のステップ
- Service Fabric CLI を[セットアップ](service-fabric-cli.md)します。
- [サンプル スクリプト](./scripts/sfctl-upgrade-application.md)を使用して、Service Fabric CLI の使用方法を学習します。
