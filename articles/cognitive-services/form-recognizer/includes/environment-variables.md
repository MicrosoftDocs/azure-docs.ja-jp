---
title: 環境変数
description: 環境変数を設定する
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/06/2020
ms.author: pafarley
ms.openlocfilehash: d63e902a59411a549235c955a39d7dbc4be068ba
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86156677"
---
作成したリソースのキーとエンドポイントを使用して、認証用に 2 つの環境変数を作成します。

* `FORM_RECOGNIZER_KEY` - 要求を認証するためのリソース キー。
* `FORM_RECOGNIZER_ENDPOINT` - API 要求を送信するためのリソース エンドポイント。 以下のようになります。 
  * `https://<your-custom-subdomain>.cognitiveservices.azure.com`

>[!NOTE]
> 2019 年 7 月 1 日より後に作成されたリソースのエンドポイントでは、次に示すカスタム サブドメイン形式を使用します。 リージョンのエンドポイントの詳細および全一覧については、「[Cognitive Services のカスタム サブドメイン名](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains)」を参照してください。 

オペレーティング システムで環境変数を設定するには、次の手順に従います。

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx FORM_RECOGNIZER_KEY <replace-with-your-form-recognizer-key>
setx FORM_RECOGNIZER_ENDPOINT <replace-with-your-form-recognizer-endpoint>
```

環境変数を追加したら、コンソール ウィンドウを閉じて再度開きます。

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export FORM_RECOGNIZER_KEY=<replace-with-your-product-name-key>
export FORM_RECOGNIZER_ENDPOINT=<replace-with-your-product-name-endpoint>
```

環境変数を追加した後、変更を有効にするには、コンソール ウィンドウから `source ~/.bashrc` を実行します。

#### <a name="macos"></a>[macOS](#tab/unix)

次のように `.bash_profile` を編集し、環境変数を追加します。

```bash
export FORM_RECOGNIZER_KEY=<replace-with-your-product-name-key>
export FORM_RECOGNIZER_ENDPOINT=<replace-with-your-product-name-endpoint>
```

環境変数を追加した後、変更を有効にするには、コンソール ウィンドウから `source .bash_profile` を実行します。
***
