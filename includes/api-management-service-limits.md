---
title: インクルード ファイル
description: インクルード ファイル
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 02/19/2021
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: ebaca1f39b16e4a06b5dcaa4e5f1de07122c6c89
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103622091"
---
| リソース | 制限 |
| ---------------------------------------------------------------------- | -------------------------- |
| スケール ユニットの最大数 | リージョンあたり 10<sup>1</sup> |
| キャッシュ サイズ | ユニットあたり 5 GiB<sup>2</sup> |
| HTTP 機関あたりのバックエンドの同時接続<sup>3</sup> | ユニットあたり 2,048<sup>4</sup> |
| キャッシュする応答の最大サイズ | 2 MiB |
| ポリシー ドキュメントの最大サイズ | 256 KiB<sup>5</sup> |
| サービス インスタンスあたりの最大カスタム ゲートウェイ ドメイン<sup>6</sup> | 20 |
| サービス インスタンスあたりの CA 証明書の最大数<sup>7</sup> | 10 |
| サブスクリプションあたりのサービス インスタンスの最大数<sup>8</sup> | 20 |
| サービス インスタンスあたりのサブスクリプションの最大数<sup>8</sup> | 500 |
| サービス インスタンスあたりのクライアント証明書の最大数<sup>8</sup> | 50 |
| サービス インスタンスあたりの API の最大数<sup>8</sup> | 50 |
| サービス インスタンスあたりの API 操作の最大数<sup>8</sup> | 1,000 |
| 最大合計要求時間<sup>8</sup> | 30 秒 |
| バッファー処理されたペイロードの最大サイズ<sup>8</sup> | 2 MiB |
| 要求 URL の最大サイズ<sup>9</sup> | 4,096 バイト |
| URL パスのセグメントの最大長<sup>10</sup> | 260 文字 |
| [検証ポリシー](../articles/api-management/validation-policies.md)によって使用される API スキーマの最大サイズ<sup>10</sup> | 4 MB |
| [コンテンツ検証ポリシー](../articles/api-management/validation-policies.md#validate-content)内の要求または応答本文の最大サイズ | 100 KB |
| セルフホステッド ゲートウェイの最大数<sup>11</sup> | 25 |

<sup>1</sup> スケーリングの制限は、価格レベルによって変わります。 価格レベルと、価格レベルのスケーリング制限については、「[API Management の価格](https://azure.microsoft.com/pricing/details/api-management/)」を参照してください。<br/>
<sup>2</sup> ユニット キャッシュ サイズは、価格レベルによって変わります。 価格レベルと、価格レベルのスケーリング制限については、「[API Management の価格](https://azure.microsoft.com/pricing/details/api-management/)」を参照してください。<br/>
<sup>3</sup>バックエンドによって明示的に終了されない場合、接続はプールされ、再利用されます。<br/>
<sup>4</sup>この制限は、Basic レベル、Standard レベル、および Premium レベルのユニットあたりです。 Developer レベルは 1,024 に制限されます。 この制限は、従量課金レベルには適用されません。<br/>
<sup>5</sup>この制限は Basic、Standard、および Premium レベルに適用されます。 従量課金レベルのポリシー ドキュメントのサイズは 16 KiB に制限されています。<br/>
<sup>6</sup>複数のカスタム ドメインは、Developer レベルと Premium レベルでのみサポートされます。<br/>
<sup>7</sup>CA 証明書は従量課金レベルではサポートされていません。<br/>
<sup>8</sup>この制限は、従量課金レベルにのみ適用されます。 他のレベルについては、これらのカテゴリに制限はありません。<br/>
<sup>9</sup>従量課金レベルにのみ適用されます。 最大 2,048 バイト長のクエリ文字列を含みます。<br/>
<sup>10</sup> この制限を増やすには、[サポート](https://azure.microsoft.com/support/options/)にお問い合わせください。<br/>
<sup>11</sup>セルフホステッド ゲートウェイは、Developer レベルと Premium レベルでのみサポートされています。 この制限は、[セルフホステッド ゲートウェイ リソース](/rest/api/apimanagement/2019-12-01/gateway)の数に適用されます。 この制限を引き上げるには、[サポート](https://azure.microsoft.com/support/options/)にお問い合わせください。 セルフホステッド ゲートウェイ リソースに関連付けられるノード (またはレプリカ) の数は、Premium レベルでは無制限であり、Developer レベルでは単一のノードに制限されることに注意してください。