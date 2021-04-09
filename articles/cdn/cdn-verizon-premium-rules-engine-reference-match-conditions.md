---
title: Azure CDN from Verizon Premium ルール エンジンの一致条件
description: Azure Content Delivery Network from Verizon Premium ルール エンジンの一致条件に関するリファレンス ドキュメント。
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/26/2020
ms.author: allensu
ms.openlocfilehash: efd6e6a93cd4ca79e6c4b6de69f8514e2d71b252
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "84323316"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-match-conditions"></a>Azure CDN from Verizon Premium ルール エンジンの一致条件

この記事では、Azure Content Delivery Network (CDN) from Verizon Premium [ルール エンジン](cdn-verizon-premium-rules-engine.md)で利用できる一致条件について詳しく説明します。

ルールの 2 番目の部分は、一致条件です。 一致条件とは、要求の特定の種類を識別するものであり、その種類に対して一連の機能が実行されます。

たとえば、以下のような場合に一致条件を使用できます。

- 特定の場所でコンテンツの要求をフィルター処理する。
- 特定の IP アドレスまたは国/地域から生成された要求をフィルター処理する。
- ヘッダー情報別に要求をフィルター処理する。

## <a name="match-conditions"></a><a name="top"></a>一致条件

* [毎回](#always)
* [[デバイス]](#device)
* [場所](#location)
* [元のドメイン](#origin)
* [Request](#request)
* [URL](#url)

### <a name="always"></a><a name="always"></a>Alway (常に)

この[「Alway (常に)」一致条件](https://docs.vdms.com/cdn/Content/HRE/M/Always.htm)では、一連の既定の機能がすべての要求に適用されます。

### <a name="device"></a><a name="device"></a>デバイス

以下の一致条件では、クライアントのユーザー エージェントに基づいて要求が識別されます。

| 名前       | 目的                                                           |
|------------|-------------------------------------------------------------------|
| Brand Name | デバイスのブランド名が次の値に一致するかどうかによって要求を識別します。 <br> **-** 特定の値 ([ブランド名のリテラル](https://docs.vdms.com/cdn/Content/HRE/M/D-Brand-Name-Literal.htm)) <br> **-** 正規表現 ([ブランド名の正規表現](https://docs.vdms.com/cdn/Content/HRE/M/D-Brand-Name-Regex.htm)) <br> **-** 特定のパターン ([ブランド名のワイルドカード](https://docs.vdms.com/cdn/Content/HRE/M/D-Brand-Name-Wildcard.htm)) |
| Device OS | デバイスの OS が次の値に一致するかどうかによって要求を識別します。 <br> **-** 特定の値 ([デバイス OS のリテラル](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Literal.htm)) <br> **-** 正規表現 ([デバイス OS の正規表現](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Regex.htm)) <br> **-** 特定のパターン ([デバイス OS のワイルドカード](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Wildcard.htm)) |
| Device OS Version | デバイスの OS バージョンが次の値に一致するかどうかによって要求を識別します。 <br> **-** 特定の値 ([デバイス OS バージョンのリテラル](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Version-Literal.htm)) <br> **-** 正規表現 ([デバイス OS バージョンの正規表現](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Version-Regex.htm)) <br> **-** 特定のパターン ([デバイス OS バージョンのワイルドカード](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Version-Wildcard.htm)) |
| [Dual Orientation? (双方向ですか)](https://docs.vdms.com/cdn/Content/HRE/M/D-Dual-Orientation.htm) | デバイスが双方向の向きをサポートするかどうかによって要求を識別します。 |
| HTML Preferred DTD | デバイスの HTML Preferred DTD が次の値に一致するかどうかによって要求を識別します。 <br> **-** 特定の値 ([HTML Preferred DTD のリテラル](https://docs.vdms.com/cdn/Content/HRE/M/D-HTML-Preferred-DTD-Literal.htm)) <br> **-** 正規表現 ([HTML Preferred DTD の正規表現](https://docs.vdms.com/cdn/Content/HRE/M/D-HTML-Preferred-DTD-Regex.htm)) <br> **-** 特定のパターン ([HTML Preferred DTD のワイルドカード](https://docs.vdms.com/cdn/Content/HRE/M/D-HTML-Preferred-DTD-Wildcard.htm)) |
| [Image Inlining? (イメージをインライン化しますか)](https://docs.vdms.com/cdn/Content/HRE/M/D-Image-Inlining.htm) | デバイスが Base64 でエンコードされたイメージをサポートするかどうかによって要求を識別します。 |
| [Is Android? (Android ですか)](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Android.htm) | デバイスが Android OS を使用しているかどうかによって要求を識別します。 |
| [Is App? (アプリですか)](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-App.htm) | ネイティブ アプリケーションがコンテンツを要求したかどうかによって要求を識別します。 |
| [Is Full Desktop? (完全なデスクトップですか)](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Full-Desktop.htm) | デバイスが完全なデスクトップ エクスペリエンスを提供するかどうかによって要求を識別します。 |
| [Is iOS? (iOS ですか)](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-iOS.htm) | デバイスが iOS を使用しているかどうかによって要求を識別します。 |
| [Is Robot? (ロボットですか)](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Robot.htm) | デバイスが自動 HTTP クライアント (ロボット クローラーなど) であると見なされるかどうかによって要求を識別します。 |
| [Is Smart TV? (スマート TV ですか)](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Smart-TV.htm) | デバイスがスマート TV であるかどうかによって要求を識別します。 |
| [Is Smartphone? (スマートフォンですか)](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Smartphone.htm) | デバイスがスマートフォンであるかどうかによって要求を識別します。
| [Is Tablet? (タブレットですか)](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Tablet.htm) | デバイスがタブレットであるかどうかによって要求を識別します。 |
| [Is Touchscreen? (タッチスクリーンですか)](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Touchscreen.htm) | デバイスのプライマリ ポインティング デバイスがタッチスクリーンであるかどうかによって要求を識別します。 |
| [Is Windows Phone? (Windows Phone ですか)](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Windows-Phone.htm) | デバイスが Windows Mobile 6.5/Windows Phone 7 以上であるかどうかによって要求を識別します。 |
| [Is Wireless Device? (ワイヤレス デバイスですか)](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Wireless-Device.htm) | デバイスがワイヤレスであるかどうかによって要求を識別します。 
| Marketing Name | デバイスのマーケティング名が次の値に一致するかどうかによって要求を識別します。 <br> **-** 特定の値 ([マーケティング名のリテラル](https://docs.vdms.com/cdn/Content/HRE/M/D-Marketing-Name-Literal.htm)) <br> **-** 正規表現 ([マーケティング名の正規表現](https://docs.vdms.com/cdn/Content/HRE/M/D-Marketing-Name-Regex.htm)) <br> **-** 特定のパターン ([マーケティング名のワイルドカード](https://docs.vdms.com/cdn/Content/HRE/M/D-Marketing-Name-Wildcard.htm)) |
| Mobile Browser | デバイスのブラウザーが次の値に一致するかどうかによって要求を識別します。 <br> **-** 特定の値 ([モバイル ブラウザーのリテラル](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Literal.htm)) <br> **-** 正規表現 ([モバイル ブラウザーの正規表現](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Regex.htm)) <br> **-** 特定のパターン ([モバイル ブラウザーのワイルドカード](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Wildcard.htm)) |
| Mobile Browser Version | デバイスのブラウザー バージョンが次の値に一致するかどうかによって要求を識別します。 <br> **-** 特定の値 ([モバイル ブラウザー バージョンのリテラル](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Version-Literal.htm)) <br> **-** 正規表現 ([モバイル ブラウザー バージョンの正規表現](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Version-Regex.htm)) <br> **-** 特定のパターン ([モバイル ブラウザー バージョンのワイルドカード](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Version-Wildcard.htm)) |
| Model Name | デバイスのモデル名が次の値に一致するかどうかによって要求を識別します。 <br> **-** 特定の値 ([モデル名のリテラル](https://docs.vdms.com/cdn/Content/HRE/M/D-Model-Name-Literal.htm)) <br> **-** 正規表現 ([モデル名の正規表現](https://docs.vdms.com/cdn/Content/HRE/M/D-Model-Name-Regex.htm)) <br> **-** 特定のパターン ([モデル名のワイルドカード](https://docs.vdms.com/cdn/Content/HRE/M/D-Model-Name-Wildcard.htm)) |
| [Progressive Download? (プログレッシブ ダウンロードですか)](https://docs.vdms.com/cdn/Content/HRE/M/D-Progressive-Download.htm) | デバイスがプログレッシブ ダウンロードをサポートするかどうかによって要求を識別します。 |
| リリース日 | デバイスのリリース日が次の値に一致するかどうかによって要求を識別します。 <br> **-** 特定の値 ([リリース日のリテラル](https://docs.vdms.com/cdn/Content/HRE/M/D-Release-Date-Literal.htm)) <br> **-** 正規表現 ([リリース日の正規表現](https://docs.vdms.com/cdn/Content/HRE/M/D-Release-Date-Regex.htm)) <br> **-** 特定のパターン ([リリース日のワイルドカード](https://docs.vdms.com/cdn/Content/HRE/M/D-Release-Date-Wildcard.htm)) |
| [Resolution Height (解像度の高さ)](https://docs.vdms.com/cdn/Content/HRE/M/D-Resolution-Height.htm) | デバイスの高さによって要求を識別します。 |
| [Resolution Width (解像度の幅)](https://docs.vdms.com/cdn/Content/HRE/M/D-Resolution-Width.htm) | デバイスの幅によって要求を識別します。 |

**[先頭に戻る](#top)**

### <a name="location"></a><a name="location"></a>場所

以下の一致条件では、要求元の場所に基づいて要求が識別されます。

| 名前       | 目的                                                           |
|------------|-------------------------------------------------------------------|
| [AS Number (AS 番号)](https://docs.vdms.com/cdn/Content/HRE/M/AS-Number.htm) | 特定のネットワークから送信される要求を識別します。 |
| 市区町村名 | 名前が次の値に一致する市区町村から送信されたかどうかによって要求を識別します。 <br> **-** 特定の値 ([市区町村名のリテラル](https://docs.vdms.com/cdn/Content/HRE/M/City-Name-Literal.htm)) <br> **-** 正規表現 ([市区町村名の正規表現](https://docs.vdms.com/cdn/Content/HRE/M/City-Name-Regex.htm)) |
| [Continent](https://docs.vdms.com/cdn/Content/HRE/M/Continent.htm) | 指定した大陸から送信される要求を識別します。 |
| [Country (国)](https://docs.vdms.com/cdn/Content/HRE/M/Country.htm) | 指定した国から送信される要求を識別します。 |
| [DMA Code (DMA コード)](https://docs.vdms.com/cdn/Content/HRE/M/DMA-Code.htm) | 指定した都市 (指定のマーケット エリア) から送信される要求を識別します。 |
| [Latitude (緯度)](https://docs.vdms.com/cdn/Content/HRE/M/Latitude.htm) | 指定した緯度から送信される要求を識別します。 |
| [Longitude (経度)](https://docs.vdms.com/cdn/Content/HRE/M/Longitude.htm) | 指定した経度から送信される要求を識別します。 |
| [Metro Code (都市コード)](https://docs.vdms.com/cdn/Content/HRE/M/Metro-Code.htm) | 指定した都市 (指定のマーケット エリア) から送信される要求を識別します。 |
| [Postal Code](https://docs.vdms.com/cdn/Content/HRE/M/Postal-Code.htm) | 指定した郵便番号から送信される要求を識別します。 |
| [Region Code (地域コード)](https://docs.vdms.com/cdn/Content/HRE/M/Region-Code.htm) | 指定した地域から送信される要求を識別します。 |

> [!NOTE]
> **Should I use Metro Code or DMA Code? (都市コードと DMA コードのどちらを使用すべきか)** <br>
これらの一致条件はどちらも同じ機能を提供します。 ただし、都市コード一致条件を使用して、DMA で要求を識別することをお勧めします。

**[先頭に戻る](#top)**

### <a name="origin"></a><a name="origin"></a>元のドメイン

以下の一致条件では、CDN ストレージまたは顧客の配信元サーバーを指す要求を識別します。

| 名前       | 目的                                                           |
|------------|-------------------------------------------------------------------|
| [CDN Origin (CDN 配信元)](https://docs.vdms.com/cdn/Content/HRE/M/CDN-Origin.htm) | CDN ストレージに格納されているコンテンツの要求を識別します。 |
| [Customer Origin (顧客配信元)](https://docs.vdms.com/cdn/Content/HRE/M/Customer-Origin.htm) | 特定の顧客の配信元サーバーに格納されているコンテンツの要求を識別します。 |

**[先頭に戻る](#top)**

### <a name="request"></a><a name="request"></a>要求

以下の一致条件では、そのプロパティに基づいて要求が識別されます。

| 名前              | 目的                                                                |
|-------------------|------------------------------------------------------------------------|
| [Client IP Address (クライアント IP アドレス)](https://docs.vdms.com/cdn/Content/HRE/M/Client-IP-Address.htm) | 特定の IP アドレスから送信される要求を識別します。 |
| Cookie Parameter (Cookie パラメーター)  | 次の値に一致する Cookie が含まれているかどうかによって要求を識別します。 <br> **-** 特定の値 ([Cookie パラメーターのリテラル](https://docs.vdms.com/cdn/Content/HRE/M/Cookie-Parameter-Literal.htm)) <br> **-** 正規表現 ([Cookie パラメーターの正規表現](https://docs.vdms.com/cdn/Content/HRE/M/Cookie-Parameter-Regex.htm) <br> **-** 特定のパターン ([Cookie パラメーターのワイルドカード](https://docs.vdms.com/cdn/Content/HRE/M/Cookie-Parameter-Wildcard.htm)) |
| [Edge CNAME (エッジ CNAME)](https://docs.vdms.com/cdn/Content/HRE/M/Edge-CNAME.htm) | 特定の CNAME を指す要求を識別します。 |
| Referring Domain (参照ドメイン) | 次の値に一致するホスト名で参照されたかどうかによって要求を識別します。 <br> **-** 特定の値 ([参照元ドメインのリテラル](https://docs.vdms.com/cdn/Content/HRE/M/Referring-Domain-Literal.htm)) <br> **-** 特定のパターン ([参照元ドメインのワイルドカード](https://docs.vdms.com/cdn/Content/HRE/M/Referring-Domain-Wildcard.htm)) |
| 要求ヘッダー | 次の値に一致するヘッダーが含まれているかどうかによって要求を識別します。 <br> **-** 特定の値 ([要求ヘッダーのリテラル](https://docs.vdms.com/cdn/Content/HRE/M/Request-Header-Literal.htm)) <br> **-** 正規表現 ([要求ヘッダーの正規表現](https://docs.vdms.com/cdn/Content/HRE/M/Request-Header-Regex.htm)) <br> **-** 特定のパターン ([要求ヘッダーのワイルドカード](https://docs.vdms.com/cdn/Content/HRE/M/Request-Header-Wildcard.htm)) |
| [Request Method (要求メソッド)](https://docs.vdms.com/cdn/Content/HRE/M/Request-Method.htm) | HTTP メソッドで要求を識別します。 |
| [Request Scheme (要求スキーム)](https://docs.vdms.com/cdn/Content/HRE/M/Request-Scheme.htm) | HTTP プロトコルで要求を識別します。 |

**[先頭に戻る](#top)**

### <a name="url"></a><a name="url"></a>URL

| 名前              | 目的                                                                |
|-------------------|------------------------------------------------------------------------|
| URL パス | ファイル名を含む相対パスが次の値に一致するかどうかによって要求を識別します。 <br> **-** 特定の値 ([URL パスのリテラル](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Literal.htm)) <br> **-** 正規表現 ([URL パスの正規表現](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Regex.htm)) <br> **-** 特定のパターン ([URL パスのワイルドカード](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Wildcard.htm)) |
| URL Path Directory (URL パス ディレクトリ) | 相対パスが次の値に一致するかどうかによって要求を識別します。 <br> **-** 特定の値 ([URL パス ディレクトリのリテラル](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Directory-Literal.htm)) <br> **-** 特定のパターン ([URL パス ディレクトリのワイルドカード](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Directory-Wildcard.htm)) |
| URL Path Extension (URL パス拡張子) | ファイル拡張子が次の値に一致するかどうかによって要求を識別します。 <br> **-** 特定の値 ([URL パス拡張子のリテラル](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Extension-Literal.htm)) <br> **-** 特定のパターン ([URL パス拡張子のワイルドカード](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Extension-Wildcard.htm)) |
| URL Path Filename (URL パス ファイル名) | ファイル名が次の値に一致するかどうかによって要求を識別します。 <br> **-** 特定の値 ([URL パス ファイル名のリテラル](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Filename-Literal.htm)) <br> **-** 特定のパターン ([URL パス ファイル名のワイルドカード](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Filename-Wildcard.htm)) |
| URL Query (URL クエリ) | クエリ文字列が次の値に一致するかどうかによって要求を識別します。 <br> **-** 特定の値 ([URL クエリのリテラル](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Literal.htm)) <br> **-** 正規表現 ([URL クエリの正規表現](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Regex.htm)) <br> **-** 特定のパターン ([URL クエリのワイルドカード](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Wildcard.htm)) |
| URL Query Parameter (URL クエリ パラメーター) | 次の値に一致する値に設定されているクエリ文字列パラメーターが含まれる要求を識別します。 <br> **-** 特定の値 ([URL クエリ パラメーターのリテラル](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Parameter-Literal.htm)) <br> **-** 特定のパターン ([URL クエリ パラメーターのワイルドカード](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Parameter-Wildcard.htm)) |

**[先頭に戻る](#top)**

最新の一致条件については、[Verizon のルール エンジン](https://docs.vdms.com/cdn/index.html#Quick_References/HRE_QR.htm#Conditio)に関するドキュメントを参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure Content Delivery Network の概要](cdn-overview.md)
- [ルール エンジンのリファレンス](cdn-verizon-premium-rules-engine-reference.md)
- [ルール エンジンの条件式](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [ルール エンジンの機能](cdn-verizon-premium-rules-engine-reference-features.md)
- [規則エンジンを使用した既定の HTTP 動作のオーバーライド](cdn-verizon-premium-rules-engine.md)
