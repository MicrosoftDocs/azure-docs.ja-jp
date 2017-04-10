---
title: "Azure Automation を使用してジョブをトリガーする | Microsoft Docs"
description: "Azure Automation を使用して StorSimple Data Manager ジョブをトリガーする方法について説明します (プライベート プレビュー)。"
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/16/2017
ms.author: vidarmsft
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 9691408bcd80afb6eba534f26749b76dd3bfe315
ms.lasthandoff: 03/21/2017

---

# <a name="use-azure-automation-to-trigger-a-job-private-preview"></a>Azure Automation を使用してジョブをトリガーする (プライベート プレビュー)

この記事では、Azure Automation を使用して StorSimple Data Manager ジョブをトリガーする方法について説明します。

## <a name="prerequisites"></a>前提条件

開始する前に、次の要件が満たされていることを確認します。

*    Azure Powershell がインストールされている。 [Azure PowerShell のダウンロード](https://azure.microsoft.com/documentation/articles/powershell-install-configure/)。
*    データ変換ジョブを初期化するための構成設定 (この設定を取得する手順については、ここで説明します)。
*    リソース グループ内のハイブリッド データ リソースでジョブ定義が正しく構成されている。
*    GitHub リポジトリから `DataTransformationApp.zip` [zip](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/raw/master/Azure%20Automation%20For%20Data%20Manager/DataTransformationApp.zip) ファイルをダウンロード済みである。
*    GitHub リポジトリから `Get-ConfigurationParams.ps1` [スクリプト](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Get-ConfigurationParams.ps1)をダウンロード済みである。
*    GitHub リポジトリから `Trigger-DataTransformation-Job.ps1` [スクリプト](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Trigger-DataTransformation-Job.ps1)をダウンロード済みである。

## <a name="step-by-step"></a>詳細な手順:

### <a name="get-azure-active-directory-permissions-for-the-automation-job-to-run-the-job-definition"></a>Automation ジョブでジョブ定義を実行するための Azure Active Directory のアクセス許可を取得する

1. Active Directory の構成パラメーターを取得するには、次の手順を実行します。

    1. ローカル コンピューターで Windows PowerShell を開きます。 [Azure PowerShell](https://azure.microsoft.com/downloads/) がインストールされていることを確認します。
    1. (上記でダウンロードしたフォルダー内の) `Get-ConfigurationParams.ps1` スクリプトを実行します。 PowerShell ウィンドウで次のコマンドを入力します。

        ```
        .\Get-ConfigurationParams.ps1 -SubscriptionName "AzureSubscriptionName" -ActiveDirectoryKey "AnyRandomPassword" -AppName "ApplicationName"
         ```

        ActiveDirectoryKey は後で使用するパスワードです。 任意のパスワードを入力します。 AppName には任意の文字列を指定できます。

2. このスクリプトは、Automation Runbook をトリガーするときに使用される次の値を出力します。 これらの値を書き留めておきます。

    - [クライアント ID]
    - テナント ID
    - Active Directory キー (上記で入力したものと同じ)
    - サブスクリプション ID

### <a name="set-up-the-automation-account"></a>Automation アカウントを設定する

1. Azure にログオンし、Automation アカウントを開きます。
2. **[資産]** タイルをクリックして、資産の一覧を開きます。
3. **[モジュール]** タイルをクリックして、モジュールの一覧を開きます。
4. **[+ モジュールの追加]** をクリックします。[モジュールの追加] ブレードが開きます。

    ![Automation アカウントの設定](./media/storsimple-data-manager-job-using-automation/add-module1m.png)

5. ローカル コンピューターの `DataTransformationApp.zip` ファイルを選択したら、**[OK]** をクリックしてモジュールをインポートします。

   Azure Automation は、モジュールをアカウントにインポートするときに、そのモジュールに関するメタデータを抽出します。 この操作には数分かかる場合があります。

   ![Automation アカウントの設定](./media/storsimple-data-manager-job-using-automation/add-module2m.png)

   

6. モジュールをデプロイ中であることが通知され、プロセスが完了すると完了通知が表示されます。  **[モジュール]** タイルで状態を確認することもできます。

### <a name="to-import-the-runbook-that-triggers-the-job-definition"></a>ジョブ定義をトリガーする Runbook をインポートするには

1. Azure ポータルで、Automation アカウントを開きます。
2. **[Runbook]** タイルをクリックして、Runbook の一覧を開きます。
3. **[+ Runbook の追加]** をクリックし、**[既存の Runbook のインポート]** をクリックします。

   ![[既存の Runbook のインポート]](./media/storsimple-data-manager-job-using-automation/import-a-runbook.png)

4. **[Runbook ファイル]** をクリックし、`Trigger-DataTransformation-Job.ps1` をインポートするファイルを選択します。
5. **[作成]** をクリックして Runbook をインポートします。 Automation アカウントの Runbook の一覧に新しい Runbook が表示されます。
7. **[Trigger-DataTransformation-Job]** Runbook をクリックし、**[編集]** をクリックします。
8. **[発行]** をクリックし、確認を求められたら **[はい]** をクリックします。


### <a name="to-run-the-runbook"></a>Runbook を実行するには
1. Azure ポータルで、Automation アカウントを開きます。
2. **[Runbook]** タイルをクリックして、Runbook の一覧を開きます。
3. **[Trigger-DataTransformation-Job]** をクリックします。
4. **[開始]** をクリックして Runbook を開始します。

   ![Runbook の開始](./media/storsimple-data-manager-job-using-automation/run-runbook1m.png)

5. **[Runbook の開始]** ブレードで、すべてのパラメーターを入力します。 **[OK]** をクリックして、データ変換ジョブを送信します。

   ![Runbook の開始](./media/storsimple-data-manager-job-using-automation/run-runbook2m.png)


## <a name="next-steps"></a>次のステップ

[StorSimple Data Manger UI を使用してデータを変換します](storsimple-data-manager-ui.md)。
