---
title: 医療向けの名前付きエンティティ
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 07/28/2020
ms.author: aahi
ms.openlocfilehash: 6880391fb54791fe5f597de2305d24f8c0e47ec6
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2020
ms.locfileid: "88122464"
---
## <a name="health-entity-categories"></a>医療エンティティ カテゴリ:

次のエンティティ カテゴリは、[Text Analytics for Health](../../how-tos/text-analytics-for-health.md) によって返されます。  このコンテナー プレビューでは英語のテキストのみがサポートされており、各コンテナー イメージには 1 つのモデル バージョンのみが用意されていることに注意してください。

### <a name="named-entity-recognition"></a>名前付きエンティティの認識

|カテゴリ  |説明   |
|----------|--------------|
| Age | 年齢。 "*30 歳*" など。 |
| AdministrativeEvent | 投与イベント。 |
| BodyStructure | 臓器やその他の構造を含む、人体の一部。 "*腕*"、"*心臓*" など。 | 
| CareEnvironment | 看護または治療が行われる環境。 "*救急処置室*" など。 | 
| ConditionQualifier | 疾患のレベル。 "*軽度*"、"*進行*"、"*びまん性*" など。 | 
| 診断 | 医療条件。 "*高血圧*"など。 | 
| Direction | 方向。 "*左側*" や "*前部*" など。 | 
| Dosage | 医薬品の数または用量。 "*25 mg*" など。  | 
| ExaminationName | 検査の方法または手順。 "*X 線*" など。 | 
| RelationalOperator | 2 つのエンティティ間の関係を定義する演算子。 "*未満*" や `>=` など。  | 
| MeasurementUnit | 測定単位 (パーセントなど)。 | 
| MeasurementValue | 測定単位の数値。 | 
| FamilyRelation | 家族関係。 "*姉妹*" など。  | 
| 頻度 | 周波数。   | 
| 性別 | 性別。 | 
| Gene | *TP53* などの遺伝子エンティティ。   | 
| HealthcareProfession | 医薬品の投与方法。 "*経口投与*" など。 | 
| MedicationClass | 医薬品クラス。 たとえば "*抗生物質*" です。  | 
| MedicationForm | 薬の形態。 "*カプセル*" など。 | 
| MedicationName  | ジェネリック医薬品およびブランド名付き医薬品。 "*イブプロフェン*" など。 | 
| MedicationRoute | 医薬品の投与方法。 "*経口投与*" など。 | 
| SymptomOrSign  | 病気の症状。 "*のどの痛み*" など。 | 
| Time | 時間。 "*8 年間*"、"*今日の午前 2 時 30 分*" など。 |
| TreatmentName  | 治療名。 "*セラピー*" など。 | 
| Variant | 遺伝子エンティティの遺伝的変異体。 | 

### <a name="relation-extraction"></a>関係抽出

関係抽出により、テキスト内で言及されている概念間の意味のある関係が識別されます。 たとえば、"条件の時間" の関係は、条件名と時間を関連付けることによって検出されます。 Text Analytics for Health では、次の関係を識別できます。

|カテゴリ  |説明   |
|----------|--------------|
| DirectionOfBodyStructure | 身体構造の方向。 |
| DirectionOfCondition | 状態の方向。 |
| DirectionOfExamination | 検査の方向。 |
| DirectionOfTreatment | 治療の方向。 |
| TimeOfCondition | 疾患の発病に関連付けられた時刻。 |
| QualifierOfCondition | 疾患に関連付けられた修飾子。 |
| DosageOfMedication | 薬の投与量。 |
| FormOfMedication | 薬の形態。 |
| RouteOfMedication | 薬を摂取する投与経路または方法。 "*経口*" など。 |
| FrequencyOfMedication | 薬が摂取される頻度。 | 
| ValueOfCondition | 疾患に関連付けられた数値。 |
| UnitOfCondition | 疾患に関連付けられた単位 (時刻など)。 |
| TimeOfMedication | 薬が摂取された時刻。 |
| TimeOfTreatment | 治療が行われた時刻。 | 
| FrequencyOfTreatment | 治療が行われる頻度。 |
| ValueOfExamination | 検査に関連付けられた数値。 | 
| UnitOfExamination | 検査に関連付けられた単位 (パーセンテージなど)。 |
| RelationOfExamination | エンティティと検査の関係。 | 
| TimeOfExamination | 検査に関連付けられた時刻。 |
| 省略形 | 省略形。  | 
