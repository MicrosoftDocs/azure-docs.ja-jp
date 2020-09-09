---
title: Azure Functions の継続的なデプロイ
description: Azure App Service の継続的なデプロイ機能を使用して、関数を発行します。
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.topic: conceptual
ms.date: 09/25/2019
ms.openlocfilehash: e49c235e11eea17fdd1a7ff7751cc0493934d725
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83123681"
---
# <a name="continuous-deployment-for-azure-functions"></a>Azure Functions の継続的なデプロイ

Azure Functions を使用し、[ソース管理の統合](functions-deployment-technologies.md#source-control)を使用してコードを継続的にデプロイできます。 ソース管理の統合を使用すると、コードの更新によって Azure へのデプロイがトリガーされるワークフローが可能になります。 Azure Functions を初めて使う場合は、まず「[Azure Functions の概要](functions-overview.md)」を参照してください。

複数の頻繁に発生する投稿を統合する場合、継続的なデプロイはプロジェクトに最適なオプションとなります。 継続的なデプロイを使用すると、コードに対して真実の 1 つの情報源が維持されるため、チームは簡単に共同作業できます。 次のソース コードの場所から、Azure Functions での継続的なデプロイを構成できます。

* [Azure Repos](https://azure.microsoft.com/services/devops/repos/)
* [GitHub](https://github.com)
* [Bitbucket](https://bitbucket.org/)

Azure の関数のデプロイの単位は関数アプリです。 関数アプリ内のすべての関数は同時にデプロイされます。 信頼できるソースが他の場所に設定されているため、継続的なデプロイが有効になった後、Azure portal での関数コードへのアクセスは*読み取り専用*として構成されます。

## <a name="requirements-for-continuous-deployment"></a>継続的なデプロイの要件

継続的なデプロイを成功させるには、ディレクトリ構造が、Azure Functions が期待する基本フォルダー構造との互換性を備えている必要があります。

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

>[!NOTE]  
> 継続的なデプロイは、従量課金プランで実行されている Linux アプリではまだサポートされていません。 

## <a name="set-up-continuous-deployment"></a><a name="credentials"></a>継続的なデプロイを設定する

既存の関数アプリの継続的なデプロイを構成するには、次の手順を実行します。 この手順は GitHub リポジトリとの統合を示しますが、Azure Repos またはその他のソース コード リポジトリに対しても同様の手順が適用されます。

1. [Azure portal](https://portal.azure.com) の関数アプリで、 **[管理者用センター]** を選択し、 **[GitHub]** を選択してから、 **[承認]** を選択します。 GitHub が既に承認済みの場合は、 **[続行]** を選択し、次のステップをスキップします。 

    :::image type="content" source="./media/functions-continuous-deployment/github.png" alt-text="Azure App Service 管理者用センター":::

3. GitHub で、 **[Authorize AzureAppService]\(AzureAppService を承認する\)** を選択します。

    :::image type="content" source="./media/functions-continuous-deployment/authorize.png" alt-text="Azure App Service を承認する":::

    GitHub パスワードを入力し、 **[続行]** を選択します。

4. 次のビルド プロバイダーのいずれかを選択します。

    * **App Service のビルド サービス**:ビルドが不要な場合、または汎用ビルドが必要な場合に最適です。
    * **Azure Pipelines (プレビュー)** :ビルドをより細かく制御する必要がある場合に最適です。 このプロバイダーは現在プレビュー段階です。

    **[続行]** をクリックします。

5. 指定したソース管理オプションに固有の情報を構成します。 GitHub では、 **[Organization]\(組織\)** 、 **[Repository]\(リポジトリ\)** 、および **[Branch]\(ブランチ\)** の値を入力または選択する必要があります。 値は、お客様のコードの場所によって決まります。 その後、 **[続行]** を選択します。

    :::image type="content" source="./media/functions-continuous-deployment/github-specifics.png" alt-text="GitHub を構成する":::

6. すべての詳細を確認し、 **[完了]** を選択してデプロイ構成を完了します。

プロセスが完了すると、指定されたソースからのすべてのコードがアプリにデプロイされます。 その時点で、デプロイ ソースの変更により、Azure 内の関数アプリにそれらの変更をデプロイする処理がトリガーされます。

> [!NOTE]
> 継続的インテグレーションを構成した後は、Functions のポータルでソース ファイルを編集することはできなくなります。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Functions のベスト プラクティス](functions-best-practices.md)
