---
title: "Azure Functions でのプロキシの操作 | Microsoft Docs"
description: "Azure Functions Proxies の使用方法の概要"
services: functions
documentationcenter: 
author: mattchenderson
manager: erikre
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 02/19/2017
ms.author: mahender
translationtype: Human Translation
ms.sourcegitcommit: facdd2abbb90a85535b8936ea655a824134c4c8b
ms.openlocfilehash: 3e8b8f9908cf24baf7a5d70521c79dbd470001f8
ms.lasthandoff: 02/22/2017


---
# <a name="working-with-azure-functions-proxies-preview"></a>Azure Functions Proxies (プレビュー) の操作

> [!Note] 
> Azure Functions Proxies は、現在、プレビューの段階です。 プレビューの間は無料ですが、プロキシの実行には標準の Functions の料金が適用されます。 詳細については、[Azure Functions の価格](https://azure.microsoft.com/pricing/details/functions/)に関するページを参照してください。

この記事では、Azure Functions Proxies を構成および操作する方法について説明します。 この機能を使用すると、他のリソースによって実装される、関数アプリのエンドポイントを指定することができます。 これらのプロキシを使用して、大きな API を複数の関数アプリに分割できます (マイクロサービス アーキテクチャでのように)。その場合でも、クライアントには単一の API サーフェスとして表示されます。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]


## <a name="a-nameenableaenabling-azure-functions-proxies"></a><a name="enable"></a>Azure Functions Proxies の有効化

既定では、プロキシは有効になっていません。 機能が無効になっていてもプロキシを作成することはできますが、実行されません。 以下の手順は、プロキシを有効にする方法を示しています。

1. [Azure Portal] を開き、関数アプリに移動します。
2. **[Function App の設定]** を選択します。
3. **[Enable Azure Functions Proxies (preview) (Azure Functions Proxies (プレビュー) を有効にする)]** をオンにします。

新機能が使用可能になったときに、ここに戻って、プロキシ ランタイムを更新することもできます。


## <a name="creating-a-proxy"></a>プロキシの作成

このセクションでは、Functions ポータルでプロキシを作成する方法を示します。

1. [Azure Portal] を開き、関数アプリに移動します。
2. 左側のナビゲーションで、**[New proxy (新しいプロキシ)]** を選択します。
3. プロキシの名前を指定します。
4. **ルート テンプレート**と **HTTP メソッド**を指定して、この関数アプリで公開されるエンドポイントを構成します。 これらのパラメーターは、[HTTP トリガー]の規則に従って動作します。
5. **バックエンド URL** を他のエンドポイントに設定します。 これは、別の関数アプリ内の関数にすることも、他の任意の API にすることもできます。
6. [作成] をクリックします。

これで、プロキシが関数アプリの新しいエンドポイントになりました。 クライアントの観点からは、Azure Functions の HttpTrigger と同じです。 プロキシの URL をコピーし、任意の HTTP クライアントでテストすることで、新しいプロキシを試してみることができます。


## <a name="a-namemodify-requestsamodifying-backend-requests"></a><a name="modify-requests"></a>バックエンド要求の変更

バックエンド URL パラメーターは、静的である必要はありません。 要求またはアプリケーション設定からの入力に条件付けることができます。


### <a name="using-request-parameters"></a>要求パラメーターの使用

ルート テンプレートで使用されるパラメーターは、バックエンド URL プロパティへの入力として使用される可能性があります。 値は、中かっこ "{}" で囲まれた名前で参照します。

たとえば、プロキシに `/pets/{petId}` のようなルート テンプレートがある場合、バックエンド URL には `https://<AnotherApp>.azurewebsites.net/api/pets/{petId}` のようにして、`{petId}` の値を含めることができます。 ルート テンプレートが `/api/{*restOfPath}` のようにワイルドカードで終了している場合、値 `{restOfPath}` は受信要求の残りのパス セグメントの文字列表現になります。


### <a name="using-application-settings"></a>アプリケーション設定の使用

[アプリケーション設定](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings#develop)を参照することもできます。その場合は、設定名をパーセント記号 "%" で囲みます。

たとえば、`https://%ORDER_PROCESSING_HOST%/api/orders` のバックエンド URL で、"%ORDER_PROCESSING_HOST%" は ORDER_PROCESSING_HOST 設定の値に置き換えられます。

> [!TIP] 
> 複数のデプロイまたはテスト環境がある場合は、バックエンド ホストのアプリケーション設定を使用してください。 そうすることで、常にその環境の適切なバックエンドと通信することができます。



## <a name="deployment-methods"></a>デプロイ方法

構成するプロキシは、関数アプリ ディレクトリのルートにある proxies.json ファイルに格納されます。 このファイルを手動で編集し、Functions でサポートされているいずれかの[デプロイ方法](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment)を使用するときにアプリの一部としてデプロイできます。

ファイルが処理されるようにするには、この機能を有効にしておく必要があります。 そのためには、「[Azure Functions Proxies の有効化](#enable)」の手順に従います。

Proxies.json は、名前付きプロキシとその定義から成るプロキシ オブジェクトによって定義されます。 サンプル ファイルは、次のようになります。

```json
{
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>"
        }
    }
}
```

各プロキシには、上の例の "proxy1" のようなフレンドリ名があります。 対応するプロキシ定義オブジェクトは、以下のプロパティによって定義されます。

* **matchCondition**: 必須 - このプロキシの実行をトリガーする要求を定義するオブジェクト。 [HTTP トリガー]と共有される、次の&2; つのプロパティが含まれています。
    * _methods_: プロキシが応答する HTTP メソッドの配列です。 指定しない場合、プロキシはルートのすべての HTTP メソッドに応答します。
    * _route_: 必須 - プロキシがどの要求 URL に応答するかを制御するルート テンプレートを定義します。 HTTP トリガーとは異なり、既定値はありません。
* **backendUri**: 要求のプロキシ先となる、バックエンド リソースの URL。 この値は、「[バックエンド要求の変更](#modify-requests)」で説明されているように、テンプレート化される場合があります。 このプロパティが含まれていない場合、Azure Functions は HTTP 200 OK で応答します。

> [!Note] 
> ルート プロパティ Azure Functions Proxies では、Functions ホスト構成の routePrefix プロパティは考慮されません。 /api のようなプレフィックスを含めたい場合は、ルート プロパティに含める必要があります。



[Azure Portal]: https://portal.azure.com
[HTTP トリガー]: https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook#http-trigger
