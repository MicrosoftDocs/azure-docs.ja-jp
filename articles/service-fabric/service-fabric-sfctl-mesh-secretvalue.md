---
title: Azure Service Fabric CLI- sfctl mesh secretvalue
description: Azure Service Fabric のコマンド ライン インターフェイスである sfctl について説明します。 Service Fabric Mesh のシークレット値リソースを取得および削除するためのコマンドの一覧が含まれます。
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 985fb505aae96f4ebd1ba8aeb61679081f303243
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86245774"
---
# <a name="sfctl-mesh-secretvalue"></a>sfctl mesh secretvalue
メッシュ secretvalue リソースを取得および削除します。

## <a name="commands"></a>コマンド

|コマンド|説明|
| --- | --- |
| delete | 名前付きシークレット リソースの指定された値を削除します。 |
| list | 指定されたシークレット リソースのすべての値の名前を一覧表示します。 |
| show | シークレット リソースの指定された値を一覧表示します。 |

## <a name="sfctl-mesh-secretvalue-delete"></a>sfctl mesh secretvalue delete
名前付きシークレット リソースの指定された値を削除します。

名前で識別されるシークレット値リソースを削除します。 リソースの名前は、通常、その値に関連付けられているバージョンです。 指定した値が使用中の場合、削除は失敗します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --secret-name -n [必須] | シークレット リソースの名前。 |
| --version -v     [必須] | シークレット バージョンの名前。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。 |

## <a name="sfctl-mesh-secretvalue-list"></a>sfctl mesh secretvalue list
指定されたシークレット リソースのすべての値の名前を一覧表示します。

指定されたシークレット リソースのすべてのシークレット値リソースに関する情報を取得します。 この情報には、実際の値ではなく、シークレット値リソースの名前が含まれています。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --secret-name -n [必須] | シークレット リソースの名前。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。 |

## <a name="sfctl-mesh-secretvalue-show"></a>sfctl mesh secretvalue show
シークレット リソースの指定された値を一覧表示します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --secret-name -n [必須] | シークレット リソースの名前。 |
| --version -v     [必須] | シークレット バージョンの名前。 |
| --show-value | シークレット バージョンの実際の値を表示します。 |

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
