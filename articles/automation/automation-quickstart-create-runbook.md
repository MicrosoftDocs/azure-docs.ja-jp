---
title: Azure クイックスタート - Azure Automation Runbook を作成する | Microsoft Docs
description: この記事では、Azure Automation Runbook を作成する方法について説明します。
services: automation
ms.date: 02/05/2019
ms.topic: quickstart
ms.subservice: process-automation
ms.custom: mvc
ms.openlocfilehash: 0717a7ac3cc663ff68ba96864aa5d37732337ca5
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836738"
---
# <a name="create-an-azure-automation-runbook"></a>Azure Automation Runbook を作成する

Azure を使用して Azure Automation Runbook を作成することができます。 この方法では、ブラウザーベースのユーザー インターフェイスで Automation Runbook を作成できます。 このクイックスタートでは、Automation PowerShell Runbook の作成、編集、テスト、公開の手順について説明します。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

https://portal.azure.com で Azure にサインインします。

## <a name="create-the-runbook"></a>Runbook の作成

まず Runbook を作成します。 このクイックスタートで作成されるサンプル Runbook は、既定で `Hello World` を出力します。

1. Automation アカウントを開きます。

1. **[プロセス オートメーション]** の **[Runbook]** をクリックします。 Runbook の一覧が表示されます。

1. リストの一番上にある **[Runbook の作成]** をクリックします。

1. **[名前]** フィールドに Runbook の名前として「`Hello-World`」を入力し、 **[Runbook の種類]** フィールドで **[PowerShell]** を選択します。 

   ![このページに Automation Runbook に関する情報を入力します](./media/automation-quickstart-create-runbook/automation-create-runbook-configure.png)

1. **Create** をクリックしてください。 Runbook が作成され、[PowerShell Runbook の編集] ページが開きます。

    ![Runbook エディターで PowerShell スクリプトを作成する](./media/automation-quickstart-create-runbook/automation-edit-runbook-empty.png)

1. 以下のコードを編集ウィンドウに入力するか貼り付けます。 その結果、既定値が `World` の `Name` というオプションの入力パラメーターが作成され、その入力値を使用する文字列が出力されます。

   ```powershell-interactive
   param
   (
       [Parameter(Mandatory=$false)]
       [String] $Name = "World"
   )

   "Hello $Name!"
   ```

1. **[保存]** をクリックして、Runbook の下書きのコピーを保存します。

    ![Runbook エディターで PowerShell スクリプトを作成する](./media/automation-quickstart-create-runbook/automation-edit-runbook.png)

## <a name="test-the-runbook"></a>Runbook をテストする

Runbook が作成されたら、Runbook をテストして動作することを確認します。

1. **[テスト ウィンドウ]** をクリックして、テスト ウィンドウを開きます。

1. **[名前]** の値を入力し、 **[開始]** をクリックします。 テスト ジョブが開始され、ジョブの状態と出力が表示されます。

    ![Runbook のテスト ジョブ](./media/automation-quickstart-create-runbook/automation-test-runbook.png)

1. 右上の **[X]** をクリックして、[テスト] ペインを閉じます。 表示されるポップアップで **[OK]** を選択します。

1. [PowerShell Runbook の編集] ページで **[発行]** をクリックして、アカウントの Runbook の公式バージョンとして Runbook を発行します。

   ![Runbook のテスト ジョブ](./media/automation-quickstart-create-runbook/automation-hello-world-runbook-job.png)

## <a name="run-the-runbook"></a>Runbook を実行する

Runbook が公開されると、概要ページが表示されます。

1. Runbook の概要ページで、 **[開始]** をクリックしてこの Runbook の [Runbook の開始] 構成ページを開きます。

   ![Runbook のテスト ジョブ](./media/automation-quickstart-create-runbook/automation-hello-world-runbook-start.png)

1. 既定値が使用されるように **[名前]** を空白のままにして、 **[OK]** をクリックします。 Runbook ジョブが送信され、ジョブ ページが表示されます。

   ![Runbook のテスト ジョブ](./media/automation-quickstart-create-runbook/automation-job-page.png)

1. [ジョブの状態] が `Running` または `Completed` に変わったら、 **[出力]** をクリックして [出力] ペインを開き、Runbook の出力を確認します。

   ![Runbook のテスト ジョブ](./media/automation-quickstart-create-runbook/automation-hello-world-runbook-job-output.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったら、Runbook を削除します。 削除するには、Runbook 一覧で Runbook を選択し、 **[削除]** をクリックします。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Runbook を作成、編集、テスト、および公開し、Runbook ジョブを開始しました。 Automation Runbook の詳細については、Automation で作成および使用できるさまざまな Runbook の種類に関する記事を参照してください。

> [!div class="nextstepaction"]
> [Azure Automation の Runbook の種類](./automation-runbook-types.md)
