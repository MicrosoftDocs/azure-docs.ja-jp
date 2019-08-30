---
title: 作成者エンティティの属性 - Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Academic Knowledge API で作成者エンティティに使用できる属性について説明します。
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: e63e9d3f8f17a2473caf3d31b83e318ddb132b43
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705085"
---
# <a name="author-entity"></a>著者エンティティ
<sub> *次の属性は、著者エンティティに固有です。(Ty = '1') </sub>

EnableAdfsAuthentication    |説明                            |Type       | Operations
------- | ------------------------------------- | --------- | ----------------------------
Id      |エンティティ ID                              |Int64      |等しい
AuN     |著者の標準化名                 |string     |等しい
DAuN    |著者の表示名                    |string     |なし
CC      |著者の引用の総数            |Int32      |なし  
ECC     |著者の引用の推定総数  |Int32      |なし
E       |拡張メタデータ (「拡張メタデータの属性」の表をご覧ください)  |string     |なし  


## <a name="extended-metadata-attributes"></a>拡張メタデータの属性 ##

EnableAdfsAuthentication    | 説明               
--------|---------------------------    
LKA.Afn     | 著者に関連付けられている所属の表示名  
LKA.AfId        | 著者に関連付けられている所属のエンティティ ID
