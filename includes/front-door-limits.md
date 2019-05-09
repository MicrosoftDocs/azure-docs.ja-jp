---
title: インクルード ファイル
description: インクルード ファイル
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.topic: include
ms.date: 9/17/2018
ms.author: sharadag
ms.custom: include file
ms.openlocfilehash: e3fa5616518675d8475937ec63afdd8e1742e8c6
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553839"
---
| リソース | 既定の制限 |
| --- | --- |
| サブスクリプションあたりの Azure Front Door Service リソース数 | 100 |
| リソースあたりのカスタム ドメインを含むフロントエンド ホスト数 | 100 |
| リソースあたりのルーティング規則数 | 100 |
| リソースあたりのバックエンド プール数 | 50 |
| バック エンド プールあたりのバック エンド | 100 |
| ルーティング規則に関して一致するパス パターン数 | 25 |
| ポリシーあたりのカスタム Web アプリケーション ファイアウォール規則数 | 10 |
| リソースあたりの Web アプリケーション ファイアウォール ポリシー数 | 100 |

### <a name="timeout-values"></a>タイムアウト値
#### <a name="client-to-front-door"></a>クライアントから Front Door
- Front Door のアイドル TCP 接続タイムアウトは 61 秒です。

#### <a name="front-door-to-application-back-end"></a>Front Door からアプリケーション バックエンド
- 応答がチャンク応答の場合、先頭のチャンクを受信したときに 200 が返されます。
- HTTP 要求がバック エンドに転送された後に、Front Door はバック エンドからの最初のパケットを 30 秒間待機します。 それからこれはクライアントに 503 エラーを返します。
- バックエンドからの最初のパケットを受信した後、Front Door はアイドル タイムアウトの 30 秒間を待ちます。 それからこれはクライアントに 503 エラーを返します。
- Front Door からバックエンド TCP セッションへのタイムアウトは、30 分です。

### <a name="upload-and-download-data-limit"></a>データ制限のアップロードとダウンロード

|  | チャンク転送エンコーディング (CTE) あり | HTTP チャンクなし |
| ---- | ------- | ------- |
| **ダウンロード** | ダウンロード サイズに制限はありません。 | ダウンロード サイズに制限はありません。 |
| **アップロード** |  各 CTE アップロードが 28.6 MB 未満である限り、制限はありません。 | サイズは、28.6   MB 以下である必要があります。 |
