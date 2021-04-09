---
title: Azure Resource Manager テンプレートを使用してサービスをデプロイする
titleSuffix: Azure Cognitive Search
description: Azure Resource Manager テンプレートを使用すると、Azure Cognitive Search サービス インスタンスをすばやくデプロイすることができます。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 11/17/2020
ms.openlocfilehash: 88a081f1ebbda52653302384c06403e223128c60
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101668386"
---
# <a name="quickstart-deploy-cognitive-search-using-an-arm-template"></a>クイック スタート:ARM テンプレートを使用して Cognitive Search をデプロイする

この記事では、Azure portal で Azure Resource Manager テンプレート (ARM テンプレート) を使用して Azure Cognitive Search リソースをデプロイする手順について説明します。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-search-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="review-the-template"></a>テンプレートを確認する

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-azure-search-create/)からのものです。

:::code language="json" source="~/quickstart-templates/101-azure-search-create/azuredeploy.json":::

このテンプレートで定義されている Azure リソースは次のとおりです。

- [Microsoft.Search/searchServices](/azure/templates/Microsoft.Search/searchServices): Azure Cognitive Search サービスを作成します。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

Azure にサインインし、テンプレートを開くには次のイメージを選択します。 このテンプレートは、Azure Cognitive Search リソースを作成するものです。

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-search-create%2Fazuredeploy.json)

ポータルには、パラメーター値を簡単に指定できるフォームが表示されます。 一部のパラメーターには、テンプレートの既定値があらかじめ入力されています。 サブスクリプション、リソース グループ、場所、サービス名を指定する必要があります。 Cognitive Services を [AI エンリッチメント](cognitive-search-concept-intro.md) パイプラインで使用したい場合 (バイナリ画像ファイルのテキスト分析を行うなど)、Cognitive Search と Cognitive Services の両方が提供されている場所を選びます。 AI エンリッチメント ワークロードでは、その両方のサービスが同じリージョンに存在する必要があります。 フォームへの入力が済んだら、使用条件に同意し、購入ボタンを選択して、デプロイを完了する必要があります。

> [!div class="mx-imgBorder"]
> ![Azure portal のテンプレートの表示](./media/search-get-started-arm/arm-portalscrnsht.png)

## <a name="review-deployed-resources"></a>デプロイされているリソースを確認する

デプロイが完了したら、ポータルから新しいリソース グループと新しい検索サービスにアクセスすることができます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Cognitive Search に関する他のクイックスタートとチュートリアルは、このクイックスタートに基づいています。 後続のクイックスタートおよびチュートリアルを引き続き実行する場合は、このリソースをそのまま残しておくことをお勧めします。 不要になったら、リソース グループを削除します。これにより、Cognitive Search サービスと関連リソースが削除されます。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、ARM テンプレートを使用して Cognitive Search サービスを作成し、デプロイを検証しました。 Cognitive Search と Azure Resource Manager の詳細については、引き続き以下の記事を参照してください。

- [Azure Cognitive Search の概要](search-what-is-azure-search.md)を確認します。
- 検索サービス用の[インデックスを作成](search-get-started-portal.md)します。
- ポータルのウィザードを使用して[デモ アプリを作成](search-create-app-portal.md)します。
- データから情報を抽出するための[スキルセットを作成](cognitive-search-quickstart-blob.md)します。
