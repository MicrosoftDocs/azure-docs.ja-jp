---
title: スキルセットのカスタム AML スキル
titleSuffix: Azure Cognitive Search
description: Azure Machine Learning モデルを使用して Azure Cognitive Search スキルセットの機能を拡張します。
manager: nitinme
author: mattmsft
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/12/2020
ms.openlocfilehash: 6cefe543ea8ba992b028448070bf041a77bfec64
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97630277"
---
# <a name="aml-skill-in-an-azure-cognitive-search-enrichment-pipeline"></a>Azure Cognitive Search のエンリッチメント パイプラインの AML スキル

> [!IMPORTANT] 
> このスキルは現在、パブリック プレビューの段階です。 プレビュー段階の機能はサービス レベル アグリーメントなしで提供しています。運用環境のワークロードに使用することはお勧めできません。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。 現在、.NET SDK によるサポートはありません。

**AML** スキルを使用すると、カスタム [Azure Machine Learning](../machine-learning/overview-what-is-azure-ml.md) (AML) モデルを使用して AI エンリッチメントを拡張できます。 AML モデルが [トレーニングされてデプロイされる](../machine-learning/concept-azure-machine-learning-architecture.md#workspace)と、**AML** スキルによって AI エンリッチメントに統合されます。

組み込みスキルと同様に、**AML** スキルには入力と出力があります。 入力は、デプロイされた AML サービスに JSON オブジェクトとして送信され、成功状態コードとともに JSON ペイロードを応答として出力します。 応答には、**AML** スキルによって指定された出力が含まれることが想定されます。 その他の応答はエラーと見なされ、エンリッチメントは実行されません。

> [!NOTE]
> インデクサーは、AML サービスから特定の標準 HTTP 状態コードが返された場合には、再試行を 2 回行います。 これらの HTTP 状態コードは次のとおりです。
> * `503 Service Unavailable`
> * `429 Too Many Requests`

## <a name="prerequisites"></a>前提条件

* [AML ワークスペース](../machine-learning/concept-workspace.md)
* [デプロイ済みモデル](../machine-learning/how-to-deploy-azure-kubernetes-service.md)がある、このワークスペース内の [Azure Kubernetes Service の AML コンピューティング先](../machine-learning/concept-compute-target.md)
  * [コンピューティング先では、SSL が有効になっている必要があります](../machine-learning/how-to-secure-web-service.md#deploy-on-azure-kubernetes-service)。 Azure Cognitive Search は **https** エンドポイントへのアクセスのみを許可します
  * 自己署名証明書は使用できません。

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Custom.AmlSkill

## <a name="skill-parameters"></a>スキルのパラメーター

パラメーターの大文字と小文字は区別されます。 どのパラメーターを使用するかは、[AML サービスが必要とする認証 (存在する場合)](#WhatSkillParametersToUse) によって異なります。

| パラメーター名 | 説明 |
|--------------------|-------------|
| `uri` | ([認証なし、またはキー認証の場合に必要](#WhatSkillParametersToUse)) _JSON_ ペイロードが送信される [AML サービスのスコアリング URI](../machine-learning/how-to-consume-web-service.md)。 **https** URI スキームのみが許可されます。 |
| `key` | ([キー認証](#WhatSkillParametersToUse)の場合に必要) [AML サービスのキー](../machine-learning/how-to-consume-web-service.md#authentication-with-keys)。 |
| `resourceId` | ([トークン認証](#WhatSkillParametersToUse)の場合に必要)。 AML サービスの Azure Resource Manager リソース ID。 subscriptions/{guid}/resourceGroups/{resource-group-name}/Microsoft.MachineLearningServices/workspaces/{workspace-name}/services/{service_name} という形式にする必要があります。 |
| `region` | ([トークン認証](#WhatSkillParametersToUse)の場合は省略可能)。 AML サービスがデプロイされている[リージョン](https://azure.microsoft.com/global-infrastructure/regions/)。 |
| `timeout` | (省略可能) 指定した場合は、API 呼び出しを行う http クライアントのタイムアウト値を示します。 XSD "dayTimeDuration" 値 ([ISO 8601 期間](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)値の制限されたサブセット) として書式設定する必要があります。 たとえば、60 秒の場合は `PT60S` とします。 設定しなかった場合は、既定値の 30 秒が選択されます。 タイムアウトは、最大で 230 秒、最小で 1 秒に設定できます。 |
| `degreeOfParallelism` | (省略可能) 指定した場合は、指定したエンドポイントに対してインデクサーが並列で行う呼び出しの数を示します。 エンドポイントが要求の負荷が高すぎるために失敗する場合は、この値を小さくできます。エンドポイントがより多くの要求を受け入れることができるため、インデクサーのパフォーマンスを向上させたい場合は、この値を上げることができます。  設定しない場合は、既定値の 5 が使用されます。 degreeOfParallelism には、最大値として 10、最小値として 1 を設定できます。

<a name="WhatSkillParametersToUse"></a>

## <a name="what-skill-parameters-to-use"></a>使用するスキル パラメーター

必要な AML スキル パラメーターは、AML サービスが使用する認証 (存在する場合) に応じて異なります。 AML サービスでは、次の 3 つの認証オプションが提供されます。

* [キーベースの認証](../machine-learning/how-to-authenticate-web-service.md#key-based-authentication)。 AML スキルからのスコアリング要求を認証するために、静的なキーが提供されます。
  * _uri_ および _key_ パラメーターを使用します。
* [トークンベースの認証](../machine-learning/how-to-authenticate-web-service.md#token-based-authentication)。 AML サービスは、[トークン ベースの認証を使用してデプロイされます](../machine-learning/how-to-authenticate-web-service.md#token-based-authentication)。 Azure Cognitive Search サービスの[マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) には、AML サービスのワークスペースの[閲覧者ロール](../machine-learning/how-to-assign-roles.md)が付与されます。 その後、AML スキルは Azure Cognitive Search サービスのマネージド ID を使用して、AML サービスに対して認証を行います。静的なキーは必要ありません。
  * _resourceId_ パラメーターを使用します。
  * Azure Cognitive Search サービスが AML ワークスペースとは異なるリージョンにある場合は、_region_ パラメーターを使用して、AML サービスがデプロイされたリージョンを設定します。
* 認証なし。 AML サービスを使用するために認証を必要としません。
  * _uri_ パラメーターを使用します。

## <a name="skill-inputs"></a>スキルの入力

このスキルの "定義済みの" 入力はありません。 このスキルの実行時に既に利用可能な 1 つ以上のフィールドを入力として選択できます。そのため、AML サービスに送信される _JSON_ ペイロードには、さまざまなフィールドが存在することになります。

## <a name="skill-outputs"></a>スキルの出力

このスキルの "定義済みの" 出力はありません。 AML サービスが返す応答に応じて出力フィールドを追加し、_JSON_ 応答からそれらを選択できるようにしてください。

## <a name="sample-definition"></a>定義例

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.AmlSkill",
    "description": "A sample model that detects the language of sentence",
    "uri": "https://contoso.count-things.com/score",
    "context": "/document",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "detected_language_code"
      }
    ]
  }
```

## <a name="sample-input-json-structure"></a>入力の JSON 構造体のサンプル

この _JSON_ 構造体は、AML サービスに送信されるペイロードを表します。 構造体の最上位レベルのフィールドは、スキル定義の `inputs` セクションで指定された "names" に対応します。 これらのフィールドの値は、それらのフィールドの `source` からの値になります (これは、ドキュメント内のフィールドからの値になる可能性もありますし、他のスキルからの値になる可能性もあります)

```json
{
  "text": "Este es un contrato en Inglés"
}
```

## <a name="sample-output-json-structure"></a>出力の JSON 構造体のサンプル

出力は、AML サービスから返された応答に対応します。 AML サービスは、_JSON_ ペイロードのみを返す必要があります (`Content-Type` 応答ヘッダーを確認することで検証されます)。また、フィールドが `output` 内の "names" に一致するエンリッチメントで、値がエンリッチメントと見なされるオブジェクトである必要もあります。

```json
{
    "detected_language_code": "es"
}
```

## <a name="inline-shaping-sample-definition"></a>インライン整形のサンプルの定義

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.AmlSkill",
    "description": "A sample model that detects the language of sentence",
    "uri": "https://contoso.count-things.com/score",
    "context": "/document",
    "inputs": [
      {
        "name": "shapedText",
        "sourceContext": "/document",
        "inputs": [
            {
              "name": "content",
              "source": "/document/content"
            }
        ]
      }
    ],
    "outputs": [
      {
        "name": "detected_language_code"
      }
    ]
  }
```

## <a name="inline-shaping-input-json-structure"></a>インライン整形の入力 JSON 構造体

```json
{
  "shapedText": { "content": "Este es un contrato en Inglés" }
}
```

## <a name="inline-shaping-sample-output-json-structure"></a>インライン整形のサンプル出力 JSON 構造体

```json
{
    "detected_language_code": "es"
}
```

## <a name="error-cases"></a>エラーになる場合
AML が利用できない場合や、成功以外の状態コードが送信された場合に加え、次の場合もエラーと見なされます。

* AML サービスからは成功の状態コードが返されたものの、応答ではそれが `application/json` でないと示されている場合、その応答は無効と見なされ、エンリッチメントは実行されません。
* AML サービスが無効な JSON を返した場合

AML サービスが利用できないか、HTTP エラーが返された場合は、HTTP エラーに関する入手可能な詳細情報が含まれるわかりやすいエラーが、インデクサーの実行履歴に追加されます。

## <a name="see-also"></a>関連項目

+ [スキルセットの定義方法](cognitive-search-defining-skillset.md)
+ [AML Service のトラブルシューティング](../machine-learning/how-to-troubleshoot-deployment.md)