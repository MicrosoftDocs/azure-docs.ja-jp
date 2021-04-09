---
title: Azure CDN の Standard ルール エンジンの一致条件 | Microsoft Docs
description: Azure Content Delivery Network (Azure CDN) の Standard ルール エンジンの一致条件に関するリファレンス ドキュメント。
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: allensu
ms.openlocfilehash: b8050b973027ac91ede0ba98f4d1c76831da9828
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "81259930"
---
# <a name="match-conditions-in-the-standard-rules-engine-for-azure-cdn"></a>Azure CDN の Standard ルール エンジンの一致条件

Azure Content Delivery Network (Azure CDN) の [Standard ルール エンジン](cdn-standard-rules-engine.md)では、ルールは、1 つまたは複数の一致条件とアクションから構成されます。 この記事では、Azure CDN の Standard ルール エンジンで利用できる一致条件について詳しく説明します。

ルールの最初の部分は、単一の一致条件または一連の一致条件です。 Azure CDN の Standard ルール エンジンでは、各ルールに最大 4 つの一致条件を含めることができます。 一致条件とは、要求の特定の種類を識別するものであり、その種類に対して指定されたアクションが実行されます。 複数の一致条件を使用すると、一致条件は AND ロジックを使用してグループ化されます。

たとえば、以下のような場合に一致条件を使用できます。

- 特定の IP アドレス、国、または地域に基づいて要求をフィルター処理します。
- ヘッダー情報別に要求をフィルター処理する。
- モバイル デバイスまたはデスクトップ デバイスからの要求をフィルター処理する。

## <a name="match-conditions"></a>一致条件

次の一致条件は、Azure CDN の Standard ルール エンジンで使用できます。 

### <a name="device-type"></a>デバイスの種類 

モバイル デバイスまたはデスクトップ デバイスで作成された要求を識別します。  

#### <a name="required-fields"></a>必須フィールド

演算子 | サポートされている値
---------|----------------
等しい、等しくない | Mobile、Desktop

### <a name="http-version"></a>HTTP バージョン

要求の HTTP バージョンに基づいて要求を識別します。

#### <a name="required-fields"></a>必須フィールド

演算子 | サポートされている値
---------|----------------
等しい、等しくない | 2.0、1.1、1.0、0.9、All

### <a name="request-cookies"></a>要求 Cookie

受信した要求の Cookie 情報に基づいて要求を識別します。

#### <a name="required-fields"></a>必須フィールド

Cookie 名 | 演算子 | Cookie 値 | 大文字と小文字の変換
------------|----------|--------------|---------------
String | [標準の演算子一覧](#standard-operator-list) | String、Int | 変換なし、大文字に変換、小文字に変換

#### <a name="key-information"></a>重要な情報

- Cookie 名を指定するときに、ワイルドカードの値 (アスタリスク (\*) を含む) を使用することはできず、正確な Cookie 名を使用する必要があります。
- この一致条件のインスタンスごとに、1 つの Cookie 名のみを指定できます。
- Cookie 名の比較では、大文字と小文字は区別されません。
- 複数の Cookie 値を指定するには、各 Cookie 値の間にスペースを入れます。 
- Cookie 値には、ワイルドカード値を利用できます。
- ワイルドカード値が指定されていない場合は、完全一致だけがこの一致条件を満たします。 たとえば、"Value" は "Value" と一致し、"Value1" とは一致しません。 

### <a name="post-argument"></a>POST 引数

要求で使用される POST 要求メソッドに対して定義された引数に基づいて要求を識別します。 

#### <a name="required-fields"></a>必須フィールド

引数名 | 演算子 | 引数値 | 大文字と小文字の変換
--------------|----------|----------------|---------------
String | [標準の演算子一覧](#standard-operator-list) | String、Int | 変換なし、大文字に変換、小文字に変換

### <a name="query-string"></a>クエリ文字列

特定のクエリ文字列パラメーターが含まれた要求を識別します。 このパラメーターは、特定のパターンに一致する値に設定されます。 要求 URL のクエリ文字列パラメーター (例: **parameter=value**) によって、この条件が満たされているかどうかが決まります。 この一致条件は、クエリ文字列パラメーターを名前で識別し、パラメーター値として 1 つ以上の値を受け入れます。

#### <a name="required-fields"></a>必須フィールド

演算子 | クエリ文字列 | 大文字と小文字の変換
---------|--------------|---------------
[標準の演算子一覧](#standard-operator-list) | String、Int | 変換なし、大文字に変換、小文字に変換

### <a name="remote-address"></a>リモート アドレス

要求元の場所または IP アドレスに基づいて要求を識別します。

#### <a name="required-fields"></a>必須フィールド

演算子 | サポートされている値
---------|-----------------
Any | 該当なし
geo の一致 | 国番号
IP の一致 | IP アドレス (スペース区切り)
いずれでもありません | 該当なし
geo の一致ではない | 国番号
IP の一致ではない | IP アドレス (スペース区切り)

#### <a name="key-information"></a>重要な情報

- CIDR 表記を使用します。
- 複数の IP アドレスと IP アドレスブロックを指定するには、次のように値の間にスペースを入れます。
  - **IPv4 の例**:*1.2.3.4 10.20.30.40* と指定した場合、アドレス 1:2:3:4 または 10.20.30.40 から配信される要求と一致します。
  - **IPv6 の例**:*1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80* と指定した場合、アドレス 1:2:3:4:5:6:7:8 または 10:20:30:40:50:60:70:80 から配信される要求と一致します。
- IP アドレス ブロックの構文では、ベース IP アドレスの末尾にスラッシュおよびプレフィックス サイズを付与します。 次に例を示します。
  - **IPv4 の例**:*5.5.5.64/26* と指定した場合、アドレス 5.5.5.64 ～ 5.5.5.127 から配信される要求と一致します。
  - **IPv6 の例**:*1:2:3:/48* と指定した場合、アドレス 1:2:3:0:0:0:0:0 ～ 1:2:3:ffff:ffff:ffff:ffff:ffff から配信される要求と一致します。

### <a name="request-body"></a>要求本文

要求の本文に表示される特定のテキストに基づいて要求を識別します。

#### <a name="required-fields"></a>必須フィールド

演算子 | 要求本文 | 大文字と小文字の変換
---------|--------------|---------------
[標準の演算子一覧](#standard-operator-list) | String、Int | 変換なし、大文字に変換、小文字に変換

### <a name="request-header"></a>要求ヘッダー

要求で特定のヘッダーを使用する要求を識別します。

#### <a name="required-fields"></a>必須フィールド

ヘッダー名 | 演算子 | ヘッダー値 | 大文字と小文字の変換
------------|----------|--------------|---------------
String | [標準の演算子一覧](#standard-operator-list) | String、Int | 変換なし、大文字に変換、小文字に変換

### <a name="request-method"></a>要求メソッド

指定された要求メソッドを使用する要求を識別します。

#### <a name="required-fields"></a>必須フィールド

演算子 | サポートされている値
---------|----------------
等しい、等しくない | GET、POST、PUT、DELETE、HEAD、OPTIONS、TRACE

#### <a name="key-information"></a>重要な情報

- GET 要求メソッドのみが、Azure CDN 上にキャッシュされたコンテンツを生成できます。 その他のすべての要求メソッドは、ネットワーク経由でプロキシ化されます。 

### <a name="request-protocol"></a>要求プロトコル

指定されたプロトコルを使用する要求を識別します。

#### <a name="required-fields"></a>必須フィールド

演算子 | サポートされている値
---------|----------------
等しい、等しくない | HTTP、HTTPS

### <a name="request-url"></a>要求 URL

指定された URL に一致する要求を識別します。

#### <a name="required-fields"></a>必須フィールド

演算子 | 要求 URL | 大文字と小文字の変換
---------|-------------|---------------
[標準の演算子一覧](#standard-operator-list) | String、Int | 変換なし、大文字に変換、小文字に変換

#### <a name="key-information"></a>重要な情報

- このルール条件を使用する場合は、必ずプロトコル情報を含めてください。 たとえば、 *https://www.\<yourdomain\>.com* とします。

### <a name="url-file-extension"></a>URL のファイル拡張子

要求元の URL のファイル名に指定されたファイル拡張子を含む要求を識別します。

#### <a name="required-fields"></a>必須フィールド

演算子 | 拡張機能 | 大文字と小文字の変換
---------|-----------|---------------
[標準の演算子一覧](#standard-operator-list) | String、Int | 変換なし、大文字に変換、小文字に変換

#### <a name="key-information"></a>重要な情報

- 拡張子に関して、先頭にピリオドを使用しないでください。たとえば、 *.html* ではなく *html* を使用します。

### <a name="url-file-name"></a>URL のファイル名

要求元の URL に指定されたファイル名を含む要求を識別します。

#### <a name="required-fields"></a>必須フィールド

演算子 | ファイル名 | 大文字と小文字の変換
---------|-----------|---------------
[標準の演算子一覧](#standard-operator-list) | String、Int | 変換なし、大文字に変換、小文字に変換

#### <a name="key-information"></a>重要な情報

- 複数のファイル名を指定するには、各ファイル名を単一のスペースで区切ります。 

### <a name="url-path"></a>URL パス

要求元の URL に指定されたパスを含む要求を識別します。

#### <a name="required-fields"></a>必須フィールド

演算子 | 値 | 大文字と小文字の変換
---------|-------|---------------
[標準の演算子一覧](#standard-operator-list) | String、Int | 変換なし、大文字に変換、小文字に変換

#### <a name="key-information"></a>重要な情報

- ファイル名の値には、ワイルドカード値を利用できます。 たとえば、各ファイル名パターンを 1 つ以上のアスタリスク (*) で構成できます。各アスタリスクは 1 つ以上の一連の文字と一致します。

## <a name="reference-for-rules-engine-match-conditions"></a>ルール エンジンの一致条件のリファレンス

### <a name="standard-operator-list"></a>標準の演算子一覧

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

*より小さい* または *以上* のような数値演算子の場合、比較は長さに基づいて行われます。 この場合、一致条件の値は、比較する長さと同じ整数である必要があります。 

## <a name="next-steps"></a>次のステップ

- [Azure CDN の概要](cdn-overview.md)
- [Standard のルール エンジンのリファレンス](cdn-standard-rules-engine-reference.md)
- [Standard ルール エンジンのアクション](cdn-standard-rules-engine-actions.md)
- [Standard ルール エンジンを使用して HTTPS を適用する](cdn-standard-rules-engine.md)
