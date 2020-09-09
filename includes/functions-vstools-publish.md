---
title: インクルード ファイル
description: インクルード ファイル
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/06/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: db6f5927e1cbdb3527014ccbb277c7ec20b02600
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/31/2020
ms.locfileid: "84231554"
---
1. **ソリューション エクスプローラー**で、プロジェクトを右クリックし、 **[発行]** を選択します。

1. **[発行先を選択]** で、次の表で指定されている発行オプションを使用します。 

    | オプション      | 説明                                |
    | ------------ |  -------------------------------------------------- |
    | **Azure Functions 従量課金プラン** | [従量課金プラン](../articles/azure-functions/functions-scale.md#consumption-plan)で実行される関数アプリを Azure クラウド環境に作成します。 従量課金プランを使用する場合、支払いの対象になるのは関数アプリの実行のみです。 他のホスティング プランでは、コストが高くなります。 従量課金プラン以外のプランで実行する場合は、[関数アプリのスケーリング](../articles/azure-functions/functions-scale.md)を管理する必要があります。| 
    | **新規作成** | 新しい関数アプリと関連リソースが、Azure に作成されます。 <br/>**[既存のものを選択]** を選択すると、Azure の既存の関数アプリ内のすべてのファイルがローカル プロジェクトのファイルによって上書きされます。 このオプションは、既存の関数アプリへの更新を再発行する場合にのみ使用します。 |
    | **パッケージ ファイルから実行します** | 関数アプリは、[Zip Deploy](../articles/azure-functions/functions-deployment-technologies.md#zip-deploy) を使用して、[Run-From-Package](../articles/azure-functions/run-functions-from-deployment-package.md) モードが有効な状態でデプロイされます。 パフォーマンスを向上させるこのデプロイは、関数を実行するために推奨される方法です。 <br/>このオプションを使用しない場合は、関数アプリ プロジェクトがローカルで実行されないようにしたうえで、Azure に発行する必要があります。 |

    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-profile.png" alt-text="発行プロファイルを作成する":::


1. **[プロファイルの作成]** を選択します。 まだ Visual Studio から Azure アカウントにサインインしていない場合は、 **[サインイン]** を選択します。 無料の Azure アカウントを作成することもできます。

1. **App Service: 新規作成** で、次の表で指定されている値を使用します。

    | 設定      | 値  | 説明                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **名前** | グローバルに一意の名前 | 新しい関数アプリを一意に識別する名前。 この名前をそのまま使用するか、新しい名前を入力します。 有効な文字は、`a-z`、`0-9`、`-` です。 |
    | **サブスクリプション** | 該当するサブスクリプション | 使用する Azure サブスクリプション。 このサブスクリプションを承諾するか、ドロップダウン リストから新しいものを選択します。 |
    | **[リソース グループ](../articles/azure-resource-manager/management/overview.md)** | リソース グループの名前 |  関数アプリを作成するリソース グループ。 ドロップダウン リストから既存のリソース グループを選択するか、または **[新規]** を選択して新しいリソース グループを作成します。|
    | **[ホスティング プラン](../articles/azure-functions/functions-scale.md)** | ホスティング プランの名前 | **[新規]** を選択して、サーバーレス プランを構成します。 必ず **[サイズ]** の下の **[従量課金プラン]** を選択してください。 [従量課金プラン](../articles/azure-functions/functions-scale.md#consumption-plan)で実行される関数アプリにプロジェクトを発行する場合は、関数アプリの実行に対してのみお支払いください。 他のホスティング プランでは、コストが高くなります。 **[従量課金プラン]** 以外のプランで実行する場合は、[関数アプリのスケーリング](../articles/azure-functions/functions-scale.md)を管理する必要があります。  |
    | **場所** | App Service の場所 | 最寄りの[リージョン](https://azure.microsoft.com/regions/)または関数がアクセスする他のサービスの近くのリージョン内の **[場所]** を選択します。 |
    | **[Azure Storage](../articles/storage/common/storage-account-create.md)** | 汎用ストレージ アカウント | Functions Runtime には Azure Storage アカウントが必須です。 **[新規]** を選択して汎用ストレージ アカウントを構成します。 または、[ストレージ アカウントの要件](../articles/azure-functions/functions-scale.md#storage-account-requirements)を満たす既存のアカウントを選択することもできます。  |

    ![[App Service の作成] ダイアログ](./media/functions-vstools-publish/functions-visual-studio-publish.png)

1. **[作成]** を選択して、これらの設定で Azure に関数アプリとその関連リソースを作成し、関数プロジェクト コードをデプロイします。 

1. **[発行]** を選択して、デプロイが完了するまで待ちます。 

    デプロイが完了すると、 **[発行]** タブに Azure の関数アプリのルート URL が表示されます。 
    
1.  [発行] タブで、 **[Cloud Explorer で管理する]** を選択します。 これにより、Cloud Explorer で新しい関数アプリ Azure リソースが開かれます。 
    
    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-complete.png" alt-text="発行成功のメッセージ":::
    
    Cloud Explorer を使用すると、Visual Studio でサイトのコンテンツを表示し、関数アプリを開始および停止し、Azure の関数アプリ リソースを直接および Azure portal で参照できます。 