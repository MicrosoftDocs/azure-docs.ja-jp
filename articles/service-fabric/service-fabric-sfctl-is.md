---
title: Azure Service Fabric CLI- sfctl is | Microsoft Docs
description: Service Fabric CLI sfctl is のコマンドについて説明します。
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
ms.openlocfilehash: 2039dd9222809d2c05aaeaf01f9d38c51f3b3797
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60837319"
---
# <a name="sfctl-is"></a>sfctl is
クエリを実行し、インフラストラクチャ サービスにコマンドを送信します。

## <a name="commands"></a>command

|command|説明|
| --- | --- |
| command | 特定の Infrastructure Service インスタンスで管理コマンドを呼び出します。 |
| query | 特定の Infrastructure Service インスタンスで読み取り専用クエリを呼び出します。 |

## <a name="sfctl-is-command"></a>sfctl is command
特定の Infrastructure Service インスタンスで管理コマンドを呼び出します。

構成済みの Infrastructure Service のインスタンスが 1 つ以上あるクラスターの場合、この API を使用して、Infrastructure Service の特定のインスタンスにインフラストラクチャ固有のコマンドを送信することができます。 使用可能なコマンドと、対応する応答の形式は、クラスターが実行されているインフラストラクチャによって異なります。 この API は、Service Fabric プラットフォームをサポートしています。コードから直接使用するものではありません。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --command [必須] | 呼び出されるコマンドのテキスト。 コマンドの内容は、インフラストラクチャに固有です。 |
| --service-id | Infrastructure Service の ID。 <br><br> これは "fabric\:" URI スキームのない Infrastructure Service の完全な名前です。 このパラメーターは、実行している Infrastructure Service のインスタンスを複数持つクラスターにのみ必要です。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 完全なデバッグ ログには --debug を使用します。 |

## <a name="sfctl-is-query"></a>sfctl is query
特定の Infrastructure Service インスタンスで読み取り専用クエリを呼び出します。

構成済みの Infrastructure Service のインスタンスが 1 つ以上あるクラスターの場合、この API を使用して、Infrastructure Service の特定のインスタンスにインフラストラクチャ固有のクエリを送信することができます。 使用可能なコマンドと、対応する応答の形式は、クラスターが実行されているインフラストラクチャによって異なります。 この API は、Service Fabric プラットフォームをサポートしています。コードから直接使用するものではありません。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --command [必須] | 呼び出されるコマンドのテキスト。 コマンドの内容は、インフラストラクチャに固有です。 |
| --service-id | Infrastructure Service の ID。 <br><br> これは "fabric\:" URI スキームのない Infrastructure Service の完全な名前です。 このパラメーターは、実行している Infrastructure Service のインスタンスを複数持つクラスターにのみ必要です。 |
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