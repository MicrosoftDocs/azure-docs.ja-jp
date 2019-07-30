---
title: Azure CDN from Verizon Premium ルール エンジンの一致条件 | Microsoft Docs
description: Azure Content Delivery Network from Verizon Premium ルール エンジンの一致条件に関するリファレンス ドキュメント。
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: 1660dca34b2f128ef5889145fcdeed0d2523b9bb
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593200"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-match-conditions"></a>Azure CDN from Verizon Premium ルール エンジンの一致条件

この記事では、Azure Content Delivery Network (CDN) from Verizon Premium [ルール エンジン](cdn-verizon-premium-rules-engine.md)で利用できる一致条件について詳しく説明します。

ルールの 2 番目の部分は、一致条件です。 一致条件とは、要求の特定の種類を識別するものであり、その種類に対して一連の機能が実行されます。

たとえば、以下のような場合に一致条件を使用できます。

- 特定の場所でコンテンツの要求をフィルター処理する。
- 特定の IP アドレスまたは国/地域から生成された要求をフィルター処理する。
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
[Country (国)](#country) | 指定した国/地域から送信される要求を識別します。

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

---

### <a name="always"></a>Always (常に)

「Always (常に)」一致条件では、一連の既定の機能がすべての要求に適用されます。

[先頭に戻る](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="as-number"></a>AS Number (AS 番号)

AS Number ネットワークは、自律システム番号 (ASN) で識別されています。 

**[一致する]** / **[一致しない]** オプションによって、「AS Number (AS 番号)」一致条件が満たされる条件が決まります。

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

[先頭に戻る](#reference-for-rules-engine-match-conditions)

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

[先頭に戻る](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="client-ip-address"></a>Client IP Address (現在の IP アドレス)

**[一致する]** / **[一致しない]** オプションによって、「Client IP Address (クライアント IP アドレス)」一致条件が満たされる条件が決まります。

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

[先頭に戻る](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="cookie-parameter"></a>Cookie Parameter (Cookie パラメーター)

**[一致する]** / **[一致しない]** オプションによって、「Cookie Parameter (Cookie パラメーター)」一致条件が満たされる条件が決まります。

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
  - Cookie 値には、[ワイルドカード値](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)を利用できます。
  - ワイルドカード値が指定されていない場合は、完全一致だけがこの一致条件を満たします。 たとえば、"Value" を指定すると "Value" と一致し、"Value1" や "Value2" とは一致しません。
  - 要求の Cookie 値に対して大文字と小文字を区別した比較を行うかどうかを制御するには、 **[大文字と小文字を区別しない]** オプションを使用します。
- キャッシュ設定の追跡方法が原因で、この一致条件は次の機能との互換性がありません。
  - Complete Cache Fill (完全キャッシュ入力)
  - Default Internal Max-Age (既定の内部最長有効期間)
  - Force Internal Max-Age (内部最長有効期間の強制)
  - Ignore Origin No-Cache (配信元のキャッシュなしを無視する)
  - Internal Max-Stale (内部 Max-Stale)

[先頭に戻る](#reference-for-rules-engine-match-conditions)
</br>

---

### <a name="cookie-parameter-regex"></a>Cookie Parameter Regex (Cookie パラメーターの正規表現)

「Cookie Parameter Regex (Cookie パラメーターの正規表現)」一致条件では、Cookie の名前と値を定義します。 [正規表現](cdn-verizon-premium-rules-engine-reference.md#regular-expressions)を使用して、必要な Cookie 値を定義できます。

**[一致する]** / **[一致しない]** オプションによって、「Cookie Parameter Regex (Cookie パラメーターの正規表現)」一致条件が満たされる条件が決まります。

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
  - 要求の Cookie 値に対して大文字と小文字を区別した比較を行うかどうかを制御するには、 **[大文字と小文字を区別しない]** オプションを使用します。
- キャッシュ設定の追跡方法が原因で、この一致条件は次の機能との互換性がありません。
  - Complete Cache Fill (完全キャッシュ入力)
  - Default Internal Max-Age (既定の内部最長有効期間)
  - Force Internal Max-Age (内部最長有効期間の強制)
  - Ignore Origin No-Cache (配信元のキャッシュなしを無視する)
  - Internal Max-Stale (内部 Max-Stale)

[先頭に戻る](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="country"></a>Country

国コードを利用して、国を指定できます。 

**[一致する]** / **[一致しない]** オプションによって、「Country (国)」一致条件が満たされる条件が決まります。

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

- Deny Access (403) (アクセス拒否 (403)) 機能: 国のフィルタリング機能の許可またはブロック部分をレプリケートするには、[Deny Access (403) (アクセス拒否 (403)) 機能](cdn-verizon-premium-rules-engine-reference-features.md#deny-access-403)を有効にします。

[先頭に戻る](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="customer-origin"></a>Customer Origin (顧客配信元)

重要な情報:

- 「Customer Origin (顧客配信元)」一致条件は、コンテンツが CDN URL 経由で要求されたか、選択した顧客配信元を参照するエッジ CNAME URL 経由で要求されたかに関係なく満たされます。
- ルールで参照される顧客配信元の構成は、[Customer Origin]\(顧客配信元\) ページから削除できません。 顧客配信元の構成を削除しようとしている場合は、事前に以下の構成で参照していないことを確認してください。
  - 「Customer Origin (顧客配信元)」一致条件
  - エッジ CNAME 構成
- AND IF ステートメントを使用して、特定の一致条件を結合しないでください。 たとえば、「Customer Origin (顧客配信元 配信元)」一致条件と「CDN (CDN 配信元)」一致条件を結合すると、一致不可能な一致パターンが作成されてしまいます。 このため、2 つの「Customer Origin (顧客配信元)」一致条件を AND IF ステートメントで結合することはできません。

[先頭に戻る](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="device"></a>Device

この「Device (デバイス)」一致条件では、モバイル デバイスからの要求をそのプロパティに基づいて識別します。 モバイル デバイスの検出は [WURFL](http://wurfl.sourceforge.net/) から実現します。 

**[一致する]** / **[一致しない]** オプションによって、「Device (デバイス)」一致条件が満たされる条件が決まります。

- **一致する**:要求者のデバイスが指定した値と一致する必要があります。 
- **一致しない**:要求者のデバイスが指定した値と一致しない必要があります。

重要な情報:

- 指定した値の大文字と小文字を区別するかどうかを指定するには、 **[大文字と小文字を区別しない]** オプションを使用します。
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
リテラル  | [リテラル値](cdn-verizon-premium-rules-engine-reference.md#literal-values)を使用して、ほとんどの文字が特別な意味を持たないようにするには、このオプションを選択します。
ワイルドカード | すべての[ワイルドカード文字] ([ワイルドカード値](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)) を利用するには、このオプションを選択します。
正規表現    | [正規表現](cdn-verizon-premium-rules-engine-reference.md#regular-expressions)を使用するには、このオプションを選択します。 正規表現は、文字のパターンを定義する際に便利です。

#### <a name="wurfl-capabilities"></a>WURFL 機能

WURFL 機能とは、モバイル デバイスを記述するカテゴリを指します。 選択した機能によって、要求の識別に使用されるモバイル デバイスの記述の種類が決まります。

次の表に、ルール エンジンの WURFL 機能とその変数を示します。

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

[先頭に戻る](#reference-for-rules-engine-match-conditions)

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

[先頭に戻る](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="referring-domain"></a>Referring Domain (参照ドメイン)

コンテンツが要求されたときの参照元に関連付けられたホスト名によって、「Referring Domain (参照ドメイン) 」条件に一致するかどうかが決まります。

**[一致する]** / **[一致しない]** オプションによって、「Referring Domain (参照ドメイン)」一致条件が満たされる条件が決まります。

- **一致する**:参照元ホスト名が指定した値と一致する必要があります。 
- **一致しない**:参照元ホスト名が指定した値と一致しない必要があります。

重要な情報:

- 複数のホスト名を指定する場合は、ホスト名ごとに単一の空白で区切ります。
- この一致条件では、[ワイルドカード値](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)がサポートされます。
- 指定した値にアスタリスクが含まれていない場合、値が参照元のホスト名と完全に一致している必要があります。 たとえば、"mydomain.com"　と指定すると、"www.mydomain.com"　とは一致しません。
- 大文字と小文字を区別した比較を行うかどうかを制御するには、 **[大文字と小文字を区別しない]** オプションを使用します。
- キャッシュ設定の追跡方法が原因で、この一致条件は次の機能との互換性がありません。
  - Complete Cache Fill (完全キャッシュ入力)
  - Default Internal Max-Age (既定の内部最長有効期間)
  - Force Internal Max-Age (内部最長有効期間の強制)
  - Ignore Origin No-Cache (配信元のキャッシュなしを無視する)
  - Internal Max-Stale (内部 Max-Stale)

[先頭に戻る](#reference-for-rules-engine-match-conditions)

</br>

---  

### <a name="request-header-literal"></a>Request Header Literal (要求ヘッダーのリテラル)

**[一致する]** / **[一致しない]** オプションによって、「Request Header Literal (要求ヘッダーのリテラル)」一致条件が満たされる条件が決まります。

- **一致する**:要求に、指定したヘッダーが含まれている必要があります。 値は、この一致条件に定義された値と一致している必要があります。
- **一致しない**:要求が次のいずれかの条件を満たす必要があります。
  - 指定したヘッダーを含まない。
  - 指定したヘッダーを含むが、値はこの一致条件に定義された値とは一致しない。
  
重要な情報:

- ヘッダー名の比較では常に、大文字と小文字は区別されません。 ヘッダー値を比較する際の大文字と小文字の区別を制御するには、 **[大文字と小文字を区別しない]** オプションを使用します。
- キャッシュ設定の追跡方法が原因で、この一致条件は次の機能との互換性がありません。
  - Complete Cache Fill (完全キャッシュ入力)
  - Default Internal Max-Age (既定の内部最長有効期間)
  - Force Internal Max-Age (内部最長有効期間の強制)
  - Ignore Origin No-Cache (配信元のキャッシュなしを無視する)
  - Internal Max-Stale (内部 Max-Stale)

[先頭に戻る](#reference-for-rules-engine-match-conditions)

</br>

---  

### <a name="request-header-regex"></a>Request Header Regex (要求ヘッダーの正規表現)

**[一致する]** / **[一致しない]** オプションによって、「Request Header Regex (要求ヘッダーの正規表現)」一致条件が満たされる条件が決まります。

- **一致する**:要求に、指定したヘッダーが含まれている必要があります。 値は、指定した[正規表現](cdn-verizon-premium-rules-engine-reference.md#regular-expressions)で定義されたパターンに一致している必要があります。
- **一致しない**:要求が次のいずれかの条件を満たす必要があります。
  - 指定したヘッダーを含まない。
  - 指定したヘッダーを含むが、値は指定した正規表現と一致しない。

重要な情報:

- ヘッダー名:
  - ヘッダー名の比較では、大文字と小文字は区別されません。
  - ヘッダー名のスペースは、"%20" に置き換えます。
- ヘッダー値:
  - ヘッダー値には、正規表現を利用できます。
  - ヘッダー値を比較する際の大文字と小文字の区別を制御するには、 **[大文字と小文字を区別しない]** オプションを使用します。
  - この一致条件が満たされるのは、ヘッダー値が指定した 1 つ以上のパターンと完全に一致する場合だけです。
- キャッシュ設定の追跡方法が原因で、この一致条件は次の機能との互換性がありません。
  - Complete Cache Fill (完全キャッシュ入力)
  - Default Internal Max-Age (既定の内部最長有効期間)
  - Force Internal Max-Age (内部最長有効期間の強制)
  - Ignore Origin No-Cache (配信元のキャッシュなしを無視する)
  - Internal Max-Stale (内部 Max-Stale)

[先頭に戻る](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-header-wildcard"></a>Request Header Wildcard (要求ヘッダーのワイルドカード)

**[一致する]** / **[一致しない]** オプションによって、「Request Header Wildcard (要求ヘッダーのワイルドカード)」一致条件が満たされる条件が決まります。

- **一致する**:要求に、指定したヘッダーが含まれている必要があります。 値は、この一致条件で定義されている 1 つ以上の値と一致する必要があります。
- **一致しない**:要求が次のいずれかの条件を満たす必要があります。
  - 指定したヘッダーを含まない。
  - 指定したヘッダーを含むが、値は指定した値と一致しない。
  
重要な情報:

- ヘッダー名:
  - ヘッダー名の比較では、大文字と小文字は区別されません。
  - ヘッダー名の空白は、"%20"　に置き換える必要があります。 この値を使用して、ヘッダー値に空白を指定することもできます。
- ヘッダー値:
  - ヘッダー値には、[ワイルドカード値](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)を利用できます。
  - ヘッダー値を比較する際の大文字と小文字の区別を制御するには、 **[大文字と小文字を区別しない]** オプションを使用します。
  - ヘッダー値が指定した 1 つ以上のパターンと完全に一致する場合に、この一致条件が満たされます。
  - 複数の値を指定する場合は、値ごとに単一の空白で区切ります。
- キャッシュ設定の追跡方法が原因で、この一致条件は次の機能との互換性がありません。
  - Complete Cache Fill (完全キャッシュ入力)
  - Default Internal Max-Age (既定の内部最長有効期間)
  - Force Internal Max-Age (内部最長有効期間の強制)
  - Ignore Origin No-Cache (配信元のキャッシュなしを無視する)
  - Internal Max-Stale (内部 Max-Stale)

[先頭に戻る](#reference-for-rules-engine-match-conditions)

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

[先頭に戻る](#reference-for-rules-engine-match-conditions)

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

[先頭に戻る](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-directory"></a>URL Path Directory (URL パス ディレクトリ)

要求された資産のファイル名を除いた相対パスで要求を識別します。

**[一致する]** / **[一致しない]** オプションによって、「URL Path Directory (URL パス ディレクトリ)」一致条件が満たされる条件が決まります。

- **一致する**:要求に、指定した URL パターンに一致する相対 URL パス (ファイル名を除く) が含まれている必要があります。
- **一致しない**:要求に、指定した URL パターンに一致しない相対 URL パス (ファイル名を除く) が含まれている必要があります。

重要な情報:

- URL の比較を、コンテンツ アクセス ポイントの前と後のどちらから開始するかを指定するには、 **[Relative to]\(基準\)** オプションを使用します。 コンテンツ アクセス ポイントは、パスの Verizon CDN ホスト名と要求された資産の相対パスの間の部分です (例: /800001/CustomerOrigin)。 場所は、サーバーの種類 (CDN や顧客配信元など) と顧客アカウント番号で識別されます。

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

- URL パスの指定には、[ワイルドカード値](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)を利用できます。

- 大文字と小文字を区別した比較を行うかどうかを制御するには、 **[大文字と小文字を区別しない]** オプションを使用します。

[先頭に戻る](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-extension"></a>URL Path Extension (URL パス拡張子)

要求された資産のファイル拡張子で要求を識別します。

**[一致する]** / **[一致しない]** オプションによって、「URL Path Extension (URL パス拡張子)」一致条件が満たされる条件が決まります。

- **一致する**:要求の URL に、指定したパターンに完全に一致するファイル拡張子が含まれている必要があります。

   たとえば、"htm" を指定した場合、"htm" 資産は一致しますが、"html" 資産は一致しません。  

- **一致しない**:URL 要求に、指定したパターンに一致しないファイル拡張子が含まれている必要があります。

重要な情報:

- **[値]** ボックスに、照合するファイル拡張子を指定します。 先頭にピリオドを使用しないでください。たとえば、".htm" ではなく "htm" を使用します。

- 大文字と小文字を区別した比較を行うかどうかを制御するには、 **[大文字と小文字を区別しない]** オプションを使用します。

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

[先頭に戻る](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-filename"></a>URL Path Filename (URL パス ファイル名)

要求された資産のファイル名で要求を識別します。 この一致条件では、ファイル名は、要求された資産の名前、ピリオド、ファイル拡張子で構成されます (例: index.html)。

**[一致する]** / **[一致しない]** オプションによって、「URL Path Filename (URL パス ファイル名)」一致条件が満たされる条件が決まります。

- **一致する**:要求の URL パスに、指定したパターンに一致するファイル名が含まれている必要があります。
- **一致しない**:要求の URL パスに、指定したパターンに一致しないファイル名が含まれている必要があります。

重要な情報:

- 大文字と小文字を区別した比較を行うかどうかを制御するには、 **[大文字と小文字を区別しない]** オプションを使用します。

- 複数のファイル拡張子を指定するには、各拡張子を単一のスペースで区切ります。

    例: index.htm index.html

- ファイル名の値のスペースは、"%20" に置き換えます。

- ファイル名の値には、[ワイルドカード値](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)を利用できます。 たとえば、各ファイル名パターンを 1 つ以上のアスタリスク (*) で構成できます。各アスタリスクは 1 つ以上の一連の文字と一致します。

- ワイルドカード文字が指定されていない場合は、完全一致だけがこの一致条件を満たします。

    たとえば、"presentation.ppt" を指定した場合、"presentation.ppt" という名前の資産は一致しますが、"presentation.pptx" という名前の資産は一致しません。

[先頭に戻る](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-literal"></a>URL Path Literal (URL パス リテラル)

要求の URL パス (ファイル名を含む) を指定した値と比較します。

**[一致する]** / **[一致しない]** オプションによって、「URL Path Literal (URL パス リテラル)」一致条件が満たされる条件が決まります。

- **一致する**:要求に、指定したパターンに一致する URL パスが含まれている必要があります。
- **一致しない**:要求に、指定したパターンに一致しない URL パスが含まれている必要があります。

重要な情報:

- URL 比較ポイントが、コンテンツ アクセス ポイントの前と後のどちらから始まるかを指定するには、 **[Relative to]\(基準\)** オプションを使用します。 

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
- 大文字と小文字を区別した比較を行うかどうかを制御するには、 **[大文字と小文字を区別しない]** オプションを使用します。
- この一致条件に指定した値は、クライアントが行った正確な要求の相対パスと比較されます。

- 特定のディレクトリに対するすべての要求を照合するには、「[URL Path Directory (URL パス ディレクトリ)](#url-path-directory)」または「[ URL Path Wildcard (URL パス ワイルドカード)](#url-path-wildcard)」一致条件を使用します。

[先頭に戻る](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-regex"></a>URL Path Regex (URL パス正規表現)

要求の URL パスを指定した[正規表現](cdn-verizon-premium-rules-engine-reference.md#regular-expressions)と比較します。

**[一致する]** / **[一致しない]** オプションによって、「URL Path Regex (URL パス正規表現)」一致条件が満たされる条件が決まります。

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
    
- 大文字と小文字を区別した比較を行うかどうかを制御するには、 **[大文字と小文字を区別しない]** オプションを使用します。
    
- URL パスのスペースは、"%20" に置き換える必要があります。

[先頭に戻る](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-wildcard"></a>URL Path Wildcard (URL パス ワイルドカード)

要求の相対 URL パスを、指定したワイルドカード パターンと比較します。

**[一致する]** / **[一致しない]** オプションによって、「URL Path Wildcard (URL パス ワイルドカード)」一致条件が満たされる条件が決まります。

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
     - CDN URL: http://wpc.0001.&lt ;domain&gt; /800001/CustomerOrigin/path/asset.htm
     - エッジ CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    追加情報:
    
     - URL パス (ルートを基準): /800001/CustomerOrigin/path/asset.htm
    
     - URL パス (配信元を基準): /path/asset.htm
    
- 複数の URL パスを指定するには、各パスを単一のスペースで区切ります。

   例: /marketing/asset.* /sales/*.htm

- URL のクエリ文字列は無視されます。
    
- 大文字と小文字を区別した比較を行うかどうかを制御するには、 **[大文字と小文字を区別しない]** オプションを使用します。
    
- URL パスのスペースは、"%20" に置き換えます。
    
- URL パスに指定する値には、[ワイルドカード値](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)を利用できます。 各 URL パス パターンには、1 つ以上のアスタリスク (*) を含めることができます。各アスタリスクは 1 つ以上の一連の文字と一致します。

#### <a name="sample-scenarios"></a>サンプル シナリオ

次の表のサンプル構成は、要求が指定した URL パターンに一致する場合に、この一致条件が満たされると想定しています。

値                   | 基準    | 結果 
------------------------|----------------|-------
\*/test.html \*/test.php  | ルートまたは配信元 | 任意のフォルダーの "test.html" または "test.php" という名前の資産の要求が、このパターンに一致します。
/80ABCD/origin/text/*   | ルート           | 要求された資産が次の条件を満たしている場合、このパターンに一致します。 <br />- "origin" という顧客配信元に存在する。 <br />- 相対パスが "text" というフォルダーで始まる (つまり、要求された資産が、"text" フォルダーまたは再帰サブフォルダーに存在する)
*/css/* */js/*          | ルートまたは配信元 | css または js フォルダーを含むすべての CDN URL またはエッジ CNAME URL が、このパターンに一致します。
*.jpg *.gif *.png       | ルートまたは配信元 | .jpg、.gif、または .png で終わる すべての CDN URL またはエッジ CNAME URL が、このパターンに一致します。 このパターンを指定する別の方法は、「[URL Path Extension (URL パス拡張子)](#url-path-extension)」一致条件を使用することです。
/images/\* /media/\*      | Origin (配信元)         | 相対パスが "images" または "media" フォルダーで始まる CDN URL またはエッジ CNAME URL が、このパターンに一致します。 <br />- CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/images/sales/event1.png<br />- サンプル エッジ CNAME URL: http:\//cdn.mydomain.com/images/sales/event1.png

[先頭に戻る](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-literal"></a>URL Query Literal (URL クエリ リテラル)

要求のクエリ文字列を指定値と比較します。

**[一致する]** / **[一致しない]** オプションによって、「URL Query Literal (URL クエリ リテラル)」一致条件が満たされる条件が決まります。

- **一致する**:要求に、指定したクエリ文字列と一致する URL クエリ文字列が含まれている必要があります。
- **一致しない**:要求に、指定したクエリ文字列と一致しない URL クエリ文字列が含まれている必要があります。

重要な情報:

- この一致条件を満たすのは、クエリ文字列の完全一致だけです。
    
- クエリ文字列を比較する際の大文字と小文字の区別を制御するには、 **[大文字と小文字を区別しない]** オプションを使用します。
    
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

[先頭に戻る](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-parameter"></a>URL Query Parameter (URL クエリ パラメーター)

指定したクエリ文字列パラメーターが含まれた要求を識別します。 このパラメーターは、指定したパターンに一致する値に設定されます。 要求 URL のクエリ文字列パラメーター (例: parameter=value) によって、この条件が満たされているかどうかが決まります。 この一致条件は、クエリ文字列パラメーターを名前で識別し、パラメーター値として 1 つ以上の値を受け入れます。 

**[一致する]** / **[一致しない]** オプションによって、「URL Query Parameter (URL クエリ パラメーター)」一致条件が満たされる条件が決まります。

- **一致する**:要求に指定したパラメーターが含まれており、値がこの一致条件で定義された 1 つ以上の値と一致する必要があります。
- **一致しない**:要求が次のいずれかの条件を満たす必要があります。
  - 指定したパラメーターが含まれていない。
  - 指定したパラメーターが含まれているが、値がこの一致条件で定義されたどの値とも一致しない。

この一致条件を使用すると、パラメーターの名前と値の組み合わせを簡単に指定できます。 柔軟性を高めるために、クエリ文字列パラメーターを照合する場合は、「[ URL Query Wildcard (URL クエリ ワイルドカード)](#url-query-wildcard)」一致条件を使用することを検討してください。

重要な情報:

- この一致条件のインスタンスごとに指定できる URL クエリ パラメーター名は 1 つに限られます。
    
- パラメーター名を指定するときに、ワイルドカード値はサポートされていないため、パラメーター名の完全一致だけが比較の対象となります。
- パラメーター値には、[ワイルドカード値](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)を含めることができます。
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

- クエリ文字列を比較する際の大文字と小文字の区別を制御するには、 **[大文字と小文字を区別しない]** オプションを使用します。
    
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

[先頭に戻る](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-regex"></a>URL Query Regex (URL クエリ正規表現)

指定したクエリ文字列パラメーターが含まれた要求を識別します。 このパラメーターは、指定した[正規表現](cdn-verizon-premium-rules-engine-reference.md#regular-expressions)に一致する値に設定されます。

**[一致する]** / **[一致しない]** オプションによって、「URL Query Regex (URL クエリ正規表現)」一致条件が満たされる条件が決まります。

- **一致する**:要求に、指定した正規表現に一致する URL クエリ文字列が含まれている必要があります。
- **一致しない**:要求に、指定した正規表現に一致しない URL クエリ文字列が含まれている必要があります。

重要な情報:

- この一致条件を満たすのは、指定した正規表現との完全一致だけです。
    
- クエリ文字列を比較する際の大文字と小文字の区別を制御するには、 **[大文字と小文字を区別しない]** オプションを使用します。
    
- このオプションでは、クエリ文字列の疑問符 (?) 区切り記号の後の最初の文字からクエリ文字列が始まります。
    
- 特定の文字には URL エンコードが必要です。 パーセント記号を使用して、次の文字を URL エンコードします。

   Character | URL エンコード | 値
   ----------|--------------|------
   スペース     | %20          | \%20
   &         | %25          | \%25

   Note that percentage symbols must be escaped.

- Double-escape special regular expression characters (for example, \^$.+) to include a backslash in the regular expression.

   For example:

   Value | Interpreted As 
   ------|---------------
   \\+    | +
   \\\\+   | \\+

- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Wildcard

Compares the specified value(s) against the request's query string.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Wildcard match condition is met.

- **Matches**: Requires the request to contain a URL query string that matches the specified wildcard value.
- **Does Not Match**: Requires the request to contain a URL query string that does not match the specified wildcard value.

Key information:

- For the purposes of this option, a query string starts with the first character after the question mark (?) delimiter for the query string.
- Parameter values can include [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values):
   - Each parameter value pattern can consist of one or more asterisks (*), where each asterisk can match a sequence of one or more characters.
   - Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

     Character | URL Encoding
     ----------|---------
     Space     | %20
     &         | %25

- Specify multiple values by delimiting each one with a single space.

   For example: *Parameter1=ValueA* *ValueB* *Parameter1=ValueC&Parameter2=ValueD*

- Only exact matches to at least one of the specified query string patterns satisfy this match condition.
    
- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

#### Sample scenarios

The following example demonstrates how this option works in specific situations:

 Name                 | Description
 ---------------------|------------
user=joe              | This pattern is matched when the query string for a requested URL is "?user=joe."
\*user=\* \*optout=\* | This pattern is matched when the CDN URL query contains either the user or optout parameter.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

## Next steps

- [Azure Content Delivery Network overview](cdn-overview.md)
- [Rules engine reference](cdn-verizon-premium-rules-engine-reference.md)
- [Rules engine conditional expressions](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Rules engine features](cdn-verizon-premium-rules-engine-reference-features.md)
- [Overriding default HTTP behavior using the rules engine](cdn-verizon-premium-rules-engine.md)\`20
title: Azure CDN from Verizon Premium rules engine match conditions | Microsoft Docs
description: Reference documentation for Azure Content Delivery Network from Verizon Premium rules engine match conditions.
services: cdn
author: mdgattuso

ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus

---

# Azure CDN from Verizon Premium rules engine match conditions

This article lists detailed descriptions of the available match conditions for the Azure Content Delivery Network (CDN) from Verizon Premium [rules engine](cdn-verizon-premium-rules-engine.md).

The second part of a rule is the match condition. A match condition identifies specific types of requests for which a set of features will be performed.

For example, you can use a match condition to:

- Filter requests for content at a particular location.
- Filter requests generated from a particular IP address or country/region.
- Filter requests by header information.

## Always match condition

The Always match condition applies a default set of features to all requests.

Name | Purpose
-----|--------
[Always](#always) | Applies a default set of features to all requests.

## Device match condition

The Device match condition identifies requests made from a mobile device based on its properties.  

Name | Purpose
-----|--------
[Device](#device) | Identifies requests made from a mobile device based on its properties.

## Location match conditions

The Location match conditions identify requests based on the requester's location.

Name | Purpose
-----|--------
[AS Number](#as-number) | Identifies requests that originate from a particular network.
[Country](#country) | Identifies requests that originate from the specified countries/regions.

## Origin match conditions

The Origin match conditions identify requests that point to Content Delivery Network storage or a customer origin server.

Name | Purpose
-----|--------
[CDN Origin](#cdn-origin) | Identifies requests for content stored in Content Delivery Network storage.
[Customer Origin](#customer-origin) | Identifies requests for content stored on a specific customer origin server.

## Request match conditions

The Request match conditions identify requests based on their properties.

Name | Purpose
-----|--------
[Client IP Address](#client-ip-address) | Identifies requests that originate from a particular IP address.
[Cookie Parameter](#cookie-parameter) | Checks the cookies associated with each request for the specified value.
[Cookie Parameter Regex](#cookie-parameter-regex) | Checks the cookies associated with each request for the specified regular expression.
[Edge Cname](#edge-cname) | Identifies requests that point to a specific edge CNAME.
[Referring Domain](#referring-domain) | Identifies requests that were referred from the specified host names.
[Request Header Literal](#request-header-literal) | Identifies requests that contain the specified header set to a specified value.
[Request Header Regex](#request-header-regex) | Identifies requests that contain the specified header set to a value that matches the specified regular expression.
[Request Header Wildcard](#request-header-wildcard) | Identifies requests that contain the specified header set to a value that matches the specified pattern.
[Request Method](#request-method) | Identifies requests by their HTTP method.
[Request Scheme](#request-scheme) | Identifies requests by their HTTP protocol.

## URL match conditions

The URL match conditions identify requests based on their URLs.

Name | Purpose
-----|--------
[URL Path Directory](#url-path-directory) | Identifies requests by their relative path.
[URL Path Extension](#url-path-extension) | Identifies requests by their file name extension.
[URL Path Filename](#url-path-filename) | Identifies requests by their file name.
[URL Path Literal](#url-path-literal) | Compares a request's relative path to the specified value.
[URL Path Regex](#url-path-regex) | Compares a request's relative path to the specified regular expression.
[URL Path Wildcard](#url-path-wildcard) | Compares a request's relative path to the specified pattern.
[URL Query Literal](#url-query-literal) | Compares a request's query string to the specified value.
[URL Query Parameter](#url-query-parameter) | Identifies requests that contain the specified query string parameter set to a value that matches a specified pattern.
[URL Query Regex](#url-query-regex) | Identifies requests that contain the specified query string parameter set to a value that matches a specified regular expression.
[URL Query Wildcard](#url-query-wildcard) | Compares the specified value against the request's query string.

## Reference for rules engine match conditions

---

### Always

The Always match condition applies a default set of features to all requests.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### AS Number

The AS Number network is defined by its autonomous system number (ASN). 

The **Matches**/**Does Not Match** option determines the conditions under which the AS Number match condition is met:

- **Matches**: Requires that the ASN of the client network matches one of the specified ASNs. 
- **Does Not Match**: Requires that the ASN of the client network does not match any of the specified ASNs.

Key information:

- Specify multiple ASNs by delimiting each one with a single space. For example, 64514 64515 matches requests that arrive from either 64514 or 64515.
- Certain requests might not return a valid ASN. A question mark (?) will match requests for which a valid ASN could not be determined.
- Specify the entire ASN for the desired network. Partial values will not be matched.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### CDN Origin

The CDN Origin match condition is met when both of the following conditions are met:

- Content from CDN storage was requested.
- The request URI uses the type of content access point (for example, /000001) that's defined in this match condition:
  - CDN URL: The request URI must contain the selected content access point.
  - Edge CNAME URL: The corresponding edge CNAME configuration must point to the selected content access point.
  
Key information:

- The content access point identifies the service that should serve the requested content.
- Don't use an AND IF statement to combine certain match conditions. For example, combining a CDN Origin match condition with a Customer Origin match condition would create a match pattern that could never be matched. For this reason, two CDN Origin match conditions cannot be combined through an AND IF statement.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Client IP Address

The **Matches**/**Does Not Match** option determines the conditions under which the Client IP Address match condition is met:

- **Matches**: Requires that the client's IP address matches one of the specified IP addresses. 
- **Does Not Match**: Requires that the client's IP address does not match any of the specified IP addresses. 

Key information:

- Use CIDR notation.
- Specify multiple IP addresses and/or IP address blocks by delimiting each one with a single space. For example:
  - **IPv4 example**: 1.2.3.4 10.20.30.40 matches any requests that arrive from either address 1.2.3.4 or 10.20.30.40.
  - **IPv6 example**: 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 matches any requests that arrive from either address 1:2:3:4:5:6:7:8 or 10:20:30:40:50:60:70:80.
- The syntax for an IP address block is the base IP address followed by a forward slash and the prefix size. For example:
  - **IPv4 example**: 5.5.5.64/26 matches any requests that arrive from addresses 5.5.5.64 through 5.5.5.127.
  - **IPv6 example**: 1:2:3:/48 matches any requests that arrive from addresses 1:2:3:0:0:0:0:0 through 1:2:3:ffff:ffff:ffff:ffff:ffff.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Cookie Parameter

The **Matches**/**Does Not Match** option determines the conditions under which the Cookie Parameter match condition is met.

- **Matches**: Requires a request to contain the specified cookie with a value that matches at least one of the values that are defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified cookie.
  - It contains the specified cookie, but its value does not match any of the values that are defined in this match condition.
  
Key information:

- Cookie name:
  - Because wildcard values, including asterisks (*), are not supported when you're specifying a cookie name, only exact cookie name matches are eligible for comparison.
  - Only a single cookie name can be specified per instance of this match condition.
  - Cookie name comparisons are case-insensitive.
- Cookie value:
  - Specify multiple cookie values by delimiting each one with a single space.
  - A cookie value can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - If a wildcard value has not been specified, then only an exact match will satisfy this match condition. For example, specifying "Value" will match "Value," but not "Value1" or "Value2."
  - Use the **Ignore Case** option to control whether a case-sensitive comparison is made against the request's cookie value.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)
</br>

---

### Cookie Parameter Regex

The Cookie Parameter Regex match condition defines a cookie name and value. You can use [regular expressions](cdn-verizon-premium-rules-engine-reference.md#regular-expressions) to define the desired cookie value.

The **Matches**/**Does Not Match** option determines the conditions under which the Cookie Parameter Regex match condition is met.

- **Matches**: Requires a request to contain the specified cookie with a value that matches the specified regular expression.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified cookie.
  - It contains the specified cookie, but its value does not match the specified regular expression.
  
Key information:

- Cookie name:
  - Because regular expressions and wildcard values, including asterisks (*), are not supported when you're specifying a cookie name, only exact cookie name matches are eligible for comparison.
  - Only a single cookie name can be specified per instance of this match condition.
  - Cookie name comparisons are case-insensitive.
- Cookie value:
  - A cookie value can take advantage of regular expressions.
  - Use the **Ignore Case** option to control whether a case-sensitive comparison is made against the request's cookie value.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Country

You can specify a country through its country code. 

The **Matches**/**Does Not Match** option determines the conditions under which the Country match condition is met:

- **Matches**: Requires the request to contain the specified country code values. 
- **Does Not Match**: Requires that the request does not contain the specified country code values.

Key information:

- Specify multiple country codes by delimiting each one with a single space.
- Wildcards are not supported when you're specifying a country code.
- The "EU" and "AP" country codes do not encompass all IP addresses in those regions.
- Certain requests might not return a valid country code. A question mark (?) will match requests for which a valid country code could not be determined.
- Country codes are case-sensitive.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

#### Implementing Country Filtering by using the rules engine

This match condition allows you to perform a multitude of customizations based on the location from which a request originated. For example, the behavior of the Country Filtering feature can be replicated through the following configuration:

- URL Path Wildcard match: Set the [URL Path Wildcard match condition](#url-path-wildcard) to the directory that will be secured. 
    Append an asterisk to the end of the relative path to ensure that access to all of its children will be restricted by this rule.

- Country match: Set the Country match condition to the desired set of countries.
  - Allow: Set the Country match condition to **Does Not Match** to allow only the specified countries access to content stored in the location defined by the URL Path Wildcard match condition.
  - Block: Set the Country match condition to **Matches** to block the specified countries from accessing content stored in the location defined by the URL Path Wildcard match condition.

- Deny Access (403) Feature: Enable the [Deny Access (403) feature](cdn-verizon-premium-rules-engine-reference-features.md#deny-access-403) to replicate the allow or block portion of the Country Filtering feature.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Customer Origin

Key information:

- The Customer Origin match condition is met regardless of whether content is requested through a CDN URL or an edge CNAME URL that points to the selected customer origin.
- A customer origin configuration that's referenced by a rule cannot be deleted from the Customer Origin page. Before you attempt to delete a customer origin configuration, make sure that the following configurations do not reference it:
  - A Customer Origin match condition
  - An edge CNAME configuration
- Don't use an AND IF statement to combine certain match conditions. For example, combining a Customer Origin match condition with a CDN Origin match condition would create a match pattern that could never be matched. For this reason, two Customer Origin match conditions cannot be combined through an AND IF statement.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Device

The Device match condition identifies requests made from a mobile device based on its properties. Mobile device detection is achieved through [WURFL](http://wurfl.sourceforge.net/). 

The **Matches**/**Does Not Match** option determines the conditions under which the Device match condition is met:

- **Matches**: Requires the requester's device to match the specified value. 
- **Does Not Match**: Requires that the requester's device does not match the specified value.

Key information:

- Use the **Ignore Case** option to specify whether the specified value is case-sensitive.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

#### String Type

A WURFL capability typically accepts any combination of numbers, letters, and symbols. Due to the flexible nature of this capability, you must choose how the value associated with this match condition is interpreted. The following table describes the available set of options:

Type     | Description
---------|------------
Literal  | Select this option to prevent most characters from taking on special meaning by using their [literal value](cdn-verizon-premium-rules-engine-reference.md#literal-values).
Wildcard | Select this option to take advantage of all [wildcard characters]([wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
Regex    | Select this option to use [regular expressions](cdn-verizon-premium-rules-engine-reference.md#regular-expressions). Regular expressions are useful for defining a pattern of characters.

#### WURFL capabilities

A WURFL capability refers to a category that describes mobile devices. The selected capability determines the type of mobile device description that is used to identify requests.

The following table lists WURFL capabilities and their variables for the rules engine.

> [!NOTE]
> The following variables are supported in the **Modify Client Request Header** and **Modify Client Response Header** features.

Capability | Variable | Description | Sample values
-----------|----------|-------------|----------------
Brand Name | %{wurfl_cap_brand_name} | A string that indicates the brand name of the device. | Samsung
Device OS | %{wurfl_cap_device_os} | A string that indicates the operating system installed on the device. | IOS
Device OS Version | %{wurfl_cap_device_os_version} | A string that indicates the version number of the operating system installed on the device. | 1.0.1
Dual Orientation | %{wurfl_cap_dual_orientation} | A Boolean that indicates whether the device supports dual orientation. | true
HTML Preferred DTD | %{wurfl_cap_html_preferred_dtd} | A string that indicates the mobile device's preferred document type definition (DTD) for HTML content. | none<br/>xhtml_basic<br/>html5
Image Inlining | %{wurfl_cap_image_inlining} | A Boolean that indicates whether the device supports Base64 encoded images. | false
Is Android | %{wurfl_vcap_is_android} | A Boolean that indicates whether the device uses the Android OS. | true
Is IOS | %{wurfl_vcap_is_ios} | A Boolean that indicates whether the device uses iOS. | false
Is Smart TV | %{wurfl_cap_is_smarttv} | A Boolean that indicates whether the device is a smart TV. | false
Is Smartphone | %{wurfl_vcap_is_smartphone} | A Boolean that indicates whether the device is a smartphone. | true
Is Tablet | %{wurfl_cap_is_tablet} | A Boolean that indicates whether the device is a tablet. This description is  OS-independent. | true
Is Wireless Device | %{wurfl_cap_is_wireless_device} | A Boolean that indicates whether the device is considered a wireless device. | true
Marketing Name | %{wurfl_cap_marketing_name} | A string that indicates the device's marketing name. | BlackBerry 8100 Pearl
Mobile Browser | %{wurfl_cap_mobile_browser} | A string that indicates the browser that's used to request content from the device. | Chrome
Mobile Browser Version | %{wurfl_cap_mobile_browser_version} | A string that indicates the version of the browser that's used to request content from the device. | 31
Model Name | %{wurfl_cap_model_name} | A string that indicates the device's model name. | s3
Progressive Download | %{wurfl_cap_progressive_download} | A Boolean that indicates whether the device supports the playback of audio and video while it is still being downloaded. | true
Release Date | %{wurfl_cap_release_date} | A string that indicates the year and month on which the device was added to the WURFL database.<br/><br/>Format: `yyyy_mm` | 2013_december
Resolution Height | %{wurfl_cap_resolution_height} | An integer that indicates the device's height in pixels. | 768
Resolution Width | %{wurfl_cap_resolution_width} | An integer that indicates the device's width in pixels. | 1024

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Edge Cname

Key information:

- The list of available edge CNAMEs is limited to those edge CNAMEs that have been configured on the Edge CNAMEs page for the platform on which the rules engine is being configured.
- Before you attempt to delete an edge CNAME configuration, make sure that an Edge Cname match condition does not reference it. Edge CNAME configurations that have been defined in a rule cannot be deleted from the Edge CNAMEs page.
- Don't use an AND IF statement to combine certain match conditions. For example, combining an Edge Cname match condition with a Customer Origin match condition would create a match pattern that could never be matched. For this reason, two Edge Cname match conditions cannot be combined through an AND IF statement.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Referring Domain

The host name associated with the referrer through which content was requested determines whether the Referring Domain condition is met.

The **Matches**/**Does Not Match** option determines the conditions under which the Referring Domain match condition is met:

- **Matches**: Requires the referring host name to match the specified values. 
- **Does Not Match**: Requires that the referring host name does not match the specified value.

Key information:

- Specify multiple host names by delimiting each one with a single space.
- This match condition supports [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
- If the specified value does not contain an asterisk, it must be an exact match for the referrer's host name. For example, specifying "mydomain.com" would not match "www.mydomain.com."
- Use the **Ignore Case** option to control whether a case-sensitive comparison is made.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---  

### Request Header Literal

The **Matches**/**Does Not Match** option determines the conditions under which the Request Header Literal match condition is met.

- **Matches**: Requires the request to contain the specified header. Its value must match the one that's defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified header.
  - It contains the specified header, but its value does not match the one that's defined in this match condition.
  
Key information:

- Header name comparisons are always case-insensitive. Use the **Ignore Case** option to control the case-sensitivity of header value comparisons.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---  

### Request Header Regex

The **Matches**/**Does Not Match** option determines the conditions under which the Request Header Regex match condition is met.

- **Matches**: Requires the request to contain the specified header. Its value must match the pattern that's defined in the specified [regular expression](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified header.
  - It contains the specified header, but its value does not match the specified regular expression.

Key information:

- Header name:
  - Header name comparisons are case-insensitive.
  - Replace spaces in the header name with "%20."
- Header value:
  - A header value can take advantage of regular expressions.
  - Use the **Ignore Case** option to control the case-sensitivity of header value comparisons.
  - The match condition is met only when a header value exactly matches at least one of the specified patterns.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Request Header Wildcard

The **Matches**/**Does Not Match** option determines the conditions under which the Request Header Wildcard match condition is met.

- **Matches**: Requires the request to contain the specified header. Its value must match at least one of the values that are defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified header.
  - It contains the specified header, but its value does not match any of the specified values.
  
Key information:

- Header name:
  - Header name comparisons are case-insensitive.
  - Spaces in the header name should be replaced with "%20." You can also use this value to specify spaces in a header value.
- Header value:
  - A header value can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - Use the **Ignore Case** option to control the case-sensitivity of header value comparisons.
  - This match condition is met when a header value exactly matches to at least one of the specified patterns.
  - Specify multiple values by delimiting each one with a single space.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Request Method

The Request Method match condition is met only when assets are requested through the selected request method. The available request methods are:

- GET
- HEAD
- POST
- OPTIONS
- PUT
- DELETE
- TRACE
- CONNECT

Key information:

- By default, only the GET request method can generate cached content on the network. All other request methods are proxied through the network.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Request Scheme

The Request Scheme match condition is met only when assets are requested through the selected protocol. The available protocols are:

- HTTP
- HTTPS

Key information:

- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Directory

Identifies a request by its relative path, which excludes the file name of the requested asset.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Directory match condition is met.

- **Matches**: Requires the request to contain a relative URL path, excluding the file name, that matches the specified URL pattern.
- **Does Not Match**: Requires the request to contain a relative URL path, excluding file name, that does not match the specified URL pattern.

Key information:

- Use the **Relative to** option to specify whether the URL comparison starts before or after the content access point. The content access point is the portion of the path that appears between the Verizon CDN hostname and the relative path to the requested asset (for example, /800001/CustomerOrigin). It identifies a location by server type (for example, CDN or customer origin) and your customer account number.

   The following values are available for the **Relative to** option:
  - **Root**: Indicates that the URL comparison point begins directly after the CDN hostname. 

  For example: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder**/index.htm

  - **Origin**: Indicates that the URL comparison point begins after the content access point (for example, /000001 or /800001/myorigin). Because the \*.azureedge.net CNAME is created relative to the origin directory on the Verizon CDN hostname by default, Azure CDN users should use the **Origin** value. 

  For example: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder**/index.htm 

  This URL points to the following Verizon CDN hostname: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder**/index.htm

- An edge CNAME URL is rewritten to a CDN URL prior to the URL comparison.

    For example, both of the following URLs point to the same asset and therefore have the same URL path.
  - CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
    
  - Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    Additional information:
  - Custom domain: https:\//my.domain.com/path/asset.htm
    
    - URL path (relative to root): /800001/CustomerOrigin/path/
    
    - URL path (relative to origin): /path/

- The portion of the URL that is used for the URL comparison ends just before the file name of the requested asset. A trailing forward slash is the last character in this type of path.

- Replace any spaces in the URL path pattern with "%20."

- Each URL path pattern can contain one or more asterisks (*), where each asterisk matches a sequence of one or more characters.

- Specify multiple URL paths in the pattern by delimiting each one with a single space.

    For example: */sales/ */marketing/

- A URL path specification can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).

- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Extension

Identifies requests by the file extension of the requested asset.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Extension match condition is met.

- **Matches**: Requires the URL of the request to contain a file extension that exactly matches the specified pattern.

   For example, if you specify "htm", "htm" assets are matched, but not "html" assets.  

- **Does Not Match**: Requires the URL request to contain a file extension that does not match the specified pattern.

Key information:

- Specify the file extensions to match in the **Value** box. Do not include a leading period; for example, use htm instead of .htm.

- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.

- Specify multiple file extensions by delimiting each extension with a single space. 

    For example: htm html

- For example, specifying "htm" matches "htm" assets, but not "html" assets.

#### Sample Scenario

The following sample configuration assumes that this match condition is met when a request matches one of the specified extensions.

Value specification: asp aspx php html

This match condition is met when it finds URLs that end with the following extensions:

- .asp
- .aspx
- .php
- .html

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Filename

Identifies requests by the file name of the requested asset. For the purposes of this match condition, a file name consists of the name of the requested asset, a period, and the file extension (for example, index.html).

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Filename match condition is met.

- **Matches**: Requires the request to contain a file name in its URL path that matches the specified pattern.
- **Does Not Match**: Requires the request to contain a file name in its URL path that does not match the specified pattern.

Key information:

- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.

- To specify multiple file extensions, separate each extension with a single space.

    For example: index.htm index.html

- Replace spaces in a file name value with "%20."

- A file name value can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). For example, each file name pattern can consist of one or more asterisks (*), where each asterisk matches a sequence of one or more characters.

- If wildcard characters are not specified, then only an exact match will satisfy this match condition.

    For example, specifying "presentation.ppt" matches an asset named "presentation.ppt," but not one named "presentation.pptx."

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Literal

Compares a request's URL path, including file name, to the specified value.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Literal match condition is met.

- **Matches**: Requires the request to contain a URL path that matches the specified pattern.
- **Does Not Match**: Requires the request to contain a URL path that does not match the specified pattern.

Key information:

- Use the **Relative to** option to specify whether the URL comparison point begins before or after the content access point. 

    The following values are available for the **Relative to** option:
  - **Root**: Indicates that the URL comparison point begins directly after the CDN hostname.

    For example: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder/index.htm**

  - **Origin**: Indicates that the URL comparison point begins after the content access point (for example, /000001 or /800001/myorigin). Because the \*.azureedge.net CNAME is created relative to the origin directory on the Verizon CDN hostname by default, Azure CDN users should use the **Origin** value. 

    For example: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder/index.htm**

  This URL points to the following Verizon CDN hostname: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder/index.htm**

- An edge CNAME URL is rewritten to a CDN URL prior to a URL comparison.

For example, both of the following URLs point to the same asset and therefore have the same URL path:

- CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
- Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm

    Additional information:
    
    - URL path (relative to root): /800001/CustomerOrigin/path/asset.htm
   
    - URL path (relative to origin): /path/asset.htm

- Query strings in the URL are ignored.
- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.
- The value specified for this match condition is compared against the relative path of the exact request made by the client.

- To match all requests made to a particular directory, use the [URL Path Directory](#url-path-directory) or the [URL Path Wildcard](#url-path-wildcard) match condition.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Regex

Compares a request's URL path to the specified [regular expression](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Regex match condition is met.

- **Matches**: Requires the request to contain a URL path that matches the specified regular expression.
- **Does Not Match**: Requires the request to contain a URL path that does not match the specified regular expression.

Key information:

- An edge CNAME URL is rewritten to a CDN URL prior to URL comparison.

    For example, both URLs point to the same asset and therefore have the same URL path.

     - CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm

     - Edge CNAME URL: http:\//my.domain.com/path/asset.htm

    Additional information:
    
     - URL path: /800001/CustomerOrigin/path/asset.htm

- Query strings in the URL are ignored.
    
- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.
    
- Spaces in the URL path should be replaced with "%20."

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Wildcard

Compares a request's relative URL path to the specified wildcard pattern.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Wildcard match condition is met.

- **Matches**: Requires the request to contain a URL path that matches the specified wildcard pattern.
- **Does Not Match**: Requires the request to contain a URL path that does not match the specified wildcard pattern.

Key information:

- **Relative to** option: This option determines whether the URL comparison point begins before or after the content access point.

   This option can have the following values:
     - **Root**: Indicates that the URL comparison point begins directly after the CDN hostname.

       For example: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder/index.htm**

     - **Origin**: Indicates that the URL comparison point begins after the content access point (for example, /000001 or /800001/myorigin). Because the \*.azureedge.net CNAME is created relative to the origin directory on the Verizon CDN hostname by default, Azure CDN users should use the **Origin** value. 

       For example: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder/index.htm**

     This URL points to the following Verizon CDN hostname: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder/index.htm**

- An edge CNAME URL is rewritten to a CDN URL prior to URL comparison.

    For example, both of the following URLs point to the same asset and therefore have the same URL path:
     - CDN URL: http://wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
     - Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    Additional information:
    
     - URL path (relative to root): /800001/CustomerOrigin/path/asset.htm
    
     - URL path (relative to origin): /path/asset.htm
    
- Specify multiple URL paths by delimiting each one with a single space.

   For example: /marketing/asset.* /sales/*.htm

- Query strings in the URL are ignored.
    
- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.
    
- Replace spaces in the URL path with "%20."
    
- The value specified for a URL path can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). Each URL path pattern can contain one or more asterisks (*), where each asterisk can match a sequence of one or more characters.

#### Sample Scenarios

The sample configurations in the following table assume that this match condition is met when a request matches the specified URL pattern:

Value                   | Relative to    | Result 
------------------------|----------------|-------
*/test.html */test.php  | Root or Origin | This pattern is matched by requests for assets named "test.html" or "test.php" in any folder.
/80ABCD/origin/text/*   | Root           | This pattern is matched when the requested asset meets the following criteria: <br />- It must reside on a customer origin called "origin." <br />- The relative path must start with a folder called "text." That is, the requested asset can either reside in the "text" folder or one of its recursive subfolders.
*/css/* */js/*          | Root or Origin | This pattern is matched by all CDN or edge CNAME URLs that contain a css or js folder.
*.jpg *.gif *.png       | Root or Origin | This pattern is matched by all CDN or edge CNAME URLs ending with .jpg, .gif, or .png. An alternative way to specify this pattern is with the [URL Path Extension match condition](#url-path-extension).
/images/* /media/*      | Origin         | This pattern is matched by CDN or edge CNAME URLs whose relative path starts with an "images" or "media" folder. <br />- CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/images/sales/event1.png<br />- Sample edge CNAME URL: http:\//cdn.mydomain.com/images/sales/event1.png

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Literal

Compares a request's query string to the specified value.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Literal match condition is met.

- **Matches**: Requires the request to contain a URL query string that matches the specified query string.
- **Does Not Match**: Requires the request to contain a URL query string that does not match the specified query string.

Key information:

- Only exact query string matches satisfy this match condition.
    
- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- Do not include a leading question mark (?) in the query string value text.
    
- Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

   Character | URL Encoding
   ----------|---------
   Space     | %20
   &         | %25

- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Parameter

Identifies requests that contain the specified query string parameter. This parameter is set to a value that matches a specified pattern. Query string parameters (for example, parameter=value) in the request URL determine whether this condition is met. This match condition identifies a query string parameter by its name and accepts one or more values for the parameter value. 

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Parameter match condition is met.

- **Matches**: Requires a request to contain the specified parameter with a value that matches at least one of the values that are defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified parameter.
  - It contains the specified parameter, but its value does not match any of the values that are defined in this match condition.

This match condition provides an easy way to specify parameter name/value combinations. For more flexibility if you are matching a query string parameter, consider using the [URL Query Wildcard](#url-query-wildcard) match condition.

Key information:

- Only a single URL query parameter name can be specified per instance of this match condition.
    
- Because wildcard values are not supported when a parameter name is specified, only exact parameter name matches are eligible for comparison.
- Parameter value(s) can include [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
   - Each parameter value pattern can consist of one or more asterisks (*), where each asterisk can match a sequence of one or more characters.
   - Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

       Character | URL Encoding
       ----------|---------
       Space     | %20
       &         | %25

- Specify multiple query string parameter values by delimiting each one with a single space. This match condition is met when a request contains one of the specified name/value combinations.

   - Example 1:

     - Configuration:

       ValueA ValueB

     - This configuration matches the following query string parameters:

       Parameter1=ValueA
    
       Parameter1=ValueB

   - Example 2:

     - Configuration: 

        Value%20A Value%20B

     - This configuration matches the following query string parameters:

       Parameter1=Value%20A

       Parameter1=Value%20B

- This match condition is met only when there is an exact match to at least one of the specified query string name/value combinations.

   For example, if you use the configuration in the previous example, the parameter name/value combination "Parameter1=ValueAdd" would not be considered a match. However, if you specify either of the following values, it will match that name/value combination:

   - ValueA ValueB ValueAdd
   - ValueA* ValueB

- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

#### Sample scenarios

The following example demonstrates how this option works in specific situations:

Name  | Value |  Result
------|-------|--------
User  | Joe   | This pattern is matched when the query string for a requested URL is "?user=joe."
User  | *     | This pattern is matched when the query string for a requested URL contains a User parameter.
Email | Joe\* | This pattern is matched when the query string for a requested URL contains an Email parameter that starts with "Joe."

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Regex

Identifies requests that contain the specified query string parameter. This parameter is set to a value that matches a specified [regular expression](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Regex match condition is met.

- **Matches**: Requires the request to contain a URL query string that matches the specified regular expression.
- **Does Not Match**: Requires the request to contain a URL query string that does not match the specified regular expression.

Key information:

- Only exact matches to the specified regular expression satisfy this match condition.
    
- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- For the purposes of this option, a query string starts with the first character after the question mark (?) delimiter for the query string.
    
- Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

   Character | URL Encoding | Value
   ----------|--------------|------
   Space     | %20          | \%20
   &         | %25          | \%25

   Note that percentage symbols must be escaped.

- Double-escape special regular expression characters (for example, \^$.+) to include a backslash in the regular expression.

   For example:

   Value | Interpreted As 
   ------|---------------
   \\+    | +
   \\\\+   | \\+

- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Wildcard

Compares the specified value(s) against the request's query string.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Wildcard match condition is met.

- **Matches**: Requires the request to contain a URL query string that matches the specified wildcard value.
- **Does Not Match**: Requires the request to contain a URL query string that does not match the specified wildcard value.

Key information:

- For the purposes of this option, a query string starts with the first character after the question mark (?) delimiter for the query string.
- Parameter values can include [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values):
   - Each parameter value pattern can consist of one or more asterisks (*), where each asterisk can match a sequence of one or more characters.
   - Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

     Character | URL Encoding
     ----------|---------
     Space     | %20
     &         | %25

- Specify multiple values by delimiting each one with a single space.

   For example: *Parameter1=ValueA* *ValueB* *Parameter1=ValueC&Parameter2=ValueD*

- Only exact matches to at least one of the specified query string patterns satisfy this match condition.
    
- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

#### Sample scenarios

The following example demonstrates how this option works in specific situations:

 Name                 | Description
 ---------------------|------------
user=joe              | This pattern is matched when the query string for a requested URL is "?user=joe."
\*user=\* \*optout=\* | This pattern is matched when the CDN URL query contains either the user or optout parameter.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

## Next steps

- [Azure Content Delivery Network overview](cdn-overview.md)
- [Rules engine reference](cdn-verizon-premium-rules-engine-reference.md)
- [Rules engine conditional expressions](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Rules engine features](cdn-verizon-premium-rules-engine-reference-features.md)
- [Overriding default HTTP behavior using the rules engine](cdn-verizon-premium-rules-engine.md)\`25
title: Azure CDN from Verizon Premium rules engine match conditions | Microsoft Docs
description: Reference documentation for Azure Content Delivery Network from Verizon Premium rules engine match conditions.
services: cdn
author: mdgattuso

ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus

---

# Azure CDN from Verizon Premium rules engine match conditions

This article lists detailed descriptions of the available match conditions for the Azure Content Delivery Network (CDN) from Verizon Premium [rules engine](cdn-verizon-premium-rules-engine.md).

The second part of a rule is the match condition. A match condition identifies specific types of requests for which a set of features will be performed.

For example, you can use a match condition to:

- Filter requests for content at a particular location.
- Filter requests generated from a particular IP address or country/region.
- Filter requests by header information.

## Always match condition

The Always match condition applies a default set of features to all requests.

Name | Purpose
-----|--------
[Always](#always) | Applies a default set of features to all requests.

## Device match condition

The Device match condition identifies requests made from a mobile device based on its properties.  

Name | Purpose
-----|--------
[Device](#device) | Identifies requests made from a mobile device based on its properties.

## Location match conditions

The Location match conditions identify requests based on the requester's location.

Name | Purpose
-----|--------
[AS Number](#as-number) | Identifies requests that originate from a particular network.
[Country](#country) | Identifies requests that originate from the specified countries/regions.

## Origin match conditions

The Origin match conditions identify requests that point to Content Delivery Network storage or a customer origin server.

Name | Purpose
-----|--------
[CDN Origin](#cdn-origin) | Identifies requests for content stored in Content Delivery Network storage.
[Customer Origin](#customer-origin) | Identifies requests for content stored on a specific customer origin server.

## Request match conditions

The Request match conditions identify requests based on their properties.

Name | Purpose
-----|--------
[Client IP Address](#client-ip-address) | Identifies requests that originate from a particular IP address.
[Cookie Parameter](#cookie-parameter) | Checks the cookies associated with each request for the specified value.
[Cookie Parameter Regex](#cookie-parameter-regex) | Checks the cookies associated with each request for the specified regular expression.
[Edge Cname](#edge-cname) | Identifies requests that point to a specific edge CNAME.
[Referring Domain](#referring-domain) | Identifies requests that were referred from the specified host names.
[Request Header Literal](#request-header-literal) | Identifies requests that contain the specified header set to a specified value.
[Request Header Regex](#request-header-regex) | Identifies requests that contain the specified header set to a value that matches the specified regular expression.
[Request Header Wildcard](#request-header-wildcard) | Identifies requests that contain the specified header set to a value that matches the specified pattern.
[Request Method](#request-method) | Identifies requests by their HTTP method.
[Request Scheme](#request-scheme) | Identifies requests by their HTTP protocol.

## URL match conditions

The URL match conditions identify requests based on their URLs.

Name | Purpose
-----|--------
[URL Path Directory](#url-path-directory) | Identifies requests by their relative path.
[URL Path Extension](#url-path-extension) | Identifies requests by their file name extension.
[URL Path Filename](#url-path-filename) | Identifies requests by their file name.
[URL Path Literal](#url-path-literal) | Compares a request's relative path to the specified value.
[URL Path Regex](#url-path-regex) | Compares a request's relative path to the specified regular expression.
[URL Path Wildcard](#url-path-wildcard) | Compares a request's relative path to the specified pattern.
[URL Query Literal](#url-query-literal) | Compares a request's query string to the specified value.
[URL Query Parameter](#url-query-parameter) | Identifies requests that contain the specified query string parameter set to a value that matches a specified pattern.
[URL Query Regex](#url-query-regex) | Identifies requests that contain the specified query string parameter set to a value that matches a specified regular expression.
[URL Query Wildcard](#url-query-wildcard) | Compares the specified value against the request's query string.

## Reference for rules engine match conditions

---

### Always

The Always match condition applies a default set of features to all requests.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### AS Number

The AS Number network is defined by its autonomous system number (ASN). 

The **Matches**/**Does Not Match** option determines the conditions under which the AS Number match condition is met:

- **Matches**: Requires that the ASN of the client network matches one of the specified ASNs. 
- **Does Not Match**: Requires that the ASN of the client network does not match any of the specified ASNs.

Key information:

- Specify multiple ASNs by delimiting each one with a single space. For example, 64514 64515 matches requests that arrive from either 64514 or 64515.
- Certain requests might not return a valid ASN. A question mark (?) will match requests for which a valid ASN could not be determined.
- Specify the entire ASN for the desired network. Partial values will not be matched.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### CDN Origin

The CDN Origin match condition is met when both of the following conditions are met:

- Content from CDN storage was requested.
- The request URI uses the type of content access point (for example, /000001) that's defined in this match condition:
  - CDN URL: The request URI must contain the selected content access point.
  - Edge CNAME URL: The corresponding edge CNAME configuration must point to the selected content access point.
  
Key information:

- The content access point identifies the service that should serve the requested content.
- Don't use an AND IF statement to combine certain match conditions. For example, combining a CDN Origin match condition with a Customer Origin match condition would create a match pattern that could never be matched. For this reason, two CDN Origin match conditions cannot be combined through an AND IF statement.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Client IP Address

The **Matches**/**Does Not Match** option determines the conditions under which the Client IP Address match condition is met:

- **Matches**: Requires that the client's IP address matches one of the specified IP addresses. 
- **Does Not Match**: Requires that the client's IP address does not match any of the specified IP addresses. 

Key information:

- Use CIDR notation.
- Specify multiple IP addresses and/or IP address blocks by delimiting each one with a single space. For example:
  - **IPv4 example**: 1.2.3.4 10.20.30.40 matches any requests that arrive from either address 1.2.3.4 or 10.20.30.40.
  - **IPv6 example**: 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 matches any requests that arrive from either address 1:2:3:4:5:6:7:8 or 10:20:30:40:50:60:70:80.
- The syntax for an IP address block is the base IP address followed by a forward slash and the prefix size. For example:
  - **IPv4 example**: 5.5.5.64/26 matches any requests that arrive from addresses 5.5.5.64 through 5.5.5.127.
  - **IPv6 example**: 1:2:3:/48 matches any requests that arrive from addresses 1:2:3:0:0:0:0:0 through 1:2:3:ffff:ffff:ffff:ffff:ffff.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Cookie Parameter

The **Matches**/**Does Not Match** option determines the conditions under which the Cookie Parameter match condition is met.

- **Matches**: Requires a request to contain the specified cookie with a value that matches at least one of the values that are defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified cookie.
  - It contains the specified cookie, but its value does not match any of the values that are defined in this match condition.
  
Key information:

- Cookie name:
  - Because wildcard values, including asterisks (*), are not supported when you're specifying a cookie name, only exact cookie name matches are eligible for comparison.
  - Only a single cookie name can be specified per instance of this match condition.
  - Cookie name comparisons are case-insensitive.
- Cookie value:
  - Specify multiple cookie values by delimiting each one with a single space.
  - A cookie value can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - If a wildcard value has not been specified, then only an exact match will satisfy this match condition. For example, specifying "Value" will match "Value," but not "Value1" or "Value2."
  - Use the **Ignore Case** option to control whether a case-sensitive comparison is made against the request's cookie value.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)
</br>

---

### Cookie Parameter Regex

The Cookie Parameter Regex match condition defines a cookie name and value. You can use [regular expressions](cdn-verizon-premium-rules-engine-reference.md#regular-expressions) to define the desired cookie value.

The **Matches**/**Does Not Match** option determines the conditions under which the Cookie Parameter Regex match condition is met.

- **Matches**: Requires a request to contain the specified cookie with a value that matches the specified regular expression.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified cookie.
  - It contains the specified cookie, but its value does not match the specified regular expression.
  
Key information:

- Cookie name:
  - Because regular expressions and wildcard values, including asterisks (*), are not supported when you're specifying a cookie name, only exact cookie name matches are eligible for comparison.
  - Only a single cookie name can be specified per instance of this match condition.
  - Cookie name comparisons are case-insensitive.
- Cookie value:
  - A cookie value can take advantage of regular expressions.
  - Use the **Ignore Case** option to control whether a case-sensitive comparison is made against the request's cookie value.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Country

You can specify a country through its country code. 

The **Matches**/**Does Not Match** option determines the conditions under which the Country match condition is met:

- **Matches**: Requires the request to contain the specified country code values. 
- **Does Not Match**: Requires that the request does not contain the specified country code values.

Key information:

- Specify multiple country codes by delimiting each one with a single space.
- Wildcards are not supported when you're specifying a country code.
- The "EU" and "AP" country codes do not encompass all IP addresses in those regions.
- Certain requests might not return a valid country code. A question mark (?) will match requests for which a valid country code could not be determined.
- Country codes are case-sensitive.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

#### Implementing Country Filtering by using the rules engine

This match condition allows you to perform a multitude of customizations based on the location from which a request originated. For example, the behavior of the Country Filtering feature can be replicated through the following configuration:

- URL Path Wildcard match: Set the [URL Path Wildcard match condition](#url-path-wildcard) to the directory that will be secured. 
    Append an asterisk to the end of the relative path to ensure that access to all of its children will be restricted by this rule.

- Country match: Set the Country match condition to the desired set of countries.
  - Allow: Set the Country match condition to **Does Not Match** to allow only the specified countries access to content stored in the location defined by the URL Path Wildcard match condition.
  - Block: Set the Country match condition to **Matches** to block the specified countries from accessing content stored in the location defined by the URL Path Wildcard match condition.

- Deny Access (403) Feature: Enable the [Deny Access (403) feature](cdn-verizon-premium-rules-engine-reference-features.md#deny-access-403) to replicate the allow or block portion of the Country Filtering feature.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Customer Origin

Key information:

- The Customer Origin match condition is met regardless of whether content is requested through a CDN URL or an edge CNAME URL that points to the selected customer origin.
- A customer origin configuration that's referenced by a rule cannot be deleted from the Customer Origin page. Before you attempt to delete a customer origin configuration, make sure that the following configurations do not reference it:
  - A Customer Origin match condition
  - An edge CNAME configuration
- Don't use an AND IF statement to combine certain match conditions. For example, combining a Customer Origin match condition with a CDN Origin match condition would create a match pattern that could never be matched. For this reason, two Customer Origin match conditions cannot be combined through an AND IF statement.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Device

The Device match condition identifies requests made from a mobile device based on its properties. Mobile device detection is achieved through [WURFL](http://wurfl.sourceforge.net/). 

The **Matches**/**Does Not Match** option determines the conditions under which the Device match condition is met:

- **Matches**: Requires the requester's device to match the specified value. 
- **Does Not Match**: Requires that the requester's device does not match the specified value.

Key information:

- Use the **Ignore Case** option to specify whether the specified value is case-sensitive.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

#### String Type

A WURFL capability typically accepts any combination of numbers, letters, and symbols. Due to the flexible nature of this capability, you must choose how the value associated with this match condition is interpreted. The following table describes the available set of options:

Type     | Description
---------|------------
Literal  | Select this option to prevent most characters from taking on special meaning by using their [literal value](cdn-verizon-premium-rules-engine-reference.md#literal-values).
Wildcard | Select this option to take advantage of all [wildcard characters]([wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
Regex    | Select this option to use [regular expressions](cdn-verizon-premium-rules-engine-reference.md#regular-expressions). Regular expressions are useful for defining a pattern of characters.

#### WURFL capabilities

A WURFL capability refers to a category that describes mobile devices. The selected capability determines the type of mobile device description that is used to identify requests.

The following table lists WURFL capabilities and their variables for the rules engine.

> [!NOTE]
> The following variables are supported in the **Modify Client Request Header** and **Modify Client Response Header** features.

Capability | Variable | Description | Sample values
-----------|----------|-------------|----------------
Brand Name | %{wurfl_cap_brand_name} | A string that indicates the brand name of the device. | Samsung
Device OS | %{wurfl_cap_device_os} | A string that indicates the operating system installed on the device. | IOS
Device OS Version | %{wurfl_cap_device_os_version} | A string that indicates the version number of the operating system installed on the device. | 1.0.1
Dual Orientation | %{wurfl_cap_dual_orientation} | A Boolean that indicates whether the device supports dual orientation. | true
HTML Preferred DTD | %{wurfl_cap_html_preferred_dtd} | A string that indicates the mobile device's preferred document type definition (DTD) for HTML content. | none<br/>xhtml_basic<br/>html5
Image Inlining | %{wurfl_cap_image_inlining} | A Boolean that indicates whether the device supports Base64 encoded images. | false
Is Android | %{wurfl_vcap_is_android} | A Boolean that indicates whether the device uses the Android OS. | true
Is IOS | %{wurfl_vcap_is_ios} | A Boolean that indicates whether the device uses iOS. | false
Is Smart TV | %{wurfl_cap_is_smarttv} | A Boolean that indicates whether the device is a smart TV. | false
Is Smartphone | %{wurfl_vcap_is_smartphone} | A Boolean that indicates whether the device is a smartphone. | true
Is Tablet | %{wurfl_cap_is_tablet} | A Boolean that indicates whether the device is a tablet. This description is  OS-independent. | true
Is Wireless Device | %{wurfl_cap_is_wireless_device} | A Boolean that indicates whether the device is considered a wireless device. | true
Marketing Name | %{wurfl_cap_marketing_name} | A string that indicates the device's marketing name. | BlackBerry 8100 Pearl
Mobile Browser | %{wurfl_cap_mobile_browser} | A string that indicates the browser that's used to request content from the device. | Chrome
Mobile Browser Version | %{wurfl_cap_mobile_browser_version} | A string that indicates the version of the browser that's used to request content from the device. | 31
Model Name | %{wurfl_cap_model_name} | A string that indicates the device's model name. | s3
Progressive Download | %{wurfl_cap_progressive_download} | A Boolean that indicates whether the device supports the playback of audio and video while it is still being downloaded. | true
Release Date | %{wurfl_cap_release_date} | A string that indicates the year and month on which the device was added to the WURFL database.<br/><br/>Format: `yyyy_mm` | 2013_december
Resolution Height | %{wurfl_cap_resolution_height} | An integer that indicates the device's height in pixels. | 768
Resolution Width | %{wurfl_cap_resolution_width} | An integer that indicates the device's width in pixels. | 1024

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Edge Cname

Key information:

- The list of available edge CNAMEs is limited to those edge CNAMEs that have been configured on the Edge CNAMEs page for the platform on which the rules engine is being configured.
- Before you attempt to delete an edge CNAME configuration, make sure that an Edge Cname match condition does not reference it. Edge CNAME configurations that have been defined in a rule cannot be deleted from the Edge CNAMEs page.
- Don't use an AND IF statement to combine certain match conditions. For example, combining an Edge Cname match condition with a Customer Origin match condition would create a match pattern that could never be matched. For this reason, two Edge Cname match conditions cannot be combined through an AND IF statement.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Referring Domain

The host name associated with the referrer through which content was requested determines whether the Referring Domain condition is met.

The **Matches**/**Does Not Match** option determines the conditions under which the Referring Domain match condition is met:

- **Matches**: Requires the referring host name to match the specified values. 
- **Does Not Match**: Requires that the referring host name does not match the specified value.

Key information:

- Specify multiple host names by delimiting each one with a single space.
- This match condition supports [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
- If the specified value does not contain an asterisk, it must be an exact match for the referrer's host name. For example, specifying "mydomain.com" would not match "www.mydomain.com."
- Use the **Ignore Case** option to control whether a case-sensitive comparison is made.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---  

### Request Header Literal

The **Matches**/**Does Not Match** option determines the conditions under which the Request Header Literal match condition is met.

- **Matches**: Requires the request to contain the specified header. Its value must match the one that's defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified header.
  - It contains the specified header, but its value does not match the one that's defined in this match condition.
  
Key information:

- Header name comparisons are always case-insensitive. Use the **Ignore Case** option to control the case-sensitivity of header value comparisons.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---  

### Request Header Regex

The **Matches**/**Does Not Match** option determines the conditions under which the Request Header Regex match condition is met.

- **Matches**: Requires the request to contain the specified header. Its value must match the pattern that's defined in the specified [regular expression](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified header.
  - It contains the specified header, but its value does not match the specified regular expression.

Key information:

- Header name:
  - Header name comparisons are case-insensitive.
  - Replace spaces in the header name with "%20."
- Header value:
  - A header value can take advantage of regular expressions.
  - Use the **Ignore Case** option to control the case-sensitivity of header value comparisons.
  - The match condition is met only when a header value exactly matches at least one of the specified patterns.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Request Header Wildcard

The **Matches**/**Does Not Match** option determines the conditions under which the Request Header Wildcard match condition is met.

- **Matches**: Requires the request to contain the specified header. Its value must match at least one of the values that are defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified header.
  - It contains the specified header, but its value does not match any of the specified values.
  
Key information:

- Header name:
  - Header name comparisons are case-insensitive.
  - Spaces in the header name should be replaced with "%20." You can also use this value to specify spaces in a header value.
- Header value:
  - A header value can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - Use the **Ignore Case** option to control the case-sensitivity of header value comparisons.
  - This match condition is met when a header value exactly matches to at least one of the specified patterns.
  - Specify multiple values by delimiting each one with a single space.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Request Method

The Request Method match condition is met only when assets are requested through the selected request method. The available request methods are:

- GET
- HEAD
- POST
- OPTIONS
- PUT
- DELETE
- TRACE
- CONNECT

Key information:

- By default, only the GET request method can generate cached content on the network. All other request methods are proxied through the network.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Request Scheme

The Request Scheme match condition is met only when assets are requested through the selected protocol. The available protocols are:

- HTTP
- HTTPS

Key information:

- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Directory

Identifies a request by its relative path, which excludes the file name of the requested asset.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Directory match condition is met.

- **Matches**: Requires the request to contain a relative URL path, excluding the file name, that matches the specified URL pattern.
- **Does Not Match**: Requires the request to contain a relative URL path, excluding file name, that does not match the specified URL pattern.

Key information:

- Use the **Relative to** option to specify whether the URL comparison starts before or after the content access point. The content access point is the portion of the path that appears between the Verizon CDN hostname and the relative path to the requested asset (for example, /800001/CustomerOrigin). It identifies a location by server type (for example, CDN or customer origin) and your customer account number.

   The following values are available for the **Relative to** option:
  - **Root**: Indicates that the URL comparison point begins directly after the CDN hostname. 

  For example: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder**/index.htm

  - **Origin**: Indicates that the URL comparison point begins after the content access point (for example, /000001 or /800001/myorigin). Because the \*.azureedge.net CNAME is created relative to the origin directory on the Verizon CDN hostname by default, Azure CDN users should use the **Origin** value. 

  For example: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder**/index.htm 

  This URL points to the following Verizon CDN hostname: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder**/index.htm

- An edge CNAME URL is rewritten to a CDN URL prior to the URL comparison.

    For example, both of the following URLs point to the same asset and therefore have the same URL path.
  - CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
    
  - Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    Additional information:
  - Custom domain: https:\//my.domain.com/path/asset.htm
    
    - URL path (relative to root): /800001/CustomerOrigin/path/
    
    - URL path (relative to origin): /path/

- The portion of the URL that is used for the URL comparison ends just before the file name of the requested asset. A trailing forward slash is the last character in this type of path.

- Replace any spaces in the URL path pattern with "%20."

- Each URL path pattern can contain one or more asterisks (*), where each asterisk matches a sequence of one or more characters.

- Specify multiple URL paths in the pattern by delimiting each one with a single space.

    For example: */sales/ */marketing/

- A URL path specification can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).

- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Extension

Identifies requests by the file extension of the requested asset.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Extension match condition is met.

- **Matches**: Requires the URL of the request to contain a file extension that exactly matches the specified pattern.

   For example, if you specify "htm", "htm" assets are matched, but not "html" assets.  

- **Does Not Match**: Requires the URL request to contain a file extension that does not match the specified pattern.

Key information:

- Specify the file extensions to match in the **Value** box. Do not include a leading period; for example, use htm instead of .htm.

- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.

- Specify multiple file extensions by delimiting each extension with a single space. 

    For example: htm html

- For example, specifying "htm" matches "htm" assets, but not "html" assets.

#### Sample Scenario

The following sample configuration assumes that this match condition is met when a request matches one of the specified extensions.

Value specification: asp aspx php html

This match condition is met when it finds URLs that end with the following extensions:

- .asp
- .aspx
- .php
- .html

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Filename

Identifies requests by the file name of the requested asset. For the purposes of this match condition, a file name consists of the name of the requested asset, a period, and the file extension (for example, index.html).

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Filename match condition is met.

- **Matches**: Requires the request to contain a file name in its URL path that matches the specified pattern.
- **Does Not Match**: Requires the request to contain a file name in its URL path that does not match the specified pattern.

Key information:

- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.

- To specify multiple file extensions, separate each extension with a single space.

    For example: index.htm index.html

- Replace spaces in a file name value with "%20."

- A file name value can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). For example, each file name pattern can consist of one or more asterisks (*), where each asterisk matches a sequence of one or more characters.

- If wildcard characters are not specified, then only an exact match will satisfy this match condition.

    For example, specifying "presentation.ppt" matches an asset named "presentation.ppt," but not one named "presentation.pptx."

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Literal

Compares a request's URL path, including file name, to the specified value.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Literal match condition is met.

- **Matches**: Requires the request to contain a URL path that matches the specified pattern.
- **Does Not Match**: Requires the request to contain a URL path that does not match the specified pattern.

Key information:

- Use the **Relative to** option to specify whether the URL comparison point begins before or after the content access point. 

    The following values are available for the **Relative to** option:
  - **Root**: Indicates that the URL comparison point begins directly after the CDN hostname.

    For example: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder/index.htm**

  - **Origin**: Indicates that the URL comparison point begins after the content access point (for example, /000001 or /800001/myorigin). Because the \*.azureedge.net CNAME is created relative to the origin directory on the Verizon CDN hostname by default, Azure CDN users should use the **Origin** value. 

    For example: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder/index.htm**

  This URL points to the following Verizon CDN hostname: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder/index.htm**

- An edge CNAME URL is rewritten to a CDN URL prior to a URL comparison.

For example, both of the following URLs point to the same asset and therefore have the same URL path:

- CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
- Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm

    Additional information:
    
    - URL path (relative to root): /800001/CustomerOrigin/path/asset.htm
   
    - URL path (relative to origin): /path/asset.htm

- Query strings in the URL are ignored.
- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.
- The value specified for this match condition is compared against the relative path of the exact request made by the client.

- To match all requests made to a particular directory, use the [URL Path Directory](#url-path-directory) or the [URL Path Wildcard](#url-path-wildcard) match condition.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Regex

Compares a request's URL path to the specified [regular expression](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Regex match condition is met.

- **Matches**: Requires the request to contain a URL path that matches the specified regular expression.
- **Does Not Match**: Requires the request to contain a URL path that does not match the specified regular expression.

Key information:

- An edge CNAME URL is rewritten to a CDN URL prior to URL comparison.

    For example, both URLs point to the same asset and therefore have the same URL path.

     - CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm

     - Edge CNAME URL: http:\//my.domain.com/path/asset.htm

    Additional information:
    
     - URL path: /800001/CustomerOrigin/path/asset.htm

- Query strings in the URL are ignored.
    
- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.
    
- Spaces in the URL path should be replaced with "%20."

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Wildcard

Compares a request's relative URL path to the specified wildcard pattern.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Wildcard match condition is met.

- **Matches**: Requires the request to contain a URL path that matches the specified wildcard pattern.
- **Does Not Match**: Requires the request to contain a URL path that does not match the specified wildcard pattern.

Key information:

- **Relative to** option: This option determines whether the URL comparison point begins before or after the content access point.

   This option can have the following values:
     - **Root**: Indicates that the URL comparison point begins directly after the CDN hostname.

       For example: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder/index.htm**

     - **Origin**: Indicates that the URL comparison point begins after the content access point (for example, /000001 or /800001/myorigin). Because the \*.azureedge.net CNAME is created relative to the origin directory on the Verizon CDN hostname by default, Azure CDN users should use the **Origin** value. 

       For example: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder/index.htm**

     This URL points to the following Verizon CDN hostname: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder/index.htm**

- An edge CNAME URL is rewritten to a CDN URL prior to URL comparison.

    For example, both of the following URLs point to the same asset and therefore have the same URL path:
     - CDN URL: http://wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
     - Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    Additional information:
    
     - URL path (relative to root): /800001/CustomerOrigin/path/asset.htm
    
     - URL path (relative to origin): /path/asset.htm
    
- Specify multiple URL paths by delimiting each one with a single space.

   For example: /marketing/asset.* /sales/*.htm

- Query strings in the URL are ignored.
    
- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.
    
- Replace spaces in the URL path with "%20."
    
- The value specified for a URL path can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). Each URL path pattern can contain one or more asterisks (*), where each asterisk can match a sequence of one or more characters.

#### Sample Scenarios

The sample configurations in the following table assume that this match condition is met when a request matches the specified URL pattern:

Value                   | Relative to    | Result 
------------------------|----------------|-------
*/test.html */test.php  | Root or Origin | This pattern is matched by requests for assets named "test.html" or "test.php" in any folder.
/80ABCD/origin/text/*   | Root           | This pattern is matched when the requested asset meets the following criteria: <br />- It must reside on a customer origin called "origin." <br />- The relative path must start with a folder called "text." That is, the requested asset can either reside in the "text" folder or one of its recursive subfolders.
*/css/* */js/*          | Root or Origin | This pattern is matched by all CDN or edge CNAME URLs that contain a css or js folder.
*.jpg *.gif *.png       | Root or Origin | This pattern is matched by all CDN or edge CNAME URLs ending with .jpg, .gif, or .png. An alternative way to specify this pattern is with the [URL Path Extension match condition](#url-path-extension).
/images/* /media/*      | Origin         | This pattern is matched by CDN or edge CNAME URLs whose relative path starts with an "images" or "media" folder. <br />- CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/images/sales/event1.png<br />- Sample edge CNAME URL: http:\//cdn.mydomain.com/images/sales/event1.png

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Literal

Compares a request's query string to the specified value.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Literal match condition is met.

- **Matches**: Requires the request to contain a URL query string that matches the specified query string.
- **Does Not Match**: Requires the request to contain a URL query string that does not match the specified query string.

Key information:

- Only exact query string matches satisfy this match condition.
    
- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- Do not include a leading question mark (?) in the query string value text.
    
- Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

   Character | URL Encoding
   ----------|---------
   Space     | %20
   &         | %25

- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Parameter

Identifies requests that contain the specified query string parameter. This parameter is set to a value that matches a specified pattern. Query string parameters (for example, parameter=value) in the request URL determine whether this condition is met. This match condition identifies a query string parameter by its name and accepts one or more values for the parameter value. 

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Parameter match condition is met.

- **Matches**: Requires a request to contain the specified parameter with a value that matches at least one of the values that are defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified parameter.
  - It contains the specified parameter, but its value does not match any of the values that are defined in this match condition.

This match condition provides an easy way to specify parameter name/value combinations. For more flexibility if you are matching a query string parameter, consider using the [URL Query Wildcard](#url-query-wildcard) match condition.

Key information:

- Only a single URL query parameter name can be specified per instance of this match condition.
    
- Because wildcard values are not supported when a parameter name is specified, only exact parameter name matches are eligible for comparison.
- Parameter value(s) can include [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
   - Each parameter value pattern can consist of one or more asterisks (*), where each asterisk can match a sequence of one or more characters.
   - Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

       Character | URL Encoding
       ----------|---------
       Space     | %20
       &         | %25

- Specify multiple query string parameter values by delimiting each one with a single space. This match condition is met when a request contains one of the specified name/value combinations.

   - Example 1:

     - Configuration:

       ValueA ValueB

     - This configuration matches the following query string parameters:

       Parameter1=ValueA
    
       Parameter1=ValueB

   - Example 2:

     - Configuration: 

        Value%20A Value%20B

     - This configuration matches the following query string parameters:

       Parameter1=Value%20A

       Parameter1=Value%20B

- This match condition is met only when there is an exact match to at least one of the specified query string name/value combinations.

   For example, if you use the configuration in the previous example, the parameter name/value combination "Parameter1=ValueAdd" would not be considered a match. However, if you specify either of the following values, it will match that name/value combination:

   - ValueA ValueB ValueAdd
   - ValueA* ValueB

- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

#### Sample scenarios

The following example demonstrates how this option works in specific situations:

Name  | Value |  Result
------|-------|--------
User  | Joe   | This pattern is matched when the query string for a requested URL is "?user=joe."
User  | *     | This pattern is matched when the query string for a requested URL contains a User parameter.
Email | Joe\* | This pattern is matched when the query string for a requested URL contains an Email parameter that starts with "Joe."

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Regex

Identifies requests that contain the specified query string parameter. This parameter is set to a value that matches a specified [regular expression](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Regex match condition is met.

- **Matches**: Requires the request to contain a URL query string that matches the specified regular expression.
- **Does Not Match**: Requires the request to contain a URL query string that does not match the specified regular expression.

Key information:

- Only exact matches to the specified regular expression satisfy this match condition.
    
- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- For the purposes of this option, a query string starts with the first character after the question mark (?) delimiter for the query string.
    
- Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

   Character | URL Encoding | Value
   ----------|--------------|------
   Space     | %20          | \%20
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

[先頭に戻る](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-wildcard"></a>URL Query Wildcard (URL クエリ ワイルドカード)

指定値を要求のクエリ文字列と比較します。

**[一致する]** / **[一致しない]** オプションによって、「URL Query Wildcard (URL クエリ ワイルドカード)」一致条件が満たされる条件が決まります。

- **一致する**:要求に、指定したワイルドカード値と一致する URL クエリ文字列が含まれている必要があります。
- **一致しない**:要求に、指定したワイルドカード値と一致しない URL クエリ文字列が含まれている必要があります。

重要な情報:

- このオプションでは、クエリ文字列の疑問符 (?) 区切り記号の後の最初の文字からクエリ文字列が始まります。
- パラメーター値には、[ワイルドカード値](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)を含めることができます。
   - 各パラメーター値パターンは、1 つ以上のアスタリスク (*) で構成できます。各アスタリスクは 1 つ以上の一連の文字と一致します。
   - 特定の文字には URL エンコードが必要です。 パーセント記号を使用して、次の文字を URL エンコードします。

     Character | URL エンコード
     ----------|---------
     スペース     | %20
     &         | %25

- 複数の値を指定する場合は、値ごとに単一の空白で区切ります。

   例: *Parameter1=ValueA* *ValueB* *Parameter1=ValueC&Parameter2=ValueD*

- この一致条件を満たすのは、指定した 1 以上のクエリ文字列パラメーターとの完全一致だけです。
    
- クエリ文字列を比較する際の大文字と小文字の区別を制御するには、 **[大文字と小文字を区別しない]** オプションを使用します。
    
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

[先頭に戻る](#reference-for-rules-engine-match-conditions)

</br>

## <a name="next-steps"></a>次の手順

- [Azure Content Delivery Network の概要](cdn-overview.md)
- [ルール エンジンのリファレンス](cdn-verizon-premium-rules-engine-reference.md)
- [ルール エンジンの条件式](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [ルール エンジンの機能](cdn-verizon-premium-rules-engine-reference-features.md)
- [規則エンジンを使用した既定の HTTP 動作のオーバーライド](cdn-verizon-premium-rules-engine.md)
