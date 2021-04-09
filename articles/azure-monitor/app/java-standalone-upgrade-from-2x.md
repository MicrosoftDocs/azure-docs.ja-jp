---
title: 2\.x からのアップグレード - Azure Monitor Application Insights Java
description: Azure Monitor Application Insights Java 2.x からのアップグレード
ms.topic: conceptual
ms.date: 11/25/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 6e1c7e15ff77fd75ff2fb70a6741ea2dd9a4cab8
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102040245"
---
# <a name="upgrading-from-application-insights-java-2x-sdk"></a>Application Insights Java 2.x SDK からのアップグレード

アプリケーションで Application Insights Java 2.x SDK を既に使用している場合は、削除する必要はありません。
Java 3.0 エージェントによって、2.x SDK を介して送信しているカスタム テレメトリの検出、キャプチャ、関連付けが行われます。一方で、テレメトリの重複を防止するために、2.x SDK によって実行される自動収集が抑制されます。

Application Insights 2.x エージェントを使用していた場合は、2.x エージェントを指す `-javaagent:` JVM 引数を削除する必要があります。

このドキュメントの残りの部分では、2.x から 3.0 にアップグレードするときに発生する可能性がある制限事項および変更点と、役に立つ回避策について説明します。

## <a name="telemetryinitializers-and-telemetryprocessors"></a>TelemetryInitializer と TelemetryProcessor

3\.0 エージェントを使用している場合、2.x SDK TelemetryInitializers と TelemetryProcessors は実行されません。
以前にこれらを必要としていたユース ケースの多くは、3.0 で[カスタム ディメンション](./java-standalone-config.md#custom-dimensions)を構成するか、[テレメトリ プロセッサ](./java-standalone-telemetry-processors.md)を構成することによって解決できます。

## <a name="multiple-applications-in-a-single-jvm"></a>1 つの JVM 内の複数のアプリケーション

現在、3.0 では、実行中のプロセスあたり 1 つの[接続文字列とロール名](./java-standalone-config.md#connection-string-and-role-name)のみがサポートされます。 特に、異なる接続文字列または異なるロール名を使用して、同じ Tomcat デプロイ内に複数の Tomcat Web アプリを作成することはできません。

## <a name="operation-names"></a>操作名

3\.0 では操作名が変更され、Application Insights ポータル U/X で全般的により適切な集計ビューを提供するようになりました。

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-3-0.png" alt-text="3.0 の操作名":::

ただし、アプリケーションによっては、以前の操作名によって提供されていた U/X の集計ビューを使用することをお勧めします。その場合は、3.0 の[テレメトリ プロセッサ](./java-standalone-telemetry-processors.md) (プレビュー) 機能を使用して、以前の動作をレプリケートできます。

### <a name="prefix-the-operation-name-with-the-http-method-get-post-etc"></a>操作名の前に http メソッド (`GET`、`POST` など) を付ける

2\.x SDK では、操作名の前に http メソッド (`GET`、`POST` など) が付けられていました。

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-prefixed-by-http-method.png" alt-text="http メソッドが前に付けられた操作名":::

次のスニペットでは、以前の動作をレプリケートするために組み合わせる 3 つのテレメトリ プロセッサを構成します。
テレメトリ プロセッサにより、次の操作が順番に実行されます。

1. 最初のテレメトリ プロセッサは、スパン プロセッサ (種類は `span`) であり、`requests` と `dependencies` に適用されます。

   これは、`http.method` という名前の属性を持ち、`/` で始まるスパン名を持つすべてのスパンと一致します。

   次に、そのスパン名が、`tempName` という名前の属性に抽出されます。

2. 2 番目のテレメトリ プロセッサも、スパン プロセッサです。

   これは、`tempName` という名前の属性を持つすべてのスパンと一致します。

   次に、スペースで区切られた 2 つの属性 `http.method` と `tempName` を連結することで、スパン名が更新されます。

3. 最後のテレメトリ プロセッサは、属性プロセッサ (種類は `attribute`) であり、属性 (現在は `requests`、`dependencies`、`traces`) を持つすべてのテレメトリに適用されます。

   これは、`tempName` という名前の属性を持つすべてのテレメトリと一致します。

   次に、カスタム ディメンションとして報告されないように、`tempName` という名前の属性が削除されます。

```
{
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "regexp",
          "attributes": [
            { "key": "http.method", "value": "" }
          ],
          "spanNames": [ "^/" ]
        },
        "name": {
          "toAttributes": {
            "rules": [ "^(?<tempName>.*)$" ]
          }
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempName" }
          ]
        },
        "name": {
          "fromAttributes": [ "http.method", "tempName" ],
          "separator": " "
        }
      },
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempName" }
          ]
        },
        "actions": [
          { "key": "tempName", "action": "delete" }
        ]
      }
    ]
  }
}
```

### <a name="set-the-operation-name-to-the-full-path"></a>操作名を完全なパスに設定する

また、2.x SDK では、操作名に完全なパスが含まれている場合がありました。

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-with-full-path.png" alt-text="完全なパスを持つ操作名":::

次のスニペットでは、以前の動作をレプリケートするために組み合わせる 4 つのテレメトリ プロセッサを構成します。
テレメトリ プロセッサにより、次の操作が順番に実行されます。

1. 最初のテレメトリ プロセッサは、スパン プロセッサ (種類は `span`) であり、`requests` と `dependencies` に適用されます。

   これは、`http.url` という名前の属性を持つすべてのスパンと一致します。

   次に、`http.url` 属性値を使用してスパン名を更新します。

   これが末尾で、`http.url` は `http://host:port/path` のようになり、必要なのはおそらく `/path` 部分のみです。

2. 2 番目のテレメトリ プロセッサも、スパン プロセッサです。

   これは、`http.url` という名前の属性を持つすべてのスパン (つまり、最初のプロセッサが一致するすべてのスパン) と一致します。

   次に、スパン名のパス部分が、`tempName` という名前の属性に抽出されます。

3. 3 番目のテレメトリ プロセッサも、スパン プロセッサです。

   これは、`tempPath` という名前の属性を持つすべてのスパンと一致します。

   次に、`tempPath` 属性からスパン名が更新されます。

4. 最後のテレメトリ プロセッサは、属性プロセッサ (種類は `attribute`) であり、属性 (現在は `requests`、`dependencies`、`traces`) を持つすべてのテレメトリに適用されます。

   これは、`tempPath` という名前の属性を持つすべてのテレメトリと一致します。

   次に、カスタム ディメンションとして報告されないように、`tempPath` という名前の属性が削除されます。

```
{
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "http.url" }
          ]
        },
        "name": {
          "fromAttributes": [ "http.url" ]
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "http.url" }
          ]
        },
        "name": {
          "toAttributes": {
            "rules": [ "https?://[^/]+(?<tempPath>/[^?]*)" ]
          }
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempPath" }
          ]
        },
        "name": {
          "fromAttributes": [ "tempPath" ]
        }
      },
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempPath" }
          ]
        },
        "actions": [
          { "key": "tempPath", "action": "delete" }
        ]
      }
    ]
  }
}
```

## <a name="dependency-names"></a>依存関係名

3\.0 では依存関係名も変更され、ここでも Application Insights ポータル U/X で全般的により適切な集計ビューを提供するようになりました。

ここでも、アプリケーションによっては、以前の依存関係名によって提供されていた U/X の集計ビューを使用することをお勧めします。その場合は、上記と同様の手法を使用して、以前の動作をレプリケートできます。

## <a name="operation-name-on-dependencies"></a>依存関係の操作名

以前の 2.x SDK では、依存関係テレメトリに要求テレメトリからの操作名も設定されていました。
Application Insights Java 3.0 では、依存関係テレメトリに操作名が設定されなくなりました。
依存関係テレメトリの親である要求の操作名を表示するには、たとえば、ログ (Kusto) クエリを記述して依存関係テーブルから要求テーブルに結合します。

```
let start = datetime('...');
let end = datetime('...');
dependencies
| where timestamp between (start .. end)
| project timestamp, type, name, operation_Id
| join (requests
    | where timestamp between (start .. end)
    | project operation_Name, operation_Id)
    on $left.operation_Id == $right.operation_Id
| summarize count() by operation_Name, type, name
```

## <a name="2x-sdk-logging-appenders"></a>2.x SDK ロギング アペンダー

3\.0 エージェントでは、ロギング アペンダーを構成する必要なく、[ログが自動収集](./java-standalone-config.md#auto-collected-logging)されます。
2\. x SDK ロギング アペンダーを使用している場合は、3.0 エージェントによって抑制されるため、これらを削除できます。

## <a name="2x-sdk-spring-boot-starter"></a>2.x SDK Spring Boot スターター

3\.0 Spring Boot スターターはありません。
3\.0 エージェントの設定と構成は、Spring Boot を使用しているかどうかにかかわらず、同じ[簡単な手順](./java-in-process-agent.md#quickstart)に従います。

2\.x SDK Spring Boot スターターからアップグレードする場合、クラウド ロールの既定名は `spring.application.name` にならないことに注意してください。
Json 構成または環境変数を使用して3.0 でクラウド ロール名を設定する方法については、[3.0 構成ドキュメント](./java-standalone-config.md#cloud-role-name)を参照してください。
