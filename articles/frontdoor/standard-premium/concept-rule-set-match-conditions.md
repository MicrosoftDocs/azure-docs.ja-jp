---
title: Azure Front Door Standard または Premium のルール セットの一致条件を構成する
description: この記事では、Azure Front Door Standard または Premium のルール セットで利用できるさまざまな一致条件の一覧を示します。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 4c65d0e7f80fab59ca7df4849df7117d482352c1
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098023"
---
# <a name="azure-front-door-standardpremium-preview-rule-set-match-conditions"></a>Azure Front Door Standard または Premium (プレビュー) のルール セットの一致条件

> [!Note]
> このドキュメントは、Azure Front Door Standard/Premium (プレビュー) を対象としています。 Azure Front Door については、 [こちら](../front-door-overview.md)を参照してください。

このチュートリアルでは、Azure portal で最初のルールのセットを使用してルール セットを作成する方法について説明します。 Azure Front Door Standard または Premium の[ルール セット](concept-rule-set.md)では、ルールは 0 個以上の一致条件と 1 つのアクションで構成されています。 この記事では、Azure Front Door Standard または Premium のルール セットで使用できる一致条件について詳しく説明します。

ルールの最初の部分は、単一の一致条件または一連の一致条件です。 ルールは、最大 10 個の一致条件で構成できます。 一致条件とは、要求の特定の種類を識別するものであり、その種類に対して指定されたアクションが実行されます。 複数の一致条件を使用すると、一致条件は AND ロジックを使用してグループ化されます。 複数の値をサポートするすべての一致条件 ("スペース区切り" と表記します) では、"OR" 演算子が想定されます。

たとえば、以下のような場合に一致条件を使用できます。

* 特定の IP アドレス、国、または地域に基づいて要求をフィルター処理します。
* ヘッダー情報別に要求をフィルター処理する。
* モバイル デバイスまたはデスクトップ デバイスからの要求をフィルター処理する。
* 要求ファイル名とファイル拡張子から要求をフィルター処理する。
* 要求 URL、プロトコル、パス、クエリ文字列、ポスト引数などから要求をフィルター処理する。

> [!IMPORTANT]
> Azure Front Door Standard/Premium (プレビュー) は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure Front Door Standard または Premium のルール セットでは、次の一致条件を使用できます。

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
String | [[オペレーター一覧]](#operator-list) | String、Int | 小文字、大文字

## <a name="query-string"></a>クエリ文字列

特定のクエリ文字列パラメーターが含まれた要求を識別します。 このパラメーターは、特定のパターンに一致する値に設定されます。 要求 URL のクエリ文字列パラメーター (例: **parameter=value**) によって、この条件が満たされているかどうかが決まります。 この一致条件は、クエリ文字列パラメーターを名前で識別し、パラメーター値として 1 つ以上の値を受け入れます。

#### <a name="required-fields"></a>必須フィールド

演算子 | クエリ文字列 | 大文字と小文字の変換
---------|--------------|---------------
[[オペレーター一覧]](#operator-list) | String、Int | 小文字、大文字

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

* CIDR 表記を使用します。
* 複数の IP アドレスと IP アドレス ブロックの場合、"OR" ロジックが使用されます。
    * **IPv4 の例**: 2 つの IP アドレス *1.2.3.4* と *10.20.30.40* を追加した場合、アドレス 1.2.3.4 または 10.20.30.40 のいずれかから要求が送信されると条件が一致します。
    * **IPv6 の例**: 2 つの IP アドレス *1:2:3:4:5:6:7:8* と *10:20:30:40:50:60:70:80* を追加した場合、アドレス 1:2:3:4:5:6:7:8 または 10:20:30:40:50:60:70:80 のいずれかから要求が送信されると条件が一致します。
* IP アドレス ブロックの構文では、ベース IP アドレスの末尾にスラッシュおよびプレフィックス サイズを付与します。 次に例を示します。
    * **IPv4 の例**:*5.5.5.64/26* と指定した場合、アドレス 5.5.5.64 ～ 5.5.5.127 から配信される要求と一致します。
    * **IPv6 の例**:*1:2:3:/48* と指定した場合、アドレス 1:2:3:0:0:0:0:0 ～ 1:2:3: ffff:ffff:ffff:ffff:ffff から配信される要求と一致します。

## <a name="request-body"></a>要求本文

要求の本文に表示される特定のテキストに基づいて要求を識別します。

#### <a name="required-fields"></a>必須フィールド

演算子 | 要求本文 | 大文字と小文字の変換
---------|--------------|---------------
[[オペレーター一覧]](#operator-list) | String、Int | 小文字、大文字

## <a name="request-header"></a>要求ヘッダー

要求で特定のヘッダーを使用する要求を識別します。

#### <a name="required-fields"></a>必須フィールド

ヘッダー名 | 演算子 | ヘッダー値 | 大文字と小文字の変換
------------|----------|--------------|---------------
String | [[オペレーター一覧]](#operator-list) | String、Int | 小文字、大文字

## <a name="request-method"></a>要求メソッド

指定された要求メソッドを使用する要求を識別します。

#### <a name="required-fields"></a>必須フィールド

演算子 | サポートされている値
---------|----------------
等しい、等しくない | GET、POST、PUT、DELETE、HEAD、OPTIONS、TRACE

#### <a name="key-information"></a>重要な情報

GET 要求メソッドのみが、Azure Front Door 上にキャッシュされたコンテンツを生成できます。 その他のすべての要求メソッドは、ネットワーク経由でプロキシ化されます。

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
[[オペレーター一覧]](#operator-list) | String、Int | 小文字、大文字

#### <a name="key-information"></a>重要な情報

このルール条件を使用する場合は、必ずプロトコル情報を含めてください。 たとえば、 *https://www.\<yourdomain\>.com* です。

## <a name="request-file-extension"></a>要求ファイル拡張子

要求元の URL のファイル名に指定されたファイル拡張子を含む要求を識別します。

#### <a name="required-fields"></a>必須フィールド

演算子 | 拡張機能 | 大文字と小文字の変換
---------|-----------|---------------
[[オペレーター一覧]](#operator-list)  | String、Int | 小文字、大文字

#### <a name="key-information"></a>重要な情報

拡張子に関して、先頭にピリオドを使用しないでください。たとえば、 *.html* ではなく *html* を使用します。

## <a name="request-file-name"></a>要求ファイル名

要求元の URL に指定されたファイル名を含む要求を識別します。

#### <a name="required-fields"></a>必須フィールド

演算子 | ファイル名 | 大文字と小文字の変換
---------|-----------|---------------
[[オペレーター一覧]](#operator-list)| String、Int | 小文字、大文字

## <a name="request-path"></a>要求パス

要求元の URL に指定されたパスを含む要求を識別します。

#### <a name="required-fields"></a>必須フィールド

演算子 | 値 | 大文字と小文字の変換
---------|-------|---------------
[[オペレーター一覧]](#operator-list) | String、Int | 小文字、大文字

## <a name="operator-list"></a><a name = "operator-list"></a>演算子リスト

標準の演算子リストからの値を受け入れるルールの場合、次の演算子が有効です。

* Any
* 等しい
* Contains
* 次の値で始まる
* [次で終わる]
* より小さい
* 以下
* より大きい
* 以上
* いずれでもありません
* 含まない
* 次の値で始まらない
* 次の値で終わらない
* 次の値より小さくない
* 次の値以下ではない
* 次の値より大きくない
* 次の値以上ではない
* Regular Expression

*より小さい* または *以上* のような数値演算子の場合、比較は長さに基づいて行われます。 一致条件の値は、比較する長さと同じ整数である必要があります。

## <a name="regular-expression"></a>Regular Expression

Regex では、次の操作はサポートされていません。

* 前方参照と部分式のキャプチャ
* 任意のゼロ幅アサーション
* サブルーチン参照と再帰パターン
* 条件付きパターン
* バックトラッキング制御動詞
* \C 1 バイト ディレクティブ
* \R 改行一致ディレクティブ
* \K 一致の開始のリセット ディレクティブ
* コールアウトと埋め込みコード
* アトミック グループ化と所有の量指定子

## <a name="next-steps"></a>次のステップ

* [ルール セット](concept-rule-set.md)の詳細について説明します。
* [最初のルール セットを構成する](how-to-configure-rule-set.md)方法について説明します。
* [ルール セットのアクション](concept-rule-set-actions.md)の詳細について説明します。
