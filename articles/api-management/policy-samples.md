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
ms.openlocfilehash: f8bd768dc7c01310874cd484f59869fbafcf3fe7
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70074204"
---
# <a name="api-management-policy-samples"></a>API Management ポリシーのサンプル

[ポリシー](api-management-howto-policies.md)は、発行者がその構成を通じて API の動作を変更できる、システムの強力な機能の 1 つです。 API の要求または応答に対して順に実行される一連のステートメントが集まってポリシーが形成されます。 次の表にサンプルへのリンクを掲載しています。各サンプルには簡単な説明がついています。

|                                                                                                                                                                      |                                                                                                                                                                                                                             |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **受信ポリシー**                                                                                                                                                 |                                                                                                                                                                                                                             |
| [転送ヘッダーを追加してバックエンド API が適切な URL を構築できるようにする](./policies/set-header-to-enable-backend-to-construct-urls.md?toc=api-management/toc.json) | 受信要求に転送ヘッダーを追加して、バックエンド API が適切な URL を構築できるようにする方法を説明します。                                                                                                        |
| [相関 ID を含むヘッダーを追加する](./policies/add-correlation-id.md?toc=api-management/toc.json)                                                             | 相関 ID を含むヘッダーを受信要求に追加する方法を示します。                                                                                                                                        |
| [バックエンド サービスに機能を追加して応答をキャッシュする](./policies/cache-response.md?toc=api-management/toc.json)                                             | バックエンド サービスに機能を追加する方法を示します。 たとえば、天気予報 API で、緯度と経度ではなく場所の名前を受け入れます。                                                                    |
| [JWT クレームに基づくアクセスを承認する](./policies/authorize-request-based-on-jwt-claims.md?toc=api-management/toc.json)                                              | API の特定の HTTP メソッドに対して、JWT クレームに基づくアクセスを承認する方法を示します。                                                                                                                                       |
| [外部承認者を使用して要求を承認する](./policies/authorize-request-using-external-authorizer.md)                                                   | API のアクセスを保護するための外部承認者の使用方法を示します。                                                                                                                                                               |
| [Google OAuth トークンを使用してアクセスを承認する](./policies/use-google-as-oauth-token-provider.md?toc=api-management/toc.json)                                            | OAuth トークン プロバイダーに Google を使用して、エンドポイントへのアクセスを承認する方法を示します。                                                                                                                                    |
| [Shared Access Signature を生成して Azure ストレージへ要求を転送する](./policies/generate-shared-access-signature.md?toc=api-management/toc.json)                  | 式を使用して [Shared Access Signature](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) を生成し、rewrite-uri ポリシーを使用して Azure ストレージに要求を転送する方法を示します。 |
| [AAD から OAuth2 アクセス トークンを取得してバックエンドに転送する](./policies/use-oauth2-for-authorization.md?toc=api-management/toc.json)                             | ゲートウェイとバックエンドとの間の承認に OAuth2 を使用する例を示します。 AAD からアクセス トークンを取得してバックエンドに転送する方法を示します。                                                    |
| [送信要求ポリシーを使用して SAP ゲートウェイから X-CSRF トークンを取得する](./policies/get-x-csrf-token-from-sap-gateway.md?toc=api-management/toc.json)                           | 多くの API で使用される X-CSRF パターンを実装する方法を示します。 この例は SAP ゲートウェイに固有のものです。                                                                                                                           |
| [本文のサイズに基づいて要求をルーティングする](./policies/route-requests-based-on-size.md?toc=api-management/toc.json)                                            | 本文のサイズに基づいて要求をルーティングする方法を示します。                                                                                                                                                       |
| [バックエンド サービスに要求コンテキスト情報を送信する](./policies/send-request-context-info-to-backend-service.md?toc=api-management/toc.json)                    | 一部のコンテキスト情報をログ記録や処理のためにバックエンド サービスに送信する方法を示します。                                                                                                                                |
| [応答をキャッシュする期間を設定する](./policies/set-cache-duration.md?toc=api-management/toc.json)                                                                          | バックエンドによって送信される Cache-control ヘッダーの maxAge 値を使用して、応答のキャッシュ期間を設定する方法を示します。                                                                                                             |
| **送信ポリシー**                                                                                                                                                |                                                                                                                                                                                                                             |
| [応答の内容をフィルターする](./policies/filter-response-content.md?toc=api-management/toc.json)                                                                         | 要求に関連付けられている製品に基づいて応答ペイロードのデータ要素をフィルターする方法を示します。                                                                                                        |
| **エラー時のポリシー**                                                                                                                                                |                                                                                                                                                                                                                             |
| [Stackify にエラーのログを記録する](./policies/log-errors-to-stackify.md?toc=api-management/toc.json)                                                                           | エラー ログ ポリシーを追加して、ログ記録のために Stackify にエラーを送信する　方法を示します。                                                                                                                                            |
