---
title: インクルード ファイル
description: インクルード ファイル
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/02/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: b96586c801acfc0419b80fa725a468dcb896f79e
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2019
ms.locfileid: "70197156"
---
1. **ソリューション エクスプローラー**で、プロジェクトを右クリックし、 **[発行]** を選択します。

2. **[発行先を選択]** ダイアログで、画像の下の表に指定されているように発行オプションを使用します。 

    ![発行先の選択](./media/functions-vstools-publish/functions-visual-studio-publish-profile.png)

    | オプション      | 説明                                |
    | ------------ |  -------------------------------------------------- |
    | **Azure Functions 従量課金プラン** |   [従量課金プラン](../articles/azure-functions/functions-scale.md#consumption-plan)で実行される関数アプリにプロジェクトを発行する場合は、関数アプリの実行に対してのみ課金されます。 他のホスティング プランでは、コストが高くなります。 詳細については、「[Azure Functions のスケールとホスティング](../articles/azure-functions/functions-scale.md)」を参照してください。 | 
    | **新規作成** | 新しい関数アプリと関連リソースが、Azure に作成されます。 **[既存のものを選択]** を選択すると、Azure の既存の関数アプリ内のすべてのファイルがローカル プロジェクトのファイルによって上書きされます。 このオプションは、既存の関数アプリへの更新を再発行する場合にのみ使用します。 |
    | **パッケージ ファイルから実行します** | 関数アプリは、[Zip Deploy](../articles/azure-functions/functions-deployment-technologies.md#zip-deploy) を使用して、[Run-From-Package](../articles/azure-functions/run-functions-from-deployment-package.md) モードが有効な状態でデプロイされます。 これは、関数を実行するために推奨される方法であり、パフォーマンスが向上します。 このオプションを使用しない場合は、関数アプリ プロジェクトがローカルで実行されないようにしたうえで、Azure に発行する必要があります。 |


3. **[発行]** を選択します。 まだ Visual Studio から Azure アカウントにサインインしていない場合は、 **[サインイン]** を選択します。 無料の Azure アカウントを作成することもできます。

4. **App Service の [新規作成]** ダイアログで、画像の下の表に指定されているように**ホスティング**設定を使用してください。

    ![[App Service の作成] ダイアログ](./media/functions-vstools-publish/functions-visual-studio-publish.png)

    | Setting      | 推奨値  | Description                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Name** | グローバルに一意の名前 | 新しい関数アプリを一意に識別する名前。 有効な文字は、`a-z`、`0-9`、および `-` です。 |
    | **サブスクリプション** | サブスクリプションの選択 | 使用する Azure サブスクリプション。 |
    | **[リソース グループ](../articles/azure-resource-manager/resource-group-overview.md)** | myResourceGroup |  関数アプリを作成するリソース グループの名前。 新しいリソース グループを作成する場合は、 **[新規]** を選択します。|
    | **[ホスティング プラン](../articles/azure-functions/functions-scale.md)** | 従量課金プラン | **[新規]** を選択してサーバーレス プランを作成した後は、必ず **[サイズ]** の下の **[従量課金プラン]** を選択します。 また、[[地域]](https://azure.microsoft.com/regions/) で、自分に近いか、または自分の関数がアクセスする他のサービスに近い **[場所]** を選択します。 **[従量課金プラン]** 以外のプランで実行する場合は、[関数アプリのスケーリング](../articles/azure-functions/functions-scale.md)を管理する必要があります。  |
    | **[Azure Storage](../articles/storage/common/storage-quickstart-create-account.md)** | 汎用ストレージ アカウント | Functions ランタイムには Azure Storage アカウントが必要です。 **[新規]** を選択して汎用ストレージ アカウントを作成します。 または、[ストレージ アカウントの要件](../articles/azure-functions/functions-scale.md#storage-account-requirements)を満たす既存のアカウントを使用することもできます。  |

5. **[作成]** を選択して、これらの設定で Azure に関数アプリと関連リソースを作成し、関数プロジェクト コードをデプロイします。 

6. デプロイが完了したら、 **[サイトの URL]** の値を書き留めておきます。これが Azure における関数アプリのアドレスになります。

    ![発行成功のメッセージ](./media/functions-vstools-publish/functions-visual-studio-publish-complete.png)
