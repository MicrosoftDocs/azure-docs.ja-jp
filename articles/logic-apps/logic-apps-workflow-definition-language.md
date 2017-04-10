---
title: "ワークフロー定義言語スキーマ - Azure Logic Apps | Microsoft Docs"
description: "Azure Logic Apps のワークフロー定義スキーマに基づいてワークフローを定義します"
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 26c94308-aa0d-4730-97b6-de848bffff91
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 03/21/2017
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 09ccdaa18d383569747612c33b63595c06a0131e
ms.lasthandoff: 03/28/2017

---

# <a name="workflow-definition-language-schema-for-azure-logic-apps"></a>Azure Logic Apps のワークフロー定義言語スキーマ

ワークフロー定義には、ロジック アプリの一部として実行される実際のロジックが含まれます。 この定義には、ロジック アプリを開始する 1 つ以上のトリガーと、ロジック アプリが実行する 1 つ以上のアクションが含まれます。  
  
## <a name="basic-workflow-definition-structure"></a>ワークフロー定義の基本構造

ワークフロー定義の基本的な構造を次に示します。  
  
```json
{
    "$schema": "<schema-of the-definition>",
    "contentVersion": "<version-number-of-definition>",
    "parameters": { <parameter-definitions-of-definition> },
    "triggers": [ { <definition-of-flow-triggers> } ],
    "actions": [ { <definition-of-flow-actions> } ],
    "outputs": { <output-of-definition> }
}
```
  
> [!NOTE]
> ロジック アプリのワークフローを作成および管理する方法については、[ワークフロー管理 REST API](https://docs.microsoft.com/rest/api/logic/workflows) のドキュメントをご覧ください。
  
|要素名|必須|説明|  
|------------------|--------------|-----------------|  
|$schema|いいえ|定義言語のバージョンが記述されている JSON スキーマ ファイルの場所を指定します。 この場所は、外部の定義を参照する場合に必要です。 このドキュメントの場所は次のとおりです。 <p>`https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#`|  
|contentVersion|いいえ|定義のバージョンを指定します。 定義を使ってワークフローをデプロイするとき、この値を使って、正しい定義が確実に使われるようにすることができます。|  
|parameters|なし|定義へのデータの入力に使うパラメーターを指定します。 最大 50 個のパラメーターを定義することができます。|  
|トリガー|いいえ|ワークフローを開始するトリガーの情報を指定します。 最大 250 個のトリガーを定義できます。|  
|アクション|いいえ|フローの実行時に行うアクションを指定します。 最大 250 個のアクションを定義できます。|  
|outputs|いいえ|デプロイされるリソースに関する情報を指定します。 最大 10 個の出力を定義できます。|  
  
## <a name="parameters"></a>parameters

このセクションでは、デプロイ時にワークフロー定義で使われるすべてのパラメーターを指定します。 定義の他のセクションでパラメーターを使う前に、このセクションですべてのパラメーターを宣言する必要があります。  
  
パラメーター定義の構造の例を次に示します。  

```json
"parameters": {
    "<parameter-name>" : {
        "type" : "<type-of-parameter-value>",
        "defaultValue": <default-value-of-parameter>,
        "allowedValues": [ <array-of-allowed-values> ],
        "metadata" : { "key": { "name": "value"} }
    }
}
```

|要素名|必須|説明|  
|------------------|--------------|-----------------|  
|type|はい|**型**: string <p> **宣言**: `"parameters": {"parameter1": {"type": "string"}` <p> **指定**: `"parameters": {"parameter1": {"value": "myparamvalue1"}}` <p> **型**: securestring <p> **宣言**: `"parameters": {"parameter1": {"type": "securestring"}}` <p> **指定**: `"parameters": {"parameter1": {"value": "myparamvalue1"}}` <p> **型**: int <p> **宣言**: `"parameters": {"parameter1": {"type": "int"}}` <p> **指定**: `"parameters": {"parameter1": {"value" : 5}}` <p> **型**: bool <p> **宣言**: `"parameters": {"parameter1": {"type": "array"}}` <p> **指定**: `"parameters": {"parameter1": { "value": true }}` <p> **型**: array <p> **宣言**: `"parameters": {"parameter1": {"type": "array"}}` <p> **指定**: `"parameters": {"parameter1": { "value": [ array-of-values ]}}` <p> **型**: object <p> **宣言**: `"parameters": {"parameter1": {"type": "object"}}` <p> **指定**: `"parameters": {"parameter1": { "value": { JSON-object } }}` <p> **型**: secureobject <p> **宣言**: `"parameters": {"parameter1": {"type": "object"}}` <p> **指定**: `"parameters": {"parameter1": { "value": { JSON-object } }}` <p> **注:** `securestring` 型と `secureobject` 型は、`GET` 操作では返されません。 すべてのパスワード、キー、シークレットで、この型を使う必要があります。|  
|defaultValue|いいえ|リソース作成時に値が指定されないときの、パラメーターの既定値を指定します。|  
|allowedValues|いいえ|パラメーターに許可される値の配列を指定します。|  
|metadata|いいえ|読み取り可能な説明や、Visual Studio または他のツールによって使われる設計時データなど、パラメーターに関する追加情報を指定します。|  
  
次の例では、アクションの body セクションでパラメーターを使う方法を示します。  
  
```json
"body" :
{
  "property1": "@parameters('parameter1')"
}
```

 パラメーターは出力で使うこともできます。  
  
## <a name="triggers-and-actions"></a>トリガーとアクション  

トリガーとアクションでは、ワークフローの実行に含めることができる呼び出しを指定します。 このセクションについて詳しくは、「[ワークフローのアクションとトリガー](logic-apps-workflow-actions-triggers.md)」をご覧ください。
  
## <a name="outputs"></a>出力
  

outputs では、ワークフローの実行から返すことができる情報を指定します。 たとえば、各実行で追跡する特定の状態または値がある場合、そのデータを実行の出力に含めることができます。 データは、その実行の管理 REST API と、その実行に対する Azure Portal の管理 UI に表示されます。 また、PowerBI などの他の外部システムにこれらの出力を送って、ダッシュボードを作成することもできます。 サービス REST API で受信した要求に応答するために出力が使われることは "*ありません*"。 `response` アクション タイプを使って受信要求に応答する例を次に示します。
  
```json
"outputs": {  
  "key1": {  
    "value": "value1",  
    "type" : "<type-of-value>"  
  }  
} 
```

|要素名|必須|Description|  
|------------------|--------------|-----------------|  
|key1|はい|出力のキー ID を指定します。 **key1** は、出力の識別に使う名前に置き換えます。|  
|値|はい|出力の値を指定します。|  
|type|はい|指定した値の型を指定します。 指定できる値の型は次のとおりです。 <p>- `string`<br />- `securestring`<br />- `int`<br />- `bool`<br />- `array`<br />- `object`|
  
## <a name="expressions"></a>式  

定義の JSON 値には、リテラル、または定義使用時に評価される式を、使うことができます。 次に例を示します。  
  
```json
"name": "value"
```

 または  
  
```json
"name": "@parameters('password') "
```

> [!NOTE]
> 一部の式は、実行開始時には存在しない可能性のある値を実行時のアクションから取得します。 **関数**を使って、これらの値の一部を取得できます。  
  
式は、JSON 文字列値内の任意の場所で使うことができ、常に別の JSON 値になります。 JSON 値が式であると特定されると、アットマーク (@) を削除することによって式の本体が抽出されます。 @ で始まるリテラル文字列が必要な場合は、@@ を使って文字列をエスケープする必要があります。 式の評価方法の例を次に示します。  
  
|JSON 値|結果|  
|----------------|------------|  
|"parameters"|文字 "parameters" が返されます。|  
|"parameters [1]"|文字 "parameters[1]" が返されます。|  
|"@@"|"@" を含む 1 文字の文字列が返されます。|  
|" @"|" @" を含む 2 文字の文字列が返されます。|  
  
"*文字列の補間*" により、式が `@{ ... }` にラップされている文字列の内部で式を使うこともできます。 For example: <p>`"name" : "First Name: @{parameters('firstName')} Last Name: @{parameters('lastName'}"`

結果は常に文字列であり、この機能は `concat` 関数に似ています。 `myNumber` が `42`、`myString` が `sampleString` と定義されているものとします。  
  
|JSON 値|結果|  
|----------------|------------|  
|"@parameters('myString')"|`sampleString` が文字列として返されます。|  
|"@{parameters('myString')}"|`sampleString` が文字列として返されます。|  
|"@parameters('myNumber')"|`42` が "*数値*" として返されます。|  
|"@{parameters('myNumber')}"|`42` が "*文字列*" として返されます。|  
|"Answer is: @{parameters('myNumber')}"|文字列 `Answer is: 42` が返されます。|  
|"@concat('Answer is: ', string(parameters('myNumber')))"|文字列 `Answer is: 42` が返されます。|  
|"Answer is: @@{parameters('myNumber')}"|文字列 `Answer is: @{parameters('myNumber')}` が返されます。|  
  
## <a name="operators"></a>演算子  

演算子は、式または関数の中で使うことができる文字です。 
  
|演算子|Description|  
|--------------|-----------------|  
|に関するページを参照してください。|ドット演算子を使うと、オブジェクトのプロパティを参照できます。|  
|?|疑問符演算子を使うと、実行時エラーを発生させずに、オブジェクトの null プロパティを参照できます。 たとえば、次の式を使って、null トリガー出力を処理できます。 <p>`@coalesce(trigger().outputs?.body?.property1, 'my default value')`|  
|'|単一引用符は、文字列リテラルをラップする唯一の方法です。 式の内部で二重引用符を使うことはできません。この区切り記号は、式全体をラップする JSON の引用符と競合するためです。|  
|[]|角かっこは、配列から特定のインデックスの値を取得するために使うことができます。 たとえば、`range(0,10)` を `forEach` 関数に渡すアクションがある場合、次の関数を使って配列から項目を取得できます。  <p>`myArray[item()]`|  
  
## <a name="functions"></a>関数  

式の中で関数を呼び出すこともできます。 次の表では、式の中で使うことができる関数を示します。  
  
|式|評価|  
|----------------|----------------|  
|"@function('Hello')"|リテラル文字列 "Hello" を最初のパラメーターとして使って、定義の関数メンバーを呼び出します。|  
|"@function('It's Cool!')"|リテラル文字列 "It's Cool!" を 最初のパラメーターとして使って、定義の関数メンバーを呼び出します。|  
|"@function().prop1"|定義の `myfunction` メンバーの prop1 プロパティの値を返します。|  
|"@function('Hello').prop1"|リテラル文字列 "Hello" を最初のパラメーターとして使って定義の関数メンバーを呼び出し、オブジェクトの prop1 プロパティを返します。|  
|"@function(parameters('Hello'))"|"Hello" パラメーターを評価し、値を関数に渡します。|  
  
### <a name="referencing-functions"></a>関数の参照  

これらの関数を使うと、ロジック アプリ内の他のアクションからの出力や、ロジック アプリの作成時に渡された値を、参照することができます。 たとえば、あるステップからのデータを参照して、別のステップで使うことができます。  
  
|関数名|Description|  
|-------------------|-----------------|  
|parameters|定義で定義されているパラメーター値を返します。 <p>`parameters('password')` <p> **パラメーター番号**: 1 <p> **名前**: Parameter <p> **説明**: 必須。 値が必要なパラメーターの値の名前です。|  
|action|式で、他の JSON の名前と値のペアから、または現在の実行時アクションの出力から、値を導出することができます。 次の例で propertyPath によって表されるプロパティは省略可能です。 propertyPath を指定しないと、アクション オブジェクト全体を参照します。 この関数は、アクションの do-until 条件の中でのみ使うことができます。 <p>`action().outputs.body.propertyPath`|  
|actions|式で、他の JSON の名前と値のペアから、または実行時アクションの出力から、値を導出することができます。 これらの式では、1 つのアクションが別のアクションに依存することを明示的に宣言します。 次の例で propertyPath によって表されるプロパティは省略可能です。 propertyPath を指定しないと、アクション オブジェクト全体を参照します。 この要素または conditions 要素のいずれかを使って依存関係を指定できますが、同じ依存リソースに両方の要素を使う必要はありません。 <p>`actions('myAction').outputs.body.propertyPath` <p> **パラメーター番号**: 1 <p> **名前**: Action name <p> **説明**: 必須。 値が必要なアクションの値の名前です。|  
|trigger|式で、他の JSON の名前と値のペアから、または実行時トリガーの出力から、値を導出することができます。 次の例で propertyPath によって表されるプロパティは省略可能です。 propertyPath を指定しないと、トリガー オブジェクト全体を参照します。 <p>`trigger().outputs.body.propertyPath` <p>トリガーの入力の中で使うと、関数は前の実行の出力を返します。 一方、トリガーの条件の中で使うと、`trigger` 関数は現在の実行の出力を返します。|  
|actionOutputs|この関数は、`actions('actionName').outputs` の短縮形です。 <p> **パラメーター番号**: 1 <p> **名前**: Action name <p> **説明**: 必須。 値が必要なアクションの値の名前です。|  
|actionBody、body|これらの関数は、`actions('actionName').outputs.body` の短縮形です。 <p> **パラメーター番号**: 1 <p> **名前**: Action name <p> **説明**: 必須。 値が必要なアクションの値の名前です。|  
|triggerOutputs|この関数は、`trigger().outputs` の短縮形です。|  
|triggerBody|この関数は、`trigger().outputs.body` の短縮形です。|  
|item|繰り返しアクションの中で使うと、この関数は、アクションのその繰り返しに対する配列の項目を返します。 たとえば、メッセージの配列の各項目に対して実行するアクションがある場合は、次の構文を使うことができます。 <p>`"input1" : "@item().subject"`|  
  
### <a name="collection-functions"></a>コレクション関数  

これらの関数はコレクションで動作し、一般に、配列や文字列、場合によっては辞書に適用します。  
  
|関数名|説明|  
|-------------------|-----------------|  
|contains|ディクショナリにキーが含まれる場合、リストに値が含まれる場合、または文字列に部分文字列が含まれる場合、true を返します。 たとえば、次の関数は `true` を返します。 <p>`contains('abacaba','aca')` <p> **パラメーター番号**: 1 <p> **名前**: Within collection <p> **説明**: 必須。 その中で検索を行うコレクションです。 <p> **パラメーター番号**: 2 <p> **名前**: Find object <p> **説明**: 必須。 **Within collection** 内で検索するオブジェクトです。|  
|length|配列または文字列内の要素の数を返します。 たとえば、次の関数は `3` を返します。  <p>`length('abc')` <p> **パラメーター番号**: 1 <p> **Name**: Collection <p> **説明**: 必須。 長さを取得する対象のコレクションです。|  
|empty|オブジェクト、配列、または文字列が空の場合は true を返します。 たとえば、次の関数は `true` を返します。 <p>`empty('')` <p> **パラメーター番号**: 1 <p> **Name**: Collection <p> **説明**: 必須。 空かどうかを確認するコレクションです。|  
|intersection|渡された複数の配列またはオブジェクトの間の共通要素を含む 1 つの配列またはオブジェクトを返します。 たとえば、次の関数は `[1, 2]` を返します。 <p>`intersection([1, 2, 3], [101, 2, 1, 10],[6, 8, 1, 2])` <p>関数のパラメーターは、オブジェクトのセットまたは配列のセットです (両方を混ぜることはできません)。 同じ名前のオブジェクトが複数ある場合は、その名前を持つ最後のオブジェクトが最終的なオブジェクトに含まれます。 <p> **パラメーター番号**: 1 ... *n* <p> **名前**: Collection *n* <p> **説明**: 必須。 評価対象のコレクションです。 結果にオブジェクトが含まれるには、渡されるすべてのコレクションにオブジェクトが存在する必要があります。|  
|union|この関数に渡された配列またはオブジェクトのすべての要素を含む 1 つの配列またはオブジェクトを返します。 たとえば、次の関数は `[1, 2, 3, 10, 101]` を返します。 <p>`union([1, 2, 3], [101, 2, 1, 10])` <p>関数のパラメーターは、オブジェクトのセットまたは配列のセットです (両方を混ぜることはできません)。 最終的な出力に同じ名前のオブジェクトが複数ある場合は、その名前を持つ最後のオブジェクトが最終的なオブジェクトに含まれます。 <p> **パラメーター番号**: 1 ... *n* <p> **名前**: Collection *n* <p> **説明**: 必須。 評価対象のコレクションです。 いずれかのコレクションに存在するオブジェクトは、結果にも含まれます。|  
|first|渡された配列または文字列の最初の要素を返します。 たとえば、次の関数は `0` を返します。 <p>`first([0,2,3])` <p> **パラメーター番号**: 1 <p> **Name**: Collection <p> **説明**: 必須。 最初のオブジェクトを取得するコレクションです。|  
|last|渡された配列または文字列の最後の要素を返します。 たとえば、次の関数は `3` を返します。 <p>`last('0123')` <p> **パラメーター番号**: 1 <p> **Name**: Collection <p> **説明**: 必須。 最後のオブジェクトを取得するコレクションです。|  
|take|渡された配列または文字列の最初の **Count** 個の要素を返します。 たとえば、次の関数は `[1, 2]` を返します。  <p>`take([1, 2, 3, 4], 2)` <p> **パラメーター番号**: 1 <p> **Name**: Collection <p> **説明**: 必須。 最初の **Count** 個のオブジェクトを取得するコレクションです。 <p> **パラメーター番号**: 2 <p> **名前**: Count <p> **説明**: 必須。 **Collection** から取得するオブジェクトの数です。 正の整数にする必要があります。|  
|skip|配列のインデックス **Count** 以降の要素を返します。 たとえば、次の関数は `[3, 4]` を返します。 <p>`skip([1, 2 ,3 ,4], 2)` <p> **パラメーター番号**: 1 <p> **Name**: Collection <p> **説明**: 必須。 最初の **Count** 個のオブジェクトをスキップするコレクションです。 <p> **パラメーター番号**: 2 <p> **名前**: Count <p> **説明**: 必須。 **Collection** の先頭から除去するオブジェクトの数です。 正の整数にする必要があります。|  
|join|配列の各要素を区切り記号で結合した文字列を返します。たとえば、次の関数は `"1,2,3,4"` を返します。<br /><br /> `join([1, 2, 3, 4], ',')`<br /><br /> **パラメーター番号**: 1<br /><br /> **Name**: Collection<br /><br /> **説明**: 必須。 項目を結合するコレクションです。<br /><br /> **パラメーター番号**: 2<br /><br /> **名前**: Delimiter<br /><br /> **説明**: 必須。 項目を区切る文字列です。|  
  
### <a name="string-functions"></a>文字列関数

次の関数は、文字列にのみ適用されます。 文字列には一部のコレクション関数を使うこともできます。  
  
|関数名|説明|  
|-------------------|-----------------|  
|concat|任意の数の文字列を結合します。 たとえば、パラメーター 1 が `p1` の場合、次の関数は `somevalue-p1-somevalue` を返します。 <p>`concat('somevalue-',parameters('parameter1'),'-somevalue')` <p> **パラメーター番号**: 1 ... *n* <p> **名前**: String *n* <p> **説明**: 必須。 1 つの文字列に結合する文字列です。|  
|substring|文字列から文字のサブセットを返します。 たとえば、次の関数は `abc` を返します。 <p>`substring('somevalue-abc-somevalue',10,3)` <p> **パラメーター番号**: 1 <p> **名前**: String <p> **説明**: 必須。 部分文字列を取得する文字列です。 <p> **パラメーター番号**: 2 <p> **名前**: Start index <p> **説明**: 必須。 パラメーター 1 で取得する部分文字列の開始位置のインデックスです。 <p> **パラメーター番号**: 3 <p> **名前**: Length <p> **説明**: 必須。 部分文字列の長さです。|  
|replace|文字列を指定された文字列に置き換えます。 たとえば、次の関数は `the new string` を返します。 <p>`replace('the old string', 'old', 'new')` <p> **パラメーター番号**: 1 <p> **名前**: string <p> **説明**: 必須。 この文字列内でパラメーター 2 を検索し、見つかった場合は、パラメーター 3 に置き換えます。 <p> **パラメーター番号**: 2 <p> **名前**: Old string <p> **説明**: 必須。 この文字列をパラメーター 1 で検索し、一致するものが見つかった場合はパラメーター 3 に置き換えます。 <p> **パラメーター番号**: 3 <p> **名前**: New string <p> **説明**: 必須。 パラメーター 1 でパラメーター 2 と一致する文字列が見つかった場合、この文字列に置き換えます。|  
|guid|この関数は、グローバルに一意の文字列 (GUID) を生成します。 たとえば、次の関数は `c2ecc88d-88c8-4096-912c-d6f2e2b138ce` という GUID を生成できます。 <p>`guid()` <p> **パラメーター番号**: 1 <p> **名前**: Format <p> **説明**: 省略可能。 [この GUID の値の書式設定方法](https://msdn.microsoft.com/library/97af8hh4%28v=vs.110%29.aspx)を示す単一の書式設定指定子です。 指定できる書式設定パラメーターは、"N"、"D"、"B"、"P"、"X" です。 指定しないと、"D" が使われます。|  
|toLower|文字列を小文字に変換します。 たとえば、次の関数は `two by two is four` を返します。 <p>`toLower('Two by Two is Four')` <p> **パラメーター番号**: 1 <p> **名前**: String <p> **説明**: 必須。 小文字に変換する文字列です。 文字列内の文字に小文字に相当する文字がない場合、変更されないまま返されます。|  
|toUpper|文字列を大文字に変換します。 たとえば、次の関数は `TWO BY TWO IS FOUR` を返します。 <p>`toUpper('Two by Two is Four')` <p> **パラメーター番号**: 1 <p> **名前**: String <p> **説明**: 必須。 大文字に変換する文字列です。 文字列内の文字に大文字に相当する文字がない場合、変更されないまま返されます。|  
|indexof|文字列内で値のインデックスを探します。大文字と小文字は区別されません。 たとえば、次の関数は `7` を返します。 <p>`indexof('hello, world.', 'world')` <p> **パラメーター番号**: 1 <p> **名前**: String <p> **説明**: 必須。 値を含む可能性のある文字列です。 <p> **パラメーター番号**: 2 <p> **名前**: String <p> **説明**: 必須。 インデックスを探す値です。|  
|lastindexof|文字列内で値の最後のインデックスを探します。大文字と小文字は区別されません。 たとえば、次の関数は `3` を返します。 <p>`lastindexof('foofoo', 'foo')` <p> **パラメーター番号**: 1 <p> **名前**: String <p> **説明**: 必須。 値を含む可能性のある文字列です。 <p> **パラメーター番号**: 2 <p> **名前**: String <p> **説明**: 必須。 インデックスを探す値です。|  
|startswith|文字列が値で始まっているかどうかを調べます。大文字と小文字は区別されません。 たとえば、次の関数は `true` を返します。 <p>`lastindexof('hello, world', 'hello')` <p> **パラメーター番号**: 1 <p> **名前**: String <p> **説明**: 必須。 値を含む可能性のある文字列です。 <p> **パラメーター番号**: 2 <p> **名前**: String <p> **説明**: 必須。 文字列が始まっている可能性のある値です。|  
|endswith|文字列が値で終わっているかどうかを調べます。大文字と小文字は区別されません。 たとえば、次の関数は `true` を返します。 <p>`lastindexof('hello, world', 'world')` <p> **パラメーター番号**: 1 <p> **名前**: String <p> **説明**: 必須。 値を含む可能性のある文字列です。 <p> **パラメーター番号**: 2 <p> **名前**: String <p> **説明**: 必須。 文字列が終わっている可能性のある値です。|  
|split|区切り記号を使って文字列を分割します。 たとえば、次の関数は `["a", "b", "c"]` を返します。 <p>`split('a;b;c',';')` <p> **パラメーター番号**: 1 <p> **名前**: String <p> **説明**: 必須。 分割する文字列です。 <p> **パラメーター番号**: 2 <p> **名前**: String <p> **説明**: 必須。 区切り記号です。|  
  
### <a name="logical-functions"></a>論理関数  

これらの関数は条件の中で役に立ち、ロジックの種類の評価に使うことができます。  
  
|関数名|Description|  
|-------------------|-----------------|  
|equals|2 つの値が等しい場合、true を返します。 たとえば、パラメーター 1 が someValue の場合、次の関数は `true` を返します。 <p>`equals(parameters('parameter1'), 'someValue')` <p> **パラメーター番号**: 1 <p> **名前**: Object 1 <p> **説明**: 必須。 **Object 2** と比較するオブジェクトです。 <p> **パラメーター番号**: 2 <p> **名前**: Object 2 <p> **説明**: 必須。 **Object 1** と比較するオブジェクトです。|  
|less|1 番目の引数が 2 番目の引数より小さい場合、true を返します。 値として指定できる型は integer、float、string だけであることに注意してください。 たとえば、次の関数は `true` を返します。 <p>`less(10,100)` <p> **パラメーター番号**: 1 <p> **名前**: Object 1 <p> **説明**: 必須。 **Object 2** より小さいかどうかを調べるオブジェクトです。 <p> **パラメーター番号**: 2 <p> **名前**: Object 2 <p> **説明**: 必須。 **Object 1** より大きいかどうかを調べるオブジェクトです。|  
|lessOrEquals|1 番目の引数が 2 番目の引数より小さいか等しい場合、true を返します。 値として指定できる型は integer、float、string だけであることに注意してください。 たとえば、次の関数は `true` を返します。 <p>`lessOrEquals(10,10)` <p> **パラメーター番号**: 1 <p> **名前**: Object 1 <p> **説明**: 必須。 **Object 2** 以下かどうかを調べるオブジェクトです。 <p> **パラメーター番号**: 2 <p> **名前**: Object 2 <p> **説明**: 必須。 **Object 1** 以上かどうかを調べるオブジェクトです。|  
|greater|1 番目の引数が 2 番目の引数より大きい場合、true を返します。 値として指定できる型は integer、float、string だけであることに注意してください。 たとえば、次の関数は `false` を返します。  <p>`greater(10,10)` <p> **パラメーター番号**: 1 <p> **名前**: Object 1 <p> **説明**: 必須。 **Object 2** より大きいかどうかを調べるオブジェクトです。 <p> **パラメーター番号**: 2 <p> **名前**: Object 2 <p> **説明**: 必須。 **Object 1** より小さいかどうかを調べるオブジェクトです。|  
|greaterOrEquals|1 番目の引数が 2 番目の引数より大きいか等しい場合、true を返します。 値として指定できる型は integer、float、string だけであることに注意してください。 たとえば、次の関数は `false` を返します。 <p>`greaterOrEquals(10,100)` <p> **パラメーター番号**: 1 <p> **名前**: Object 1 <p> **説明**: 必須。 **Object 2** 以上かどうかを調べるオブジェクトです。 <p> **パラメーター番号**: 2 <p> **名前**: Object 2 <p> **説明**: 必須。 **Object 1** 以下かどうかを調べるオブジェクトです。|  
|and|両方のパラメーターが true の場合、true を返します。 両方の引数は、ブール値である必要があります。 たとえば、次の関数は `false` を返します。 <p>`and(greater(1,10),equals(0,0))` <p> **パラメーター番号**: 1 <p> **名前**: Boolean 1 <p> **説明**: 必須。 `true` でなければならない 1 番目の引数です。 <p> **パラメーター番号**: 2 <p> **名前**: Boolean 2 <p> **説明**: 必須。 `true` でなければならない 2 番目の引数です。|  
|or|どちらかのパラメーターが true の場合、true を返します。 両方の引数は、ブール値である必要があります。 たとえば、次の関数は `true` を返します。 <p>`or(greater(1,10),equals(0,0))` <p> **パラメーター番号**: 1 <p> **名前**: Boolean 1 <p> **説明**: 必須。 `true` になることができる 1 番目の引数です。 <p> **パラメーター番号**: 2 <p> **名前**: Boolean 2 <p> **説明**: 必須。 `true` になることができる 2 番目の引数です。|  
|not|両方のパラメーターが `false` の場合、true を返します。 両方の引数は、ブール値である必要があります。 たとえば、次の関数は `true` を返します。 <p>`not(contains('200 Success','Fail'))` <p> **パラメーター番号**: 1 <p> **名前**: Boolean <p> **説明**: 両方のパラメーターが `false` の場合、true を返します。 両方の引数は、ブール値である必要があります。 次の関数は `true` を返します。`not(contains('200 Success','Fail'))`|  
|if|式が `true` または `false` のどちらになるかに基づいて、指定された値を返します。  たとえば、次の関数は `"yes"` を返します。 <p>`if(equals(1, 1), 'yes', 'no')` <p> **パラメーター番号**: 1 <p> **名前**: Expression <p> **説明**: 必須。 式がどちらの値を返す必要があるかを決定するブール値です。 <p> **パラメーター番号**: 2 <p> **名前**: True <p> **説明**: 必須。 式が `true` の場合に返す値です。 <p> **パラメーター番号**: 3 <p> **名前**: False <p> **説明**: 必須。 式が `false` の場合に返す値です。|  
  
### <a name="conversion-functions"></a>変換関数  

これらの関数は、言語の各ネイティブ型の間の変換に使われます。  
  
- string  
  
- integer  
  
- float  
  
- boolean  
  
- arrays  
  
- dictionaries  

-   forms  
  
|関数名|Description|  
|-------------------|-----------------|  
|int|パラメーターを整数に変換します。 たとえば、この関数は 100 を文字列ではなく数値として返します。 <p>`int('100')` <p> **パラメーター番号**: 1 <p> **名前**: Value <p> **説明**: 必須。 整数に変換する値です。|  
|string|パラメーターを文字列に変換します。 たとえば、次の関数は `'10'` を返します。 <p>`string(10)` <p>オブジェクトを文字列に変換することもできます。 たとえば、`myPar` パラメーターが 1 つつのプロパティ `abc : xyz` を含むオブジェクトの場合、この関数は `{"abc" : "xyz"}` を返します。 <p>`string(parameters('myPar'))` <p> **パラメーター番号**: 1 <p> **名前**: Value <p> **説明**: 必須。 文字列に変換する値です。|  
|json|パラメーターを JSON 型の値に変換します。`string()` の逆です。 たとえば、この関数は `[1,2,3]` を文字列ではなく配列として返します。 <p>`parse('[1,2,3]')` <p>同様に、文字列をオブジェクトに変換することもできます。 たとえば、次の関数は `{ "abc" : "xyz" }` を返します。 <p>`json('{"abc" : "xyz"}')` <p> **パラメーター番号**: 1 <p> **名前**: String <p> **説明**: 必須。 ネイティブな型の値に変換する文字列です。 <p>`json()` 関数は、XML の入力もサポートします。 たとえば、次のようなパラメーター値があるものとします。 <p>`<?xml version="1.0"?> <root>   <person id='1'>     <name>Alan</name>     <occupation>Engineer</occupation>   </person> </root>` <p>これは、次の JSON に変換されます。 <p>`{ "?xml": { "@version": "1.0" },   "root": {     "person": [     {       "@id": "1",       "name": "Alan",       "occupation": "Engineer"     }   ]   } }`|  
|float|パラメーター引数を浮動小数点数に変換します。 たとえば、次の関数は `10.333` を返します。 <p>`float('10.333')` <p> **パラメーター番号**: 1 <p> **名前**: Value <p> **説明**: 必須。 浮動小数点数に変換する値です。|  
|bool|パラメーターをブール値に変換します。 たとえば、次の関数は `false` を返します。 <p>`bool(0)` <p> **パラメーター番号**: 1 <p> **名前**: Value <p> **説明**: 必須。 ブール値に変換する値です。|  
|coalesce|渡された引数で最初の null 以外のオブジェクトを返します。 **注**: 空の文字列は null ではありません。 たとえば、パラメーター 1 と 2 が定義されていない場合、この関数は `fallback` を返します。  <p>`coalesce(parameters('parameter1'), parameters('parameter2') ,'fallback')` <p> **パラメーター番号**: 1 ... *n* <p> **名前**: Object*n* <p> **説明**: 必須。 null かどうか調べるオブジェクトです。|  
|base64|入力文字列の base64 表現を返します。 たとえば、次の関数は `c29tZSBzdHJpbmc=` を返します。 <p>`base64('some string')` <p> **パラメーター番号**: 1 <p> **名前**: String 1 <p> **説明**: 必須。 base 64 表現にエンコードする文字列です。|  
|base64ToBinary|base64 でエンコードされた文字列のバイナリ表現を返します。 たとえば、次の関数は `some string` のバイナリ表現を返します。 <p>`base64ToBinary('c29tZSBzdHJpbmc=')` <p> **パラメーター番号**: 1 <p> **名前**: String <p> **説明**: 必須。 base64 でエンコードされた文字列です。|  
|base64ToString|base64 でエンコードされた文字列の文字列表現を返します。 たとえば、次の関数は `some string` を返します。 <p>`base64ToString('c29tZSBzdHJpbmc=')` <p> **パラメーター番号**: 1 <p> **名前**: String <p> **説明**: 必須。 base64 でエンコードされた文字列です。|  
|Binary|値のバイナリ表現を返します。  たとえば、次の関数は `some string` のバイナリ表現を返します。 <p>`binary('some string')` <p> **パラメーター番号**: 1 <p> **名前**: Value <p> **説明**: 必須。 バイナリに変換する値です。|  
|dataUriToBinary|データの URI のバイナリ表現を返します。 たとえば、次の関数は `some string` のバイナリ表現を返します。 <p>`dataUriToBinary('data:;base64,c29tZSBzdHJpbmc=')` <p> **パラメーター番号**: 1 <p> **名前**: String <p> **説明**: 必須。 バイナリ表現に変換するデータ URI です。|  
|dataUriToString|データの URI の文字列表現を返します。 たとえば、次の関数は `some string` を返します。 <p>`dataUriToString('data:;base64,c29tZSBzdHJpbmc=')` <p> **パラメーター番号**: 1 <p> **名前**: String<p> **説明**: 必須。 文字列表現に変換するデータ URI です。|  
|dataUri|値のデータ URI を返します。 たとえば、次の関数はデータ URI `text/plain;charset=utf8;base64,c29tZSBzdHJpbmc=` を返します。 <p>`dataUri('some string')` <p> **パラメーター番号**: 1<p> **名前**: Value<p> **説明**: 必須。 データ URI に変換する値です。|  
|decodeBase64|入力の base64 文字列の文字列表現を返します。 たとえば、次の関数は `some string` を返します。 <p>`decodeBase64('c29tZSBzdHJpbmc=')` <p> **パラメーター番号**: 1 <p> **名前**: String <p> **説明**: 入力の base64 文字列の文字列表現を返します。|  
|encodeUriComponent|渡される URL でエスケープされた文字列です。 たとえば、次の関数は `You+Are%3ACool%2FAwesome` を返します。 <p>`encodeUriComponent('You Are:Cool/Awesome')` <p> **パラメーター番号**: 1 <p> **名前**: String <p> **説明**: 必須。 URL の安全でない文字をエスケープする文字列です。|  
|decodeUriComponent|渡された文字列の URL エスケープを解除します。 たとえば、次の関数は `You Are:Cool/Awesome` を返します。 <p>`encodeUriComponent('You+Are%3ACool%2FAwesome')` <p> **パラメーター番号**: 1 <p> **名前**: String <p> **説明**: 必須。 URL の安全でない文字をデコードする文字列です。|  
|decodeDataUri|入力データの URI 文字列のバイナリ表現を返します。 たとえば、次の関数は `some string` のバイナリ表現を返します。 <p>`decodeDataUri('data:;base64,c29tZSBzdHJpbmc=')` <p> **パラメーター番号**: 1 <p> **名前**: String <p> **説明**: 必須。 バイナリ表現にデコードする dataURI です。|  
|uriComponent|値の URI でエンコードされた表現を返します。 たとえば、次の関数は `You+Are%3ACool%2FAwesome` を返します。 <p>`uriComponent('You Are:Cool/Awesome')` <p> **パラメーター番号**: 1<p> **名前**: String <p> **説明**: 必須。 URI でエンコードする文字列です。|  
|uriComponentToBinary|URI でエンコードされた文字列のバイナリ表現を返します。 たとえば、次の関数は `You Are:Cool/Awesome` のバイナリ表現を返します。 <p>`uriComponentToBinary('You+Are%3ACool%2FAwesome')` <p> **パラメーター番号**: 1 <p> **名前**: String<p> **説明**: 必須。 URI でエンコードされた文字列です。|  
|uriComponentToString|URI でエンコードされた文字列の文字列表現を返します。 たとえば、次の関数は `You Are:Cool/Awesome` を返します。 <p>`uriComponentToBinary('You+Are%3ACool%2FAwesome')` <p> **パラメーター番号**: 1<p> **名前**: String<p> **説明**: 必須。 URI でエンコードされた文字列です。|  
|xml|値の XML 表現を返します。 たとえば、次の関数は `'\<name>Alan\</name>'` によって表される XML コンテンツを返します。 <p>`xml('\<name>Alan\</name>')` <p>`xml()` 関数は、JSON オブジェクトの入力もサポートします。 たとえば、パラメーター `{ "abc": "xyz" }` は、XML コンテンツ `\<abc>xyz\</abc>` に変換されます。 <p> **パラメーター番号**: 1<p> **名前**: Value<p> **説明**: 必須。 XML に変換する値です。|  
|xpath|xpath 式が評価された値の xpath 式と一致する XML ノードの配列を返します。 <p> **例 1** <p>パラメーター `p1` の値が、次の XML の文字列表現であるものとします。 <p>`<?xml version="1.0"?> <lab>   <robot>     <parts>5</parts>     <name>R1</name>   </robot>   <robot>     <parts>8</parts>     <name>R2</name>   </robot> </lab>` <p>コード `xpath(xml(parameters('p1'), '/lab/robot/name')` <p>は、次の値は返します。 <p>`[ <name>R1</name>, <name>R2</name> ]` <p>一方、コード <p>`xpath(xml(parameters('p1'), ' sum(/lab/robot/parts)')` <p>は、次の値は返します。 <p>`13` <p> <p> **例 2** <p>次のような XML コンテンツがあるものとします。 <p>`<?xml version="1.0"?> <File xmlns="http://foo.com">   <Location>bar</Location> </File>` <p>コード `@xpath(xml(body('Http')), '/*[name()=\"File\"]/*[name()=\"Location\"]')` <p>またはコード <p>`@xpath(xml(body('Http')), '/*[local-name()=\"File\" and namespace-uri()=\"http://foo.com\"]/*[local-name()=\"Location\" and namespace-uri()=\"\"]')` <p>は、次の値は返します。 <p>`<Location xmlns="http://abc.com">xyz</Location>` <p>また、コード `@xpath(xml(body('Http')), 'string(/*[name()=\"File\"]/*[name()=\"Location\"])')` <p>は、次の値は返します。 <p>``xyz`` <p> **パラメーター番号**: 1 <p> **名前**: Xml <p> **説明**: 必須。 XPath 式を評価する XML です。 <p> **パラメーター番号**: 2 <p> **名前**: XPath <p> **説明**: 必須。 評価する XPath 式です。|  
|array|パラメーターを配列に変換します。 たとえば、次の関数は `["abc"]` を返します。 <p>`array('abc')` <p> **パラメーター番号**: 1 <p> **名前**: Value <p> **説明**: 必須。 配列に変換する値です。|
|createArray|パラメーターから配列を作成します。 たとえば、次の関数は `["a", "c"]` を返します。 <p>`createArray('a', 'c')` <p> **パラメーター番号**: 1 ... *n* <p> **名前**: Any *n* <p> **説明**: 必須。 配列に結合する値です。|
|triggerFormDataValue|form-data または form-encoded トリガー出力のキーと一致する単一の値を返します。  一致が複数ある場合は、エラーになります。  たとえば、次の式は `bar` を返します。`triggerFormDataValue('foo')`<br /><br />**パラメーター番号**: 1<br /><br />**名前**: Key Name<br /><br />**説明**: 必須。 取得するフォーム データ値のキー名です。|
|triggerFormDataMultiValues|form-data または form-encoded トリガー出力のキーと一致する値の配列を返します。  たとえば、次の式は `["bar"]` を返します。`triggerFormDataValue('foo')`<br /><br />**パラメーター番号**: 1<br /><br />**名前**: Key Name<br /><br />**説明**: 必須。 取得するフォーム データ値のキー名です。|
|triggerMultipartBody|トリガーのマルチパート出力のパートの本文を返します。<br /><br />**パラメーター番号**: 1<br /><br />**名前**: Index<br /><br />**説明**: 必須。 取得するパートのインデックスです。|
|formDataValue|form-data または form-encoded アクション出力のキーと一致する単一の値を返します。  一致が複数ある場合は、エラーになります。  たとえば、次の式は `bar` を返します。`formDataValue('someAction', 'foo')`<br /><br />**パラメーター番号**: 1<br /><br />**名前**: Action Name<br /><br />**説明**: 必須。 form-data または form-encoded 応答を含むアクションの名前です。<br /><br />**パラメーター番号**: 2<br /><br />**名前**: Key Name<br /><br />**説明**: 必須。 取得するフォーム データ値のキー名です。|
|formDataMultiValues|form-data または form-encoded アクション出力のキーと一致する値の配列を返します。  たとえば、次の式は `["bar"]` を返します。`formDataMultiValues('someAction', 'foo')`<br /><br />**パラメーター番号**: 1<br /><br />**名前**: Action Name<br /><br />**説明**: 必須。 form-data または form-encoded 応答を含むアクションの名前です。<br /><br />**パラメーター番号**: 2<br /><br />**名前**: Key Name<br /><br />**説明**: 必須。 取得するフォーム データ値のキー名です。|
|multipartBody|アクションのマルチパート出力のパートの本文を返します。<br /><br />**パラメーター番号**: 1<br /><br />**名前**: Action Name<br /><br />**説明**: 必須。 マルチパート応答を含むアクションの名前です。<br /><br />**パラメーター番号**: 2<br /><br />**名前**: Index<br /><br />**説明**: 必須。 取得するパートのインデックスです。|

### <a name="math-functions"></a>算術関数  

これらの関数は、**integer** 型および **float** 型の値に使うことができます。  
  
|関数名|説明|  
|-------------------|-----------------|  
|追加|2 つの数値を加算した結果を返します。 たとえば、次の関数は `20.333` を返します。 <p>`add(10,10.333)` <p> **パラメーター番号**: 1 <p> **名前**: Summand 1 <p> **説明**: 必須。 **Summand 2** に加算する値です。 <p> **パラメーター番号**: 2 <p> **名前**: Summand 2 <p> **説明**: 必須。 **Summand 1** に加算する値です。|  
|sub|2 つの数値を減算した結果を返します。 たとえば、次の関数は `-0.333` を返します。 <p>`sub(10,10.333)` <p> **パラメーター番号**: 1 <p> **名前**: Minuend <p> **説明**: 必須。 **Subtrahend** を減算する値です。 <p> **パラメーター番号**: 2 <p> **名前**: Subtrahend <p> **説明**: 必須。 **Minuend** から減算する値です。|  
|mul|2 つの数値を乗算した結果を返します。 たとえば、次の関数は `103.33` を返します。 <p>`mul(10,10.333)` <p> **パラメーター番号**: 1 <p> **名前**: Multiplicand 1 <p> **説明**: 必須。 **Multiplicand 2** と乗算する値です。 <p> **パラメーター番号**: 2 <p> **名前**: Multiplicand 2 <p> **説明**: 必須。 **Multiplicand 1** と乗算する値です。|  
|div|2 つの数値を除算した結果を返します。 たとえば、次の関数は `1.0333` を返します。 <p>`div(10.333,10)` <p> **パラメーター番号**: 1 <p> **名前**: Dividend <p> **説明**: 必須。 **Divisor** によって除算される値です。 <p> **パラメーター番号**: 2 <p> **名前**: Divisor <p> **説明**: 必須。 **Dividend** を除算する値です。|  
|mod|2 つの数値を除算した余りを返します (剰余)。 たとえば、次の関数は `2` を返します。 <p>`mod(10,4)` <p> **パラメーター番号**: 1 <p> **名前**: Dividend <p> **説明**: 必須。 **Divisor** によって除算される値です。 <p> **パラメーター番号**: 2 <p> **名前**: Divisor <p> **説明**: 必須。 **Dividend** を除算する値です。 除算の後、残りの部分が取得されます。|  
|Min|この関数を呼び出すには 2 つの異なるパターンがあります。 <p>次の `min` は配列を受け取り、関数は `0` を返します。 <p>`min([0,1,2])` <p>または、次の関数はコンマ区切りリストを受け取り、やはり `0` を返します。 <p>`min(0,1,2)` <p> **注**: すべての値は数値でなければならないので、パラメーターが配列の場合、配列は数値だけを含む必要があります。 <p> **パラメーター番号**: 1 <p> **名前**: Collection または Value <p> **説明**: 必須。 最小値を検索する値の配列、またはセットの最初の値です。 <p> **パラメーター番号**: 2 ... *n* <p> **名前**: Value *n* <p> **説明**: 省略可能。 パラメーター 1 が Value の場合、追加の値を渡すことができ、渡したすべての値の最小値が返されます。|  
|max|この関数を呼び出すには 2 つの異なるパターンがあります。 <p>次の `max` は配列を受け取り、関数は `2` を返します。 <p>`max([0,1,2])` <p>または、次の関数はコンマ区切りリストを受け取り、やはり `2` を返します。 <p>`max(0,1,2)` <p> **注**: すべての値は数値でなければならないので、パラメーターが配列の場合、配列は数値だけを含む必要があります。 <p> **パラメーター番号**: 1 <p> **名前**: Collection または Value <p> **説明**: 必須。 最大値を検索する値の配列、またはセットの最初の値です。 <p> **パラメーター番号**: 2 ... *n* <p> **名前**: Value *n* <p> **説明**: 省略可能。 パラメーター 1 が Value の場合、追加の値を渡すことができ、渡したすべての値の最大値が返されます。|  
|range|特定の値から始まる整数の配列を生成します。 返される配列の長さを定義します。 <p>たとえば、次の関数は `[3,4,5,6]` を返します。 <p>`range(3,4)` <p> **パラメーター番号**: 1 <p> **名前**: Start index <p> **説明**: 必須。 配列の最初の整数です。 <p> **パラメーター番号**: 2 <p> **名前**: Count <p> **説明**: 必須。 この値は、配列内にある整数の個数です。|  
|rand|指定された範囲 (両端の値を含む) 内で整数の乱数を生成します。 たとえば、次の関数は `42` を返す可能性があります。 <p>`rand(-1000,1000)` <p> **パラメーター番号**: 1 <p> **名前**: Minimum <p> **説明**: 必須。 返される可能性のある最小の整数です。 <p> **パラメーター番号**: 2 <p> **名前**: Maximum <p> **説明**: 必須。 この値は、返される可能性のある最大の整数です。|  
  
### <a name="date-functions"></a>データ関数  
  
|関数名|Description|  
|-------------------|-----------------|  
|utcnow|現在のタイムスタンプを文字列として返します (例: `2017-03-15T13:27:36Z`)。 <p>`utcnow()` <p> **パラメーター番号**: 1 <p> **名前**: Format <p> **説明**: 省略可能。 このタイムスタンプの値を書式設定する方法を示す、[単一の書式指定子文字](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx)または[カスタム書式指定パターン](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)です。 形式を指定しないと、ISO 8601 形式 ("o") が使われます。|  
|addseconds|渡されたタイムスタンプ文字列に秒数を表す整数を追加します。 秒数は正でも負でもかまいません。 書式指定子を指定しない場合の既定値は、ISO 8601 形式 ("o") の文字列です。 例: `2015-03-15T13:27:00Z` <p>`addseconds('2015-03-15T13:27:36Z', -36)` <p> **パラメーター番号**: 1 <p> **名前**: Timestamp <p> **説明**: 必須。 時刻を表す文字列です。 <p> **パラメーター番号**: 2 <p> **名前**: Seconds <p> **説明**: 必須。 追加する秒の値です。 負の値にして秒数を減算できます。 <p> **パラメーター番号**: 3 <p> **名前**: Format <p> **説明**: 省略可能。 このタイムスタンプの値を書式設定する方法を示す、[単一の書式指定子文字](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx)または[カスタム書式指定パターン](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)です。 形式を指定しないと、ISO 8601 形式 ("o") が使われます。|  
|addminutes|渡されたタイムスタンプ文字列に分の値を表す整数を追加します。 分の値は正でも負でもかまいません。 書式指定子を指定しない場合の既定値は、ISO 8601 形式 ("o") の文字列です。 例: `2015-03-15T14:00:36Z` <p>`addminutes('2015-03-15T13:27:36Z', 33)` <p> **パラメーター番号**: 1 <p> **名前**: Timestamp <p> **説明**: 必須。 時刻を表す文字列です。 <p> **パラメーター番号**: 2 <p> **名前**: Minutes <p> **説明**: 必須。 追加する分の値です。 負の値にして分の値を減算できます。 <p> **パラメーター番号**: 3 <p> **名前**: Format <p> **説明**: 省略可能。 このタイムスタンプの値を書式設定する方法を示す、[単一の書式指定子文字](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx)または[カスタム書式指定パターン](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)です。 形式を指定しないと、ISO 8601 形式 ("o") が使われます。|  
|addhours|渡されたタイムスタンプ文字列に時間数を表す整数を追加します。 時間の値は正でも負でもかまいません。 書式指定子を指定しない場合の既定値は、ISO 8601 形式 ("o") の文字列です。 例: `2015-03-16T01:27:36Z` <p>`addhours('2015-03-15T13:27:36Z', 12)` <p> **パラメーター番号**: 1 <p> **名前**: Timestamp <p> **説明**: 必須。 時刻を表す文字列です。 <p> **パラメーター番号**: 2 <p> **名前**: Hours <p> **説明**: 必須。 追加する時間数です。 負の値にして時間数を減算できます。 <p> **パラメーター番号**: 3 <p> **名前**: Format <p> **説明**: 省略可能。 このタイムスタンプの値を書式設定する方法を示す、[単一の書式指定子文字](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx)または[カスタム書式指定パターン](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)です。 形式を指定しないと、ISO 8601 形式 ("o") が使われます。|  
|adddays|渡されたタイムスタンプ文字列に日数を表す整数を追加します。 日の値は正でも負でもかまいません。 書式指定子を指定しない場合の既定値は、ISO 8601 形式 ("o") の文字列です。 例: `2015-02-23T13:27:36Z` <p>`addseconds('2015-03-15T13:27:36Z', -20)` <p> **パラメーター番号**: 1 <p> **名前**: Timestamp <p> **説明**: 必須。 時刻を表す文字列です。 <p> **パラメーター番号**: 2 <p> **名前**: Days <p> **説明**: 必須。 追加する日数です。 負の値にして日数を減算できます。 <p> **パラメーター番号**: 3 <p> **名前**: Format <p> **説明**: 省略可能。 このタイムスタンプの値を書式設定する方法を示す、[単一の書式指定子文字](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx)または[カスタム書式指定パターン](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)です。 形式を指定しないと、ISO 8601 形式 ("o") が使われます。|  
|formatDateTime|日付形式の文字列を返します。 書式指定子を指定しない場合の既定値は、ISO 8601 形式 ("o") の文字列です。 例: `2015-02-23T13:27:36Z` <p>`formatDateTime('2015-03-15T13:27:36Z', 'o')` <p> **パラメーター番号**: 1 <p> **名前**: Date <p> **説明**: 必須。 日付を表す文字列です。 <p> **パラメーター番号**: 2 <p> **名前**: Format <p> **説明**: 省略可能。 このタイムスタンプの値を書式設定する方法を示す、[単一の書式指定子文字](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx)または[カスタム書式指定パターン](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)です。 形式を指定しないと、ISO 8601 形式 ("o") が使われます。|  
|startOfHour|渡された文字列タイムスタンプに時刻の開始を返します。 例: `2017-03-15T13:00:00Z`<br /><br /> `startOfHour('2017-03-15T13:27:36Z')`<br /><br /> **パラメーター番号**: 1<br /><br /> **名前**: Timestamp<br /><br /> **説明**: 必須。 時刻を表す文字列です。<br /><br />**パラメーター番号**: 2<br /><br /> **名前**: Format<br /><br /> **説明**: 省略可能。 このタイムスタンプの値を書式設定する方法を示す、[単一の書式指定子文字](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx)または[カスタム書式指定パターン](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)です。 形式を指定しないと、ISO 8601 形式 ("o") が使われます。|  
|startOfDay|渡された文字列タイムスタンプに 1 日の開始を返します。 例: `2017-03-15T00:00:00Z`<br /><br /> `startOfDay('2017-03-15T13:27:36Z')`<br /><br /> **パラメーター番号**: 1<br /><br /> **名前**: Timestamp<br /><br /> **説明**: 必須。 時刻を表す文字列です。<br /><br />**パラメーター番号**: 2<br /><br /> **名前**: Format<br /><br /> **説明**: 省略可能。 このタイムスタンプの値を書式設定する方法を示す、[単一の書式指定子文字](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx)または[カスタム書式指定パターン](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)です。 形式を指定しないと、ISO 8601 形式 ("o") が使われます。| 
|startOfMonth|渡された文字列タイムスタンプに月の開始を返します。 例: `2017-03-01T00:00:00Z`<br /><br /> `startOfMonth('2017-03-15T13:27:36Z')`<br /><br /> **パラメーター番号**: 1<br /><br /> **名前**: Timestamp<br /><br /> **説明**: 必須。 時刻を表す文字列です。<br /><br />**パラメーター番号**: 2<br /><br /> **名前**: Format<br /><br /> **説明**: 省略可能。 このタイムスタンプの値を書式設定する方法を示す、[単一の書式指定子文字](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx)または[カスタム書式指定パターン](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)です。 形式を指定しないと、ISO 8601 形式 ("o") が使われます。| 
|dayOfWeek|文字列タイムスタンプの曜日部分を返します。  日曜日は 0、月曜日は 1 などです。 例: `3`<br /><br /> `dayOfWeek('2017-03-15T13:27:36Z')`<br /><br /> **パラメーター番号**: 1<br /><br /> **名前**: Timestamp<br /><br /> **説明**: 必須。 時刻を表す文字列です。| 
|dayOfMonth|文字列タイムスタンプの日付部分を返します。 例: `15`<br /><br /> `dayOfMonth('2017-03-15T13:27:36Z')`<br /><br /> **パラメーター番号**: 1<br /><br /> **名前**: Timestamp<br /><br /> **説明**: 必須。 時刻を表す文字列です。| 
|dayOfYear|文字列タイムスタンプの年間通算日数部分を返します。 例: `74`<br /><br /> `dayOfYear('2017-03-15T13:27:36Z')`<br /><br /> **パラメーター番号**: 1<br /><br /> **名前**: Timestamp<br /><br /> **説明**: 必須。 時刻を表す文字列です。| 
|ticks|文字列タイムスタンプの刻みプロパティを返します。 例: `1489603019`<br /><br /> `ticks('2017-03-15T18:36:59Z')`<br /><br /> **パラメーター番号**: 1<br /><br /> **名前**: Timestamp<br /><br /> **説明**: 必須。 時刻を表す文字列です。| 
  
### <a name="workflow-functions"></a>ワークフロー関数  

以下の関数を使うと、実行時にワークフロー自体に関する情報を取得できます。  
  
|関数名|Description|  
|-------------------|-----------------|  
|listCallbackUrl|トリガーまたはアクションを開始するために呼び出す文字列を返します。 <p> **注**: この関数は **httpWebhook** および **apiConnectionWebhook** においてのみ使うことができ、**manual**、**recurrence**、**http**、**apiConnection** では使えません。 <p>たとえば、`listCallbackUrl()` 関数は次の値を返します。 <p>`https://prod-01.westus.logic.azure.com:443/workflows/1235...ABCD/triggers/manual/run?api-version=2015-08-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=xxx...xxx` |  
|workflow|この関数は、実行時のワークフロー自体の詳細をすべて返します。 名前、場所、リソース ID など、管理 API で入手できるすべての情報を取得できます。 これらのプロパティについて詳しくは、[Rest API に関するページ](http://go.microsoft.com/fwlink/p/?LinkID=525617)をご覧ください。 たとえば、次の関数は `westus` のような場所を返します。 <p>`workflow().location` <p> **注**: `@workflow` は現在トリガー内でサポートされています。|

## <a name="next-steps"></a>次のステップ

[ワークフローのアクションとトリガー](logic-apps-workflow-actions-triggers.md)
