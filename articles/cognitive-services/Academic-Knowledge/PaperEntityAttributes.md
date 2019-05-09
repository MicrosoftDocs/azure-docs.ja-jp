---
title: 論文エンティティの属性 - Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Academic Knowledge API で論文エンティティに使用できる属性について説明します。
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: alch
ms.openlocfilehash: bd37665e962ada59149b54075d7f8acbea895c50
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2019
ms.locfileid: "57437464"
---
# <a name="paper-entity"></a>論文エンティティ

<sub> *次の属性は、論文エンティティに固有です。(Ty = '0') </sub>


Name    |説明                                        |type       | 操作
------- | ------------------------------------------------- | --------- | ----------------------------
Id      |エンティティ ID                                          |Int64      |等しい
Ti      |論文のタイトル                                        |String     |Equals、<br/>StartsWith
L       | "\@@@" で区切られた論文の言語コード            |String     |等しい
Y       |論文の年                                         |Int32      |Equals、<br/>IsBetween
D       |論文の日付                                         |Date       |Equals、<br/>IsBetween
CC      |引用数                                     |Int32      |なし  
ECC     |推定引用数                           |Int32      |なし
AA.AuN  |著者名                                        |String     |Equals、<br/>StartsWith
AA.AuId |著者 ID                                          |Int64      |等しい
AA.AfN  |著者の所属名                            |String     |Equals、<br/>StartsWith
AA.AfId |著者の所属 ID                              |Int64      |等しい
AA.S    |論文の著者の順序                         |Int32      |等しい
F.FN    |研究分野の名前                                |String     |Equals、<br/>StartsWith
F.FId   |研究分野の ID                                  |Int64      |等しい
J.JN    |ジャーナル名                                       |String     |Equals、<br/>StartsWith
J.JId   |ジャーナル ID                                         |Int64      |等しい
C.CN    |会議シリーズ名                             |String     |Equals、<br/>StartsWith
C.CId   |会議シリーズ ID                               |Int64      |等しい
RId     |参照論文 ID                              |Int64[]    |等しい
W       |論文のタイトルと要約の単語                |String[]   |等しい
E       |拡張メタデータ (次の表を参照)                |String     |なし  
        


## <a name="extended-metadata-attributes"></a>拡張メタデータの属性 ##

Name    | 説明               
--------|---------------------------    
DN      | 論文の表示名 
S       | ソース - 静的ランク順になった論文の Web ソースの一覧
S.Ty    | ソースの種類 (1:HTML、2:Text、3:PDF、4:DOC、5:PPT、6:XLS、7:PS)
S.U     | ソース URL
VFN     | 場所の完全な名前 - ジャーナルまたは会議の完全名
VSN     | 場所の短縮名 - ジャーナルまたは会議の短縮名
V       | ボリューム - ジャーナルのボリューム
BV      | ジャーナル名
BT      | 
PB      | ジャーナルの省略形
I       | 号 - ジャーナルの号
FP      | FirstPage - 論文の最初のページ
LP      | LastPage - 論文の最後のページ
DOI     | デジタル オブジェクト識別子
CC      | 引用のコンテキスト - 参照されている論文 ID の一覧とそれに対応する論文のコンテキスト (例: [{123:[“brown foxes are known for jumping as referenced in paper 123”, “the lazy dogs are a historical misnomer as shown in paper 123”]})
IA      | 逆要約
IA.IndexLength| インデックスの項目数 (要約のワード カウント)
IA.InvertedIndex| 要約の単語と、対応する元の要約の位置の一覧 (例: [{“the”:[0, 15, 30]}, {“brown”:[1]}, {“fox”:[2]}])
