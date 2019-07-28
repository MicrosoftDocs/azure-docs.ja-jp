---
title: Azure Service Fabric CLI- sfctl mesh deployment | Microsoft Docs
description: Service Fabric CLI- sfctl mesh deployment のコマンドについて説明します。
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
ms.openlocfilehash: e6b484dabd77a142961db2d97242896790fa3d8b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60836960"
---
# <a name="sfctl-mesh-deployment"></a>sfctl mesh deployment
Service Fabric Mesh リソースを作成します。

## <a name="commands"></a>command

|command|説明|
| --- | --- |
| create | Service Fabric Mesh リソースのデプロイを作成します。 |

## <a name="sfctl-mesh-deployment-create"></a>sfctl mesh deployment create
Service Fabric Mesh リソースのデプロイを作成します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --input-yaml-files [必須] | すべての yaml ファイルのコンマ区切りの相対/絶対ファイル パス、または yaml ファイルを含むディレクトリ (再帰的) の相対/絶対パス。 |
| --parameters | yaml ファイルまたはオーバーライドする必要があるパラメーターを含む json オブジェクトに対する相対/絶対パス。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。 |

### <a name="examples"></a>例

yaml ファイルに記述されているパラメーターをオーバーライドすることで、すべてのリソースを統合してクラスターにデプロイします

```
sfctl mesh deployment create --input-yaml-files ./app.yaml,./network.yaml --parameters
./param.yaml
```

yaml ファイルに記述されているパラメーターをオーバーライドすることで、ディレクトリ内のすべてのリソースを統合してクラスターにデプロイします

```
sfctl mesh deployment create --input-yaml-files ./resources --parameters ./param.yaml
```

json オブジェクトとして直接渡されるパラメーターをオーバーライドすることで、ディレクトリ内のすべてのリソースを統合してクラスターにデプロイします

```
sfctl mesh deployment create --input-yaml-files ./resources --parameters "{ 'my_param' :
{'value' : 'my_value'} }"
```


## <a name="next-steps"></a>次の手順
- Service Fabric CLI を[セットアップ](service-fabric-cli.md)します。
- [サンプル スクリプト](/azure/service-fabric/scripts/sfctl-upgrade-application)を使用して、Service Fabric CLI の使用方法を学習します。