---
title: Azure Front Door ルール エンジンの一致条件
description: この記事では、Azure Front Door ルール エンジンで利用できるさまざまな一致条件の一覧を提供します。
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2020
ms.author: duau
ms.openlocfilehash: a352624fc802e4224aa1b60768c064c0054cdfd6
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102035876"
---
# <a name="azure-front-door-rules-engine-match-conditions"></a>Azure Front Door ルール エンジンの一致条件

[AFD ルール エンジン](front-door-rules-engine.md)では、ルールは 0 個以上の一致条件とアクションで構成されます。 この記事では、AFD ルール エンジンで使用できる一致条件について詳しく説明します。

ルールの最初の部分は、単一の一致条件または一連の一致条件です。 ルールは、最大 10 個の一致条件で構成できます。 一致条件とは、要求の特定の種類を識別するものであり、その種類に対して指定されたアクションが実行されます。 複数の一致条件を使用すると、一致条件は AND ロジックを使用してグループ化されます。 複数の値をサポートするすべての一致条件 ("スペース区切り" と表記します) では、"OR" 演算子が想定されます。

たとえば、以下のような場合に一致条件を使用できます。

- 特定の IP アドレス、国、または地域に基づいて要求をフィルター処理します。
- ヘッダー情報別に要求をフィルター処理する。
- モバイル デバイスまたはデスクトップ デバイスからの要求をフィルター処理する。

Azure Front Door ルール エンジンでは、次の一致条件を使用できます。  

## <a name="device-type"></a>デバイスの種類 

モバイル デバイスまたはデスクトップ デバイスで作成された要求を識別します。  

#### <a name="required-fields"></a>必須フィールド

演算子 | サポートされている値
---------|----------------
等しい、等しくない | Mobile、Desktop

## <a name="post-argument"></a>POST 引数

要求で使用される POST 要求メソッドに対して定義された引数に基づいて要求を識別します。 

#### <a name="required-fields"></a>必須フィールド

引数名 | 演算子 | 引数値 | 大文字と小文字の変換
--------------|----------|----------------|---------------
String | [標準の演算子一覧](#standard-operator-list) | String、Int | 小文字、大文字、切り捨て、空白の削除、URL エンコード、URL デコード

## <a name="query-string"></a>クエリ文字列

特定のクエリ文字列パラメーターが含まれた要求を識別します。 このパラメーターは、特定のパターンに一致する値に設定されます。 要求 URL のクエリ文字列パラメーター (例: **parameter=value**) によって、この条件が満たされているかどうかが決まります。 この一致条件は、クエリ文字列パラメーターを名前で識別し、パラメーター値として 1 つ以上の値を受け入れます。

#### <a name="required-fields"></a>必須フィールド

演算子 | クエリ文字列 | 大文字と小文字の変換
---------|--------------|---------------
[標準の演算子一覧](#standard-operator-list) | String、Int | 小文字、大文字、切り捨て、空白の削除、URL エンコード、URL デコード

## <a name="remote-address"></a>リモート アドレス

要求元の場所または IP アドレスに基づいて要求を識別します。

#### <a name="required-fields"></a>必須フィールド

演算子 | サポートされている値
---------|-----------------
geo の一致 | 国番号
IP の一致 | IP アドレス (スペース区切り)
geo の一致ではない | 国番号
IP の一致ではない | IP アドレス (スペース区切り)

#### <a name="key-information"></a>重要な情報

- CIDR 表記を使用します。
- 複数の IP アドレスと IP アドレスブロックを指定するには、次のように値の間にスペースを入れます。
  - **IPv4 の例**:*1.2.3.4 10.20.30.40* と指定した場合、アドレス 1:2:3:4 または 10.20.30.40 から配信される要求と一致します。
  - **IPv6 の例**:*1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80* と指定した場合、アドレス 1:2:3:4:5:6:7:8 または 10:20:30:40:50:60:70:80 から配信される要求と一致します。
- IP アドレス ブロックの構文では、ベース IP アドレスの末尾にスラッシュおよびプレフィックス サイズを付与します。 次に例を示します。
  - **IPv4 の例**:*5.5.5.64/26* と指定した場合、アドレス 5.5.5.64 ～ 5.5.5.127 から配信される要求と一致します。
  - **IPv6 の例**:*1:2:3:/48* と指定した場合、アドレス 1:2:3:0:0:0:0:0 ～ 1:2:3: ffff:ffff:ffff:ffff:ffff から配信される要求と一致します。

## <a name="request-body"></a>要求本文

要求の本文に表示される特定のテキストに基づいて要求を識別します。

#### <a name="required-fields"></a>必須フィールド

演算子 | 要求本文 | 大文字と小文字の変換
---------|--------------|---------------
[標準の演算子一覧](#standard-operator-list) | String、Int | 小文字、大文字、切り捨て、空白の削除、URL エンコード、URL デコード

## <a name="request-header"></a>要求ヘッダー

要求で特定のヘッダーを使用する要求を識別します。

#### <a name="required-fields"></a>必須フィールド

ヘッダー名 | 演算子 | ヘッダー値 | 大文字と小文字の変換
------------|----------|--------------|---------------
String | [標準の演算子一覧](#standard-operator-list) | String、Int | 小文字、大文字、切り捨て、空白の削除、URL エンコード、URL デコード

## <a name="request-method"></a>要求メソッド

指定された要求メソッドを使用する要求を識別します。

#### <a name="required-fields"></a>必須フィールド

演算子 | サポートされている値
---------|----------------
等しい、等しくない | GET、POST、PUT、DELETE、HEAD、OPTIONS、TRACE

#### <a name="key-information"></a>重要な情報

- GET 要求メソッドのみが、Azure Front Door 上にキャッシュされたコンテンツを生成できます。 その他のすべての要求メソッドは、ネットワーク経由でプロキシ化されます。 

## <a name="request-protocol"></a>要求プロトコル

指定されたプロトコルを使用する要求を識別します。

#### <a name="required-fields"></a>必須フィールド

演算子 | サポートされている値
---------|----------------
等しい、等しくない | HTTP、HTTPS

## <a name="request-url"></a>要求 URL

指定された URL に一致する要求を識別します。

#### <a name="required-fields"></a>必須フィールド

演算子 | 要求 URL | 大文字と小文字の変換
---------|-------------|---------------
[標準の演算子一覧](#standard-operator-list) | String、Int | 小文字、大文字、切り捨て、空白の削除、URL エンコード、URL デコード

#### <a name="key-information"></a>重要な情報

- このルール条件を使用する場合は、必ずプロトコル情報を含めてください。 (例: `https://www.<yourdomain\>.com`)。

## <a name="request-file-extension"></a>要求ファイル拡張子

要求元の URL のファイル名に指定されたファイル拡張子を含む要求を識別します。

#### <a name="required-fields"></a>必須フィールド

演算子 | 拡張機能 | 大文字と小文字の変換
---------|-----------|---------------
[標準の演算子一覧](#standard-operator-list) | String、Int | 小文字、大文字、切り捨て、空白の削除、URL エンコード、URL デコード

#### <a name="key-information"></a>重要な情報

- 拡張子に関して、先頭にピリオドを使用しないでください。たとえば、 *.html* ではなく *html* を使用します。

## <a name="request-file-name"></a>要求ファイル名

要求元の URL に指定されたファイル名を含む要求を識別します。

#### <a name="required-fields"></a>必須フィールド

演算子 | ファイル名 | 大文字と小文字の変換
---------|-----------|---------------
[標準の演算子一覧](#standard-operator-list) | String、Int | 小文字、大文字、切り捨て、空白の削除、URL エンコード、URL デコード

#### <a name="key-information"></a>重要な情報

- 複数のファイル名を指定するには、Enter キーを押して各ファイル名を区切ります。 

## <a name="request-path"></a>要求パス

要求元の URL に指定されたパスを含む要求を識別します。

#### <a name="required-fields"></a>必須フィールド

演算子 | 値 | 大文字と小文字の変換
---------|-------|---------------
[標準の演算子一覧](#standard-operator-list) | String、Int | 小文字、大文字、切り捨て、空白の削除、URL エンコード、URL デコード

#### <a name="key-information"></a>重要な情報

- このルールの条件を使用する際は、パスの最初の `/` の後から照合が始まることに注意してください。 たとえば、`https://www.<yourdomain>.com/folder/page` の場合は `folder/page` から照合が始まります。

## <a name="standard-operator-list"></a>標準の演算子一覧

標準の演算子リストからの値を受け入れるルールの場合、次の演算子が有効です。

- Any
- 等しい 
- Contains 
- 次の値で始まる 
- [次で終わる] 
- より小さい
- 以下
- より大きい
- 以上
- いずれでもありません
- 含まない
- 次の値で始まらない 
- 次の値で終わらない 
- 次の値より小さくない
- 次の値以下ではない
- 次の値より大きくない
- 次の値以上ではない

*より小さい* または *以上* のような数値演算子の場合、比較は長さに基づいて行われます。 一致条件の値は、比較する長さと同じ整数である必要があります。 


## <a name="next-steps"></a>次のステップ

- 最初の[ルール エンジン](front-door-tutorial-rules-engine.md)を構成する方法について確認します。 
- [ルール エンジンのアクション](front-door-rules-engine-actions.md)の詳細を確認します
- [Azure Front Door のルール エンジン](front-door-rules-engine.md)の詳細を確認します
