---
title: Azure クイックスタート - Azure Automation アカウントを作成する | Microsoft Docs
description: Azure Automation アカウントを作成し、Runbook を実行する方法について説明します
services: automation
author: csand-msft
ms.author: csand
ms.date: 04/04/2019
ms.topic: quickstart
ms.service: automation
ms.subservice: process-automation
ms.custom: mvc
ms.openlocfilehash: 7f7905a4b09e685ad98a1663333aa32bc1d7ae90
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59009514"
---
# <a name="create-an-azure-automation-account"></a>Azure Automation アカウントを作成する

Azure を使用して Azure Automation アカウントを作成することができます。 この方法では、ブラウザーベースのユーザー インターフェイスで Automation アカウントとそれに関連するリソースを作成して構成できます。 このクイックスタートでは、Automation アカウントを作成し、そのアカウントで Runbook を実行する手順について説明します。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

 https://portal.azure.com で Azure にサインインします

## <a name="create-automation-account"></a>Automation アカウントを作成する

1. Azure の左上にある **[リソースの作成]** ボタンをクリックします。

1. **[管理ツール]**、**[Automation]** の順に選択します。

1. アカウント情報を入力します。 **[Azure 実行アカウントの作成]** で **[はい]** を選択し、Azure に対する認証が簡単になるアーティファクトが自動的に有効になるようにします。 Automation アカウントの作成時には、選択後に名前を変更できないことに注意してください。 "*Automation アカウント名は、リージョンおよびリソース グループごとに一意です。削除された Automation アカウントの名前はすぐには使用できない場合があります。*" 1 つの Automation アカウントで、特定のテナントのすべてのリージョンおよびサブスクリプションにわたってリソースを管理できます。 完了したら、**[作成]** をクリックして、Automation アカウントの展開を開始します。

    ![このページに Automation アカウントに関する情報を入力します](./media/automation-quickstart-create-account/create-automation-account-portal-blade.png)  

    > [!NOTE]
    > Automation アカウントをデプロイできる場所の最新の一覧については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/en-us/global-infrastructure/services/?products=automation&regions=all)」を参照してください。

1. デプロイが完了したら、**[すべてのサービス]** をクリックし、**[Automation アカウント]** を選択し、作成した Automation アカウントを選択します。

    ![Automation アカウントの概要](./media/automation-quickstart-create-account/automation-account-overview.png)

## <a name="run-a-runbook"></a>Runbook を実行する

チュートリアルの Runbook のいずれかを実行します。

1. **[プロセス オートメーション]** の **[Runbook]** をクリックします。 Runbook の一覧が表示されます。 既定では、アカウントのいくつかのチュートリアル Runbook が有効です。

    ![Automation アカウントの Runbook 一覧](./media/automation-quickstart-create-account/automation-runbooks-overview.png)

1. **AzureAutomationTutorialScript** Runbook を選択します。 この操作で、Runbook の概要ページが開きます。

    ![Runbook の概要](./media/automation-quickstart-create-account/automation-tutorial-script-runbook-overview.png)

1. **[開始]** をクリックし、**[Runbook の開始]** ページで **[OK]** をクリックして Runbook を開始します。

    ![Runbook ジョブ ページ](./media/automation-quickstart-create-account/automation-tutorial-script-job.png)

1. **[ジョブの状態]** が "**実行中**" に変わったら、**[出力]** または **[すべてのログ]** をクリックして Runbook ジョブの出力を確認します。 このチュートリアル Runbook では、Azure リソースの一覧が出力されます。

## <a name="next-steps"></a>次の手順

このクイックスタートでは、Automation アカウントを展開し、Runbook ジョブを開始し、ジョブ結果を表示しました。 Azure Automation の詳細については、最初の Runbook を作成するクイックスタートに進みます。

> [!div class="nextstepaction"]
> [Automation クイックスタート - Runbook を作成する](./automation-quickstart-create-runbook.md)

