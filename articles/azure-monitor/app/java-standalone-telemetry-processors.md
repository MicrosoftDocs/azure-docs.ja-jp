---
title: テレメトリ プロセッサ (プレビュー) - Azure Monitor Application Insights for Java
description: Azure Monitor Application Insights for Java でテレメトリ プロセッサを構成する方法
ms.topic: conceptual
ms.date: 10/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: c0745dd4069c64292fbcaef666d843ae2d25f7b3
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632582"
---
# <a name="telemetry-processors-preview---azure-monitor-application-insights-for-java"></a>テレメトリ プロセッサ (プレビュー) - Azure Monitor Application Insights for Java

> [!NOTE]
> この機能はまだプレビュー段階です。

Application Insights 用の Java 3.0 エージェントには現在、データがエクスポートされる前に利用統計情報を処理する機能があります。

テレメトリ プロセッサのユース ケースを次に示します。
 * 機微なデータをマスクする
 * 条件付きでカスタム ディメンションを追加する
 * 集計に使用される名前を更新して Azure portal で表示する
 * インジェスト コストを制御するためにスパン属性を削除する

## <a name="terminology"></a>用語

テレメトリ プロセッサに取り掛かる前に、スパンという用語が何を指しているかを理解することが重要です。

スパンとは、これらの 3 つのいずれかに対する一般用語です。

* 着信要求
* 送信依存関係 (別のサービスへのリモート呼び出しなど)
* プロセス内の依存関係 (サービスのサブコンポーネントによって実行される作業など)

テレメトリ プロセッサの目的に関して、スパンの重要なコンポーネントは次のとおりです。

* 名前
* 属性

スパン名は、Azure portal で要求と依存関係に使用される主な表示です。

スパン属性は、指定された要求または依存関係の標準とカスタムの両方のプロパティを表します。

## <a name="telemetry-processor-types"></a>テレメトリ プロセッサの種類

現在、2 種類のテレメトリ プロセッサがあります。

#### <a name="attribute-processor"></a>属性プロセッサ

属性プロセッサには、属性を挿入、更新、削除、またはハッシュする機能があります。
既存の属性から 1 つまたは複数の新しい属性を (正規表現を使用して) 抽出することもできます。

#### <a name="span-processor"></a>スパン プロセッサ

スパン プロセッサには、テレメトリ名を更新する機能があります。
スパン名から 1 つまたは複数の新しい属性を (正規表現を使用して) 抽出することもできます。

> [!NOTE]
> 現在のところ、テレメトリ プロセッサが処理するのは文字列型の属性だけであり、ブール値型や数値型の属性は処理しないので注意してください。

## <a name="getting-started"></a>作業の開始

次のテンプレートを使用して、`applicationinsights.json` という名前の構成ファイルを作成し、`applicationinsights-agent-*.jar` と同じディレクトリに配置します。

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

## <a name="includeexclude-criteria"></a>包含/除外条件

属性プロセッサとスパン プロセッサの両方で、オプションの `include` および `exclude` 条件がサポートされています。
プロセッサが適用されるのは、その `include` 条件 (指定されている場合) に一致していて、"_かつ_" `exclude` 条件 (指定されている場合) に一致しないスパンだけです。

このオプションを構成するには、`include` と `exclude` またはそのいずれかで、少なくとも 1 つの `matchType` と、`spanNames` または `attributes` のいずれかが必須となります。
包含/除外構成では、複数の条件を指定できます。
一致と見なされるようにするには、指定されたすべての条件が true と評価される必要があります。 

**必須フィールド**: 
* `matchType` では、`spanNames` および `attributes` 配列内の項目をどのように解釈するかを制御します。 指定できる値は `regexp` または `strict` です。 

**省略可能なフィールド**: 
* `spanNames` は少なくとも 1 つの項目に一致する必要があります。 
* `attributes` では、照合対象の属性の一覧を指定します。 一致と見なされるようにするには、これらすべての属性が完全に一致している必要があります。

> [!NOTE]
> `include` と `exclude` の両方が指定されている場合は、`exclude` プロパティの前に `include` プロパティがチェックされます。

#### <a name="sample-usage"></a>使用例

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
詳しく理解するには、[テレメトリ プロセッサの例](./java-standalone-telemetry-processors-examples.md)に関するドキュメントを参照してください。

## <a name="attribute-processor"></a>属性プロセッサ

属性プロセッサによりスパンの属性が変更されます。 必要に応じて、スパンを含有/除外する機能がサポートされます。 構成ファイルで指定された順序で実行されるアクションの一覧が取得されます。 サポートされているアクションは次のとおりです。

### `insert`

キーがまだ存在しないスパンに新しい属性を挿入します。   

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
`insert` アクションでは、次のものが必要です
  * `key`
  * `value` または `fromAttribute` のいずれか
  * `action`:`insert`

### `update`

キーが存在するスパンの属性を更新します

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
`update` アクションでは、次のものが必要です
  * `key`
  * `value` または `fromAttribute` のいずれか
  * `action`:`update`


### `delete` 

スパンから属性を削除します

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
`delete` アクションでは、次のものが必要です
  * `key`
  * `action`: `delete`

### `hash`

既存の属性値をハッシュ (SHA1) します

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
`hash` アクションでは、次のものが必要です
* `key`
* `action` : `hash`

### `extract`

> [!NOTE]
> この機能は、3.0.2 以降にのみ存在します

正規表現規則を使用して、入力キーから規則で指定されたターゲット キーに値を抽出します。 ターゲット キーが既に存在する場合は、上書きされます。 これは、既存の属性がソースとして設定されている[スパン プロセッサ](#extract-attributes-from-span-name) `toAttributes` 設定と同様に動作します。

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
`extract` アクションでは、次のものが必要です
* `key`
* `pattern`
* `action` : `extract`

詳しく理解するには、[テレメトリ プロセッサの例](./java-standalone-telemetry-processors-examples.md)に関するドキュメントを参照してください。

## <a name="span-processor"></a>スパン プロセッサ

スパン プロセッサにより、スパン名、またはそのスパン名に基づくスパンの属性が変更されます。 必要に応じて、スパンを含有/除外する機能がサポートされます。

### <a name="name-a-span"></a>スパンに名前を付ける

name セクションの一部として、次の設定が必須となります。

* `fromAttributes`:キーの属性値は、構成で指定された順序で新しい名前を作成するために使用されます。 名前を変更するには、すべての属性キーをプロセッサのスパンに指定する必要があります。

必要に応じて、次の設定を構成できます。

* `separator`:指定された文字列は値を分割するために使用されます
> [!NOTE]
> 名前の変更が、属性プロセッサによって変更されている属性に依存している場合は、確実にパイプライン仕様の属性プロセッサの後にスパン プロセッサが指定されるようにします。

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

### <a name="extract-attributes-from-span-name"></a>スパン名から属性を抽出する

スパン名を照合し、部分式に基づいてそこから属性を抽出する正規表現の一覧を取得します。 `toAttributes` セクションで指定する必要があります。

次の設定は必須です。

`rules` : スパン名から属性値を抽出する規則の一覧。 スパン名の値は、抽出された属性名で置き換えられます。 一覧内の各規則は正規表現パターンの文字列です。 スパン名は正規表現に対してチェックされます。 正規表現に一致する場合、正規表現のすべての名前付き部分式が属性として抽出され、スパンに追加されます。 各部分式の名前が属性名になり、部分式の一致部分がその属性値になります。 スパン名の一致する部分は、抽出された属性名で置き換えられます。 属性がスパン内に既に存在する場合、それらは上書きされます。 すべての規則について、指定された順序でプロセスが繰り返されます。 後続の各規則は、前の規則を処理した後の出力であるスパン名で動作します。

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

## <a name="list-of-attributes"></a>属性の一覧

テレメトリ プロセッサで使用できる一般的なスパン属性の一覧を次に示します。

### <a name="http-spans"></a>HTTP スパン

| 属性  | Type | [説明] | 
|---|---|---|
| `http.method` | string | HTTP 要求メソッド。|
| `http.url` | string | `scheme://host[:port]/path?query[#fragment]` 形式の完全な HTTP 要求 URL。 通常、フラグメントは HTTP を介して送信されませんが、それが既知のものであれば、含める必要があります。|
| `http.status_code` | number | [HTTP 応答状態コード](https://tools.ietf.org/html/rfc7231#section-6).|
| `http.flavor` | string | 使用される HTTP プロトコルの種類 |
| `http.user_agent` | string | クライアントから送信された [HTTP ユーザー エージェント](https://tools.ietf.org/html/rfc7231#section-5.5.3) ヘッダーの値。 |

### <a name="jdbc-spans"></a>JDBC スパン

| 属性  | Type | [説明]  |
|---|---|---|
| `db.system` | string | 使用されているデータベース管理システム (DBMS) 製品の識別子。 |
| `db.connection_string` | string | データベースへの接続に使用する接続文字列。 埋め込みの資格情報は削除することをお勧めします。|
| `db.user` | string | データベースにアクセスするためのユーザー名 |
| `db.name` | string | この属性は、アクセス対象のデータベースの名前を報告するために使用されます。 データベースを切り替えるコマンドの場合は、これはターゲット データベースに設定する必要があります (コマンドが失敗する場合でも)。|
| `db.statement` | string | 実行対象のデータベース ステートメント。|
