---
title: Azure Service Fabric CLI- sfctl メッシュ アプリ | Microsoft Docs
description: Service Fabric CLI sfctl メッシュ アプリ コマンドについて説明します。
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: fb812412c7dd07800c1e2231c9472a122ab7d7d4
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58661838"
---
# <a name="sfctl-mesh-app"></a>sfctl mesh app
アプリケーション リソースを取得および削除します。

## <a name="commands"></a>command

|command|説明|
| --- | --- |
| delete | アプリケーション リソースを削除します。 |
| list | アプリケーションのすべてのリソースを一覧表示します。 |
| show | 指定した名前のアプリケーション リソースを取得します。 |

## <a name="sfctl-mesh-app-delete"></a>sfctl mesh app delete
アプリケーション リソースを削除します。

名前で識別されるアプリケーション リソースを削除します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --name -n (必須) | アプリケーションの名前です。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。 |

## <a name="sfctl-mesh-app-list"></a>sfctl mesh app list
アプリケーションのすべてのリソースを一覧表示します。

特定のリソース グループ内のすべてのアプリケーション リソースに関する情報を取得します。 この情報には、アプリケーションの説明とその他のプロパティが含まれます。

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。 |

## <a name="sfctl-mesh-app-show"></a>sfctl mesh app show
指定した名前のアプリケーション リソースを取得します。

指定した名前のアプリケーション リソースに関する情報を取得します。 この情報には、アプリケーションの説明とその他のプロパティが含まれます。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --name -n (必須) | アプリケーションの名前です。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。 |


## <a name="next-steps"></a>次の手順
- Service Fabric CLI を[セットアップ](service-fabric-cli.md)します。
- [サンプル スクリプト](/azure/service-fabric/scripts/sfctl-upgrade-application)を使用して、Service Fabric CLI の使用方法を学習します。