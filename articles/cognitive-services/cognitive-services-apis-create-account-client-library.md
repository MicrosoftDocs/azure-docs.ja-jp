---
title: Azure Management クライアント ライブラリを使用して Cognitive Services リソースを作成する
titleSuffix: Azure Cognitive Services
description: Azure Management クライアント ライブラリを使用して、Azure Cognitive Services リソースを作成および管理します。
services: cognitive-services
keywords: Cognitive Services, コグニティブ インテリジェンス, コグニティブ ソリューション, AI サービス
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 03/15/2021
ms.author: pafarley
zone_pivot_groups: programming-languages-set-ten
ms.openlocfilehash: e042ac263d3a30a9790ba6a3a3d404e5e9cb9aad
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/15/2021
ms.locfileid: "103472160"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-management-client-library"></a>クイック スタート:Azure Management クライアント ライブラリを使用して Cognitive Services リソースを作成する

このクイックスタートを利用すると、Azure Management クライアント ライブラリを使用して、Azure Cognitive Services リソースを作成および管理できます。

Azure Cognitive Services は、開発者がコグニティブ インテリジェンスを自分のアプリケーションに組み込むために利用できる、REST API とクライアント ライブラリを備えたクラウド ベース サービスのファミリです。 成果を挙げるために、開発者に人工知能 (AI) やデータ サイエンスのスキルや知識は必要ありません。 開発者は Azure Cognitive Services を使用して、見たり、聞いたり、話したり、理解したり、推論し始めたりできるコグニティブ ソリューションを使用したコグニティブ機能をそのアプリケーションに容易に追加することができます。

個別の AI サービスは、ご利用の Azure サブスクリプションに作成した Azure [リソース](../azure-resource-manager/management/manage-resources-portal.md)によって表されます。 リソースの作成後、生成されたキーとエンドポイントを使用して、アプリケーションを認証できます。

::: zone pivot="programming-language-csharp"

[!INCLUDE [C# SDK quickstart](includes/quickstarts/management-csharp.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [Java SDK quickstart](includes/quickstarts/management-java.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [NodeJS SDK quickstart](includes/quickstarts/management-node.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Python SDK quickstart](includes/quickstarts/management-python.md)]

::: zone-end
