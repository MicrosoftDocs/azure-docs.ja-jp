---
title: クイックスタート - ARM テンプレートを使用して Azure API Management インスタンスを作成する
description: Azure Resource Manager テンプレート (ARM テンプレート) を使用して Developer レベルで Azure API Management インスタンスを作成する方法について学習します。
services: azure-resource-manager
author: dlepow
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: danlep
ms.date: 10/09/2020
ms.openlocfilehash: 1610c51b613712c06410247e2eb673a50a9988b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92792244"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-using-an-arm-template"></a>クイックスタート: ARM テンプレートを使用して新しい Azure API Management サービス インスタンスを作成する

このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用して Azure API Management (APIM) サービス インスタンスを作成する方法を説明します。 APIM は、外部開発者、パートナー開発者、および内部開発者に API を公開してデータおよびサービスの可能性の扉を開くことができるように、組織を支援します。 API Management は、開発者の取り組み、ビジネス インサイト、分析、セキュリティ、保護を通じて API プログラムの価値を高め、企業にコア コンピテンシーをもたらします。 APIM を使用すると、任意の場所でホストされている既存のバックエンド サービスの最新の API ゲートウェイを作成し、管理できます。 詳細については、[概要](api-management-key-concepts.md)に関するページを参照してください。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-api-management-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="review-the-template"></a>テンプレートを確認する

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-azure-api-management-create/)からのものです。

:::code language="json" source="~/quickstart-templates/101-azure-api-management-create/azuredeploy.json":::

テンプレートには、次のリソースが定義されています。

- **[Microsoft.ApiManagement/service](/azure/templates/microsoft.apimanagement/service)**

その他の Azure API Management テンプレートのサンプルは、[Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Apimanagement&pageNumber=1&sort=Popular)のページから入手できます。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

1. Azure にサインインし、テンプレートを開くには次のイメージを選択します。 このテンプレートを使用すると、自動的に生成された名前を使用して API Management サービス インスタンスが作成されます。

    [![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-api-management-create%2Fazuredeploy.json)

    この例では、Azure API Management を評価するための経済的なオプションである Developer レベルにインスタンスが構成されます。 このレベルは運用目的では使用できません。 API Management レベルのスケーリングの詳細については、[アップグレードとスケーリング](upgrade-and-scale.md)に関するをページをご覧ください。

1. 次の値を選択または入力します。
    - **サブスクリプション**: Azure サブスクリプションを選択します。
    - **リソース グループ**: **[新規作成]** を選択し、リソース グループの一意の名前を入力し、 **[OK]** を選択します。
    - **[リージョン]** : リソース グループの場所を選択します。 例:**米国中部**。
    - **[Publisher Email]\(公開者の電子メール\)** : 通知を受信するためのメール アドレスを入力します。
    - **[Publisher Name]\(公開者名\)** : API 公開者用に選択した名前を入力します。
    - **[SKU]** : 既定値の **Developer** を受け入れます。
    - **[Sku Count]\(SKU 数\)** : 既定値を受け入れます。
    - **[場所]** : API Management サービスに対して生成された場所を受け入れます。

    :::image type="content" source="media/quickstart-arm-template/create-instance-template.png" alt-text="API Management テンプレートのプロパティ":::

1. **[Review + Create]\(確認と作成\)** を選択し、使用条件を確認します。 同意する場合は、 **[作成]** を選択します。

    > [!TIP]
    >  Developer レベルに API Management サービスを作成してアクティブにするには、30 から 40 分かかる場合があります。

1. インスタンスが正常に作成されると、次の通知が表示されます。

    :::image type="content" source="media/quickstart-arm-template/deployment-notification.png" alt-text="デプロイの通知":::

 テンプレートをデプロイするには Azure portal を使用します。 Azure portal だけでなく、Azure PowerShell、Azure CLI、および REST API を使用することもできます。 他のデプロイ方法については、「[テンプレートのデプロイ](../azure-resource-manager/templates/deploy-cli.md)」を参照してください。

## <a name="review-deployed-resources"></a>デプロイされているリソースを確認する

Azure portal を使用してデプロイされたリソースを確認するか、Azure CLI や Azure PowerShell などのツールを使用してデプロイされたリソースを一覧表示します。

1. [Azure portal](https://portal.azure.com) で、**API Management サービス** を検索して選択し、作成したサービス インスタンスを選択します。
1. **[概要]** ページで、サービスのプロパティを確認します。

:::image type="content" source="media/quickstart-arm-template/service-instance-created.png" alt-text="サービスの概要ページ":::

API Management サービス インスタンスがオンラインになったら、使用する準備ができています。 最初の API を[インポートして公開する](import-and-publish.md)ためのチュートリアルを開始します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

後続のチュートリアルを引き続き実行する場合は、API Management インスタンスをそのまま残しておくことができます。 不要になったら、リソース グループを削除します。これにより、リソース グループ内のリソースが削除されます。

1. [Azure portal](https://portal.azure.com) で、 **[リソース グループ]** を検索して選択します。 **[ホーム]** ページで **[リソース グループ]** を選択することもできます。
1. **[リソース グループ]** ページで、リソース グループを選択します。
1. [リソース グループ] ページで、**[リソース グループの削除]** を選択します。

    リソース グループの削除
1. リソース グループの名前を入力し、**[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [チュートリアル:最初の API のインポートと発行](import-and-publish.md)
