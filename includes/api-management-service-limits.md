---
title: インクルード ファイル
description: インクルード ファイル
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 03/22/2018
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: fc945a7e9389c8aec48a6a1dba969fbf92002d3a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553886"
---
| リソース | 制限 |
| --- | --- |
| スケール ユニットの最大数 | リージョンあたり 10<sup>1</sup> |
| キャッシュ サイズ | ユニットあたり 5 GB<sup>2</sup> |
| HTTP 機関あたりのバックエンドの同時接続<sup>3</sup> | ユニットあたり 2,048<sup>4</sup> |
| キャッシュする応答の最大サイズ | 2 MB |
| ポリシー ドキュメントの最大サイズ | 256 KB<sup>5</sup> | 
| サービス インスタンスあたりの最大カスタム ゲートウェイ ドメイン<sup>6</sup> | 20 |
| サービス インスタンスあたりの CA 証明書の最大数 | 10 | 
| サブスクリプションあたりのサービス インスタンスの最大数<sup>7</sup> | 20 | 
| サービス インスタンスあたりのサブスクリプションの最大数<sup>7</sup> | 500 |
| サービス インスタンスあたりのクライアント証明書の最大数<sup>7</sup> | 50 | 
| サービス インスタンスあたりの API の最大数<sup>7</sup> | 50 | 
| サービス インスタンスあたりの API 操作の最大数<sup>7</sup> | 1,000 | 
| 最大合計要求時間<sup>7</sup> | 30 秒 | 
| バッファー処理されたペイロードの最大サイズ<sup>7</sup> | 2 MB | 


<sup>1</sup> スケーリングの制限は、価格レベルによって変わります。 価格レベルと、価格レベルのスケーリング制限については、「[API Management の価格](https://azure.microsoft.com/pricing/details/api-management/)」を参照してください。<br/>
<sup>2</sup> ユニット キャッシュ サイズは、価格レベルによって変わります。 価格レベルと、価格レベルのスケーリング制限については、「[API Management の価格](https://azure.microsoft.com/pricing/details/api-management/)」を参照してください。<br/>
<sup>3</sup>バックエンドによって明示的に終了されない場合、接続はプールされ、再利用されます。<br/>
<sup>4</sup>この制限は、Basic レベル、Standard レベル、および Premium レベルのユニットあたりです。 Developer レベルは 1,024 に制限されます。 この制限は、従量課金レベルには適用されません。<br/> 
<sup>5</sup>この制限は Basic、Standard、および Premium レベルに適用されます。 従量課金レベルのポリシー ドキュメントのサイズは 4 KB に制限されています。<br/>
<sup>6</sup>このリソースは Premium レベルでのみ使用できます。<br/>
<sup>7</sup>このリソースは 従量課金レベルにのみ適用されます。<br/>



