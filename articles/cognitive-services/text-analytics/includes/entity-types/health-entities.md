---
title: 医療向けの名前付きエンティティ
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 10/02/2020
ms.author: aahi
ms.openlocfilehash: 614d0fe69cee88791559758d5e08dda66672669b
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/30/2021
ms.locfileid: "99097304"
---
[Text Analytics for Health](../../how-tos/text-analytics-for-health.md) を使用すると、次のカテゴリの医療概念が検出されます。  (このコンテナー プレビューでは英語のテキストのみがサポートされており、各コンテナー イメージには 1 つのモデル バージョンのみが用意されていることにご注意ください。)


| カテゴリ  | 説明  |
|---------|---------|
| [ANATOMY](#anatomy) | 身体および解剖学的な系統、部位、位置、または領域に関する情報を取得するための概念。 |
 | [DEMOGRAPHICS](#demographics) | 性別と年齢に関する情報を取得するための概念。 |
 | [EXAMINATION](#examinations) | 診断手順と検査に関する情報を取得するための概念。 |
 | [GENOMICS](#genomics) | 遺伝子と変異に関する情報を取得するための概念。 |
 | [HEALTHCARE](#healthcare) | 管理イベント、医療環境、医療関係職に関する情報を取得するための概念。 |
 | [MEDICAL CONDITION](#medical-condition) | 診断、症状、兆候に関する情報を取得するための概念。 |
 | [MEDICATION](#medication) | 薬の名前、クラス、投薬量、投与方法など、薬に関する情報を取得するための概念。 |
 | [SOCIAL](#social) | 家族関係など、医療関連の社会的な側面に関する情報を取得するための概念。 |
 | [TREATMENT](#treatment) | 治療手段に関する情報を取得するための概念。 |
  
各カテゴリには、次の 2 つの概念グループが含まれル場合があります。

* **エンティティ** - 診断、薬剤名、治療名など、医療の概念を取得するための用語。  たとえば、"*気管支炎*" は診断であり、"*アスピリン*" は薬剤名です。  このグループの言及は、UMLS 概念 ID にリンクされている可能性があります。
* **属性** - 検出されたエンティティに関する詳細情報を提供する語句。たとえば、"*重度*" は "*気管支炎*" の状態修飾子であり、*81 mg* は "*アスピリン*" の投薬量です。  このカテゴリの言及は、UMLS 概念 ID にリンクされません。

さらに、サービスにより、属性とエンティティの間の関係 (たとえば、"*身体構造*" に対する "*方向*"、"*薬剤名*" に対する "*投薬量*") や、エンティティ間の関係 (たとえば省略形の検出) など、さまざまな概念間の関係が認識されます。

## <a name="anatomy"></a>構造

### <a name="entities"></a>エンティティ

**BODY_STRUCTURE** - 身体の系統、解剖学的な位置または領域、身体の部位。 たとえば、腕、膝、腹部、鼻、肝臓、頭部、呼吸器系、リンパ球など。

:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure.png" alt-text="身体構造エンティティの例。":::


:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure-2.png" alt-text="身体構造エンティティの拡張された例。":::

### <a name="attributes"></a>属性

**DIRECTION** - 方向の語句。身体の構造を特徴付ける左、横、上、後部など。

:::image type="content" source="../../media/ta-for-health/anatomy-attributes.png" alt-text="方向属性の例。":::

### <a name="supported-relations"></a>サポートされる関係

* **DIRECTION_OF_BODY_STRUCTURE**

## <a name="demographics"></a>人口統計データ

### <a name="entities"></a>エンティティ

**AGE** - 年齢に関するすべての用語と語句。患者、家族、その他のものなど。 たとえば、40 歳、51 才、3 か月、成人、乳児、初老、若者、未成年、中年など。

:::image type="content" source="../../media/ta-for-health/age-entity.png" alt-text="年齢エンティティの例。":::

:::image type="content" source="../../media/ta-for-health/age-entity-2.png" alt-text="年齢エンティティの別の例。":::


**GENDER** - 対象者の性別を示す用語。 たとえば、男性、女性、女、紳士、淑女など。

:::image type="content" source="../../media/ta-for-health/gender-entity.png" alt-text="性別エンティティの例。":::

### <a name="attributes"></a>属性

**RELATIONAL_OPERATOR** - 人口統計エンティティと追加情報の間の関係を表す語句。

:::image type="content" source="../../media/ta-for-health/relational-operator.png" alt-text="関係演算子の例。":::

## <a name="examinations"></a>検査

### <a name="entities"></a>エンティティ

**EXAMINATION_NAME** – 診断手順と検査。 たとえば、MRI、ECG、HIV 検査、ヘモグロビン、血小板数、"*ブリストル便性状スケール*" などのスケール システム。

:::image type="content" source="../../media/ta-for-health/exam-name-entities.png" alt-text="検査エンティティの例。":::

:::image type="content" source="../../media/ta-for-health/exam-name-entities-2.png" alt-text="検査名エンティティのもう 1 つの例。":::

### <a name="attributes"></a>属性

**DIRECTION** – 検査の特徴を示す方向の用語。

:::image type="content" source="../../media/ta-for-health/exam-direction-attribute.png" alt-text="検査名エンティティの方向属性の例。":::

**MEASUREMENT_UNIT** – 検査の単位。 たとえば、"*ヘモグロビン > 9.5 g/dL*"。*g/dL* という用語は "*ヘモグロビン*" 検査の単位です。

:::image type="content" source="../../media/ta-for-health/exam-unit-attribute.png" alt-text="検査名エンティティの測定単位属性の例。":::

**MEASUREMENT_VALUE** – 検査の値。 たとえば、"*ヘモグロビン > 9.5 g/dL*"。*9.5* という用語は "*ヘモグロビン*" 検査の値です。

:::image type="content" source="../../media/ta-for-health/exam-value-attribute.png" alt-text="検査名エンティティの測定値属性の例。":::

**RELATIONAL_OPERATOR** – 検査と追加情報の間の関係を表す語句。 たとえば、対象検査に対して必要な測定値。

:::image type="content" source="../../media/ta-for-health/exam-relational-operator-attribute.png" alt-text="検査名エンティティの関係演算子の例。":::

**TIME** – 検査の開始や長さ (期間) に関する時間関連の用語。 たとえば、検査が行われた日時。

:::image type="content" source="../../media/ta-for-health/exam-time-attribute.png" alt-text="検査名エンティティの時間属性の例。":::

### <a name="supported-relations"></a>サポートされる関係

+ **DIRECTION_OF_EXAMINATION**
+   **RELATION_OF_EXAMINATION**
+   **TIME_OF_EXAMINATION**
+   **UNIT_OF_EXAMINATION**
+   **VALUE_OF_EXAMINATION**

## <a name="genomics"></a>Genomics

### <a name="entities"></a>エンティティ

**GENE** – 遺伝子に関するすべての言及。 たとえば、MTRR、F2 など。

:::image type="content" source="../../media/ta-for-health/genomics-entities.png" alt-text="遺伝子エンティティの例。":::

**VARIANT** – 遺伝子のバリエーションに関するすべての言及。 たとえば、c.524C>T、(MTRR):r.1462_1557del96 など
  
## <a name="healthcare"></a>医療

### <a name="entities"></a>エンティティ
  
**ADMINISTRATIVE_EVENT** – 医療システムに関連するが、管理または半ば管理の性質を持つイベント。 たとえば、登録、受付、試験、研究エントリ、移植、退院、入院など。 

:::image type="content" source="../../media/ta-for-health/healthcare-event-entity.png" alt-text="医療イベント エンティティの例。":::

**CARE_ENVIRONMENT** – 患者が治療を受ける環境または場所。 たとえば、緊急救命室、医師の診察室、有酸素運動ユニット、ホスピス、病院など。

:::image type="content" source="../../media/ta-for-health/healthcare-environment-entity.png" alt-text="このスクリーンショットは、医療環境エンティティの例を示しています。":::

**HEALTHCARE_PROFESSION** – 有資格または無資格の医療関係者。 たとえば、歯科医、病理学者、神経科医、放射線科医、薬剤師、栄養士、理学療法士、カイロプラクターなど。

:::image type="content" source="../../media/ta-for-health/healthcare-profession-entity.png" alt-text="このスクリーンショットは、医療環境エンティティの別の例を示しています。":::

:::image type="content" source="../../media/ta-for-health/healthcare-profession-entity-2.png" alt-text="医療環境エンティティの別の例。":::

## <a name="medical-condition"></a>病状

### <a name="entities"></a>エンティティ

**DIAGNOSIS** – 病気、症候群、中毒。 たとえば、乳がん、アルツハイマー病、HTN、CHF、脊髄損傷など。

:::image type="content" source="../../media/ta-for-health/medical-condition-entity.png" alt-text="病状エンティティの例。":::

:::image type="content" source="../../media/ta-for-health/medical-condition-entity-2.png" alt-text="病状エンティティの別の例。":::

**SYMPTOM_OR_SIGN** – 病気またはその他の診断の主観的または客観的な証拠。 たとえば、胸痛、頭痛、目まい、発疹、SOB、柔らかい腹部、正常な腸音、栄養良好など。

:::image type="content" source="../../media/ta-for-health/medical-condition-symptom-entity.png" alt-text="病状の兆候または症状エンティティの例。":::

:::image type="content" source="../../media/ta-for-health/medical-condition-symptom-entity-2.png" alt-text="病状の兆候または症状エンティティの別の例。":::

### <a name="attributes"></a>属性

**CONDITION_QUALIFIER** - 病状の説明に使用される性質に関する用語。 次のすべてのサブカテゴリは修飾子と見なされます。

1.  時間に関連する表現: これらは、時間を定性的に説明する用語です。たとえば、突然、急性、慢性、長期など。 
2.  性質の表現: これらは、病状の "性質" を説明する用語です。たとえば、ヒリヒリする、鋭いなど。
3.  重症度の表現: 重度、軽度、わずか、抑えられない。
4.  範囲の表現: 局所、病巣、広範性。
5.  放射の表現: 放散する、放射。
6.  状態のスケール: 場合によっては、状態がスケールによって特徴付けられることがあります。これは、有限個の順序付けられた値のリストです。 たとえば、ステージ III の膵臓がんの患者。
7.  状態の経過: 状態の経過や進行に関連する用語。たとえば、改善、悪化、解消、鎮静など。 

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis.png" alt-text="状態の修飾子属性と診断エンティティの例。":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis-2.png" alt-text="状態の修飾子属性と診断エンティティの別の例。":::

:::image type="content" source="../../media/ta-for-health/conditional-qualifier-symptom-medication.png" alt-text="状態の修飾子属性と症状および薬剤エンティティの例。":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis-3.png" alt-text="このスクリーンショットは、状態の修飾子属性と診断エンティティの別の例を示しています。":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-symptom.png" alt-text="このスクリーンショットは、状態の修飾子属性と診断エンティティの追加の例を示しています。":::

**DIRECTION** - 身体の病状の特徴を示す方向の用語。

:::image type="content" source="../../media/ta-for-health/medical-condition-direction-attribute.png" alt-text="方向属性と病状エンティティの例。":::

**FREQUENCY** - 病状が発生した、発生している、または発生するはずの頻度。

:::image type="content" source="../../media/ta-for-health/medical-condition-frequency-attribute.png" alt-text="頻度属性と病状エンティティの例。":::

:::image type="content" source="../../media/ta-for-health/medical-condition-frequency-attribute-2.png" alt-text="方向属性と症状または兆候エンティティの別の例。":::

**MEASUREMENT_UNIT** - 病状の特徴を示す単位。 たとえば、"*1.5 x 2 x 1 cm の腫瘍*" では、*cm* という用語は "*腫瘍*" の測定単位です。 

:::image type="content" source="../../media/ta-for-health/medical-condition-measure-unit-attribute.png" alt-text="測定単位属性と病状エンティティの例。":::

**MEASUREMENT_VALUE** - 病状の特徴を示す値。 たとえば、"*1.5 x 2 x 1 cm の腫瘍*" では、*1.5 x 2 x 1* という用語は "*腫瘍*" の測定値です。 

:::image type="content" source="../../media/ta-for-health/medical-condition-measure-value-attribute.png" alt-text="方向属性と症状または兆候エンティティの例を示すスクリーンショット。":::

**RELATIONAL_OPERATOR** - 病状の追加情報の間の関係を表す語句。 たとえば、時刻や測定値など。 

:::image type="content" source="../../media/ta-for-health/medical-condition-relational-operator.png" alt-text="方向属性と症状または兆候エンティティの別の例を示すスクリーンショット。":::

**TIME** - 病状の開始や長さ (期間) に関する時間関連の用語。 たとえば、症状が始まったとき (発病) や、病気が発生したときなど。

:::image type="content" source="../../media/ta-for-health/medical-condition-time-attribute.png" alt-text="方向属性と症状または兆候エンティティの追加の例を示すスクリーンショット。":::

### <a name="supported-relations"></a>サポートされる関係

+ **DIRECTION_OF_CONDITION**
+   **QUALIFIER_OF_CONDITION**
+   **TIME_OF_CONDITION**
+   **UNIT_OF_CONDITION**
+   **VALUE_OF_CONDITION**

## <a name="medication"></a>薬剤

### <a name="entities"></a>エンティティ

**MEDICATION_CLASS** – 類似した作用機序、関連する作用形態、類似した化学構造を持ち、同じ疾病の治療に使用される薬剤のセット。 たとえば、ACE 抑制剤、オピオイド、抗生物質、鎮痛剤など。

:::image type="content" source="../../media/ta-for-health/medication-entities-class.png" alt-text="薬剤クラス エンティティの例。":::

**MEDICATION_NAME** – 著作権を持つブランド名やブランド名以外の名前など、薬剤に関する言及。 たとえば、アドビル、イブプロフェンなど。

:::image type="content" source="../../media/ta-for-health/medication-entities-name.png" alt-text="薬剤名エンティティの例。":::

### <a name="attributes"></a>属性

**DOSAGE** - 処方された薬剤の量。 たとえば、点滴塩化ナトリウム溶液 *1000 mL* など。

:::image type="content" source="../../media/ta-for-health/medication-dosage.png" alt-text="薬剤投薬量属性の例。":::

**FREQUENCY** - 薬剤を投与する必要がある頻度。

:::image type="content" source="../../media/ta-for-health/medication-frequency.png" alt-text="薬剤頻度属性の例。":::

:::image type="content" source="../../media/ta-for-health/medication-frequency-2.png" alt-text="薬剤頻度属性の別の例。":::

**MEDICATION_FORM** - 薬剤の形態。 たとえば、溶液、錠剤、カプセル、タブレット、パッチ、ジェル、ペースト、フォーム、スプレー、ドロップ、クリーム、シロップなど。

:::image type="content" source="../../media/ta-for-health/medication-form.png" alt-text="薬剤形態属性の例。":::

**MEDICATION_ROUTE** - 薬剤の投与方法。 たとえば、経口、膣内、IV、硬膜外、局所、吸入など。

:::image type="content" source="../../media/ta-for-health/medication-route.png" alt-text="薬剤投与方法属性の例。":::

**RELATIONAL_OPERATOR** - 薬剤と追加情報の間の関係を表す語句。 たとえば、必要な測定値。

:::image type="content" source="../../media/ta-for-health/medication-relational-operator.png" alt-text="関係演算子属性と薬剤エンティティの例を示すスクリーンショット。":::

:::image type="content" source="../../media/ta-for-health/medication-time.png" alt-text="関係演算子属性と薬剤エンティティの別の例を示すスクリーンショット。":::

### <a name="supported-relations"></a>サポートされる関係

+ **DOSAGE_OF_MEDICATION**
+   **FORM_OF_MEDICATION**
+   **FREQUENCY_OF_MEDICATION**
+   **ROUTE_OF_MEDICATION**
+   **TIME_OF_MEDICATION**

## <a name="social"></a>ソーシャル

### <a name="entities"></a>エンティティ

**FAMILY_RELATION** – 対象者の家族関係に関する言及。 たとえば、父、娘、兄弟、親など。

:::image type="content" source="../../media/ta-for-health/family-relation.png" alt-text="治療時間属性の別の例を示すスクリーンショット。":::

## <a name="treatment"></a>処理方法

### <a name="entities"></a>エンティティ

**TREATMENT_NAME** – 治療手段。 たとえば、膝置換手術、骨髄移植、TAVI、ダイエットなど。

:::image type="content" source="../../media/ta-for-health/treatment-entities-name.png" alt-text="治療名エンティティの例。":::

### <a name="attributes"></a>属性

**DIRECTION** - 治療の特徴を示す方向の用語。

:::image type="content" source="../../media/ta-for-health/treatment-direction.png" alt-text="治療方向属性の例を示すスクリーンショット。":::

**FREQUENCY** - 治療が行われる、または治療を行う必要がある頻度。

:::image type="content" source="../../media/ta-for-health/treatment-frequency.png" alt-text="治療方向属性の別の例を示すスクリーンショット。":::
 
**RELATIONAL_OPERATOR** - 治療と追加情報の間の関係を表す語句。  たとえば、前回の施術からの経過時間。

:::image type="content" source="../../media/ta-for-health/treatment-relational-operator.png" alt-text="治療関係演算子属性の例。":::

**TIME** - 治療の開始や長さ (期間) に関する時間関連の用語。 たとえば、治療が行われた日付など。

:::image type="content" source="../../media/ta-for-health/treatment-time.png" alt-text="治療時間属性の例を示すスクリーンショット。":::

### <a name="supported-relations"></a>サポートされる関係

+ **DIRECTION_OF_TREATMENT**
+   **TIME_OF_TREATMENT**
+   **FREQUENCY_OF_TREATMENT**
