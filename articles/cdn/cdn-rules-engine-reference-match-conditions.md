---
title: "Azure CDN ルール エンジンの一致条件 | Microsoft Docs"
description: "Azure CDN ルール エンジンの一致条件と機能に関するリファレンス ドキュメント。"
services: cdn
documentationcenter: 
author: Lichard
manager: akucer
editor: 
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: rli
translationtype: Human Translation
ms.sourcegitcommit: dccb945e170bd3e3f23283359db25e574a2d4296
ms.openlocfilehash: f4886b1d78dfa87cf25737fb46c12b5963034f27
ms.lasthandoff: 01/25/2017


---

# <a name="azure-cdn-rules-engine-match-conditions"></a>Azure CDN ルール エンジンの一致条件
このトピックでは、Azure Content Delivery Network (CDN) [ルール エンジン](cdn-rules-engine.md)で利用できる一致条件について詳しく説明します。

ルールの&2; 番目の部分は、一致条件です。 一致条件とは、要求の特定の種類を識別するものであり、その種類に対して一連の機能が実行されます。

たとえば、特定の場所のコンテンツの要求、特定の IP アドレスまたは国から生成された要求、あるいはヘッダー情報をフィルター処理するために使用されます。

## <a name="always"></a>Always (常に)

この「Alway (常に)」一致条件では、一連の既定の機能がすべての要求に適用されます。

## <a name="device"></a>Device (デバイス)

この「Device (デバイス)」一致条件では、モバイル デバイスからの要求をそのプロパティに基づいて識別します。  モバイル デバイスの検出は [WURFL](http://wurfl.sourceforge.net/) から実現します。  WURFL 機能とその CDN ルール エンジンの変数は以下に表示されています。

> [!NOTE] 
> 次の変数は、**Modify Client Request Header (クライアント要求ヘッダーの修正)** と **Modify Client Response Header (クライアント応答ヘッダーの修正)** 機能でサポートされています。

機能 | 変数 | 説明 | サンプル値
-----------|----------|-------------|----------------
Brand Name | % {wurfl_cap_brand_name} | デバイスのブランド名を示す文字列です。 | Samsung
Device OS | % {wurfl_cap_device_os} | デバイスにインストールされているオペレーティング システムを示す文字列です。 | IOS
Device OS Version | % {wurfl_cap_device_os_version} | デバイスにインストールされている OS のバージョン番号を示す文字列です。 | 1.0.1
Dual Orientation | % {wurfl_cap_dual_orientation} | デバイスが双方向の向きをサポートするかどうかを示すブール値です。 | true
HTML Preferred DTD | % {wurfl_cap_html_preferred_dtd} | HTML コンテンツ向けにモバイル デバイスで推奨されるドキュメント型定義 (DTD) を示す文字列です。 | なし<br/>xhtml_basic<br/>html5
Image Inlining | % {wurfl_cap_image_inlining} | デバイスが Base64 でエンコードされたイメージをサポートするかどうかを示すブール値です。 | false
Is Android | % {wurfl_vcap_is_android} | デバイスが Android OS を使用するかどうかを示すブール値です。 | true
Is IOS | % {wurfl_vcap_is_ios} | デバイスが iOS を使用するかどうかを示すブール値です。 | false
Is Smart TV | % {wurfl_cap_is_smarttv} | デバイスがスマート TV であるかどうかを示すブール値です。 | false
Is Smartphone | % {wurfl_vcap_is_smartphone} | デバイスがスマートフォンであるかどうかを示すブール値です。 | true
Is Tablet | % {wurfl_cap_is_tablet} | デバイスがタブレットであるかどうかを示すブール値です。 これは、OS に依存しない説明です。 | true
Is Wireless Device | % {wurfl_cap_is_wireless_device} | デバイスがワイヤレス デバイスとして認識されているかどうかを示すブール値です。 | true
Marketing Name | % {wurfl_cap_marketing_name} | デバイスのマーケティング名を示す文字列です。 | BlackBerry 8100 Pearl
Mobile Browser | % {wurfl_cap_mobile_browser} | デバイスからコンテンツの要求に使用するブラウザーを示す文字列です。 | Chrome
Mobile Browser Version | % {wurfl_cap_mobile_browser_version} | デバイスからコンテンツの要求に使用するブラウザーのバージョンを示す文字列です。 | 31
Model Name | % {wurfl_cap_model_name} | デバイスのモデル名を示す文字列です。 | s3
プログレッシブ ダウンロード | %{wurfl_cap_progressive_download} | デバイスが、ダウンロード中のオーディオまたはビデオの再生をサポートしているかどうかを示すブール値です。 | true
リリース日 | %{wurfl_cap_release_date} | デバイスが WURFL データベースに追加された年月を示す文字列です。<br/><br/>形式: `yyyy_mm` | 2013_december
Resolution Height | % {wurfl_cap_resolution_height} | デバイスの高さ (ピクセル単位) を示す整数です。 | 768
Resolution Width | % {wurfl_cap_resolution_width} | デバイスの幅 (ピクセル単位) を示す整数です。 | 1024


## <a name="location"></a>場所

以下の一致条件では、要求元の場所に基づいて要求が識別されます。

名前 | 目的
-----|--------
AS Number (AS 番号) | 特定のネットワークから送信される要求を識別します。
Country (国) | 指定した国から送信される要求を識別します。


## <a name="origin"></a>Origin (配信元)

以下の一致条件では、CDN ストレージまたは顧客の配信元サーバーを指す要求を識別します。

名前 | 目的
-----|--------
CDN Origin (CDN 配信元) | CDN ストレージに格納されているコンテンツの要求を識別します。
Customer Origin (顧客配信元) | 特定の顧客の配信元サーバーに格納されているコンテンツの要求を識別します。


## <a name="request"></a>Request (要求)

以下の一致条件では、そのプロパティに基づいて要求が識別されます。

名前 | 目的
-----|--------
Client IP Address (現在の IP アドレス) | 特定の IP アドレスから送信される要求を識別します。
Cookie Parameter (Cookie パラメーター) | 指定した値の各要求に関連付けられている Cookie を確認します。
Cookie Parameter Regex (Cookie パラメーターの正規表現) | 指定した正規表現の各要求に関連付けられている Cookie を確認します。
Edge Cname (エッジ CNAME) | 特定の CNAME を指す要求を識別します。
Referring Domain (参照ドメイン) | 指定したホスト名から参照された要求を識別します。
Request Header Literal (要求ヘッダーのリテラル) | 指定値に設定されている指定ヘッダーが含まれる要求を識別します。
Request Header Regex (要求ヘッダーの正規表現) | 指定正規表現に一致する値に設定されている指定ヘッダーが含まれる要求を識別します。
Request Header Wildcard (要求ヘッダーのワイルドカード) | 指定パターンに一致する値に設定されている指定ヘッダーが含まれる要求を識別します。
Request Method (要求メソッド) | HTTP メソッドで要求を識別します。
Request Scheme (要求スキーム) | HTTP プロトコルで要求を識別します。

## <a name="url"></a>URL

以下の一致条件では、その URL に基づいて要求が識別されます。

名前 | 目的
-----|--------
URL Path Directory (URL パス ディレクトリ) | 相対パスで要求を識別します。
URL Path Extension (URL パス拡張子) | ファイル名の拡張子で要求を識別します。
URL Path Filename (URL パス ファイル名) | ファイル名で要求を識別します。
URL Path Literal (URL パス リテラル) | 要求の相対パスを指定値と比較します。
URL Path Regex (URL パス正規表現) | 要求の相対パスを指定正規表現と比較します。
URL Path Wildcard (URL パス ワイルドカード) | 要求の相対パスを指定パターンと比較します。
URL Query Literal (URL クエリ リテラル) | 要求のクエリ文字列を指定値と比較します。
URL Query Parameter (URL クエリ パラメーター) | 指定パターンに一致する値に設定されている指定クエリ文字列パラメーターが含まれる要求を識別します。
URL Query Regex (URL クエリ正規表現) | 指定正規表現に一致する値に設定されている指定クエリ文字列パラメーターが含まれる要求を識別します。
URL Query Wildcard (URL クエリ ワイルドカード) | 指定値を要求のクエリ文字列と比較します。


## <a name="next-steps"></a>次のステップ
* [Azure CDN の概要](cdn-overview.md)
* [ルール エンジンのリファレンス](cdn-rules-engine-reference.md)
* [ルール エンジンの条件式](cdn-rules-engine-reference-conditional-expressions.md)
* [ルール エンジンの機能](cdn-rules-engine-reference-features.md)
* [規則エンジンを使用した既定の HTTP 動作のオーバーライド](cdn-rules-engine.md)


