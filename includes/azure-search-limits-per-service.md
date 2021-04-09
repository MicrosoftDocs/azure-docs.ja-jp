---
title: インクルード ファイル
description: インクルード ファイル
services: search
author: HeidiSteen
ms.service: cognitive-search
ms.topic: include
ms.date: 05/11/2020
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: fe5c8129434ddb4eec2dd25a3f123f28b4db221b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "83682618"
---
検索サービスは、ディスク領域と、インデックスまたはインデクサーの最大数のハード制限のうち、先に達したものによって制約されます。 次の表では、ストレージの制限についてまとめています。 オブジェクトの上限については、[リソース別の制限](../articles/search/search-limits-quotas-capacity.md#index-limits)に関するセクションを参照してください。

| リソース | Free | Basic<sup>1</sup> | S1 | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| -------- | --- | --- | --- | --- | --- | --- | --- | --- |
| サービス レベル アグリーメント (SLA)<sup>2</sup>  |いいえ |はい |はい |はい |はい |はい |はい |はい |
| パーティションあたりのストレージ容量 |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB (テラバイト) |2 TB |
| サービスあたりのパーティション数 |該当なし |1 |12 |12 |12 |3 |12 |12 |
| パーティション サイズ |該当なし |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB (テラバイト) |2 TB |
| レプリカ |該当なし |3 |12 |12 |12 |12 |12 |12 |

<sup>1</sup> Basic には、1 つの固定パーティションがあります。 追加の検索ユニットを使用することで、レプリカを追加してより多くのクエリを実行できます。

<sup>2</sup> 専用リソースの課金対象のサービスでは、サービス レベル アグリーメントが有効です。 Free サービスおよびプレビュー機能には SLA がありません。 課金対象のサービスで、SLA が有効になるのは、サービスに対して十分な冗長性がプロビジョニングされている場合です。 クエリ (読み取り) の SLA には複数のレプリカが必要です。 クエリとインデックス作成 (読み取り/書き込み) の SLA には 3 つ以上のレプリカが必要です。 パーティション数は SLA には関係ありません。 