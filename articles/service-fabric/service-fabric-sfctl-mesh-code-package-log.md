---
title: Azure Service Fabric CLI- sfctl mesh code-package-log | Microsoft Docs
description: Azure Service Fabric CLI- sfctl mesh code-package-log のコマンドについて説明します。
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
ms.openlocfilehash: e7bc8491071946eaa2e322517e5d36d681a49130
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60837642"
---
# <a name="sfctl-mesh-code-package-log"></a>sfctl mesh code-package-log
特定のサービス レプリカの指定されたコード パッケージのコンテナーのログを取得します。

## <a name="commands"></a>command

|command|説明|
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
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。 |


## <a name="next-steps"></a>次の手順
- Service Fabric CLI を[セットアップ](service-fabric-cli.md)します。
- [サンプル スクリプト](/azure/service-fabric/scripts/sfctl-upgrade-application)を使用して、Service Fabric CLI の使用方法を学習します。