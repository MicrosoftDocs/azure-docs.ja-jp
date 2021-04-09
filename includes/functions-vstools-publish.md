---
title: インクルード ファイル
description: インクルード ファイル
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/30/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 5b537b88052ce4042e346732f3dc63aaec6621cc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100529663"
---
1. **ソリューション エクスプローラー** でプロジェクトを右クリックし、 **[発行]** を選択します。 **[ターゲット]** で **[Azure]** を選択し、 **[次へ]** を選択します。

1. **[特定のターゲット]** で、 **[Azure Function App (Windows)]\(Azure 関数アプリ (Windows)\)** を選択します。これで、Windows で動作する関数アプリが作成されます。

1. **[Function Instance]\(関数インスタンス\)** で、 **[Create a new Azure Function]\(新しい Azure 関数の作成\)** を選択します。 

    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-new-resource.png" alt-text="新しい関数アプリ インスタンスの作成":::

1. 次の表に示されている値を使用して、新しいインスタンスを作成します。

    | 設定      | 値  | 説明                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **名前** | グローバルに一意の名前 | 新しい関数アプリを一意に識別する名前。 この名前をそのまま使用するか、新しい名前を入力します。 有効な文字は、`a-z`、`0-9`、`-` です。 |
    | **サブスクリプション** | 該当するサブスクリプション | 使用する Azure サブスクリプション。 このサブスクリプションを承諾するか、ドロップダウン リストから新しいものを選択します。 |
    | **[リソース グループ](../articles/azure-resource-manager/management/overview.md)** | リソース グループの名前 |  関数アプリを作成するリソース グループ。 ドロップダウン リストから既存のリソース グループを選択するか、または **[新規]** を選択して新しいリソース グループを作成します。|
    | **[プランの種類](../articles/azure-functions/functions-scale.md)** | 従量課金 | [従量課金プラン](../articles/azure-functions/consumption-plan.md)で実行される関数アプリにプロジェクトを発行する場合は、関数アプリの実行に対してのみお支払いください。 他のホスティング プランでは、コストが高くなります。 |
    | **場所** | App Service の場所 | 最寄りの [リージョン](https://azure.microsoft.com/regions/)または関数がアクセスする他のサービスの近くのリージョン内の **[場所]** を選択します。 |
    | **[Azure Storage](../articles/azure-functions/storage-considerations.md)** | 汎用ストレージ アカウント | Functions Runtime には Azure Storage アカウントが必須です。 **[新規]** を選択して汎用ストレージ アカウントを構成します。 または、[ストレージ アカウントの要件](../articles/azure-functions/storage-considerations.md#storage-account-requirements)を満たす既存のアカウントを選択することもできます。  |

    ![[App Service の作成] ダイアログ](./media/functions-vstools-publish/functions-visual-studio-publish.png)

1. **[作成]** を選択して、関数アプリとその関連リソースを Azure で作成します。 リソース作成のステータスがウィンドウの左下に表示されます。 

1. **[Functions instance]\(関数インスタンス\)** に戻り、 **[Run from package file]\(パッケージ ファイルから実行する\)** がオンになっていることを確認します。 関数アプリは、[Zip Deploy](../articles/azure-functions/functions-deployment-technologies.md#zip-deploy) を使用して、[Run-From-Package](../articles/azure-functions/run-functions-from-deployment-package.md) モードが有効な状態でデプロイされます。 これは、パフォーマンスが向上するため、関数プロジェクトの推奨されるデプロイ方法です。 

    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-profile-step-4.png" alt-text="プロファイル作成の完了":::

1. **[完了]** を選択し、[発行] ページで **[発行]** を選択して、プロジェクト ファイルを含むパッケージを Azure の新しい関数アプリにデプロイします。 

    デプロイが完了すると、 **[発行]** タブに Azure の関数アプリのルート URL が表示されます。 
    
1.  [発行] タブで、 **[Cloud Explorer で管理する]** を選択します。 これにより、Cloud Explorer で新しい関数アプリ Azure リソースが開かれます。 
    
    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-complete.png" alt-text="発行成功のメッセージ":::
    
    Cloud Explorer を使用すると、Visual Studio でサイトのコンテンツを表示し、関数アプリを開始および停止し、Azure の関数アプリ リソースを直接および Azure portal で参照できます。 
