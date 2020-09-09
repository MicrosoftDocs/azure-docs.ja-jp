---
title: Azure API Management ポリシーのサンプル | Microsoft Docs
description: Azure API Management で使用できるポリシーについて説明します。
services: api-management
documentationcenter: ''
author: vladvino
manager: cflower
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 10/31/2017
ms.author: apimpm
ms.custom: mvc
ms.openlocfilehash: b5b8c82aa420b62e3b6e68ee53352eb9f77988f2
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86506691"
---
# <a name="api-management-policy-samples"></a>API Management ポリシーのサンプル

[ポリシー](../api-management-howto-policies.md)は、発行者がその構成を通じて API の動作を変更できる、システムの強力な機能の 1 つです。 API の要求または応答に対して順に実行される一連のステートメントが集まってポリシーが形成されます。 次の表にサンプルへのリンクを掲載しています。各サンプルには簡単な説明がついています。

| 受信ポリシー | 説明 |
| ---------------- | ----------- |
| [転送ヘッダーを追加してバックエンド API が適切な URL を構築できるようにする](./set-header-to-enable-backend-to-construct-urls.md) | 受信要求に転送ヘッダーを追加して、バックエンド API が適切な URL を構築できるようにする方法を説明します。                                                                                                        |
| [相関 ID を含むヘッダーを追加する](./add-correlation-id.md)                                                             | 相関 ID を含むヘッダーを受信要求に追加する方法を示します。                                                                                                                                        |
| [バックエンド サービスに機能を追加して応答をキャッシュする](./cache-response.md)                                             | バックエンド サービスに機能を追加する方法を示します。 たとえば、天気予報 API で、緯度と経度ではなく場所の名前を受け入れます。                                                                    |
| [JWT クレームに基づくアクセスを承認する](./authorize-request-based-on-jwt-claims.md)                                              | API の特定の HTTP メソッドに対して、JWT クレームに基づくアクセスを承認する方法を示します。                                                                                                                                       |
| [外部承認者を使用して要求を承認する](./authorize-request-using-external-authorizer.md)                                                   | API のアクセスを保護するための外部承認者の使用方法を示します。                                                                                                                                                               |
| [Google OAuth トークンを使用してアクセスを承認する](./use-google-as-oauth-token-provider.md)                                            | OAuth トークン プロバイダーに Google を使用して、エンドポイントへのアクセスを承認する方法を示します。                                                                                                                                    |
| [Application Gateway を使用する際に IP アドレスをフィルター処理する](./filter-ip-addresses-when-using-appgw.md) | Application Gateway を介して API Management インスタンスにアクセスするときに、ポリシー内で IP フィルターを使用する方法を示します
| [Shared Access Signature を生成して Azure ストレージへ要求を転送する](./generate-shared-access-signature.md)                  | 式を使用して [Shared Access Signature](../../storage/common/storage-sas-overview.md) を生成し、rewrite-uri ポリシーを使用して Azure ストレージに要求を転送する方法を示します。 |
| [AAD から OAuth2 アクセス トークンを取得してバックエンドに転送する](./use-oauth2-for-authorization.md)                             | ゲートウェイとバックエンドとの間の承認に OAuth2 を使用する例を示します。 AAD からアクセス トークンを取得してバックエンドに転送する方法を示します。                                                    |
| [送信要求ポリシーを使用して SAP ゲートウェイから X-CSRF トークンを取得する](./get-x-csrf-token-from-sap-gateway.md)                           | 多くの API で使用される X-CSRF パターンを実装する方法を示します。 この例は SAP ゲートウェイに固有のものです。                                                                                                                           |
| [本文のサイズに基づいて要求をルーティングする](./route-requests-based-on-size.md)                                            | 本文のサイズに基づいて要求をルーティングする方法を示します。                                                                                                                                                       |
| [バックエンド サービスに要求コンテキスト情報を送信する](./send-request-context-info-to-backend-service.md)                    | 一部のコンテキスト情報をログ記録や処理のためにバックエンド サービスに送信する方法を示します。                                                                                                                                |
| [応答をキャッシュする期間を設定する](./set-cache-duration.md)                                                                          | バックエンドによって送信される Cache-control ヘッダーの maxAge 値を使用して、応答のキャッシュ期間を設定する方法を示します。                                                                                                             |
| **送信ポリシー** | **説明** |
| [応答の内容をフィルターする](./filter-response-content.md)                                                                         | 要求に関連付けられている製品に基づいて応答ペイロードのデータ要素をフィルターする方法を示します。                                                                                                        |
| **エラー時のポリシー** | **説明** |
| [Stackify にエラーのログを記録する](./log-errors-to-stackify.md)                                                                           | エラー ログ ポリシーを追加して、ログ記録のために Stackify にエラーを送信する　方法を示します。                                                                                                                                            |
