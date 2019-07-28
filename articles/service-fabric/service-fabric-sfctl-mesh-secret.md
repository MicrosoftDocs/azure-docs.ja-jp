---
title: Azure Service Fabric CLI- sfctl mesh secret | Microsoft Docs
description: Service Fabric CLI sfctl mesh secret のコマンドについて説明します。
services: service-fabric
documentationcenter: na
author: christina-kang
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
ms.openlocfilehash: acc5862a8794da4fde991025eacfcccf6e16916a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60607680"
---
# <a name="sfctl-mesh-secret"></a>sfctl mesh secret
メッシュ シークレット リソースを取得および削除します。

## <a name="commands"></a>command

|command|説明|
| --- | --- |
| delete | シークレット リソースを削除します。 |
| list | すべてのシークレット リソースを一覧表示します。 |
| show | 指定した名前のシークレット リソースを取得します。 |

## <a name="sfctl-mesh-secret-delete"></a>sfctl mesh secret delete
シークレット リソースを削除します。

指定されたシークレット リソースと、その名前付きの値すべてを削除します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --name -n (必須) | シークレットの名前。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。 |

## <a name="sfctl-mesh-secret-list"></a>sfctl mesh secret list
すべてのシークレット リソースを一覧表示します。

特定のリソース グループ内のすべてのシークレット リソースに関する情報を取得します。 この情報には、シークレットの説明とその他のプロパティが含まれます。

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。 |

## <a name="sfctl-mesh-secret-show"></a>sfctl mesh secret show
指定した名前のシークレット リソースを取得します。

指定した名前のシークレット リソースに関する情報を取得します。 この情報には、シークレットの説明とその他のプロパティが含まれます。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --name -n (必須) | シークレットの名前。 |

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