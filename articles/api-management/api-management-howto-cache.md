---
title: キャッシュを追加して Azure API Management のパフォーマンスを向上させる | Microsoft Docs
description: API Management のサービスの呼び出しで、遅延、帯域幅の消費、Web サービスの負荷を改善させる方法について説明します。
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 740f6a27-8323-474d-ade2-828ae0c75e7a
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: apimpm
ms.openlocfilehash: a0459eb67b5a79219e556cb03473a5ddf691b49d
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2019
ms.locfileid: "58577018"
---
# <a name="add-caching-to-improve-performance-in-azure-api-management"></a>キャッシュを追加して Azure API Management のパフォーマンスを向上させる

API Management では、応答のキャッシュ用に操作を構成できます。 応答のキャッシュを行うと、API の遅延、帯域幅の消費、頻繁に変更されないデータの Web サービスの負荷が大幅に小さくなります。

キャッシュの詳細については、「[API Management のキャッシュ ポリシー](api-management-caching-policies.md)」と「[Azure API Management のカスタム キャッシュ](api-management-sample-cache-by-key.md)」を参照してください。

![キャッシュ ポリシー](media/api-management-howto-cache/cache-policies.png)

ここでは、次の内容について学習します。

> [!div class="checklist"]
> * API の応答キャッシュの追加
> * 動作中のキャッシュの確認

## <a name="availability"></a>可用性

> [!NOTE]
> 内部キャッシュは Azure API Management の**従量課金**レベルでは利用できません。 代わりに[外部 Azure Cache for Redis を使用](api-management-howto-cache-external.md)できます。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

+ [Azure API Management インスタンスを作成する](get-started-create-service-instance.md)
+ [API をインポートおよび発行する](import-and-publish.md)

## <a name="caching-policies"> </a>キャッシュ ポリシーの追加

この例に示すキャッシュ ポリシーでは、**GetSpeakers** 操作に対する最初の要求は、バックエンド サービスからの応答を返します。 この応答はキャッシュされ、指定されたヘッダーとクエリ文字列パラメーターによってキーが設定されます。 パラメーターが一致する後続の操作の呼び出しに対しては、キャッシュの有効期間が超過するまで、キャッシュに格納された応答が返されます。

1. Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。
2. APIM インスタンスを参照します。
3. **[API]** タブを選択します。
4. API の一覧で **[Demo Conference API]\(デモ会議 API\)** をクリックします。
5. **[GetSpeakers]** を選択します。
6. 画面の上部の **[デザイン]** タブを選択します。
7. **[受信処理]** セクションで、**[</>]** アイコンをクリックします。

    ![コード エディター](media/api-management-howto-cache/code-editor.png)

8. **inbound** 要素で、次のポリシーを追加します。

        <cache-lookup vary-by-developer="false" vary-by-developer-groups="false">
            <vary-by-header>Accept</vary-by-header>
            <vary-by-header>Accept-Charset</vary-by-header>
            <vary-by-header>Authorization</vary-by-header>
        </cache-lookup>

9. **outbound** 要素で、次のポリシーを追加します。

        <cache-store caching-mode="cache-on" duration="20" />

    **[期間]** は、キャッシュに入れられた応答の有効期間を指定します。 この例では、間隔は **20** 秒です。

> [!TIP]
> 外部キャッシュを使用している場合は、「[Azure API Management で外部の Azure Cache for Redis を使用する](api-management-howto-cache-external.md)」の説明に従って、キャッシュ ポリシーの `caching-type` 属性を指定する必要がある場合があります。 詳しくは、「[API Management のキャッシュ ポリシー](api-management-caching-policies.md)」をご覧ください。

## <a name="test-operation"> </a>操作の呼び出しとキャッシュのテスト
動作中のキャッシュを確認するには、開発者ポータルから操作を呼び出します。

1. Azure Portal で、APIM インスタンスに移動します。
2. **[API]** タブを選択します。
3. キャッシュ ポリシーを追加した API を選択します。
4. **[GetSpeakers]** 操作を選択します。
5. 右上のメニューの **[テスト]** タブをクリックします。
6. **[送信]** をクリックします。

## <a name="next-steps"> </a>次のステップ
* キャッシュ ポリシーの詳細については、[API Management ポリシー リファレンス][API Management policy reference]の[キャッシュ ポリシー][Caching policies]に関するページを参照してください。
* ポリシー式を使ってキーごとにアイテムをキャッシュする方法については、「 [Azure API Management のカスタム キャッシュ](api-management-sample-cache-by-key.md)」を参照してください。
* 外部の Azure Cache for Redis の使用方法について詳しくは、「[Azure API Management で外部の Azure Cache for Redis を使用する](api-management-howto-cache-external.md)」をご覧ください。

[api-management-management-console]: ./media/api-management-howto-cache/api-management-management-console.png
[api-management-echo-api]: ./media/api-management-howto-cache/api-management-echo-api.png
[api-management-echo-api-operations]: ./media/api-management-howto-cache/api-management-echo-api-operations.png
[api-management-caching-tab]: ./media/api-management-howto-cache/api-management-caching-tab.png
[api-management-operation-dropdown]: ./media/api-management-howto-cache/api-management-operation-dropdown.png
[api-management-policy-editor]: ./media/api-management-howto-cache/api-management-policy-editor.png
[api-management-developer-portal-menu]: ./media/api-management-howto-cache/api-management-developer-portal-menu.png
[api-management-apis-echo-api]: ./media/api-management-howto-cache/api-management-apis-echo-api.png
[api-management-open-console]: ./media/api-management-howto-cache/api-management-open-console.png
[api-management-console]: ./media/api-management-howto-cache/api-management-console.png


[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md

[API Management policy reference]: https://msdn.microsoft.com/library/azure/dn894081.aspx
[Caching policies]: https://msdn.microsoft.com/library/azure/dn894086.aspx

[Create an API Management service instance]: get-started-create-service-instance.md

[Configure an operation for caching]: #configure-caching
[Review the caching policies]: #caching-policies
[Call an operation and test the caching]: #test-operation
[Next steps]: #next-steps
