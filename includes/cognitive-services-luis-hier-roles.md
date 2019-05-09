---
title: インクルード ファイル
description: インクルード ファイル
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 10/23/2018
ms.author: diberry
ms.openlocfilehash: 3803bc7f1e0da01fbaa8aa11bc6e8fc5c508b5ae
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/23/2019
ms.locfileid: "56741542"
---
## <a name="roles-versus-hierarchical-entities"></a>ロールと階層エンティティ

階層エンティティまたはロール付きシンプル エンティティを含むパターンを使用する必要がありますか? 

場合によります。 パターンと発話の例は、それらがユーザーの発話を表し、意図に特有であるという点で同等です。  

発話に明確なパターンがない場合は、階層エンティティを使用します。 

|階層エンティティ|ロール付きシンプル エンティティ|
|--|--|
|意図の発話の例およびパターンで使用できます|パターンでのみ使用できます|
|意図には子エンティティでラベル付けされた発話の例が含まれる必要があります|意図内では、発話の例でロールにラベル付けすることはできません|
|エンティティを抽出するためには、意図の中に発話の例が**より多く**必要な場合があります|意図内で必要な発話の例は**より少ない**はずです|
