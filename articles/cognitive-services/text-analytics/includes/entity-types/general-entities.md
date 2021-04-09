---
title: 名前付き一般エンティティ
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 01/15/2021
ms.author: aahi
ms.openlocfilehash: c1ff099dd6dffe06e9707ff23fffd57ae753ab64
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "99500277"
---
Text Analytics 用の固有表現認識機能からは、次の一般的な (非識別) エンティティ カテゴリが返されます。 たとえば、`/entities/recognition/general` エンドポイントに要求を送信する場合などです。


| カテゴリ | 説明                          |
|------------|-------------|--------------------------------------|-------------------------------------------------------------|--------------------------------------|
| [Person](#category-person)     | 人の名前。  |
| [PersonType](#category-persontype) | 人物の職種または職務。 |
| [場所](#category-location)    | 自然および人工のランドマーク、構造、地物、地政学的実体 |
| [組織](#category-organization)  | 企業、政治団体、音楽バンド、スポーツ クラブ、政府機関、および公的機関。  |
| [Event](#category-event)  | 歴史上の出来事、社会的事件、自然発生的な事象。 |
| [製品](#category-product) | さまざまなカテゴリの物。 |
| [Skill](#category-skill) | 能力、スキル、または専門知識。  |
| [アドレス](#category-address) | 完全な郵送先住所。  |
| [電話番号](#category-phonenumber) | 電話番号です。 |
| [Email](#category-email) | メール アドレス。 |
| [URL](#category-url) | Web サイトへの URL。 |
| [IP](#category-ip) | ネットワーク IP アドレス。 |
| [DateTime](#category-datetime) | 日付と時刻。 |
| [Quantity](#category-quantity) | 数値的測定値と単位。 |


### <a name="category-person"></a>カテゴリ:Person

このカテゴリには、次のエンティティが含まれます。

:::row:::
    :::column span="":::
        **エンティティ**

        Person

    :::column-end:::
    :::column span="2":::
        **詳細**

        人の名前。
      
    :::column-end:::
    :::column span="2":::
      **サポートされているドキュメントの言語**

      `ar`, `cs`, `da`, `nl`, `en`, `fi`, `fr`, `de`, `he`, <br> `hu`, `it`, `ja`, `ko`, `no`, `pl`, `pt-br`, `pt`-`pt`, `ru`, `es`, `sv`, `tr`   
      
   :::column-end:::
:::row-end:::

### <a name="category-persontype"></a>カテゴリ:PersonType

このカテゴリには、次のエンティティが含まれます。


:::row:::
    :::column span="":::
        **エンティティ**

        PersonType

    :::column-end:::
    :::column span="2":::
        **詳細**

        人物の職種または職務
      
    :::column-end:::
    :::column span="2":::
      **サポートされているドキュメントの言語**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::

### <a name="category-location"></a>カテゴリ:場所

このカテゴリには、次のエンティティが含まれます。

:::row:::
    :::column span="":::
        **エンティティ**

        場所

    :::column-end:::
    :::column span="2":::
        **詳細**

        自然および人工のランドマーク、構造、地物、地政学的実体。
      
    :::column-end:::
    :::column span="2":::
      **サポートされているドキュメントの言語**

      `ar`, `cs`, `da`, `nl`, `en`, `fi`, `fr`, `de`, `he`, `hu`, `it`, `ja`, `ko`, `no`, `pl`, `pt-br`, `pt-pt`, `ru`, `es`, `sv`, `tr`   
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>サブカテゴリー

このカテゴリのエンティティには、次のサブカテゴリが含まれる場合があります。

:::row:::
    :::column span="":::
        **エンティティのサブカテゴリ**

        地政学的実体 (GPE)

    :::column-end:::
    :::column span="2":::
        **詳細**

        市区町村、国および地域、都道府県。
      
    :::column-end:::
    :::column span="2":::
      **サポートされているドキュメントの言語**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        構造体

    :::column-end:::
    :::column span="2":::

        人工構造物。 
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        地理

    :::column-end:::
    :::column span="2":::

        河川、海洋、砂漠などの地理的および自然的特徴。
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::

### <a name="category-organization"></a>カテゴリ:Organization

このカテゴリには、次のエンティティが含まれます。

:::row:::
    :::column span="":::
        **エンティティ**

        Organization

    :::column-end:::
    :::column span="2":::
        **詳細**

        企業、政治団体、音楽バンド、スポーツ クラブ、政府機関、および公的機関。 国籍と宗教は、このエンティティ型には含まれていません。
      
    :::column-end:::
    :::column span="2":::
      **サポートされているドキュメントの言語**

      `ar`, `cs`, `da`, `nl`, `en`, `fi`, `fr`, `de`, `he`, `hu`, `it`, `ja`, `ko`, `no`, `pl`, `pt-br`, `pt-pt`, `ru`, `es`, `sv`, `tr`   
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>サブカテゴリー

このカテゴリのエンティティには、次のサブカテゴリが含まれる場合があります。

:::row:::
    :::column span="":::
        **エンティティのサブカテゴリ**

        医療

    :::column-end:::
    :::column span="2":::
        **詳細**

        医療関係の企業および団体。
      
    :::column-end:::
    :::column span="2":::
      **サポートされているドキュメントの言語**

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        証券取引

    :::column-end:::
    :::column span="2":::

        証券取引団体。 
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        スポーツ

    :::column-end:::
    :::column span="2":::

        スポーツ関連の組織。
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::

### <a name="category-event"></a>カテゴリ:Event

このカテゴリには、次のエンティティが含まれます。

:::row:::
    :::column span="":::
        **エンティティ**

        イベント

    :::column-end:::
    :::column span="2":::
        **詳細**

        歴史上の出来事、社会的事件、自然発生的な事象。
      
    :::column-end:::
    :::column span="2":::
      **サポートされているドキュメントの言語**

      `en`、`es`、`fr`、`de`、`it`、`zh-hans`、`ja`、`ko`、`pt-pt`、`pt-br`  
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>サブカテゴリー

このカテゴリのエンティティには、次のサブカテゴリが含まれる場合があります。

:::row:::
    :::column span="":::
        **エンティティのサブカテゴリ**

        文化

    :::column-end:::
    :::column span="2":::
        **詳細**

        文化行事や祝日。
      
    :::column-end:::
    :::column span="2":::
      **サポートされているドキュメントの言語**

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Natural

    :::column-end:::
    :::column span="2":::

        自然発生的な事象。
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        スポーツ

    :::column-end:::
    :::column span="2":::

        スポーツ イベント。
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::

### <a name="category-product"></a>カテゴリ:Product

このカテゴリには、次のエンティティが含まれます。

:::row:::
    :::column span="":::
        **エンティティ**

        Product

    :::column-end:::
    :::column span="2":::
        **詳細**

        さまざまなカテゴリの物。
      
    :::column-end:::
    :::column span="2":::
      **サポートされているドキュメントの言語**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::


#### <a name="subcategories"></a>サブカテゴリー

このカテゴリのエンティティには、次のサブカテゴリが含まれる場合があります。

:::row:::
    :::column span="":::
        **エンティティのサブカテゴリ**

        コンピューティング製品
    :::column-end:::
    :::column span="2":::
        **詳細**

        コンピューティング製品。
      
    :::column-end:::
    :::column span="2":::
      **サポートされているドキュメントの言語**

      `en`   
      
   :::column-end:::
:::row-end:::

### <a name="category-skill"></a>カテゴリ:スキル

このカテゴリには、次のエンティティが含まれます。

:::row:::
    :::column span="":::
        **エンティティ**

        スキル

    :::column-end:::
    :::column span="2":::
        **詳細**

        能力、スキル、または専門知識。
      
    :::column-end:::
    :::column span="2":::
      **サポートされているドキュメントの言語**

      `en`  
      
   :::column-end:::
:::row-end:::

### <a name="category-address"></a>カテゴリ:Address

このカテゴリには、次のエンティティが含まれます。

:::row:::
    :::column span="":::
        **エンティティ**

        Address

    :::column-end:::
    :::column span="2":::
        **詳細**

        完全な郵送先住所。
      
    :::column-end:::
    :::column span="2":::
      **サポートされているドキュメントの言語**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-phonenumber"></a>カテゴリ:PhoneNumber

このカテゴリには、次のエンティティが含まれます。

:::row:::
    :::column span="":::
        **エンティティ**

        PhoneNumber

    :::column-end:::
    :::column span="2":::
        **詳細**

        電話番号 (米国および EU の電話番号のみ)。
      
    :::column-end:::
    :::column span="2":::
      **サポートされているドキュメントの言語**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt` `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-email"></a>カテゴリ:Email

このカテゴリには、次のエンティティが含まれます。

:::row:::
    :::column span="":::
        **エンティティ**

        Email

    :::column-end:::
    :::column span="2":::
        **詳細**

        メール アドレス。
      
    :::column-end:::
    :::column span="2":::
      **サポートされているドキュメントの言語**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-url"></a>カテゴリ:URL

このカテゴリには、次のエンティティが含まれます。

:::row:::
    :::column span="":::
        **エンティティ**

        URL

    :::column-end:::
    :::column span="2":::
        **詳細**

        Web サイトへの URL。 
      
    :::column-end:::
    :::column span="2":::
      **サポートされているドキュメントの言語**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-ip"></a>カテゴリ:IP

このカテゴリには、次のエンティティが含まれます。

:::row:::
    :::column span="":::
        **エンティティ**

        IP

    :::column-end:::
    :::column span="2":::
        **詳細**

        ネットワーク IP アドレス。 
      
    :::column-end:::
    :::column span="2":::
      **サポートされているドキュメントの言語**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-datetime"></a>カテゴリ:DateTime

このカテゴリには、次のエンティティが含まれます。

:::row:::
    :::column span="":::
        **エンティティ**

        DateTime

    :::column-end:::
    :::column span="2":::
        **詳細**

        日付と時刻。 
      
    :::column-end:::
    :::column span="2":::
      **サポートされているドキュメントの言語**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

このカテゴリのエンティティには、次のサブカテゴリが含まれる場合があります

#### <a name="subcategories"></a>サブカテゴリー

このカテゴリのエンティティには、次のサブカテゴリが含まれる場合があります。

:::row:::
    :::column span="":::
        **エンティティのサブカテゴリ**

        Date

    :::column-end:::
    :::column span="2":::
        **詳細**

        カレンダーの日付。
      
    :::column-end:::
    :::column span="2":::
      **サポートされているドキュメントの言語**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Time

    :::column-end:::
    :::column span="2":::

        時間帯。
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        DateRange

    :::column-end:::
    :::column span="2":::

        日付の範囲。
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        TimeRange

    :::column-end:::
    :::column span="2":::

        時間の範囲。
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Duration

    :::column-end:::
    :::column span="2":::

        期間。
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        オン

    :::column-end:::
    :::column span="2":::

        設定された繰り返し時間。
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::

### <a name="category-quantity"></a>カテゴリ:Quantity

このカテゴリには、次のエンティティが含まれます。

:::row:::
    :::column span="":::
        **エンティティ**

        Quantity

    :::column-end:::
    :::column span="2":::
        **詳細**

        数値と数量です。
      
    :::column-end:::
    :::column span="2":::
      **サポートされているドキュメントの言語**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>サブカテゴリー

このカテゴリのエンティティには、次のサブカテゴリが含まれる場合があります。

:::row:::
    :::column span="":::
        **エンティティのサブカテゴリ**

        Number

    :::column-end:::
    :::column span="2":::
        **詳細**

        数値
      
    :::column-end:::
    :::column span="2":::
      **サポートされているドキュメントの言語**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        パーセント

    :::column-end:::
    :::column span="2":::

        パーセント
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        序数

    :::column-end:::
    :::column span="2":::

        序数。
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Age

    :::column-end:::
    :::column span="2":::

        年齢。
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        通貨

    :::column-end:::
    :::column span="2":::

        通貨
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Dimensions

    :::column-end:::
    :::column span="2":::

        ディメンションと測定。
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        気温

    :::column-end:::
    :::column span="2":::

        気温。
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
