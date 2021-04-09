---
title: テレメトリ プロセッサ (プレビュー) - Azure Monitor Application Insights for Java
description: Azure Monitor Application Insights for Java でテレメトリ プロセッサを構成する方法について説明します。
ms.topic: conceptual
ms.date: 10/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: 35e53454e5b2c6265082bbedb4a8b60e82df7191
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101734572"
---
# <a name="telemetry-processors-preview---azure-monitor-application-insights-for-java"></a>テレメトリ プロセッサ (プレビュー) - Azure Monitor Application Insights for Java

> [!NOTE]
> テレメトリ プロセッサ機能はプレビュー段階です。

Application Insights の Java 3.0 エージェントでは、テレメトリ データを、データがエクスポートされる前に処理することができます。

テレメトリ プロセッサのユース ケースを次にいくつか示します。
 * 機密データを作成する。
 * 条件付きでカスタム ディメンションを追加する。
 * スパン名を更新する。これは、Azure portal で同様のテレメトリを集計するために使用されます。
 * インジェスト コストを制御するためにスパン属性を削除する。

## <a name="terminology"></a>用語

テレメトリ プロセッサについて学習する前に、"*スパン*" という用語を理解しておく必要があります。 スパンは、次を表す一般的な用語です。

* 着信要求。
* 送信依存関係 (別のサービスへのリモート呼び出しなど)。
* プロセス内の依存関係 (サービスのサブコンポーネントによって実行される操作など)。

テレメトリ プロセッサに関しては、次のスパンのコンポーネントが重要です。

* 名前
* 属性

スパン名は、Azure portal における要求と依存関係の主要な表示です。 スパン属性は、特定の要求または依存関係の、標準とカスタムの両方のプロパティを表します。

## <a name="telemetry-processor-types"></a>テレメトリ プロセッサの種類

現在、テレメトリ プロセッサには、属性プロセッサとスパン プロセッサの 2 種類があります。

属性プロセッサでは、属性を挿入、更新、削除、またはハッシュすることができます。
正規表現を使用して、既存の属性から 1 つ以上の新しい属性を抽出することもできます。

スパン プロセッサでは、テレメトリ名を更新することができます。
正規表現を使用して、スパン名から 1 つ以上の新しい属性を抽出することもできます。

> [!NOTE]
> 現在、テレメトリ プロセッサによって処理されるのは、文字列型の属性のみです。 ブール値または数値型の属性は処理されません。

## <a name="getting-started"></a>作業の開始

開始するには、*applicationinsights.json* という名前の構成ファイルを作成します。 これを、*applicationinsights-agent-\*.jar* と同じディレクトリに保存します。 次に示すテンプレートを使用します。

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        ...
      },
      {
        "type": "attribute",
        ...
      },
      {
        "type": "span",
        ...
      }
    ]
  }
}
```

## <a name="include-criteria-and-exclude-criteria"></a>include 条件と exclude 条件

属性プロセッサとスパン プロセッサの両方で、オプションの `include` および `exclude` 条件がサポートされています。
プロセッサが適用されるのは、その `include` 条件 (指定されている場合) に一致していて、"_かつ_" `exclude` 条件 (指定されている場合) に一致しないスパンに対してのみです。

このオプションを構成するには、`include` または `exclude` (またはその両方) の下に、少なくとも 1 つの `matchType` と、`spanNames` または `attributes` のいずれかを指定します。
include と exclude の構成では、複数の条件を指定できます。
一致が成立するためには、指定したすべての条件が true と評価される必要があります。 

* **必須フィールド**: `matchType` によって、`spanNames` 配列および `attributes` 配列内の項目をどのように解釈するかを制御します。 設定可能な値は `regexp` および `strict` です。 

* **省略可能なフィールド**: 
    * `spanNames` は少なくとも 1 つの項目に一致する必要があります。 
    * `attributes` では、照合対象の属性の一覧を指定します。 一致が成立するためには、これらの属性すべてが完全に一致する必要があります。
    
> [!NOTE]
> `include` と `exclude` の両方が指定されている場合は、`exclude` プロパティをチェックする前に `include` プロパティがチェックされます。

### <a name="sample-usage"></a>使用例

```json
"processors": [
  {
    "type": "attribute",
    "include": {
      "matchType": "strict",
      "spanNames": [
        "spanA",
        "spanB"
      ]
    },
    "exclude": {
      "matchType": "strict",
      "attributes": [
        {
          "key": "redact_trace",
          "value": "false"
        }
      ]
    },
    "actions": [
      {
        "key": "credit_card",
        "action": "delete"
      },
      {
        "key": "duplicate_key",
        "action": "delete"
      }
    ]
  }
]
```
詳細については、[テレメトリ プロセッサの例](./java-standalone-telemetry-processors-examples.md)に関する記事を参照してください。

## <a name="attribute-processor"></a>属性プロセッサ

属性プロセッサでは、スパンの属性が変更されます。 スパンを含めたり除外したりする機能がサポートされます。 受け取ったアクションのリストが、構成ファイルで指定された順序で実行されます。 プロセッサでは、次のアクションがサポートされています。

- `insert`
- `update`
- `delete`
- `hash`
- `extract`
### `insert`

`insert` アクションでは、キーがまだ存在しないスパンに新しい属性が挿入されます。   

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "value": "value1",
        "action": "insert"
      }
    ]
  }
]
```
`insert` アクションには、次の設定が必要です。
* `key`
* `value` または `fromAttribute` のいずれか
* `action`: `insert`

### `update`

`update` アクションでは、キーが既に存在するスパンの属性が更新されます。

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "value": "newValue",
        "action": "update"
      }
    ]
  }
]
```
`update` アクションには、次の設定が必要です。
* `key`
* `value` または `fromAttribute` のいずれか
* `action`: `update`


### `delete` 

`delete` アクションでは、スパンから属性が削除されます

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "action": "delete"
      }
    ]
  }
]
```
`delete` アクションには、次の設定が必要です。
* `key`
* `action`: `delete`

### `hash`

`hash` アクションでは、既存の属性値がハッシュ (SHA1) されます。

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "action": "hash"
      }
    ]
  }
]
```
`hash` アクションには、次の設定が必要です。
* `key`
* `action`: `hash`

### `extract`

> [!NOTE]
> `extract` 機能は、バージョン 3.0.2 以降でのみ使用できます。

`extract` アクションでは、正規表現規則を使用して、入力キーから規則で指定したターゲット キーに、値が抽出されます。 ターゲット キーが既に存在する場合は、オーバーライドされます。 このアクションは、既存の属性がソースである、[スパン プロセッサ](#extract-attributes-from-the-span-name)の `toAttributes` 設定と同様に動作します。

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "pattern": "<regular pattern with named matchers>",
        "action": "extract"
      }
    ]
  }
]
```
`extract` アクションには、次の設定が必要です。
* `key`
* `pattern`
* `action`: `extract`

詳細については、[テレメトリ プロセッサの例](./java-standalone-telemetry-processors-examples.md)に関する記事を参照してください。

## <a name="span-processor"></a>スパン プロセッサ

スパン プロセッサにより、スパン名、またはそのスパン名に基づくスパンの属性が変更されます。 スパンを含めたり除外したりする機能がサポートされます。

### <a name="name-a-span"></a>スパンに名前を付ける

`name` セクションには `fromAttributes` 設定が必要です。 これらの属性の値は新しい名前を作成するために使用され、構成で指定した順序に従って連結されます。 プロセッサによってスパン名が変更されるのは、これらの属性すべてがスパンに存在する場合のみです。

`separator` 設定は省略可能です。 この設定は文字列です。 値を分割するために指定します。
> [!NOTE]
> 名前の変更が、属性プロセッサによる属性の変更に依存している場合は、パイプライン仕様で属性プロセッサの後にスパン プロセッサを指定するようにしてください。

```json
"processors": [
  {
    "type": "span",
    "name": {
      "fromAttributes": [
        "attributeKey1",
        "attributeKey2",
      ],
      "separator": "::"
    }
  }
] 
```

### <a name="extract-attributes-from-the-span-name"></a>スパン名から属性を抽出する

`toAttributes` セクションには、スパン名と照合する正規表現の一覧を含めます。 部分式に基づいて属性が抽出されます。

`rules` 設定は必須です。 この設定では、スパン名から属性値を抽出するために使用される規則の一覧を指定します。 

スパン名の値は、抽出された属性名で置き換えられます。 一覧内の各規則は、正規表現 (regex) パターン文字列です。 

抽出された属性名によって値が置き換えられる手順を次に示します。

1. 正規表現に対してスパン名がチェックされます。 
1. 正規表現に一致する場合、正規表現のすべての名前付き部分式が属性として抽出されます。 
1. 抽出された属性がスパンに追加されます。 
1. 各部分式名が属性名になります。 
1. 部分式の一致する部分が属性値になります。 
1. スパン名の一致する部分が、抽出された属性名に置き換えられます。 属性がスパン内に既に存在する場合は、上書きされます。 
 
すべての規則について、指定した順序で、このプロセスが繰り返されます。 後続の各規則は、前の規則の出力であるスパン名に対して処理されます。

```json
"processors": [
  {
    "type": "span",
    "name": {
      "toAttributes": {
        "rules": [
          "rule1",
          "rule2",
          "rule3"
        ]
      }
    }
  }
]

```

## <a name="common-span-attributes"></a>共通のスパン属性

このセクションでは、テレメトリ プロセッサで使用できるいくつかの共通のスパン属性を一覧表示します。

### <a name="http-spans"></a>HTTP スパン

| 属性  | Type | 説明 | 
|---|---|---|
| `http.method` | string | HTTP 要求メソッド。|
| `http.url` | string | `scheme://host[:port]/path?query[#fragment]` 形式の完全な HTTP 要求 URL。 通常、フラグメントは HTTP 経由で送信されません。 しかし、フラグメントがわかっている場合は、含める必要があります。|
| `http.status_code` | number | [HTTP 応答状態コード](https://tools.ietf.org/html/rfc7231#section-6).|
| `http.flavor` | string | HTTP プロトコルの種類。 |
| `http.user_agent` | string | クライアントから送信された [HTTP ユーザー エージェント](https://tools.ietf.org/html/rfc7231#section-5.5.3) ヘッダーの値。 |

### <a name="jdbc-spans"></a>JDBC スパン

| 属性  | Type | 説明  |
|---|---|---|
| `db.system` | string | 使用されているデータベース管理システム (DBMS) 製品の識別子。 |
| `db.connection_string` | string | データベースへの接続に使用された接続文字列。 埋め込みの資格情報は削除することをお勧めします。|
| `db.user` | string | データベースにアクセスするためのユーザー名 |
| `db.name` | string | アクセス対象のデータベースの名前を報告するために使用される文字列。 データベースを切り替えるコマンドの場合、この文字列はターゲット データベースに設定する必要があります (コマンドが失敗する場合でも)。|
| `db.statement` | string | 実行されているデータベース ステートメント。|
