---
title: Azure Service Fabric CLI- sfctl sa-cluster | Microsoft Docs
description: Service Fabric CLI sfctl standalone cluster のコマンドについて説明します。
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
ms.openlocfilehash: a652439729e538b3ce2545ab3b09284e6645ce9d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60556391"
---
# <a name="sfctl-sa-cluster"></a>sfctl sa-cluster
スタンドアロンの Service Fabric クラスターを管理します。

## <a name="commands"></a>command

|command|説明|
| --- | --- |
| config | Service Fabric スタンドアロン クラスターの構成を取得します。 |
| config-upgrade | Service Fabric スタンドアロン クラスターの構成のアップグレードを開始します。 |
| upgrade-status | Service Fabric スタンドアロン クラスターのクラスター構成アップグレードの状態を取得します。 |

## <a name="sfctl-sa-cluster-config"></a>sfctl sa-cluster config
Service Fabric スタンドアロン クラスターの構成を取得します。

クラスター構成には、クラスターでのさまざまなノードの種類、セキュリティの構成、障害、アップグレード ドメイン トポロジなどを含む、クラスターのプロパティが含まれています。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --configuration-api-version [必須] | スタンドアロン クラスター json 構成の API バージョン。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。 |

## <a name="sfctl-sa-cluster-config-upgrade"></a>sfctl sa-cluster config-upgrade
Service Fabric スタンドアロン クラスターの構成のアップグレードを開始します。

指定された構成アップグレード パラメーターを検証し、パラメーターが有効であれば、クラスター構成のアップグレードを開始します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --cluster-config            [必須] | クラスター構成。 |
| --application-health-policies | アプリケーションの種類名と異常の最大パーセンテージのペアの JSON でエンコードされたディクショナリ。このパーセンテージを超えるとエラーが発生します。 |
| --delta-unhealthy-nodes | アップグレードの間のデルタ正常性低下の最大許容パーセンテージ。 使用可能な値は 0 から 100 までの整数値です。 |
| --health-check-retry | アプリケーションまたはクラスターが正常ではない場合に正常性チェックを実行する間隔。  既定値\: PT0H0M0S。 |
| --health-check-stable | アプリケーションまたはクラスターが正常な状態である必要がある時間。この時間を超えると、アップグレードが次のアップグレード ドメインに進みます。  既定値\: PT0H0M0S。 <br><br> 最初に、ISO 8601 の期間を表す文字列として解釈されます。 それが失敗した場合、ミリ秒単位の合計数を表す数値として解釈されます。 |
| --health-check-wait | アップグレード ドメインの完了後、正常性チェック プロセスを開始するまでの、待機時間の長さ。  既定値\: PT0H0M0S。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |
| --unhealthy-applications | アップグレードの間の異常なアプリケーションの最大許容パーセンテージ。 使用可能な値は 0 から 100 までの整数値です。 |
| --unhealthy-nodes | アップグレードの間の異常なノードの最大許容パーセンテージ。 使用可能な値は 0 から 100 までの整数値です。 |
| --upgrade-domain-delta-unhealthy-nodes | アップグレードの間のアップグレード ドメイン デルタ正常性低下の最大許容パーセンテージ。 使用可能な値は 0 から 100 までの整数値です。 |
| --upgrade-domain-timeout | 各ドメインがアップグレードを完了する必要がある時間。この時間を超えると、FailureAction が実行されます。  既定値\: PT0H0M0S。 <br><br> 最初に、ISO 8601 の期間を表す文字列として解釈されます。 それが失敗した場合、ミリ秒単位の合計数を表す数値として解釈されます。 |
| --upgrade-timeout | アップグレード全体を完了する必要がある時間。この時間を超えると、FailureAction が実行されます。  既定値\: PT0H0M0S。 <br><br> 最初に、ISO 8601 の期間を表す文字列として解釈されます。 それが失敗した場合、ミリ秒単位の合計数を表す数値として解釈されます。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。 |

### <a name="examples"></a>例

クラスター構成の更新を開始します

```
sfctl sa-cluster config-upgrade --cluster-config <YOUR CLUSTER CONFIG> --application-health-
policies "{"fabric:/System":{"ConsiderWarningAsError":true}}"
```

## <a name="sfctl-sa-cluster-upgrade-status"></a>sfctl sa-cluster upgrade-status
Service Fabric スタンドアロン クラスターのクラスター構成アップグレードの状態を取得します。

Service Fabric スタンドアロン クラスターのクラスター構成アップグレードの状態の詳細を取得します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

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