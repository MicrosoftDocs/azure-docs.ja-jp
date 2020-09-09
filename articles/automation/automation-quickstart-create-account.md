---
title: Azure クイックスタート - Azure Automation アカウントを作成する | Microsoft Docs
description: この記事では、Azure Automation アカウントを作成して Runbook を実行する方法を説明します。
services: automation
ms.date: 04/04/2019
ms.topic: quickstart
ms.subservice: process-automation
ms.custom: mvc
ms.openlocfilehash: a07c6ac524aa213519ace1ae204ac2d76db802aa
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836704"
---
# <a name="create-an-azure-automation-account"></a>Azure Automation アカウントを作成する

Azure Automation アカウントは、Azure のさまざまなリソースにアクセスできるブラウザーベースのユーザー インターフェイス、Azure portal を使用して作成できます。 1 つの Automation アカウントで、特定のテナントのすべてのリージョンおよびサブスクリプションにわたってリソースを管理できます。 

このクイックスタートでは、Automation アカウントを作成し、そのアカウントで Runbook を実行する手順について説明します。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure にサインイン](https://portal.azure.com)します。

## <a name="create-automation-account"></a>Automation アカウントを作成する

1. Azure アカウントの名前を選択します。 Automation アカウント名は、リージョンおよびリソース グループごとに一意です。 削除された Automation アカウントの名前はすぐには使用できない場合があります。

    > [!NOTE]
    > 一度ユーザー インターフェイスで入力されたアカウント名を変更することはできません。 

2. Azure portal の左上隅にある **[リソースの作成]** ボタンをクリックします。

3. **[IT & Management Tools]\(IT & 管理ツール\)** 、 **[Automation]** の順に選択します。

4. アカウント情報 (選択したアカウント名を含む) を入力します。 **[Azure 実行アカウントの作成]** で **[はい]** を選択し、Azure に対する認証が簡単になるアーティファクトが自動的に有効になるようにします。 必要な情報をすべて入力したら、 **[作成]** をクリックして、Automation アカウントのデプロイを開始します。

    ![このページに Automation アカウントに関する情報を入力します](./media/automation-quickstart-create-account/create-automation-account-portal-blade.png)  

    > [!NOTE]
    > Automation アカウントをデプロイできる場所の最新の一覧については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=automation&regions=all)」を参照してください。

5. デプロイが完了したら、 **[すべてのサービス]** をクリックします。

6. **[Automation アカウント]** を選択し、作成した Automation アカウントを選択します。

    ![Automation アカウントの概要](./media/automation-quickstart-create-account/automation-account-overview.png)

## <a name="run-a-runbook"></a>Runbook を実行する

チュートリアルの Runbook のいずれかを実行します。

1. **[プロセス オートメーション]** の **[Runbook]** をクリックします。 Runbook の一覧が表示されます。 既定では、アカウントのいくつかのチュートリアル Runbook が有効です。

    ![Automation アカウントの Runbook 一覧](./media/automation-quickstart-create-account/automation-runbooks-overview.png)

1. **AzureAutomationTutorialScript** Runbook を選択します。 この操作で、Runbook の概要ページが開きます。

    ![Runbook の概要](./media/automation-quickstart-create-account/automation-tutorial-script-runbook-overview.png)

1. **[開始]** をクリックし、[Runbook の開始] ページで **[OK]** をクリックして Runbook を開始します。

    ![Runbook ジョブ ページ](./media/automation-quickstart-create-account/automation-tutorial-script-job.png)

1. [ジョブの状態] が `Running` に変わったら、 **[出力]** または **[すべてのログ]** をクリックして Runbook ジョブの出力を確認します。 このチュートリアル Runbook では、Azure リソースの一覧が出力されます。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Automation アカウントを展開し、Runbook ジョブを開始し、ジョブ結果を表示しました。 Azure Automation の詳細については、最初の Runbook を作成するクイックスタートに進みます。

> [!div class="nextstepaction"]
> [Automation クイックスタート - Azure Automation Runbook を作成する](./automation-quickstart-create-runbook.md)

