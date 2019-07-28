---
title: Azure Service Fabric CLI- sfctl mesh service | Microsoft Docs
description: Service Fabric CLI sfctl mesh service のコマンドについて説明します。
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
ms.openlocfilehash: e8b735780f4ed3402845d9d401f8e37701b9a1a6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60836928"
---
# <a name="sfctl-mesh-service"></a>sfctl mesh service
サービスの詳細を取得し、アプリケーション リソースのサービスを一覧表示します。

## <a name="commands"></a>command

|command|説明|
| --- | --- |
| list | すべてのサービスのリソースを一覧表示します。 |
| show | 指定した名前のサービスのリソースを取得します。 |

## <a name="sfctl-mesh-service-list"></a>sfctl mesh service list
すべてのサービスのリソースを一覧表示します。

アプリケーション リソースのすべてのサービスに関する情報を取得します。 この情報には、サービスの説明とその他のプロパティが含まれます。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --app-name --application-name [必須] | アプリケーションの名前です。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。 |

## <a name="sfctl-mesh-service-show"></a>sfctl mesh service show
指定した名前のサービスのリソースを取得します。

指定した名前のサービス リソースに関する情報を取得します。 この情報には、サービスの説明とその他のプロパティが含まれます。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --app-name --application-name [必須] | アプリケーションの名前です。 |
| --name -n                     [必須] | サービスの名前。 |

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