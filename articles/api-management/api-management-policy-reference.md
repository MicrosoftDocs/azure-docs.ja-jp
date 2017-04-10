---
title: "Azure API Management ポリシー リファレンス"
description: "API Management の構成に使用できるポリシーについて説明します。"
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 9d4ac609-b221-4032-93ae-e27a1254d43d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: 6b77e338e1c7f0f79ea3c25b0b073296f7de0dcf
ms.openlocfilehash: 988e0cfd8329a89ba21ab2ea292785b144e08dab


---
# <a name="azure-api-management-policy-reference"></a>Azure API Management ポリシー リファレンス
このセクションでは、[API Management ポリシー リファレンス][API Management policy reference]に関するページで説明されているポリシーのインデックスを示します。 ポリシーを追加および構成する方法については、[API Management のポリシー][Policies in API Management]に関するページを参照してください。

ポリシーの式は、ポリシーで特に指定されていない限り、任意の API Management ポリシーで属性値またはテキスト値として使用できます。 [制御フロー][Control flow] ポリシーや[変数の設定][Set variable]ポリシーなど、一部のポリシーはポリシーの式に基づいています。 詳細については、「[高度なポリシー][Advanced policies]」と[ポリシーの式][Policy expressions]に関するページを参照してください。

## <a name="policy-reference-index"></a>ポリシー リファレンスのインデックス
* [アクセス制限ポリシー][Access restriction policies]
  * [HTTP ヘッダーを確認する][Check HTTP header] - HTTP ヘッダーの存在と値の両方、またはそのどちらかを適用します。
  * [呼び出しレートをサブスクリプション別に制限する][Limit call rate by subscription] - 呼び出しレートをサブスクリプションに基づいて制限することで、API の使用量の急増を防ぎます。
  * [呼び出しレートをキー別に制限する](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) - 呼び出しレートをキーに基づいて制限することで、API の使用量の急増を防ぎます。
  * [呼び出し元 IP を制限する][Restrict caller IPs] - 特定の IP アドレスとアドレス範囲の両方、またはそのどちらかからの呼び出しをフィルター処理 (許可/拒否) します。
  * [使用量のクォータをサブスクリプション別に設定する][Set usage quota by subscription] - 更新可能な呼び出しまたは有効期間中の呼び出しのボリュームと帯域幅クォータの両方またはそのどちらかをサブスクリプションに基づいて適用できます。
  * [使用量のクォータをキー別に設定する](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) - 更新可能な呼び出しまたは有効期間中の呼び出しのボリュームと帯域幅クォータの両方またはそのどちらかをキーに基づいて適用できます。
  * [JWT を検証する][Validate JWT] - 指定された HTTP ヘッダーまたは指定されたクエリ パラメーターから抽出した JWT の存在と有効性を適用します。
* [高度なポリシー][Advanced policies]
  * [制御フロー][Control flow] - ブール[式][expressions]の評価の結果に基づいてポリシー ステートメントを条件付きで適用します。
  * [要求を転送する][Forward request] - バックエンド サービスに要求を転送します。
  * [Event Hub にログを記録する][Log to Event Hub] - [Logger](https://msdn.microsoft.com/library/azure/mt592020.aspx#Logger) エンティティによって定義されたメッセージ ターゲットに、指定された形式でメッセージを送信します。
  * [再試行](https://msdn.microsoft.com/en-us/library/dn894085.aspx#Retry) - 条件が満たされるまで、囲まれたポリシー ステートメントの実行を再試行します。 実行は、指定された間隔で、指定された最大試行回数まで繰り返されます。
  * [応答を返す](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse) - パイプラインの実行を中止し、指定された応答を呼び出し元に直接返します。
  * [1 方向の要求を送信する](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest) - 指定された URL に要求を送信します。応答は待機しません。
  * [要求を送信する](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) - 指定された URL に要求を送信します。
  * [要求メソッドを設定する](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod) - 要求の HTTP メソッドを変更できます。
  * [状態コードを設定する](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetStatus) - HTTP 状態コードを指定された値に変更します。
  * [変数の設定][Set variable] - 名前付き[コンテキスト][context]変数の値を、後でアクセスできるように保持します。
  * [トレース](https://msdn.microsoft.com/en-us/library/dn894085.aspx#Trace) - [API Inspector](api-management-howto-api-inspector.md) の出力に文字列を追加します。
  * [待機](https://msdn.microsoft.com/library/azure/dn894085.aspx#Wait) - 含まれている要求の送信、キャッシュからの値の取得、または制御フロー ポリシーが完了するまで待機してから次に進みます。
* [認証ポリシー][Authentication policies]
  * [基本認証][Authenticate with Basic] - 基本認証を使用してバックエンド サービスで認証します。
  * [クライアント証明書による認証][Authenticate with client certificate] - クライアント証明書を使用してバックエンド サービスで認証します。
* [キャッシュ ポリシー][Caching policies] 
  * [キャッシュから取得][Get from cache] - キャッシュを検索して、キャッシュに格納された有効な応答があればそれを返します。
  * [キャッシュに格納][Store to cache] - 指定されたキャッシュ制御の構成に従って応答をキャッシュに格納します。
  * [キャッシュから値を取得](https://msdn.microsoft.com/library/azure/dn894086.aspx#GetFromCacheByKey) - キャッシュされたキー別の項目を取得します。
  * [値をキャッシュに格納](https://msdn.microsoft.com/library/azure/dn894086.aspx#StoreToCacheByKey) - 項目をキー別にキャッシュに格納します。
  * [キャッシュから値を削除](https://msdn.microsoft.com/en-us/library/dn894086.aspx#RemoveCacheByKey) - キー別にキャッシュ内の項目を削除します。
* [ドメイン間ポリシー][Cross domain policies] 
  * [ドメイン間呼び出しを許可][Allow cross-domain calls] - Adobe Flash や Microsoft Silverlight ブラウザーベースのクライアントから API を利用できるようにします。
  * [CORS][CORS] - クロス オリジン リソース共有 (CORS) のサポートを操作または API に追加して、ブラウザーベースのクライアントからのドメイン間呼び出しを可能にします。
  * [JSONP][JSONP] - JSON with padding (JSONP) のサポートを操作または API に追加して、JavaScript ブラウザーベースのクライアントからのドメイン間呼び出しを可能にします。
* [変換ポリシー][Transformation policies] 
  * [JSON から XML への変換][Convert JSON to XML] - 要求本文または応答本文を JSON から XML に変換します。
  * [XML から JSON への変換][Convert XML to JSON] - 要求本文または応答本文を XML から JSON に変換します。
  * [本文中の文字列の置換][Find and replace string in body] - 要求または応答内の部分文字列を検索して別の部分文字列で置き換えます。
  * [コンテンツ内の URL のマスク][Mask URLs in content] - ゲートウェイを経由して同じリンクをポイントするよう、応答本文のリンクを書き換えます (マスクします)。
  * [バックエンド サービスの設定][Set backend service] - 受信要求のバックエンド サービスを変更します。
  * [本文の設定][Set body] - 着信要求と発信要求のメッセージ本文を設定します。
  * [HTTP ヘッダーの設定][Set HTTP header] - 既存の応答/要求ヘッダーに値を割り当てるか、新しい応答/要求ヘッダーを追加します。
  * [クエリ文字列パラメーターの設定][Set query string parameter] - 要求クエリ文字列パラメーターの追加、値の置換、または削除を行います。
  * [URL の書き換え][Rewrite URL] - 要求 URL をパブリックな形式から Web サービスで想定されている形式に変換します。

## <a name="next-steps"></a>次のステップ
ポリシー式に関する詳細については、次のビデオをご覧ください。

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Policy-Expressions-in-Azure-API-Management/player]
> 
> 

[Access restriction policies]: https://msdn.microsoft.com/library/azure/dn894078.aspx
[Check HTTP header]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#CheckHTTPHeader
[Limit call rate by subscription]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#LimitCallRate
[Restrict caller IPs]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#RestrictCallerIPs
[Set usage quota by subscription]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#SetUsageQuota
[Validate JWT]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#ValidateJWT

[Advanced policies]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Control flow]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Set variable]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[expressions]: https://msdn.microsoft.com/library/azure/dn910913.aspx
[context]: https://msdn.microsoft.com/library/azure/ea160028-fc04-4782-aa26-4b8329df3448#ContextVariables
[Forward request]: https://msdn.microsoft.com/library/azure/dn894085.aspx#ForwardRequest
[Log to Event Hub]: https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub

[Authentication policies]: https://msdn.microsoft.com/library/azure/dn894079.aspx
[Authenticate with Basic]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#Basic
[Authenticate with client certificate]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#ClientCertificate
[Caching policies]: https://msdn.microsoft.com/library/azure/dn894086.aspx
[Get from cache]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#GetFromCache
[Store to cache]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#StoreToCache

[Cross domain policies]: https://msdn.microsoft.com/library/azure/dn894084.aspx
[Allow cross-domain calls]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#AllowCrossDomainCalls
[CORS]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#CORS
[JSONP]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#JSONP

[Transformation policies]: https://msdn.microsoft.com/library/azure/dn894083.aspx
[Convert JSON to XML]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertJSONtoXML
[Convert XML to JSON]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertXMLtoJSON
[Find and replace string in body]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#Findandreplacestringinbody
[Mask URLs in content]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#MaskURLSContent
[Set backend service]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetBackendService
[Set body]: https://msdn.microsoft.com/library/azure/dn894083.aspx#SetBody
[Set HTTP header]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetHTTPheader
[Set query string parameter]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetQueryStringParameter
[Rewrite URL]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL



[Policies in API Management]: api-management-howto-policies.md
[API Management policy reference]: https://msdn.microsoft.com/library/azure/dn894081.aspx

[Policy expressions]: https://msdn.microsoft.com/library/azure/dn910913.aspx





<!--HONumber=Dec16_HO2-->


