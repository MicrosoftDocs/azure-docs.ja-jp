---
title: インクルード ファイル
description: インクルード ファイル
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 02/14/2020
ms.topic: include
ms.openlocfilehash: 5eb2517b4b71b61dd70406f345c005d007d9e3ef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "91535961"
---
pattern.any エンティティは、エンティティの表現が原因で発話の残りの部分からエンティティの終わりを判別するのが難しい自由形式データを見つけるために使用できます。

人事アプリは、従業員が会社のフォームを見つける際に役立ちます。

|発話|
|--|
|**HRF-123456** はどこにありますか?|
|誰が **HRF-123234** を作成しましたか?|
|**HRF-456098** はフランス語で発行されますか?|

ただし、各フォームには、前の表で使用されているフォーマット済みの名前と、`Request relocation from employee new to the company 2018 version 5` のようなわかりやすい名前の両方があります。

わかりやすいフォーム名を持つ発話は次のようになります。

|発話|
|--|
|**会社の新しい従業員からの配置換えリクエスト 2018 バージョン 5** はどこにありますか?|
|誰が **"会社の新しい従業員からの配置換えリクエスト 2018 バージョン 5"** を作成しましたか?|
|**会社の新しい従業員からの配置換えリクエスト 2018 バージョン 5** はフランス語で発行されますか?|

さまざまな長さには、エンティティの末尾がどこであるかについて LUIS を混乱させる可能性のある単語が含まれています。 パターンで Pattern.any エンティティを使用すると、フォーム名の先頭と末尾を指定できるため、LUIS はフォーム名を正しく抽出できます。

|テンプレート発話の例|
|--|
|{FormName} はどこですか[?]|
|誰が {FormName} を作成しましたか[?]|
|{FormName} はフランス語で発行されますか[?]|

Pattern.any の[参照](../reference-entity-pattern-any.md)情報をご覧ください