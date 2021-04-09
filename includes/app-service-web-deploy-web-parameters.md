---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 329db6b3fc0bd6d11e5fbac9472aa03899caec2a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86050211"
---
Azure リソース マネージャーを使用して、テンプレートのデプロイ時に値を指定するパラメーターを定義します。 テンプレートには、すべてのパラメーター値を含む Parameters という名前のセクションがあります。
これらの値のパラメーターを定義する必要があります。これらの値は、デプロイするプロジェクトやデプロイ先の環境に応じて異なります。 常に同じ値に対してはパラメーターを定義しないでください。 テンプレート内のそれぞれのパラメーターの値は、デプロイされるリソースを定義するために使用されます。 

デプロイ中にユーザーが指定できる値を指定するには、パラメーターを定義するときに **allowedValues** フィールドを使用します。 **defaultValue** フィールドは、デプロイ中に値が指定されなかった場合にパラメーターに割り当てる値を指定するために使用します。

テンプレートに含まれるそれぞれのパラメーターについて説明します。

### <a name="sitename"></a>siteName
作成する Web アプリの名前。

```config
"siteName":{
  "type":"string"
}
```

### <a name="hostingplanname"></a>hostingPlanName
Web アプリをホストするために使用する App Service プランの名前。

```config
"hostingPlanName":{
  "type":"string"
}
```

### <a name="sku"></a>sku
ホスティング プランの価格レベル。

```config
"sku": {
  "type": "string",
  "allowedValues": [
    "F1",
    "D1",
    "B1",
    "B2",
    "B3",
    "S1",
    "S2",
    "S3",
    "P1",
    "P2",
    "P3",
    "P4"
  ],
  "defaultValue": "S1",
  "metadata": {
    "description": "The pricing tier for the hosting plan."
  }
}
```

テンプレートでは、このパラメーターに指定できる値を定義します。値が指定されない場合は既定値 (S1) が割り当てられます。

### <a name="workersize"></a>workerSize
ホスティング プランのインスタンス サイズ (小、中、または大)。

```config
"workerSize":{
  "type":"string",
  "allowedValues":[
    "0",
    "1",
    "2"
  ],
  "defaultValue":"0"
}
```

テンプレートには、このパラメーターに指定できる値 (0、1、または 2) を定義します。値が指定されない場合は既定値 (0) が割り当てられます。 値はそれぞれ、小、中、および大に対応しています。

