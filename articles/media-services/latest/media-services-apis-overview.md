---
title: v3 API を使用した開発 - Azure | Microsoft Docs
description: この記事では、Media Services v3 を使用して開発を行う際にエンティティと API に適用される規則について説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/15/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 5ae7563892cb4792f5c329b2850d7b88d37c0e7d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2019
ms.locfileid: "59698274"
---
# <a name="developing-with-media-services-v3-apis"></a>Media Services v3 API を使用した開発

この記事では、Media Services v3 を使用して開発を行う際にエンティティと API に適用される規則について説明します。

## <a name="accessing-the-azure-media-services-api"></a>Azure Media Services API へのアクセス

Azure Media Services リソースにアクセスするには、Azure Active Directory (AD) のサービス プリンシパル認証を使うことができます。
Media Services API では、REST API 要求を行うユーザーまたはアプリケーションは、Media Services アカウント リソースへのアクセス権を持ち、**共同作成者**または**所有者**のロールを使用することが必要です。 **閲覧者**ロールで API にアクセスすることはできますが、使用できる操作は **Get** または **List**  だけです。 詳細については、「[Media Services アカウント用のロールベースのアクセス制御](rbac-overview.md)」を参照してください。

サービス プリンシパルを作成する代わりに、Azure リソースに対するマネージド ID を使い、Azure Resource Manager で Media Services API にアクセスすることを検討してください。 Azure リソースに対するマネージド ID の詳細については、「[Azure リソースのマネージド ID とは](../../active-directory/managed-identities-azure-resources/overview.md)」を参照してください。

### <a name="azure-ad-service-principal"></a>Azure AD のサービス プリンシパル 

Azure AD アプリケーションとサービス プリンシパルを作成する場合、アプリケーションは専用のテナントに置く必要があります。 アプリケーションを作成した後、アプリの**共同作成者**または**所有者**ロールのアクセス権を、Media Services アカウントに付与します。 

Azure AD アプリケーションを作成するためのアクセス許可を自分が持っているかどうかわからない場合は、「[必要なアクセス許可](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)」を参照してください。

次の図の番号は、要求のフローを時系列で表したものです。

![中間層アプリ](./media/use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. 中間層アプリが、次のパラメーターが含まれた Azure AD アクセス トークンを要求します。  

   * Azure AD テナント エンドポイント。
   * Media Services リソース URI。
   * REST Media Services のリソース URI。
   * Azure AD アプリケーションの値 (クライアント ID と クライアント シークレット)。
   
   必要な値をすべて取得するには、「[Azure CLI で Azure Media Services API にアクセスする](access-api-cli-how-to.md)」をご覧ください

2. Azure AD アクセス トークンが中間層アプリに送信されます。
4. 中間層アプリが、Azure AD トークンを使用して要求を Azure Media REST API に送信します。
5. Media Services からデータが中間層アプリに返されます。

## <a name="naming-conventions"></a>名前付け規則

Azure Media Services v3 のリソース名 (アセット、ジョブ、変換など) には、Azure Resource Manager の名前付け規則が適用されます。 Azure Resource Manager に従って、リソース名は常に一意となります。 そのため、リソース名には一意識別子の文字列 (GUID など) を使用できます。 

Media Services リソース名には、"<",">"、"%"、"&"、":"、"&#92;"、"?"、"/"、"*"、"+"、"."、一重引用符などの制御文字を使用することができません。 それ以外の文字は使用できます。 リソース名の最大文字数は 260 文字です。 

Azure Resource Manager の名前付けの詳細については、[名前付けの要件](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource)と[名前付け規則](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。

## <a name="long-running-operations"></a>長時間にわたって実行される操作

Azure Media Services の [Swagger ファイル](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json)に `x-ms-long-running-operation` とマークされた操作は長期操作です。 

非同期の Azure 操作を追跡する方法について詳しくは、[非同期操作](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation)に関するセクションを参照してください。

Media Services には、次のような長期操作があります。

* LiveEvent の作成
* LiveEvent の更新
* LiveEvent の削除
* LiveEvent の開始
* LiveEvent の停止
* LiveEvent のリセット
* LiveOutput の作成
* LiveOutput の削除
* StreamingEndpoint の作成
* StreamingEndpoint の更新
* StreamingEndpoint の削除
* StreamingEndpoint の開始
* StreamingEndpoint の停止
* StreamingEndpoint のスケーリング

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Media Services エンティティのフィルター処理、順序付け、ページング

[Azure Media Services エンティティのフィルター処理、順序付け、ページング](entities-overview.md)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

[SDK/ツールを使用して Media Services v3 API での開発を始める](developers-guide.md)
