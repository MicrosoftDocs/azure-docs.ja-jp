---
title: Azure CDN ルール エンジンの一致条件 | Microsoft Docs
description: Azure Content Delivery Network ルール エンジンの一致条件に関するリファレンス ドキュメント
services: cdn
documentationcenter: ''
author: Lichard
manager: akucer
editor: ''
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2017
ms.author: rli
ms.openlocfilehash: 877d994968dbc575c8baa7ac4c8a40b76f6d617f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59283479"
---
# <a name="azure-cdn-rules-engine-match-conditions"></a>Azure CDN ルール エンジンの一致条件 
この記事では、Azure Content Delivery Network (CDN) [ルール エンジン](cdn-rules-engine.md)で利用できる一致条件について詳しく説明します。

ルールの 2 番目の部分は、一致条件です。 一致条件とは、要求の特定の種類を識別するものであり、その種類に対して一連の機能が実行されます。

たとえば、以下のような場合に一致条件を使用できます。
- 特定の場所でコンテンツの要求をフィルター処理する。
- 特定の IP アドレスまたは国から生成された要求をフィルター処理する。
- ヘッダー情報別に要求をフィルター処理する。

## <a name="always-match-condition"></a>「Always (常に)」一致条件

「Always (常に)」一致条件では、一連の既定の機能がすべての要求に適用されます。

Name | 目的
-----|--------
[Always (常に)](#always) | 一連の既定の機能をすべての要求に適用します。

## <a name="device-match-condition"></a>「Device (デバイス)」一致条件

この「Device (デバイス)」一致条件では、モバイル デバイスからの要求をそのプロパティに基づいて識別します。  

Name | 目的
-----|--------
[デバイス](#device) | モバイル デバイスからの要求をそのプロパティに基づいて識別します。

## <a name="location-match-conditions"></a>「Location (場所)」一致条件

「Location (場所)」一致条件では、要求者の位置情報に基づいて要求を識別します。

Name | 目的
-----|--------
[AS Number (AS 番号)](#as-number) | 特定のネットワークから送信される要求を識別します。
[Country (国)](#country) | 指定した国から送信される要求を識別します。

## <a name="origin-match-conditions"></a>「配信元 (Origin)」一致条件

「配信元 (Origin)」一致条件では、Content Delivery Network ストレージまたは顧客の配信元サーバーを参照する要求を識別します。

Name | 目的
-----|--------
[CDN Origin (CDN 配信元)](#cdn-origin) | Content Delivery Network ストレージに格納されたコンテンツの要求を識別します。
[Customer Origin (顧客配信元)](#customer-origin) | 特定の顧客の配信元サーバーに格納されているコンテンツの要求を識別します。

## <a name="request-match-conditions"></a>「要求 (Request)」一致条件

「要求 (Request)」一致条件では、プロパティに基づいて要求が識別されます。

Name | 目的
-----|--------
[Client IP Address (クライアント IP アドレス)](#client-ip-address) | 特定の IP アドレスから送信される要求を識別します。
[Cookie Parameter (Cookie パラメーター)](#cookie-parameter) | 指定した値の各要求に関連付けられている Cookie を確認します。
[Cookie Parameter Regex (Cookie パラメーターの正規表現)](#cookie-parameter-regex) | 指定した正規表現の各要求に関連付けられている Cookie を確認します。
[Edge Cname (エッジ CNAME)](#edge-cname) | 特定の CNAME を指す要求を識別します。
[Referring Domain (参照ドメイン)](#referring-domain) | 指定したホスト名から参照された要求を識別します。
[Request Header Literal (要求ヘッダーのリテラル)](#request-header-literal) | 指定値に設定されている指定ヘッダーが含まれる要求を識別します。
[Request Header Regex (要求ヘッダーの正規表現)](#request-header-regex) | 指定正規表現に一致する値に設定されている指定ヘッダーが含まれる要求を識別します。
[Request Header Wildcard (要求ヘッダーのワイルドカード)](#request-header-wildcard) | 指定パターンに一致する値に設定されている指定ヘッダーが含まれる要求を識別します。
[Request Method (要求メソッド)](#request-method) | HTTP メソッドで要求を識別します。
[Request Scheme (要求スキーム)](#request-scheme) | HTTP プロトコルで要求を識別します。

## <a name="url-match-conditions"></a>「URL」一致条件

「URL」一致条件では、URL に基づいて要求が識別されます。

Name | 目的
-----|--------
[URL Path Directory (URL パス ディレクトリ)](#url-path-directory) | 相対パスで要求を識別します。
[URL Path Extension (URL パス拡張子)](#url-path-extension) | ファイル名の拡張子で要求を識別します。
[URL Path Filename (URL パス ファイル名)](#url-path-filename) | ファイル名で要求を識別します。
[URL Path Literal (URL パス リテラル)](#url-path-literal) | 要求の相対パスを指定値と比較します。
[URL Path Regex (URL パス正規表現)](#url-path-regex) | 要求の相対パスを指定正規表現と比較します。
[URL Path Wildcard (URL パス ワイルドカード)](#url-path-wildcard) | 要求の相対パスを指定パターンと比較します。
[URL Query Literal (URL クエリ リテラル)](#url-query-literal) | 要求のクエリ文字列を指定値と比較します。
[URL Query Parameter (URL クエリ パラメーター)](#url-query-parameter) | 指定パターンに一致する値に設定されている指定クエリ文字列パラメーターが含まれる要求を識別します。
[URL Query Regex (URL クエリ正規表現)](#url-query-regex) | 指定正規表現に一致する値に設定されている指定クエリ文字列パラメーターが含まれる要求を識別します。
[URL Query Wildcard (URL クエリ ワイルドカード)](#url-query-wildcard) | 指定値を要求のクエリ文字列と比較します。


## <a name="reference-for-rules-engine-match-conditions"></a>ルール エンジンの一致条件のリファレンス
<a name="main"></a>
---
### <a name="always"></a>Always (常に)

「Always (常に)」一致条件では、一連の既定の機能がすべての要求に適用されます。

[先頭に戻る](#main)

</br>

---
### <a name="as-number"></a>AS Number (AS 番号) 
AS Number ネットワークは、自律システム番号 (ASN) で識別されています。 

**[一致する]**/**[一致しない]** オプションによって、「AS Number (AS 番号)」一致条件が満たされる条件が決まります。
- **一致する**:クライアント ネットワークの ASN が、指定した ASN のいずれかと一致する必要があります。 
- **一致しない**:クライアント ネットワークの ASN が、指定したどの ASN とも一致しない必要があります。

重要な情報: 
- 複数の ASN を指定する場合は、ASN ごとに単一の空白で区切ります。 たとえば、"64514 64515" と指定した場合、64514 または 64515 のいずれかから配信された要求と一致します。
- 特定の要求から有効な ASN を返されない場合があります。 疑問符 (?) は、有効な ASN を特定できなかった要求と一致します。
- 目的のネットワークの ASN 全体を指定します。 部分的な値を指定しても、一致しません。
- キャッシュ設定の追跡方法が原因で、この一致条件は次の機能との互換性がありません。
  - Complete Cache Fill (完全キャッシュ入力)
  - Default Internal Max-Age (既定の内部最長有効期間)
  - Force Internal Max-Age (内部最長有効期間の強制)
  - Ignore Origin No-Cache (配信元のキャッシュなしを無視する)
  - Internal Max-Stale (内部 Max-Stale)

[先頭に戻る](#main)

</br>

---
### <a name="cdn-origin"></a>CDN Origin (CDN 配信元)
以下の両方の条件が満たされている場合に、「CDN Origin (CDN 配信元)」一致条件が満たされます。
- CDN ストレージのコンテンツが要求された。
- 要求 URI で、この一致条件で定義されているコンテンツ アクセス ポイントの種類 (例: /000001) を使用している。
  - CDN URL:要求 URI に、選択したコンテンツ アクセス ポイントが含まれている必要があります。
  - エッジ CNAME URL:対応するエッジ CNAME 構成では、選択したコンテンツ アクセス ポイントを参照する必要があります。
  
重要な情報: 
 - コンテンツ アクセス ポイントは、要求されたコンテンツを提供する必要があるサービスを識別します。
 - AND IF ステートメントを使用して、特定の一致条件を結合しないでください。 たとえば、「CDN Origin (CDN 配信元)」一致条件と「Customer Origin (顧客配信元)」一致条件を結合すると、一致不可能な一致パターンが作成されてしまいます。 このため、2 つの「CDN Origin (CDN 配信元)」一致条件を AND IF ステートメントで結合することはできません。

[先頭に戻る](#main)

</br>

---
### <a name="client-ip-address"></a>Client IP Address (現在の IP アドレス)
**[一致する]**/**[一致しない]** オプションによって、「Client IP Address (クライアント IP アドレス)」一致条件が満たされる条件が決まります。
- **一致する**:クライアントの IP アドレスが、指定した IP アドレスのいずれかと一致する必要があります。 
- **一致しない**:クライアントの IP アドレスが、指定したどの IP アドレスとも一致しない必要があります。 

重要な情報: 
- CIDR 表記を使用します。
- 複数の IP アドレスまたは IP アドレス ブロックを指定する場合は、IP アドレスまたは IP ブロックごとに単一の空白で区切ります。 例: 
  - **IPv4 の例**:"1.2.3.4 10.20.30.40" と指定した場合、アドレス 1.2.3.4 または 10.20.30.40 から配信される要求と一致します。
  - **IPv6 の例**:"1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80" と指定した場合、アドレス 1:2:3:4:5:6:7:8 または 10:20:30:40:50:60:70:80 から配信される要求と一致します。
- IP アドレス ブロックの構文では、ベース IP アドレスの末尾にスラッシュおよびプレフィックス サイズを付与します。 例: 
  - **IPv4 の例**:"5.5.5.64/26" と指定した場合、アドレス 5.5.5.64 ～ 5.5.5.127 から配信される要求と一致します。
  - **IPv6 の例**:"1:2:3:/48" と指定した場合、アドレス 1:2:3:0:0:0:0:0 ～ 1:2:3:ffff:ffff:ffff:ffff:ffff から配信される要求と一致します。
- キャッシュ設定の追跡方法が原因で、この一致条件は次の機能との互換性がありません。
  - Complete Cache Fill (完全キャッシュ入力)
  - Default Internal Max-Age (既定の内部最長有効期間)
  - Force Internal Max-Age (内部最長有効期間の強制)
  - Ignore Origin No-Cache (配信元のキャッシュなしを無視する)
  - Internal Max-Stale (内部 Max-Stale)

[先頭に戻る](#main)

</br>

---
### <a name="cookie-parameter"></a>Cookie Parameter (Cookie パラメーター)
**[一致する]**/**[一致しない]** オプションによって、「Cookie Parameter (Cookie パラメーター)」一致条件が満たされる条件が決まります。
- **一致する**:この一致条件に定義された 1 つ以上の値と一致する値を持つ特定の Cookie が、要求に含まれている必要があります。
- **一致しない**:要求が次のいずれかの条件を満たす必要があります。
  - 指定した Cookie を含まない。
  - 指定した Cookie を含むが、値はこの一致条件に定義されたいずれの値とも一致しない。
  
重要な情報: 
- Cookie 名:  
  - Cookie 名を指定するときに、アスタリスク (*) を含むワイルドカード値はサポートされていないため、Cookie 名の完全一致だけが比較の対象となります。
  - この一致条件のインスタンスごとに、1 つの Cookie 名のみを指定できます。
  - Cookie 名の比較では、大文字と小文字は区別されません。
- Cookie 値:  
  - 複数の Cookie 値を指定する場合は、値ごとに単一の空白で区切ります。
  - Cookie 値には、[ワイルドカード値](cdn-rules-engine-reference.md#wildcard-values)を利用できます。 
  - ワイルドカード値が指定されていない場合は、完全一致だけがこの一致条件を満たします。 たとえば、"Value" を指定すると "Value" と一致し、"Value1" や "Value2" とは一致しません。
  - 要求の Cookie 値に対して大文字と小文字を区別した比較を行うかどうかを制御するには、**[大文字と小文字を区別しない]** オプションを使用します。
- キャッシュ設定の追跡方法が原因で、この一致条件は次の機能との互換性がありません。
  - Complete Cache Fill (完全キャッシュ入力)
  - Default Internal Max-Age (既定の内部最長有効期間)
  - Force Internal Max-Age (内部最長有効期間の強制)
  - Ignore Origin No-Cache (配信元のキャッシュなしを無視する)
  - Internal Max-Stale (内部 Max-Stale)

[先頭に戻る](#main)

</br>

---
### <a name="cookie-parameter-regex"></a>Cookie Parameter Regex (Cookie パラメーターの正規表現)
「Cookie Parameter Regex (Cookie パラメーターの正規表現)」一致条件では、Cookie の名前と値を定義します。 [正規表現](cdn-rules-engine-reference.md#regular-expressions)を使用して、必要な Cookie 値を定義できます。 

**[一致する]**/**[一致しない]** オプションによって、「Cookie Parameter Regex (Cookie パラメーターの正規表現)」一致条件が満たされる条件が決まります。
- **一致する**:指定した正規表現と一致する値を持つ特定の Cookie が、要求に含まれている必要があります。
- **一致しない**:要求が次のいずれかの条件を満たす必要があります。
  - 指定した Cookie を含まない。
  - 指定した Cookie を含むが、値は指定した正規表現と一致しない。
  
重要な情報: 
- Cookie 名:  
  - Cookie 名を指定するときに、アスタリスク (*) を含む正規表現とワイルドカード値はサポートされていないため、Cookie 名の完全一致だけが比較の対象となります。
  - この一致条件のインスタンスごとに、1 つの Cookie 名のみを指定できます。
  - Cookie 名の比較では、大文字と小文字は区別されません。
- Cookie 値:  
  - Cookie 値には、正規表現を利用できます。
  - 要求の Cookie 値に対して大文字と小文字を区別した比較を行うかどうかを制御するには、**[大文字と小文字を区別しない]** オプションを使用します。
- キャッシュ設定の追跡方法が原因で、この一致条件は次の機能との互換性がありません。
  - Complete Cache Fill (完全キャッシュ入力)
  - Default Internal Max-Age (既定の内部最長有効期間)
  - Force Internal Max-Age (内部最長有効期間の強制)
  - Ignore Origin No-Cache (配信元のキャッシュなしを無視する)
  - Internal Max-Stale (内部 Max-Stale)

[先頭に戻る](#main)

</br>

---
### <a name="country"></a>Country
国コードを利用して、国を指定できます。 

**[一致する]**/**[一致しない]** オプションによって、「Country (国)」一致条件が満たされる条件が決まります。
- **一致する**:要求に、指定した国コード値が含まれている必要があります。 
- **一致しない**:要求に、指定した国コード値が含まれていない必要があります。

重要な情報: 
- 複数の国コードを指定する場合は、コードごとに単一の空白で区切ります。
- 国コードを指定している場合、ワイルドカードはサポートされません。
- "EU" および "AP" 国コードは、それらのリージョンにあるすべての IP アドレスを包むわけではありません。
- 特定の要求は、有効な国コードを返さない場合があります。 疑問符 (?) は、有効な国コードを特定できなかった要求と一致します。
- 国コードでは大文字と小文字が区別されます。
- キャッシュ設定の追跡方法が原因で、この一致条件は次の機能との互換性がありません。
  - Complete Cache Fill (完全キャッシュ入力)
  - Default Internal Max-Age (既定の内部最長有効期間)
  - Force Internal Max-Age (内部最長有効期間の強制)
  - Ignore Origin No-Cache (配信元のキャッシュなしを無視する)
  - Internal Max-Stale (内部 Max-Stale)

#### <a name="implementing-country-filtering-by-using-the-rules-engine"></a>ルール エンジンを使用した国のフィルタリングの実装
この一致条件を使用すると、要求の送信元の場所に基づいて多数のカスタマイズを行うことができます。 たとえば、次の構成を使用して、国のフィルタリンク機能の動作をレプリケートできます。

- URL パス ワイルドカードの一致:[「URL Path Wildcard (URL パス ワイルドカード)」一致条件](#url-path-wildcard)を、セキュリティで保護するディレクトリに設定します。 
    相対パスの末尾にアスタリスクを追加すると、すべての子へのアクセスが、このルールによって制限されるようになります。

- 国の一致:「Country (国)」一致条件を必要な国のセットに設定します。
   - 許可:「URL Path Wildcard (URL パス ワイルドカード)」一致条件で定義されている場所に保存されたコンテンツへのアクセスを、指定した国にのみ許可するには、「Country (国)」一致条件を **[一致しない]** に設定します。
   - ブロック:指定した国が、「URL Path Wildcard (URL パス ワイルドカード)」一致条件で定義されている場所に保存されたコンテンツにアクセスできないようにするには、「Country (国)」一致条件を **[一致する]** に設定します。

- Deny Access (403) (アクセス拒否 (403)) 機能: 国のフィルタリング機能の許可またはブロック部分をレプリケートするには、[Deny Access (403) (アクセス拒否 (403)) 機能](cdn-rules-engine-reference-features.md#deny-access-403)を有効にします。

[先頭に戻る](#main)

</br>

---
### <a name="customer-origin"></a>Customer Origin (顧客配信元)

重要な情報:  
- 「Customer Origin (顧客配信元)」一致条件は、コンテンツが CDN URL 経由で要求されたか、選択した顧客配信元を参照するエッジ CNAME URL 経由で要求されたかに関係なく満たされます。
- ルールで参照される顧客配信元の構成は、[Customer Origin]\(顧客配信元\) ページから削除できません。 顧客配信元の構成を削除しようとしている場合は、事前に以下の構成で参照していないことを確認してください。
  - 「Customer Origin (顧客配信元)」一致条件
  - エッジ CNAME 構成
- AND IF ステートメントを使用して、特定の一致条件を結合しないでください。 たとえば、「Customer Origin (顧客配信元 配信元)」一致条件と「CDN (CDN 配信元)」一致条件を結合すると、一致不可能な一致パターンが作成されてしまいます。 このため、2 つの「Customer Origin (顧客配信元)」一致条件を AND IF ステートメントで結合することはできません。

[先頭に戻る](#main)

</br>

---
### <a name="device"></a>Device

この「Device (デバイス)」一致条件では、モバイル デバイスからの要求をそのプロパティに基づいて識別します。 モバイル デバイスの検出は [WURFL](http://wurfl.sourceforge.net/) から実現します。 

**[一致する]**/**[一致しない]** オプションによって、「Device (デバイス)」一致条件が満たされる条件が決まります。
- **一致する**:要求者のデバイスが指定した値と一致する必要があります。 
- **一致しない**:要求者のデバイスが指定した値と一致しない必要があります。

重要な情報: 

- 指定した値の大文字と小文字を区別するかどうかを指定するには、**[大文字と小文字を区別しない]** オプションを使用します。
- キャッシュ設定の追跡方法が原因で、この一致条件は次の機能との互換性がありません。
  - Complete Cache Fill (完全キャッシュ入力)
  - Default Internal Max-Age (既定の内部最長有効期間)
  - Force Internal Max-Age (内部最長有効期間の強制)
  - Ignore Origin No-Cache (配信元のキャッシュなしを無視する)
  - Internal Max-Stale (内部 Max-Stale)

#### <a name="string-type"></a>文字列の種類
通常、WURFL 機能では、数字、文字、記号の任意の組み合わせを受け入れます。 この機能は柔軟性があるため、この一致条件に関連する値を解釈する方法を選択する必要があります。 次の表に、使用可能な一連のオプションを示します。

Type     | 説明
---------|------------
リテラル  | [リテラル値](cdn-rules-engine-reference.md#literal-values)を使用して、ほとんどの文字が特別な意味を持たないようにするには、このオプションを選択します。
ワイルドカード | すべての[ワイルドカード文字] ([ワイルドカード値](cdn-rules-engine-reference.md#wildcard-values)) を利用するには、このオプションを選択します。
正規表現    | [正規表現](cdn-rules-engine-reference.md#regular-expressions)を使用するには、このオプションを選択します。 正規表現は、文字のパターンを定義する際に便利です。

#### <a name="wurfl-capabilities"></a>WURFL 機能
WURFL 機能とは、モバイル デバイスを記述するカテゴリを指します。 選択した機能によって、要求の識別に使用されるモバイル デバイスの記述の種類が決まります。

次の表に、ルール エンジンの WURFL 機能とその変数を示します。
<br>
> [!NOTE] 
> 次の変数は、**Modify Client Request Header (クライアント要求ヘッダーの修正)** と **Modify Client Response Header (クライアント応答ヘッダーの修正)** 機能でサポートされています。

機能 | 変数 | 説明 | サンプルの値
-----------|----------|-------------|----------------
Brand Name | % {wurfl_cap_brand_name} | デバイスのブランド名を示す文字列です。 | Samsung
Device OS | % {wurfl_cap_device_os} | デバイスにインストールされているオペレーティング システムを示す文字列です。 | IOS
Device OS Version | % {wurfl_cap_device_os_version} | デバイスにインストールされているオペレーティング システムのバージョン番号を示す文字列です。 | 1.0.1
Dual Orientation | % {wurfl_cap_dual_orientation} | デバイスが双方向の向きをサポートするかどうかを示すブール値です。 | true
HTML Preferred DTD | % {wurfl_cap_html_preferred_dtd} | HTML コンテンツ向けにモバイル デバイスで推奨されるドキュメント型定義 (DTD) を示す文字列です。 | なし<br/>xhtml_basic<br/>html5
Image Inlining | % {wurfl_cap_image_inlining} | デバイスが Base64 でエンコードされたイメージをサポートするかどうかを示すブール値です。 | false
Is Android | % {wurfl_vcap_is_android} | デバイスが Android OS を使用するかどうかを示すブール値です。 | true
Is IOS | % {wurfl_vcap_is_ios} | デバイスが iOS を使用するかどうかを示すブール値です。 | false
Is Smart TV | % {wurfl_cap_is_smarttv} | デバイスがスマート TV であるかどうかを示すブール値です。 | false
Is Smartphone | % {wurfl_vcap_is_smartphone} | デバイスがスマートフォンであるかどうかを示すブール値です。 | true
Is Tablet | % {wurfl_cap_is_tablet} | デバイスがタブレットであるかどうかを示すブール値です。 この記述は OS に依存しません。 | true
Is Wireless Device | % {wurfl_cap_is_wireless_device} | デバイスがワイヤレス デバイスとして認識されているかどうかを示すブール値です。 | true
Marketing Name | % {wurfl_cap_marketing_name} | デバイスのマーケティング名を示す文字列です。 | BlackBerry 8100 Pearl
Mobile Browser | % {wurfl_cap_mobile_browser} | デバイスからコンテンツの要求に使用するブラウザーを示す文字列です。 | Chrome
Mobile Browser Version | % {wurfl_cap_mobile_browser_version} | デバイスからコンテンツの要求に使用するブラウザーのバージョンを示す文字列です。 | 31
Model Name | % {wurfl_cap_model_name} | デバイスのモデル名を示す文字列です。 | s3
プログレッシブ ダウンロード | %{wurfl_cap_progressive_download} | デバイスが、ダウンロード中のオーディオおよびビデオの再生をサポートしているかどうかを示すブール値です。 | true
リリース日 | %{wurfl_cap_release_date} | デバイスが WURFL データベースに追加された年月を示す文字列です。<br/><br/>形式: `yyyy_mm` | 2013_december
Resolution Height | % {wurfl_cap_resolution_height} | デバイスの高さ (ピクセル単位) を示す整数です。 | 768
Resolution Width | % {wurfl_cap_resolution_width} | デバイスの幅 (ピクセル単位) を示す整数です。 | 1024

[先頭に戻る](#main)

</br>

---
### <a name="edge-cname"></a>Edge Cname (エッジ CNAME)
重要な情報:  
- 利用可能なエッジ CNAME の一覧は、ルール エンジンが構成されているプラットフォームの [Edge CNAMEs]\(エッジ CNAME\) ページで構成済みのエッジ CNAME に限られています。
- エッジ CNAME 構成を削除しようとしている場合は、事前に「Edge Cname (エッジ CNAME)」一致条件でその構成を参照していないことを確認してください。 規則で定義されたエッジ CNAME 構成は、[Edge Cname]\(エッジ Cname\) ページから削除できません。 
- AND IF ステートメントを使用して、特定の一致条件を結合しないでください。 たとえば、「Edge Cname (エッジ Cname)」一致条件と「Customer Origin (顧客配信元)」一致条件を結合すると、一致不可能な一致パターンが作成されてしまいます。 このため、2 つの「Edge Cname (エッジ Cname)」一致条件を AND IF ステートメントで結合することはできません。
- キャッシュ設定の追跡方法が原因で、この一致条件は次の機能との互換性がありません。
  - Complete Cache Fill (完全キャッシュ入力)
  - Default Internal Max-Age (既定の内部最長有効期間)
  - Force Internal Max-Age (内部最長有効期間の強制)
  - Ignore Origin No-Cache (配信元のキャッシュなしを無視する)
  - Internal Max-Stale (内部 Max-Stale)

[先頭に戻る](#main)

</br>

---
### <a name="referring-domain"></a>Referring Domain (参照ドメイン)
コンテンツが要求されたときの参照元に関連付けられたホスト名によって、「Referring Domain (参照ドメイン) 」条件に一致するかどうかが決まります。 

**[一致する]**/**[一致しない]** オプションによって、「Referring Domain (参照ドメイン)」一致条件が満たされる条件が決まります。
- **一致する**:参照元ホスト名が指定した値と一致する必要があります。 
- **一致しない**:参照元ホスト名が指定した値と一致しない必要があります。

重要な情報: 
- 複数のホスト名を指定する場合は、ホスト名ごとに単一の空白で区切ります。
- この一致条件では、[ワイルドカード値](cdn-rules-engine-reference.md#wildcard-values)がサポートされます。
- 指定した値にアスタリスクが含まれていない場合、値が参照元のホスト名と完全に一致している必要があります。 たとえば、"mydomain.com"　と指定すると、"www.mydomain.com"　とは一致しません。
- 大文字と小文字を区別した比較を行うかどうかを制御するには、**[大文字と小文字を区別しない]** オプションを使用します。
- キャッシュ設定の追跡方法が原因で、この一致条件は次の機能との互換性がありません。
  - Complete Cache Fill (完全キャッシュ入力)
  - Default Internal Max-Age (既定の内部最長有効期間)
  - Force Internal Max-Age (内部最長有効期間の強制)
  - Ignore Origin No-Cache (配信元のキャッシュなしを無視する)
  - Internal Max-Stale (内部 Max-Stale)

[先頭に戻る](#main)

</br>

---
### <a name="request-header-literal"></a>Request Header Literal (要求ヘッダーのリテラル)
**[一致する]**/**[一致しない]** オプションによって、「Request Header Literal (要求ヘッダーのリテラル)」一致条件が満たされる条件が決まります。
- **一致する**:要求に、指定したヘッダーが含まれている必要があります。 値は、この一致条件に定義された値と一致している必要があります。
- **一致しない**:要求が次のいずれかの条件を満たす必要があります。
  - 指定したヘッダーを含まない。
  - 指定したヘッダーを含むが、値はこの一致条件に定義された値とは一致しない。
  
重要な情報: 
- ヘッダー名の比較では常に、大文字と小文字は区別されません。 ヘッダー値を比較する際の大文字と小文字の区別を制御するには、**[大文字と小文字を区別しない]** オプションを使用します。
- キャッシュ設定の追跡方法が原因で、この一致条件は次の機能との互換性がありません。
  - Complete Cache Fill (完全キャッシュ入力)
  - Default Internal Max-Age (既定の内部最長有効期間)
  - Force Internal Max-Age (内部最長有効期間の強制)
  - Ignore Origin No-Cache (配信元のキャッシュなしを無視する)
  - Internal Max-Stale (内部 Max-Stale)

[先頭に戻る](#main)

</br>

---
### <a name="request-header-regex"></a>Request Header Regex (要求ヘッダーの正規表現)
**[一致する]**/**[一致しない]** オプションによって、「Request Header Regex (要求ヘッダーの正規表現)」一致条件が満たされる条件が決まります。
- **一致する**:要求に、指定したヘッダーが含まれている必要があります。 値は、指定した[正規表現](cdn-rules-engine-reference.md#regular-expressions)で定義されたパターンに一致している必要があります。
- **一致しない**:要求が次のいずれかの条件を満たす必要があります。
  - 指定したヘッダーを含まない。
  - 指定したヘッダーを含むが、値は指定した正規表現と一致しない。

重要な情報: 
- ヘッダー名:  
  - ヘッダー名の比較では、大文字と小文字は区別されません。
  - ヘッダー名のスペースは、"%20" に置き換えます。 
- ヘッダー値:  
  - ヘッダー値には、正規表現を利用できます。
  - ヘッダー値を比較する際の大文字と小文字の区別を制御するには、**[大文字と小文字を区別しない]** オプションを使用します。
  - この一致条件が満たされるのは、ヘッダー値が指定した 1 つ以上のパターンと完全に一致する場合だけです。
- キャッシュ設定の追跡方法が原因で、この一致条件は次の機能との互換性がありません。
  - Complete Cache Fill (完全キャッシュ入力)
  - Default Internal Max-Age (既定の内部最長有効期間)
  - Force Internal Max-Age (内部最長有効期間の強制)
  - Ignore Origin No-Cache (配信元のキャッシュなしを無視する)
  - Internal Max-Stale (内部 Max-Stale) 

[先頭に戻る](#main)

</br>

---
### <a name="request-header-wildcard"></a>Request Header Wildcard (要求ヘッダーのワイルドカード)
**[一致する]**/**[一致しない]** オプションによって、「Request Header Wildcard (要求ヘッダーのワイルドカード)」一致条件が満たされる条件が決まります。
- **一致する**:要求に、指定したヘッダーが含まれている必要があります。 値は、この一致条件で定義されている 1 つ以上の値と一致する必要があります。
- **一致しない**:要求が次のいずれかの条件を満たす必要があります。
  - 指定したヘッダーを含まない。
  - 指定したヘッダーを含むが、値は指定した値と一致しない。
  
重要な情報: 
- ヘッダー名:  
  - ヘッダー名の比較では、大文字と小文字は区別されません。
  - ヘッダー名の空白は、"%20"　に置き換える必要があります。 この値を使用して、ヘッダー値に空白を指定することもできます。
- ヘッダー値:  
  - ヘッダー値には、[ワイルドカード値](cdn-rules-engine-reference.md#wildcard-values)を利用できます。
  - ヘッダー値を比較する際の大文字と小文字の区別を制御するには、**[大文字と小文字を区別しない]** オプションを使用します。
  - ヘッダー値が指定した 1 つ以上のパターンと完全に一致する場合に、この一致条件が満たされます。
  - 複数の値を指定する場合は、値ごとに単一の空白で区切ります。
- キャッシュ設定の追跡方法が原因で、この一致条件は次の機能との互換性がありません。
  - Complete Cache Fill (完全キャッシュ入力)
  - Default Internal Max-Age (既定の内部最長有効期間)
  - Force Internal Max-Age (内部最長有効期間の強制)
  - Ignore Origin No-Cache (配信元のキャッシュなしを無視する)
  - Internal Max-Stale (内部 Max-Stale)

[先頭に戻る](#main)

</br>

---
### <a name="request-method"></a>Request Method (要求メソッド)
「Request Method (要求メソッド)」一致条件は、選択した要求メソッドを使用して資産が要求された場合にのみ満たされます。 使用可能な要求メソッドは次のとおりです。
- GET
- HEAD 
- POST 
- OPTIONS 
- PUT 
- DELETE 
- TRACE 
- CONNECT 

重要な情報: 
- 既定では、GET 要求メソッドのみが、ネットワーク上にキャッシュされたコンテンツを生成できます。 その他のすべての要求メソッドは、ネットワーク経由でプロキシ化されます。
- キャッシュ設定の追跡方法が原因で、この一致条件は次の機能との互換性がありません。
  - Complete Cache Fill (完全キャッシュ入力)
  - Default Internal Max-Age (既定の内部最長有効期間)
  - Force Internal Max-Age (内部最長有効期間の強制)
  - Ignore Origin No-Cache (配信元のキャッシュなしを無視する)
  - Internal Max-Stale (内部 Max-Stale)

[先頭に戻る](#main)

</br>

---
### <a name="request-scheme"></a>Request Scheme (要求スキーム)
「Request Scheme (要求スキーム)」一致条件は、選択したプロトコルを使用して資産が要求された場合にのみ満たされます。 使用可能なプロトコルは次のとおりです。 
- HTTP
- HTTPS

重要な情報: 
- キャッシュ設定の追跡方法が原因で、この一致条件は次の機能との互換性がありません。
  - Complete Cache Fill (完全キャッシュ入力)
  - Default Internal Max-Age (既定の内部最長有効期間)
  - Force Internal Max-Age (内部最長有効期間の強制)
  - Ignore Origin No-Cache (配信元のキャッシュなしを無視する)
  - Internal Max-Stale (内部 Max-Stale)

[先頭に戻る](#main)

</br>

---
### <a name="url-path-directory"></a>URL Path Directory (URL パス ディレクトリ)
要求された資産のファイル名を除いた相対パスで要求を識別します。

**[一致する]**/**[一致しない]** オプションによって、「URL Path Directory (URL パス ディレクトリ)」一致条件が満たされる条件が決まります。
- **一致する**:要求に、指定した URL パターンに一致する相対 URL パス (ファイル名を除く) が含まれている必要があります。
- **一致しない**:要求に、指定した URL パターンに一致しない相対 URL パス (ファイル名を除く) が含まれている必要があります。

重要な情報: 
- URL の比較を、コンテンツ アクセス ポイントの前と後のどちらから開始するかを指定するには、**[Relative to]\(基準\)** オプションを使用します。 コンテンツ アクセス ポイントは、パスの Verizon CDN ホスト名と要求された資産の相対パスの間の部分です (例: /800001/CustomerOrigin)。 場所は、サーバーの種類 (CDN や顧客配信元など) と顧客アカウント番号で識別されます。

   **[Relative to]\(基準\)** オプションで使用できる値は次のとおりです。
   - **ルート**:URL 比較ポイントが、CDN ホスト名の直後から始まることを示します。 

     例: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder**/index.htm

   - **配信元**:URL 比較ポイントが、コンテンツ アクセス ポイント (例: 000001、/800001/myorigin) の後から始まることを示します。 \*.azureedge.net CNAME は、既定で Verizon CDN ホスト名の配信元ディレクトリを基準として作成されるため、Azure CDN ユーザーは値として **[Origin]\(配信元\)** を使用する必要があります。 

     例: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder**/index.htm 

     この URL は、Verizon CDN ホスト名 http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder/index.htm** を参照します。

- エッジ CNAME URL は、URL の比較の前に CDN URL に書き換えられます。

    たとえば、次の URL はどちらも同じ資産を参照するため、同じ URL パスになります。
  - CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
    
  - エッジ CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    追加情報:
  - カスタム ドメイン: https:\//my.domain.com/path/asset.htm
    
  - URL パス (ルートを基準): /800001/CustomerOrigin/path/
    
  - URL パス (配信元を基準): /path/

- URL の比較に使用される URL の部分は、要求された資産のファイル名の直前までです。 この種類のパスでは、末尾のスラッシュが最後の文字になります。
    
- URL パス パターンのスペースは、"%20" に置き換えます。
    
- 各 URL パス パターンには、1 つ以上のアスタリスク (*) を含めることができます。各アスタリスクは 1 つ以上の一連の文字と一致します。
    
- パターンに複数の URL パスを指定するには、各パスを単一のスペースで区切ります。

    例: */sales/ */marketing/

- URL パスの指定には、[ワイルドカード値](cdn-rules-engine-reference.md#wildcard-values)を利用できます。

- 大文字と小文字を区別した比較を行うかどうかを制御するには、**[大文字と小文字を区別しない]** オプションを使用します。

[先頭に戻る](#main)

</br>

---
### <a name="url-path-extension"></a>URL Path Extension (URL パス拡張子)
要求された資産のファイル拡張子で要求を識別します。

**[一致する]**/**[一致しない]** オプションによって、「URL Path Extension (URL パス拡張子)」一致条件が満たされる条件が決まります。
- **一致する**:要求の URL に、指定したパターンに完全に一致するファイル拡張子が含まれている必要があります。

   たとえば、"htm" を指定した場合、"htm" 資産は一致しますが、"html" 資産は一致しません。  

- **一致しない**:URL 要求に、指定したパターンに一致しないファイル拡張子が含まれている必要があります。

重要な情報: 
- **[値]** ボックスに、照合するファイル拡張子を指定します。 先頭にピリオドを使用しないでください。たとえば、".htm" ではなく "htm" を使用します。

- 大文字と小文字を区別した比較を行うかどうかを制御するには、**[大文字と小文字を区別しない]** オプションを使用します。

- 複数のファイル拡張子を指定するには、各拡張子を単一のスペースで区切ります。 

    例: htm html

- たとえば、"htm" を指定した場合、"htm" 資産は一致しますが、"html" 資産は一致しません。


#### <a name="sample-scenario"></a>サンプル シナリオ

次のサンプル構成は、要求が指定した拡張子のいずれかと一致する場合に、この一致条件が満たされると想定しています。

値の指定: asp aspx php html

次の拡張子で終わる URL が見つかった場合に、この一致条件が満たされます。
- .asp
- .aspx
- .php
- .html

[先頭に戻る](#main)

</br>

---
### <a name="url-path-filename"></a>URL Path Filename (URL パス ファイル名)
要求された資産のファイル名で要求を識別します。 この一致条件では、ファイル名は、要求された資産の名前、ピリオド、ファイル拡張子で構成されます (例: index.html)。

**[一致する]**/**[一致しない]** オプションによって、「URL Path Filename (URL パス ファイル名)」一致条件が満たされる条件が決まります。
- **一致する**:要求の URL パスに、指定したパターンに一致するファイル名が含まれている必要があります。
- **一致しない**:要求の URL パスに、指定したパターンに一致しないファイル名が含まれている必要があります。

重要な情報: 
- 大文字と小文字を区別した比較を行うかどうかを制御するには、**[大文字と小文字を区別しない]** オプションを使用します。

- 複数のファイル拡張子を指定するには、各拡張子を単一のスペースで区切ります。

    例: index.htm index.html

- ファイル名の値のスペースは、"%20" に置き換えます。
    
- ファイル名の値には、[ワイルドカード値](cdn-rules-engine-reference.md#wildcard-values)を利用できます。 たとえば、各ファイル名パターンを 1 つ以上のアスタリスク (*) で構成できます。各アスタリスクは 1 つ以上の一連の文字と一致します。
    
- ワイルドカード文字が指定されていない場合は、完全一致だけがこの一致条件を満たします。

    たとえば、"presentation.ppt" を指定した場合、"presentation.ppt" という名前の資産は一致しますが、"presentation.pptx" という名前の資産は一致しません。

[先頭に戻る](#main)

</br>

---
### <a name="url-path-literal"></a>URL Path Literal (URL パス リテラル)
要求の URL パス (ファイル名を含む) を指定した値と比較します。

**[一致する]**/**[一致しない]** オプションによって、「URL Path Literal (URL パス リテラル)」一致条件が満たされる条件が決まります。
- **一致する**:要求に、指定したパターンに一致する URL パスが含まれている必要があります。
- **一致しない**:要求に、指定したパターンに一致しない URL パスが含まれている必要があります。

重要な情報: 
- URL 比較ポイントが、コンテンツ アクセス ポイントの前と後のどちらから始まるかを指定するには、**[Relative to]\(基準\)** オプションを使用します。 

    **[Relative to]\(基準\)** オプションで使用できる値は次のとおりです。
  - **ルート**:URL 比較ポイントが、CDN ホスト名の直後から始まることを示します。

    例: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder**/index.htm

  - **配信元**:URL 比較ポイントが、コンテンツ アクセス ポイント (例: 000001、/800001/myorigin) の後から始まることを示します。 \*.azureedge.net CNAME は、既定で Verizon CDN ホスト名の配信元ディレクトリを基準として作成されるため、Azure CDN ユーザーは値として **[Origin]\(配信元\)** を使用する必要があります。 

    例: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder/index.htm**

    この URL は、Verizon CDN ホスト名 http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder/index.htm** を参照します。

- エッジ CNAME URL は、URL の比較の前に CDN URL に書き換えられます。

    たとえば、次の URL はどちらも同じ資産を参照するため、同じ URL パスになります。
  - CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
  - エッジ CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    追加情報:
    
  - URL パス (ルートを基準): /800001/CustomerOrigin/path/asset.htm
   
  - URL パス (配信元を基準): /path/asset.htm

- URL のクエリ文字列は無視されます。
- 大文字と小文字を区別した比較を行うかどうかを制御するには、**[大文字と小文字を区別しない]** オプションを使用します。
- この一致条件に指定した値は、クライアントが行った正確な要求の相対パスと比較されます。

- 特定のディレクトリに対するすべての要求を照合するには、「[URL Path Directory (URL パス ディレクトリ)](#url-path-directory)」または「[ URL Path Wildcard (URL パス ワイルドカード)](#url-path-wildcard)」一致条件を使用します。

[先頭に戻る](#main)

</br>

---
### <a name="url-path-regex"></a>URL Path Regex (URL パス正規表現)
要求の URL パスを指定した[正規表現](cdn-rules-engine-reference.md#regular-expressions)と比較します。

**[一致する]**/**[一致しない]** オプションによって、「URL Path Regex (URL パス正規表現)」一致条件が満たされる条件が決まります。
- **一致する**:要求に、指定した正規表現に一致する URL パスが含まれている必要があります。
- **一致しない**:要求に、指定した正規表現に一致しない URL パスが含まれている必要があります。

重要な情報: 
- エッジ CNAME URL は、URL の比較の前に CDN URL に書き換えられます。 
 
    たとえば、次の URL はどちらも同じ資産を参照するため、同じ URL パスになります。

  - CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm

  - エッジ CNAME URL: http:\//my.domain.com/path/asset.htm
    
    追加情報:
    
  - URL パス: /800001/CustomerOrigin/path/asset.htm

- URL のクエリ文字列は無視されます。
    
- 大文字と小文字を区別した比較を行うかどうかを制御するには、**[大文字と小文字を区別しない]** オプションを使用します。
    
- URL パスのスペースは、"%20" に置き換える必要があります。

[先頭に戻る](#main)

</br>

---
### <a name="url-path-wildcard"></a>URL Path Wildcard (URL パス ワイルドカード)
要求の相対 URL パスを、指定したワイルドカード パターンと比較します。

**[一致する]**/**[一致しない]** オプションによって、「URL Path Wildcard (URL パス ワイルドカード)」一致条件が満たされる条件が決まります。
- **一致する**:要求に、指定したワイルドカード パターンに一致する URL パスが含まれている必要があります。
- **一致しない**:要求に、指定したワイルドカード パターンに一致しない URL パスが含まれている必要があります。

重要な情報: 
- **[Relative to]\(基準\)** オプション: URL 比較ポイントが、コンテンツ アクセス ポイントの前と後のどちらから始まるかを指定します。

   このオプションには次の値を指定できます。
  - **ルート**:URL 比較ポイントが、CDN ホスト名の直後から始まることを示します。

    例: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder**/index.htm

  - **配信元**:URL 比較ポイントが、コンテンツ アクセス ポイント (例: 000001、/800001/myorigin) の後から始まることを示します。 \*.azureedge.net CNAME は、既定で Verizon CDN ホスト名の配信元ディレクトリを基準として作成されるため、Azure CDN ユーザーは値として **[Origin]\(配信元\)** を使用する必要があります。 

    例: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder/index.htm**

    この URL は、Verizon CDN ホスト名 http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder/index.htm** を参照します。

- エッジ CNAME URL は、URL の比較の前に CDN URL に書き換えられます。

    たとえば、次の URL はどちらも同じ資産を参照するため、同じ URL パスになります。
  - CDN URL: http://wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
  - エッジ CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    追加情報:
    
  - URL パス (ルートを基準): /800001/CustomerOrigin/path/asset.htm
    
  - URL パス (配信元を基準): /path/asset.htm
    
- 複数の URL パスを指定するには、各パスを単一のスペースで区切ります。

   例: /marketing/asset.* /sales/*.htm

- URL のクエリ文字列は無視されます。
    
- 大文字と小文字を区別した比較を行うかどうかを制御するには、**[大文字と小文字を区別しない]** オプションを使用します。
    
- URL パスのスペースは、"%20" に置き換えます。
    
- URL パスに指定する値には、[ワイルドカード値](cdn-rules-engine-reference.md#wildcard-values)を利用できます。 各 URL パス パターンには、1 つ以上のアスタリスク (*) を含めることができます。各アスタリスクは 1 つ以上の一連の文字と一致します。

#### <a name="sample-scenarios"></a>サンプル シナリオ

次の表のサンプル構成は、要求が指定した URL パターンに一致する場合に、この一致条件が満たされると想定しています。

値                   | 基準    | 結果 
------------------------|----------------|-------
\*/test.html \*/test.php  | ルートまたは配信元 | 任意のフォルダーの "test.html" または "test.php" という名前の資産の要求が、このパターンに一致します。
/80ABCD/origin/text/*   | ルート           | 要求された資産が次の条件を満たしている場合、このパターンに一致します。 <br />- "origin" という顧客配信元に存在する。 <br />- 相対パスが "text" というフォルダーで始まる  (つまり、要求された資産が、"text" フォルダーまたは再帰サブフォルダーに存在する)
*/css/* */js/*          | ルートまたは配信元 | css または js フォルダーを含むすべての CDN URL またはエッジ CNAME URL が、このパターンに一致します。
*.jpg *.gif *.png       | ルートまたは配信元 | .jpg、.gif、または .png で終わる すべての CDN URL またはエッジ CNAME URL が、このパターンに一致します。 このパターンを指定する別の方法は、「[URL Path Extension (URL パス拡張子)](#url-path-extension)」一致条件を使用することです。
/images/\* /media/\*      | Origin (配信元)         | 相対パスが "images" または "media" フォルダーで始まる CDN URL またはエッジ CNAME URL が、このパターンに一致します。 <br />- CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/images/sales/event1.png<br />- サンプル エッジ CNAME URL: http:\//cdn.mydomain.com/images/sales/event1.png

[先頭に戻る](#main)

</br>

---
### <a name="url-query-literal"></a>URL Query Literal (URL クエリ リテラル)
要求のクエリ文字列を指定値と比較します。

**[一致する]**/**[一致しない]** オプションによって、「URL Query Literal (URL クエリ リテラル)」一致条件が満たされる条件が決まります。
- **一致する**:要求に、指定したクエリ文字列と一致する URL クエリ文字列が含まれている必要があります。
- **一致しない**:要求に、指定したクエリ文字列と一致しない URL クエリ文字列が含まれている必要があります。

重要な情報: 

- この一致条件を満たすのは、クエリ文字列の完全一致だけです。
    
- クエリ文字列を比較する際の大文字と小文字の区別を制御するには、**[大文字と小文字を区別しない]** オプションを使用します。
    
- クエリ文字列値のテキストの先頭に疑問符 (?) を使用しないでください。
    
- 特定の文字には URL エンコードが必要です。 パーセント記号を使用して、次の文字を URL エンコードします。

   Character | URL エンコード
   ----------|---------
   スペース     | %20
   &         | %25

- キャッシュ設定の追跡方法が原因で、この一致条件は次の機能との互換性がありません。
   - Complete Cache Fill (完全キャッシュ入力)
   - Default Internal Max-Age (既定の内部最長有効期間)
   - Force Internal Max-Age (内部最長有効期間の強制)
   - Ignore Origin No-Cache (配信元のキャッシュなしを無視する)
   - Internal Max-Stale (内部 Max-Stale)

[先頭に戻る](#main)

</br>

---
### <a name="url-query-parameter"></a>URL Query Parameter (URL クエリ パラメーター)
指定したクエリ文字列パラメーターが含まれた要求を識別します。 このパラメーターは、指定したパターンに一致する値に設定されます。 要求 URL のクエリ文字列パラメーター (例: parameter=value) によって、この条件が満たされているかどうかが決まります。 この一致条件は、クエリ文字列パラメーターを名前で識別し、パラメーター値として 1 つ以上の値を受け入れます。 

**[一致する]**/**[一致しない]** オプションによって、「URL Query Parameter (URL クエリ パラメーター)」一致条件が満たされる条件が決まります。
- **一致する**:要求に指定したパラメーターが含まれており、値がこの一致条件で定義された 1 つ以上の値と一致する必要があります。
- **一致しない**:要求が次のいずれかの条件を満たす必要があります。
  - 指定したパラメーターが含まれていない。
  - 指定したパラメーターが含まれているが、値がこの一致条件で定義されたどの値とも一致しない。

この一致条件を使用すると、パラメーターの名前と値の組み合わせを簡単に指定できます。 柔軟性を高めるために、クエリ文字列パラメーターを照合する場合は、「[ URL Query Wildcard (URL クエリ ワイルドカード)](#url-query-wildcard)」一致条件を使用することを検討してください。

重要な情報: 
- この一致条件のインスタンスごとに指定できる URL クエリ パラメーター名は 1 つに限られます。
    
- パラメーター名を指定するときに、ワイルドカード値はサポートされていないため、パラメーター名の完全一致だけが比較の対象となります。
- パラメーター値には、[ワイルドカード値](cdn-rules-engine-reference.md#wildcard-values)を含めることができます。
   - 各パラメーター値パターンは、1 つ以上のアスタリスク (*) で構成できます。各アスタリスクは 1 つ以上の一連の文字と一致します。
   - 特定の文字には URL エンコードが必要です。 パーセント記号を使用して、次の文字を URL エンコードします。

       Character | URL エンコード
       ----------|---------
       スペース     | %20
       &         | %25

- 複数のクエリ文字列パラメーター値を指定するには、各値を単一のスペースで区切ります。 要求に、指定した名前と値の組み合わせのいずれかが含まれている場合に、この一致条件が満たされます。

   - 例 1:

     - Configuration:

       ValueA ValueB

     - この構成は、次のクエリ文字列パラメーターと一致します。

       Parameter1=ValueA
    
       Parameter1=ValueB

   - 例 2:

     - Configuration: 

        Value%20A Value%20B

     - この構成は、次のクエリ文字列パラメーターと一致します。

       Parameter1=Value%20A

       Parameter1=Value%20B

- この一致条件が満たされるのは、クエリ文字列の、指定した 1 つ以上の名前と値の組み合わせとの完全一致が存在する場合だけです。

   たとえば、前の例の構成を使用した場合、パラメーターの名前と値の組み合わせ "Parameter1=ValueAdd" は一致とは見なされません。 ただし、次のいずれかの値を指定した場合は、この名前と値の組み合わせと一致します。

   - ValueA ValueB ValueAdd
   - ValueA* ValueB

- クエリ文字列を比較する際の大文字と小文字の区別を制御するには、**[大文字と小文字を区別しない]** オプションを使用します。
    
- キャッシュ設定の追跡方法が原因で、この一致条件は次の機能との互換性がありません。
   - Complete Cache Fill (完全キャッシュ入力)
   - Default Internal Max-Age (既定の内部最長有効期間)
   - Force Internal Max-Age (内部最長有効期間の強制)
   - Ignore Origin No-Cache (配信元のキャッシュなしを無視する)
   - Internal Max-Stale (内部 Max-Stale)

#### <a name="sample-scenarios"></a>サンプル シナリオ
次の例は、このオプションが特定の状況でどのように機能するかを示しています。

Name  | 値 |  結果
------|-------|--------
User  | Joe   | 要求された URL のクエリ文字列が "?user=joe" の場合、このパターンに一致します。
User  | *     | 要求された URL のクエリ文字列に User パラメーターが含まれている場合、このパターンに一致します。
Email | Joe\* | 要求された URL のクエリ文字列に、"Joe" で始まる Email パラメーターが含まれている場合、このパターンに一致します。

[先頭に戻る](#main)

</br>

---
### <a name="url-query-regex"></a>URL Query Regex (URL クエリ正規表現)
指定したクエリ文字列パラメーターが含まれた要求を識別します。 このパラメーターは、指定した[正規表現](cdn-rules-engine-reference.md#regular-expressions)に一致する値に設定されます。

**[一致する]**/**[一致しない]** オプションによって、「URL Query Regex (URL クエリ正規表現)」一致条件が満たされる条件が決まります。
- **一致する**:要求に、指定した正規表現に一致する URL クエリ文字列が含まれている必要があります。
- **一致しない**:要求に、指定した正規表現に一致しない URL クエリ文字列が含まれている必要があります。

重要な情報: 
- この一致条件を満たすのは、指定した正規表現との完全一致だけです。
    
- クエリ文字列を比較する際の大文字と小文字の区別を制御するには、**[大文字と小文字を区別しない]** オプションを使用します。
    
- このオプションでは、クエリ文字列の疑問符 (?) 区切り記号の後の最初の文字からクエリ文字列が始まります。
    
- 特定の文字には URL エンコードが必要です。 パーセント記号を使用して、次の文字を URL エンコードします。

   Character | URL エンコード | 値
   ----------|--------------|------
   スペース     | %20          | \%20
   &         | %25          | \%25

   パーセント記号はエスケープする必要があります。

- 正規表現にバックスラッシュを含めるには、正規表現の特殊文字 (例: \^$.+) をダブル エスケープします。

   例: 

   値 | 解釈 
   ------|---------------
   \\+    | +
   \\\\+   | \\+

- キャッシュ設定の追跡方法が原因で、この一致条件は次の機能との互換性がありません。
   - Complete Cache Fill (完全キャッシュ入力)
   - Default Internal Max-Age (既定の内部最長有効期間)
   - Force Internal Max-Age (内部最長有効期間の強制)
   - Ignore Origin No-Cache (配信元のキャッシュなしを無視する)
   - Internal Max-Stale (内部 Max-Stale)


[先頭に戻る](#main)

</br>

---
### <a name="url-query-wildcard"></a>URL Query Wildcard (URL クエリ ワイルドカード)
指定値を要求のクエリ文字列と比較します。

**[一致する]**/**[一致しない]** オプションによって、「URL Query Wildcard (URL クエリ ワイルドカード)」一致条件が満たされる条件が決まります。
- **一致する**:要求に、指定したワイルドカード値と一致する URL クエリ文字列が含まれている必要があります。
- **一致しない**:要求に、指定したワイルドカード値と一致しない URL クエリ文字列が含まれている必要があります。

重要な情報: 
- このオプションでは、クエリ文字列の疑問符 (?) 区切り記号の後の最初の文字からクエリ文字列が始まります。
- パラメーター値には、[ワイルドカード値](cdn-rules-engine-reference.md#wildcard-values)を含めることができます。
   - 各パラメーター値パターンは、1 つ以上のアスタリスク (*) で構成できます。各アスタリスクは 1 つ以上の一連の文字と一致します。
   - 特定の文字には URL エンコードが必要です。 パーセント記号を使用して、次の文字を URL エンコードします。

     Character | URL エンコード
     ----------|---------
     スペース     | %20
     &         | %25

- 複数の値を指定する場合は、値ごとに単一の空白で区切ります。

   例: *Parameter1=ValueA* *ValueB* *Parameter1=ValueC&Parameter2=ValueD*

- この一致条件を満たすのは、指定した 1 以上のクエリ文字列パラメーターとの完全一致だけです。
    
- クエリ文字列を比較する際の大文字と小文字の区別を制御するには、**[大文字と小文字を区別しない]** オプションを使用します。
    
- キャッシュ設定の追跡方法が原因で、この一致条件は次の機能との互換性がありません。
   - Complete Cache Fill (完全キャッシュ入力)
   - Default Internal Max-Age (既定の内部最長有効期間)
   - Force Internal Max-Age (内部最長有効期間の強制)
   - Ignore Origin No-Cache (配信元のキャッシュなしを無視する)
   - Internal Max-Stale (内部 Max-Stale)

#### <a name="sample-scenarios"></a>サンプル シナリオ
次の例は、このオプションが特定の状況でどのように機能するかを示しています。

 Name                 | 説明
 ---------------------|------------
user=joe              | 要求された URL のクエリ文字列が "?user=joe" の場合、このパターンに一致します。
\*user=\* \*optout=\* | CDN URL クエリに user または optout パラメーターが含まれている場合、このパターンに一致します。

[先頭に戻る](#main)

</br>

## <a name="next-steps"></a>次の手順
* [Azure Content Delivery Network の概要](cdn-overview.md)
* [ルール エンジンのリファレンス](cdn-rules-engine-reference.md)
* [ルール エンジンの条件式](cdn-rules-engine-reference-conditional-expressions.md)
* [ルール エンジンの機能](cdn-rules-engine-reference-features.md)
* [規則エンジンを使用した既定の HTTP 動作のオーバーライド](cdn-rules-engine.md)

