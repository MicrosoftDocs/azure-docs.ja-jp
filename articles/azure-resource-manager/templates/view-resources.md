---
title: リソースのプロパティについて
description: リソースのプロパティの確認方法について説明します。
ms.topic: conceptual
ms.date: 06/10/2020
ms.openlocfilehash: c8bbe0dcb1bc9dc9751a1dc0d0b98a6368473546
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91327750"
---
# <a name="discover-resource-properties"></a>リソースのプロパティについて

Resource Manager テンプレートを作成する前に、使用可能なリソースの種類と、テンプレートで使用する値について理解しておく必要があります。 この記事では、テンプレートに含めるプロパティを確認する方法をいくつか紹介します。

## <a name="find-resource-provider-namespaces"></a>リソース プロバイダーの名前空間の確認

ARM テンプレート内のリソースは、リソース プロバイダーの名前空間とリソースの種類を使用して定義されます。 たとえば、Microsoft. Storage/storageAccounts は、ストレージ アカウントのリソースの種類の完全な名前です。 Microsoft. Storage は名前空間です。 使用するリソースの種類の名前空間をまだ把握していない場合は、「[Azure サービスのリソース プロバイダー](../management/azure-services-resource-providers.md)」を参照してください。

![Resource Manager のリソース プロバイダー名前空間のマッピング](./media/view-resources/resource-provider-namespace-and-azure-service-mapping.png)

## <a name="export-templates"></a>テンプレートのエクスポート

既存のリソースのテンプレート プロパティを取得する最も簡単な方法は、テンプレートをエクスポートすることです。 詳細については、「[Azure portal のテンプレートへの単一および複数リソースのエクスポート](./export-template-portal.md)」を参照してください。

## <a name="use-resource-manager-tools-extension"></a>Resource Manager ツール拡張機能の使用

Visual Studio Code と Azure Resource Manager ツールの拡張機能は、各リソースの種類に必要なプロパティを正確に確認するのに役立ちます。 これらが提供する Intellisense とスニペットを使用して、テンプレートでリソースを定義する方法を簡略化することができます。 詳細については、「[クイック スタート: Visual Studio Code を使って Azure Resource Manager テンプレートを作成する](./quickstart-create-templates-use-visual-studio-code.md#add-an-azure-resource)」を参照してください。

次のスクリーンショットは、ストレージ アカウント リソースがテンプレートに追加されることを示しています。

![Resource Manager ツール拡張機能のスニペット](./media/view-resources/resource-manager-tools-extension-snippets.png)

また、この拡張機能には、構成プロパティのオプションの一覧も表示されます。

![Resource Manager ツール拡張機能の構成値](./media/view-resources/resource-manager-tools-extension-configurable-properties.png)

## <a name="use-template-reference"></a>テンプレート リファレンスの使用

Azure Resource Manager テンプレート リファレンスは、テンプレート スキーマの最も包括的なリソースです。 API バージョン、テンプレート形式、およびプロパティ情報を見つけることができます。

1. [Azure Resource Manager テンプレート リファレンス](/azure/templates/)に移動します。
1. 左側のナビゲーションで、 **[Storage]** \(ストレージ\) を選択し、 **[すべてのリソース]** を選択します。 [すべてのリソース] ページには、リソースの種類とバージョンが表示されます。

    ![テンプレート リファレンスのリソースのバージョン](./media/view-resources/resource-manager-template-reference-resource-versions.png)

    リソースの種類がわかっている場合は、`https://docs.microsoft.com/azure/templates/{provider-namespace}/{resource-type}` の URL 形式でこのページに直接移動することができます。

1. 最新のバージョンを選択します。 最新バージョンの API を使用することをお勧めします。

    **[テンプレートの形式]** セクションには、ストレージ アカウントのすべてのプロパティが一覧表示されます。 次に示すように、**sku** がリストに含まれています。

    ![テンプレート リファレンスのストレージ アカウント形式](./media/view-resources/resource-manager-template-reference-storage-account-sku.png)

    下にスクロールして、 **[プロパティ値]** セクションに **[Sku object]** \(Sku オブジェクト\) を表示します。 この記事では、SKU 名に使用できる値を紹介しています。

    ![テンプレート リファレンスのストレージ アカウント SKU 値](./media/view-resources/resource-manager-template-reference-storage-account-sku-values.png)

    ページの最後の **[クイック スタート テンプレート]** セクションに、リソースの種類を含む Azure クイックスタート テンプレートが一覧表示されます。

    ![テンプレート リファレンスのストレージ アカウントのクイックスタート テンプレート](./media/view-resources/resource-manager-template-reference-quickstart-templates.png)

テンプレート リファレンスへは、各 Azure サービス ドキュメント サイトからリンクされています。  たとえば、[Key Vault ドキュメントのサイト](../../key-vault/general/overview.md)は次のように表示されます。

![Resource Manager テンプレート リファレンスの Key Vault](./media/view-resources/resource-manager-template-reference-key-vault.png)

## <a name="use-resource-explorer"></a>リソース エクスプローラーの使用

リソース エクスプローラーは、Azure portal に埋め込まれています。 この方法を使用するには、ストレージ アカウントが必要です。 所有していない場合は、次のボタンを選択して作成します。

[![Azure へのデプロイ](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-storage-account-create%2fazuredeploy.json)

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 検索ボックスに **"resource explorer"** と入力し、 **[リソース エクスプローラー]** を選択します。

    ![Azure portal でのリソース エクスプローラーの検索を示すスクリーンショット。](./media/view-resources/azure-portal-resource-explorer.png)

1. 左側の **[サブスクリプション]** を展開し、Azure サブスクリプションを展開します。 ストレージ アカウントは、 **[プロバイダー]** または **[ResourceGroups]** にあります。

    ![Azure portal のリソース エクスプローラー](./media/view-resources/azure-portal-resource-explorer-home.png)

    - **[プロバイダー]** : **[プロバイダー]**  ->  **[Microsoft.Storage]**  ->  **[storageAccounts]** と展開して、ストレージ アカウントを選択します。
    - **[ResourceGroups**]: ストレージ アカウントが含まれているリソース グループを選択し、 **[リソース]** を選択してから、ストレージ アカウントを選択します。

    右側には、次のような既存のストレージ アカウントの SKU 構成が表示されます。

    ![Azure portal リソース エクスプローラー ストレージ アカウントの sku](./media/view-resources/azure-portal-resource-explorer-sku.png)

## <a name="use-resourcesazurecom"></a>Resources.azure.com の使用

Resources.azure.com は一般向けの Web サイトで、Azure サブスクリプションを持つすべてのユーザーがアクセスできます。 これはプレビュー段階にあります。  代わりに、[リソース エクスプローラー](#use-resource-explorer)の使用を検討してください。 このツールにより、次の機能が提供されます。

- Azure リソース管理 API を簡単に調べる。
- API のドキュメントとスキーマ情報を取得する。
- 自身のサブスクリプションで API 呼び出しを直接実行する。

このツールを使用してスキーマ情報の取得方法を示すには、ストレージ アカウントが必要です。 所有していない場合は、次のボタンを選択して作成します。

[![Azure へのデプロイ](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-storage-account-create%2fazuredeploy.json)

1. [resources.azure.com](https://resources.azure.com/) に移動します。 左側のウィンドウに内容が表示されるまで、しばらく時間がかかります。
1. **[サブスクリプション]** を選択します。

    ![resource.azure.com API マッピング](./media/view-resources/resources-azure-com-api-mapping.png)

    左側のノードは、右側の API 呼び出しに一致します。 API 呼び出しを実行するには、 **[GET]** ボタンを選択します。
1. 左側の **[サブスクリプション]** を展開し、Azure サブスクリプションを展開します。 ストレージ アカウントは、 **[プロバイダー]** または **[ResourceGroups]** にあります。

    - **[プロバイダー]** : **[プロバイダー]**  ->  **[Microsoft.Storage]**  ->  **[storageAccounts]** と展開して、ストレージ アカウントに移動します。
    - **[ResourceGroups**]: ストレージ アカウントが含まれているリソース グループを選択し、 **[リソース]** を選択します。

    右側には、次のような既存のストレージ アカウントの SKU 構成が表示されます。

    ![Azure portal リソース エクスプローラー ストレージ アカウントの sku](./media/view-resources/azure-portal-resource-explorer-sku.png)

## <a name="next-steps"></a>次のステップ

この記事では、テンプレート スキーマ情報を見つける方法について説明しました。 Resource Manager テンプレートの作成については、「[ARM テンプレートの構造と構文について](./template-syntax.md)」を参照してください。
