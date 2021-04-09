---
title: Identification エンティティ
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 02/17/2021
ms.author: aahi
ms.openlocfilehash: a376b050d79709885e3542d330bb6b1eea48d046
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750072"
---
### <a name="financial-account-identification"></a>金融アカウント ID

このエンティティ カテゴリには、銀行関係の情報や公的な ID 形式が含まれます。

#### <a name="category-aba-routing-number"></a>カテゴリ: ABA ルーティング ナンバー

このカテゴリには、次のエンティティが含まれます。

:::row:::
    :::column span="":::
        **エンティティ**

        ABA ルーティング ナンバー

    :::column-end:::
    :::column span="2":::
        **詳細**

        米国銀行協会 (ABA) トランジット ルーティング ナンバー。
      
    :::column-end:::
:::row-end:::

#### <a name="category-swift-code"></a>カテゴリ: SWIFT コード

このカテゴリには、次のエンティティが含まれます。

:::row:::
    :::column span="":::
        **エンティティ**

        SWIFT コード

    :::column-end:::
    :::column span="2":::
        **詳細**

        支払指示情報の SWIFT コード。
      
    :::column-end:::
:::row-end:::

#### <a name="category-credit-card"></a>カテゴリ: クレジット カード

このカテゴリには、次のエンティティが含まれます。

:::row:::
    :::column span="":::
        **エンティティ**

        クレジット カード

    :::column-end:::
    :::column span="2":::
        **詳細**

        クレジット カード番号。 
      
    :::column-end:::
:::row-end:::

#### <a name="category-international-banking-account-number-iban"></a>カテゴリ: IBAN (International Banking Account Number) 

このカテゴリには、次のエンティティが含まれます。

:::row:::
    :::column span="":::
        **エンティティ**

        クレジット カード

    :::column-end:::
    :::column span="2":::
        **詳細**

        支払指示情報の IBAN コード。
      
    :::column-end:::
:::row-end:::

### <a name="government-and-countryregion-specific-identification"></a>政府機関と国または地域固有の識別情報

> [!NOTE]
> 次の財務エンティティと国または地域固有のエンティティは、`domain=phi` パラメーターでは返されません。
> * パスポート番号
> * 税 ID

以下のエンティティがグループ化され、国別に表示されます。

#### <a name="argentina"></a>アルゼンチン

:::row:::
    :::column span="":::
        **エンティティ**

        アルゼンチンの国民 ID (DNI) 番号

    :::column-end:::
:::row-end:::


#### <a name="austria"></a>オーストリア

:::row:::
    :::column span="":::
        **エンティティ**

        オーストリアの身分証明書

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        オーストリアの納税者番号

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        オーストリアの付加価値税 (VAT) 番号

    :::column-end:::
:::row-end:::



#### <a name="australia"></a>オーストラリア

:::row:::
    :::column span="":::
        **エンティティ**

        オーストラリアの銀行口座番号

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        オーストラリアのビジネス番号

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        オーストラリアの企業番号

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        オーストラリアの運転免許証番号  

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        オーストラリアの医療口座番号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        オーストラリアのパスポート番号

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        オーストラリアのパスポート番号

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        オーストラリアの納税者番号

    :::column-end:::

:::row-end:::


#### <a name="belgium"></a>ベルギー

:::row:::
    :::column span="":::
        **エンティティ**

        ベルギーの国民番号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        ベルギーの付加価値税 (VAT) 番号

    :::column-end:::

:::row-end:::


#### <a name="brazil"></a>ブラジル 

:::row:::
    :::column span="":::
        **エンティティ**

        ブラジルの法人番号 (CNPJ)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        ブラジルの CPF 番号

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        ブラジルの国民 ID カード (RG)

    :::column-end:::
:::row-end:::

#### <a name="canada"></a>Canada

:::row:::
    :::column span="":::
        **エンティティ**

        カナダの銀行口座番号

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        カナダの運転免許証番号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        カナダの医療サービス番号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        カナダのパスポート番号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        カナダの個人保健識別番号 (PHIN)

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        カナダの社会保険番号

    :::column-end:::
:::row-end:::

#### <a name="chile"></a>チリ 

:::row:::
    :::column span="":::
        **エンティティ**

        チリの身分証明書番号

    :::column-end:::
:::row-end:::

#### <a name="china"></a>中国

:::row:::
    :::column span="":::
        **エンティティ**

        中国の在留身分証明書 (PRC) 番号

    :::column-end:::
:::row-end:::


#### <a name="european-union-eu"></a>欧州連合 (EU)

:::row:::
    :::column span="":::
        **エンティティ**

        EU デビット カード番号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU の運転免許証番号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU の国民識別番号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU のパスポート番号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU の社会保障番号 (SSN) または同等の ID

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU 納税者番号 (TIN)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU の GPS 座標

    :::column-end:::
:::row-end:::

#### <a name="france"></a>フランス

:::row:::
    :::column span="":::
        **エンティティ**

        フランスの運転免許証番号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        フランスの健康保険番号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        フランスの国民 ID カード (CNI)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        フランスのパスポート番号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        フランスの社会保障番号 (INSEE)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        フランスの納税者番号 (Numéro SPI)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        フランスの付加価値税 (VAT) 番号

    :::column-end:::
:::row-end:::

#### <a name="germany"></a>ドイツ

:::row:::
    :::column span="":::
        **エンティティ**

        ドイツの運転免許証番号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        ドイツの身分証明書番号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        ドイツのパスポート番号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        ドイツの納税者番号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        ドイツの付加価値税番号

    :::column-end:::
:::row-end:::

#### <a name="hong-kong"></a>香港特別行政区

:::row:::
    :::column span="":::
        **エンティティ**

        香港特別行政区の身分証明書 (HKID) 番号

    :::column-end:::
:::row-end:::

#### <a name="hungary"></a>ハンガリー

:::row:::
    :::column span="":::
        **エンティティ**

        ハンガリーの個人識別番号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        ハンガリーの納税者番号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        ハンガリーの付加価値税番号

    :::column-end:::
:::row-end:::

#### <a name="india"></a>インド

:::row:::
    :::column span="":::
        **エンティティ**

        インドの基本税務番号 (PAN)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        インドの一意識別 (Aadhaar) 番号

    :::column-end:::
:::row-end:::


#### <a name="indonesia"></a>インドネシア

:::row:::
    :::column span="":::
        **エンティティ**

        インドネシアの身分証明書 (KTP) 番号

    :::column-end:::
:::row-end:::

#### <a name="ireland"></a>アイルランド

:::row:::
    :::column span="":::
        **エンティティ**

        アイルランドの個人公共サービス (PPS) 番号

    :::column-end:::
:::row-end:::

#### <a name="israel"></a>イスラエル

:::row:::
    :::column span="":::
        **エンティティ**

        イスラエルの国民 ID

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        イスラエルの銀行口座番号

    :::column-end:::
:::row-end:::

#### <a name="italy"></a>イタリア

:::row:::
    :::column span="":::
        **エンティティ**

        イタリアの運転免許証 ID

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        イタリアの個人納税者番号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        イタリアの付加価値税番号

    :::column-end:::
:::row-end:::


#### <a name="japan"></a>日本

:::row:::
    :::column span="":::
        **エンティティ**

        日本の銀行口座番号

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        日本の運転免許証番号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        日本の "マイナンバー" (個人)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        日本の "マイナンバー" (企業)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        日本の住民票コード

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        日本の在留カード番号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        日本の社会保険番号 (SIN)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        日本のパスポート番号

    :::column-end:::
:::row-end:::

#### <a name="luxembourg"></a>ルクセンブルク

:::row:::
    :::column span="":::
        **エンティティ**

        ルクセンブルグの国民識別番号 (自然人)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        ルクセンブルグの国民識別番号 (自然人以外)

    :::column-end:::
:::row-end:::

#### <a name="malta"></a>マルタ

:::row:::
    :::column span="":::
        **エンティティ**

        マルタの身分証明書番号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        マルタの納税者番号

    :::column-end:::
:::row-end:::


#### <a name="new-zealand"></a>ニュージーランド

:::row:::
    :::column span="":::
        **エンティティ**

        ニュージーランドの銀行口座番号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        ニュージーランドの運転免許証番号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        ニュージーランドの内国税収入番号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        ニュージーランド保健省 (Ministry of Health) 番号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       ニュージーランドの社会福祉番号

    :::column-end:::
:::row-end:::


#### <a name="philippines"></a>フィリピン

:::row:::
    :::column span="":::
        **エンティティ**

        フィリピンの多目的統一 ID 番号

    :::column-end:::
:::row-end:::

#### <a name="portugal"></a>ポルトガル 

:::row:::
    :::column span="":::
        **エンティティ**

        ポルトガルの市民カード番号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       ポルトガルの納税者番号

    :::column-end:::
:::row-end:::

#### <a name="singapore"></a>シンガポール

:::row:::
    :::column span="":::
        **エンティティ**

        シンガポールの国民登録 ID カード (NRIC) 番号

    :::column-end:::
:::row-end:::


#### <a name="south-africa"></a>南アフリカ

:::row:::
    :::column span="":::
        **エンティティ**

        南アフリカの識別番号

    :::column-end:::
:::row-end:::


#### <a name="south-korea"></a>韓国

:::row:::
    :::column span="":::
        **エンティティ**

        韓国の住民登録番号

    :::column-end:::
:::row-end:::

#### <a name="spain"></a>スペイン

:::row:::
    :::column span="":::
        **エンティティ**

        スペインの DNI

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        スペインの社会保障番号 (SSN)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        スペインの納税者番号

    :::column-end:::
:::row-end:::
 
#### <a name="switzerland"></a>スイス

:::row:::
    :::column span="":::
        **エンティティ**

        スイスの社会保障番号 AHV

    :::column-end:::
:::row-end:::


#### <a name="taiwan"></a>台湾 

:::row:::
    :::column span="":::
        **エンティティ**

        台湾の国民 ID

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       台湾の在留証明書 (ARC/TARC)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        台湾のパスポート番号

    :::column-end:::
:::row-end:::

#### <a name="united-kingdom"></a>イギリス

:::row:::
    :::column span="":::
        **エンティティ**

        英国 運転免許証番号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       英国 選挙人名簿番号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       英国 国民健康保険 (NHS) 番号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       英国 国民保険番号 (NINO)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       英国 または米国パスポート番号

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

       英国 納税者参照番号

    :::column-end:::

:::row-end:::


#### <a name="united-states"></a>United States

:::row:::
    :::column span="":::
        **エンティティ**

        米国社会保障番号 (SSN)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       米国運転免許証番号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       米国または英国 パスポート番号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       米国米国の個人納税者番号 (ITIN)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       米国麻薬取締局 (DEA) 番号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       米国銀行口座番号

    :::column-end:::
:::row-end:::
