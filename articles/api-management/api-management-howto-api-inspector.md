---
title: "API Inspector を使用した呼び出しのトレース - Azure API Management | Microsoft Docs"
description: "Azure API Management で API Inspector を使用して呼び出しをトレースする方法について説明します。"
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 4b222327-c8a4-4f33-9a06-adff2a9834d9
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: 94e13ac6fec09081484a2f7f5d7bc1871822743f
ms.openlocfilehash: a9d4d3be7f046af975f6dc25670070204848588c
ms.lasthandoff: 01/31/2017

---
# <a name="how-to-use-the-api-inspector-to-trace-calls-in-azure-api-management"></a>Azure API Management で API Inspector を使用して呼び出しをトレースする方法
API Management には、API のデバッグとトラブルシューティングに役立つ API Inspector ツールが用意されています。 API Inspector は、プログラムで使用することも、開発者ポータルから直接使用することもできます。 

操作のトレースに加え、API Inspector は、 [ポリシー式](https://msdn.microsoft.com/library/azure/dn910913.aspx) の評価もトレースします。 デモについては、「 [Cloud Cover Episode 177: More API Management Features (クラウド カバー エピソード 177: その他の API Management 機能の紹介)](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) 」を 21:00 まで早送りしてご覧ください。

このガイドでは、API Inspector の使い方を順をおって説明していきます。

> [!NOTE]
> API インスペクター トレースは、 [管理者](api-management-howto-create-groups.md) アカウントに属するサブスクリプション キーが要求に含まれている場合に限り生成され利用できるようになります。
> 
> 

## <a name="trace-call"> </a> API Inspector を使用した呼び出しのトレース
API Inspector を使用するには、**ocp-apim-trace: true** 要求ヘッダーを操作の呼び出しに追加します。その後、**ocp-apim-trace-location** 応答ヘッダーで示される URL を使用してトレースをダウンロードし、内容を確認します。 この操作は、プログラムで実行することも、開発者ポータルから直接実行することもできます。

このチュートリアルは API Inspector を使って、[初めての API の管理](api-management-get-started.md)に関する概要チュートリアルで構成される Basic Calculator API を使用した操作のトレース方法を説明します。 このチュートリアルをまだ完了していない場合は、Basic Calculator API をインポートするか (わずかの時間しかかかりません)、Echo API など、他のお好きな API を使用できます。 それぞれの API Management サービス インスタンスには、Echo API があらかじめ構成されています。API Management を体験、学習する目的で使用することができます。 Echo API は、受け取った入力をそのまま返します。 これを使用するには、任意の HTTP 動詞を呼び出します。すると、送った値がそのまま返されます。 

最初に、ご利用の API Management サービスの Azure Portal で **[開発者ポータル]** をクリックします。 開発者ポータルには、API の操作を見てテストするための便利な環境が用意されており、操作を直接呼び出すことができます。

> まだ API Management サービス インスタンスを作成していない場合は、[Azure API Management の使用][Get started with Azure API Management]に関するチュートリアルの「[API Management インスタンスの作成][Create an API Management service instance]」を参照してください。
> 
> 

![API Management 開発者ポータル][api-management-developer-portal-menu]

上部のメニューの **[API]** をクリックし、**[Basic Calculator]** をクリックします。

![[Echo API]][api-management-api]

**2 つの整数を追加する**操作を試すには、**[試してみる]** をクリックします。

![試してみる][api-management-open-console]

既定のパラメーターの値はそのままにし、 **[サブスクリプション キー]** ボックスの一覧で使用する製品のサブスクリプション キーを選択します。

開発者ポータルの既定では、**Ocp-Apim-Trace** ヘッダーは **true** に設定されています。 このヘッダーにより、トレースが生成されるかどうかが構成されます。

![[送信]][api-management-http-get]

**[送信]** をクリックし、操作を呼び出します。

![[送信]][api-management-send-results]

応答ヘッダーは、次の例のような値を含む **ocp-apim-trace-location** となります。

```
ocp-apim-trace-location : https://contosoltdxw7zagdfsprykd.blob.core.windows.net/apiinspectorcontainer/ZW3e23NsW4wQyS-SHjS0Og2-2?sv=2013-08-15&sr=b&sig=Mgx7cMHsLmVDv%2B%2BSzvg3JR8qGTHoOyIAV7xDsZbF7%2Bk%3D&se=2014-05-04T21%3A00%3A13Z&sp=r&verify_guid=a56a17d83de04fcb8b9766df38514742
```

トレースは、次のステップに示すように、指定された場所からダウンロードして内容を確認できます。 最新のログ エントリ 100 件のみが格納されることに注意してください。ログの場所は順番に再利用されます。 トレースを有効にして行った呼び出しが 100 回を超えると、格納された最初のトレースの上書きが開始されます。

## <a name="inspect-trace"> </a>トレースの確認
トレース内の値を確認するには、 **ocp-apim-trace-location** URL からトレース ファイルをダウンロードします。 これは JSON 形式のテキスト ファイルで、次の例に示すようなエントリを含んでいます。

```json
{
    "traceId": "abcd8ea63d134c1fabe6371566c7cbea",
    "traceEntries": {
        "inbound": [
            {
                "source": "handler",
                "timestamp": "2015-06-23T19:51:35.2998610Z",
                "elapsed": "00:00:00.0725926",
                "data": {
                    "request": {
                        "method": "GET",
                        "url": "https://contoso5.azure-api.net/calc/add?a=51&b=49",
                        "headers": [
                            {
                                "name": "Ocp-Apim-Subscription-Key",
                                "value": "5d7c41af64a44a68a2ea46580d271a59"
                            },
                            {
                                "name": "Connection",
                                "value": "Keep-Alive"
                            },
                            {
                                "name": "Host",
                                "value": "contoso5.azure-api.net"
                            }
                        ]
                    }
                }
            },
            {
                "source": "mapper",
                "timestamp": "2015-06-23T19:51:35.2998610Z",
                "elapsed": "00:00:00.0726213",
                "data": {
                    "configuration": {
                        "api": {
                            "from": "/calc",
                            "to": {
                                "scheme": "http",
                                "host": "calcapi.cloudapp.net",
                                "port": 80,
                                "path": "/api",
                                "queryString": "",
                                "query": {},
                                "isDefaultPort": true
                            }
                        },
                        "operation": {
                            "method": "GET",
                            "uriTemplate": "/add?a={a}&b={b}"
                        },
                        "user": {
                            "id": 1,
                            "groups": [
                                "Administrators",
                                "Developers"
                            ]
                        },
                        "product": {
                            "id": 1
                        }
                    }
                }
            },
            {
                "source": "handler",
                "timestamp": "2015-06-23T19:51:35.2998610Z",
                "elapsed": "00:00:00.0727522",
                "data": {
                    "message": "Request is being forwarded to the backend service.",
                    "request": {
                        "method": "GET",
                        "url": "http://calcapi.cloudapp.net/api/add?a=51&b=49",
                        "headers": [
                            {
                                "name": "Ocp-Apim-Subscription-Key",
                                "value": "5d7c41af64a44a68a2ea46580d271a59"
                            },
                            {
                                "name": "X-Forwarded-For",
                                "value": "33.52.215.35"
                            }
                        ]
                    }
                }
            }
        ],
        "outbound": [
            {
                "source": "handler",
                "timestamp": "2015-06-23T19:51:35.4256650Z",
                "elapsed": "00:00:00.1960601",
                "data": {
                    "response": {
                        "status": {
                            "code": 200,
                            "reason": "OK"
                        },
                        "headers": [
                            {
                                "name": "Pragma",
                                "value": "no-cache"
                            },
                            {
                                "name": "Content-Length",
                                "value": "124"
                            },
                            {
                                "name": "Cache-Control",
                                "value": "no-cache"
                            },
                            {
                                "name": "Content-Type",
                                "value": "application/xml; charset=utf-8"
                            },
                            {
                                "name": "Date",
                                "value": "Tue, 23 Jun 2015 19:51:35 GMT"
                            },
                            {
                                "name": "Expires",
                                "value": "-1"
                            },
                            {
                                "name": "Server",
                                "value": "Microsoft-IIS/8.5"
                            },
                            {
                                "name": "X-AspNet-Version",
                                "value": "4.0.30319"
                            },
                            {
                                "name": "X-Powered-By",
                                "value": "ASP.NET"
                            }
                        ]
                    }
                }
            },
            {
                "source": "handler",
                "timestamp": "2015-06-23T19:51:35.4256650Z",
                "elapsed": "00:00:00.1961112",
                "data": {
                    "message": "Response headers have been sent to the caller. Starting to stream the response body."
                }
            },
            {
                "source": "handler",
                "timestamp": "2015-06-23T19:51:35.4256650Z",
                "elapsed": "00:00:00.1963155",
                "data": {
                    "message": "Response body streaming to the caller is complete."
                }
            }
        ]
    }
}
```

## <a name="next-steps"> </a>次のステップ
* 「 [Cloud Cover Episode 177: More API Management Features (クラウド カバー エピソード 177: その他の API Management 機能の紹介)](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/)」でポリシー式のトレースのデモをご覧ください。 デモを表示するには、21:00 まで早送りします。

> [!VIDEO https://channel9.msdn.com/Shows/Cloud+Cover/Episode-177-More-API-Management-Features-with-Vlad-Vinogradsky/player]
> 
> 

[Use API Inspector to trace a call]: #trace-call
[Inspect the trace]: #inspect-trace
[Next steps]: #next-steps

[Configure API settings]: api-management-howto-create-apis.md#configure-api-settings
[Responses]: api-management-howto-add-operations.md#responses
[How create and publish a product]: api-management-howto-add-products.md

[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[Azure Classic Portal]: https://manage.windowsazure.com/


[api-management-developer-portal-menu]: ./media/api-management-howto-api-inspector/api-management-developer-portal-menu.png
[api-management-api]: ./media/api-management-howto-api-inspector/api-management-api.png
[api-management-echo-api-get]: ./media/api-management-howto-api-inspector/api-management-echo-api-get.png
[api-management-developer-key]: ./media/api-management-howto-api-inspector/api-management-developer-key.png
[api-management-open-console]: ./media/api-management-howto-api-inspector/api-management-open-console.png
[api-management-http-get]: ./media/api-management-howto-api-inspector/api-management-http-get.png
[api-management-send-results]: ./media/api-management-howto-api-inspector/api-management-send-results.png





