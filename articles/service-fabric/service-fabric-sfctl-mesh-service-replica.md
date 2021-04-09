---
title: Azure Service Fabric CLI- sfctl mesh service-replica
description: Azure Service Fabric のコマンド ライン インターフェイスである sfctl について説明します。 アプリケーション リソースに対するレプリカの詳細を取得するためのコマンドの一覧が含まれます。
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: cbfdba30663e2aa531ab1db955b0e035a0588709
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86245723"
---
# <a name="sfctl-mesh-service-replica"></a>sfctl mesh service-replica
アプリケーション リソース内にあるレプリカの詳細を取得し、指定されたサービスのレプリカを一覧表示します。

## <a name="commands"></a>コマンド

|コマンド|説明|
| --- | --- |
| list | サービスのすべてのレプリカを一覧表示します。 |
| show | アプリケーションのサービスの指定されたレプリカを取得します。 |

## <a name="sfctl-mesh-service-replica-list"></a>sfctl mesh service-replica list
サービスのすべてのレプリカを一覧表示します。

サービスのすべてのレプリカに関する情報を取得します。 この情報には、サービス レプリカの説明とその他のプロパティが含まれます。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --app-name --application-name [必須] | アプリケーションの名前です。 |
| --service-name                [必須] | サービスの名前。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。 |

## <a name="sfctl-mesh-service-replica-show"></a>sfctl mesh service-replica show
アプリケーションのサービスの指定されたレプリカを取得します。

指定した名前のサービス レプリカに関する情報を取得します。 この情報には、サービス レプリカの説明とその他のプロパティが含まれます。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --app-name --application-name [必須] | アプリケーションの名前です。 |
| --name -n                     [必須] | サービス レプリカの名前。 |
| --service-name                [必須] | サービスの名前。 |

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
