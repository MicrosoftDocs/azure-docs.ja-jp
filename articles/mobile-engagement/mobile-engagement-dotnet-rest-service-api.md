---
title: "REST API を使用して Azure Mobile Engagement サービス API にアクセスする"
description: "Mobile Engagement REST API を使用して Azure Mobile Engagement サービス API にアクセスする方法について説明します"
services: mobile-engagement
documentationcenter: mobile
author: wesmc7777
manager: erikre
editor: 
ms.assetid: e8df4897-55ee-45df-b41e-ff187e3d9d12
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/05/2016
ms.author: wesmc;ricksal
ms.openlocfilehash: 4b21bca6fee7012ce1dba96950ae075eded414f8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="using-rest-to-access-azure-mobile-engagement-service-apis"></a>REST を使用して Azure Mobile Engagement サービス API にアクセスする
Azure Mobile Engagement では、デバイス、リーチ/プッシュ キャンペーンなどを管理するために、[Azure Mobile Engagement REST API](https://msdn.microsoft.com/library/azure/mt683754.aspx) が提供されます。

> [!NOTE]
> Azure Mobile Engagement サービスは 2018 年 3 月に停止予定であり、現在は既存のお客様のみご利用いただけます。 詳細については、「[Mobile Engagement](https://azure.microsoft.com/en-us/services/mobile-engagement/)」を参照してください。

REST API を直接使用したくない場合は、任意の言語の SDK を生成するツールと共に使用できる、 [Swagger ファイル](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-mobileengagement/2014-12-01/swagger/mobile-engagement.json) も提供しています。 Swagger ファイルから SDK を生成するには、 [AutoRest](https://github.com/Azure/AutoRest) を使用することをお勧めします。 C# ラッパーを使用してこれらの API と対話できるようにするのと同様の方法で .NET SDK を作成しましたので、認証トークン ネゴシエーションを行ったり、自分で更新したりする必要はありません。 .NET SDK for API の使い方については、[サービス API の .NET SDK のサンプル](mobile-engagement-dotnet-sdk-service-api.md)に関する記事をご覧ください。
