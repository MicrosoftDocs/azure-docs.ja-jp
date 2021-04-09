---
title: Azure Automation Runbook を使用して StorSimple デバイスを管理する
description: Azure portal で Azure Automation Runbook を使用して StorSimple 8000 シリーズ デバイスを管理する方法について説明します。
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 10/23/2017
ms.author: alkohli
ms.openlocfilehash: b5f2a86fb7c776436ea707ef293a66c033014c33
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96021080"
---
# <a name="use-azure-automation-runbooks-to-manage-storsimple-devices"></a>Azure Automation Runbook を使用して StorSimple デバイスを管理する

この記事では、Azure Portal で Azure Automation Runbook を使用して StorSimple 8000 シリーズ デバイスを管理する方法について説明します。 この Runbook を実行するように環境を構成する手順を示すサンプル Runbook も含まれています。


## <a name="configure-add-and-run-azure-runbook"></a>Azure Runbook の構成、追加、および実行

このセクションでは、StorSimple 用の Windows PowerShell スクリプトの例を使用して、スクリプトを Runbook にインポートし、Runbook を発行して実行するために必要なさまざまな手順について詳しく説明します。

### <a name="prerequisites"></a>前提条件

開始する前に、次の要件が満たされていることを確認します。

* StorSimple 8000 シリーズ デバイスに登録されている StorSimple デバイス マネージャー サービスに関連付けられているアクティブな Azure サブスクリプションがあること。

* Windows PowerShell 5.0 がコンピューター (または、StorSimple を使用している場合は Windows Server ホスト) にインストールされていること。

### <a name="create-automation-runbook-module-in-windows-powershell"></a>Windows PowerShell で Automation Runbook モジュールを作成する

StorSimple 8000 シリーズ デバイス管理用のオートメーション モジュールを作成するには、次の手順を実行します。

1. Windows PowerShell を起動します。 新しいフォルダーを作成し、ディレクトリを新しいフォルダーに変更します。

    ```powershell
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```

2. 前の手順で作成したフォルダーに [NuGet CLI をダウンロード](https://www.nuget.org/downloads)します。 _nuget.exe_ にはさまざまなバージョンがあります。 SDK に対応するバージョンを選択します。 各ダウンロード リンクは、 _.exe_ ファイルを直接ポイントします。 ブラウザーから実行するのではなく、ファイルを右クリックしてコンピューターに保存してください。

    次のコマンドを実行して、スクリプトをダウンロードし、前に作成した同じフォルダーに格納することもできます。

    ```
        wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```

3. 依存する SDK をダウンロードします。

    ```
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```

4. サンプル GitHub プロジェクトからスクリプトをダウンロードします。

    ```
        wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1 -Out Monitor-Backups.ps1
    ```

5. StorSimple 8000 シリーズ デバイス管理用の Azure Automation Runbook モジュールを作成します。 Windows PowerShell ウィンドウで、次のコマンドを入力します。

    ```powershell
        # set path variables
        $downloadDir = "C:\scripts\StorSimpleSDKTools"
        $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.StorSimple8000Series"

        #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Series"
        mkdir "$moduleDir"
        Copy-Item "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Microsoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft.Rest.ClientRuntime*.dll" $moduleDir
        Copy-Item "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9-preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\lib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir

        #Don't change the name of the Archive
        compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Management.StorSimple8000Series.zip
    ```

6. オートメーション モジュールの zip ファイルが `C:\scripts\StorSimpleSDKTools` に作成されていることを確認します。

    ![StorSimpleSDKTools フォルダーのスクリーンショット。オートメーション モジュールの zip ファイルを確認できます](./media/storsimple-8000-automation-azurerm-runbook/verify-automation-module.png)

7. Windows PowerShell を使用してオートメーション モジュールを作成すると、次の出力が表示されます。

    ```powershell
    mkdir C:\scripts\StorSimpleSDKTools
    ```

    ```Output
        Directory: C:\scripts

    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----       10/18/2017   8:43 AM                StorSimpleSDKTools
    ```

    ```powershell
    wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```

    ```powershell
    C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
    ```

    ```Output
    -------------------------------------------
    CUT              CUT
    -------------------------------------------
    Successfully installed 'Microsoft.Azure.Management.Storsimple8000series 1.0.0' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 1.77 sec
    ```

    ```powershell
    C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
    ```

    ```Output
    -------------------------------------------
    CUT              CUT
    -------------------------------------------
    Successfully installed 'Microsoft.IdentityModel.Clients.ActiveDirectory 2.28.3' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 927.64 ms
    ```

    ```powershell
    C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```

    ```Output
    -------------------------------------------
    CUT              CUT
    -------------------------------------------
    Successfully installed 'Microsoft.Rest.ClientRuntime.Azure.Authentication 2.2.9-preview' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 717.48 ms
    ```

    ```powershell
    wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1 -Out Monitor-Backups.ps1
    # set path variables
    $downloadDir = "C:\scripts\StorSimpleSDKTools"
    $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.StorSimple8000Series"
    #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Series"
    mkdir "$moduleDir"
    ```

    ```Output
        Directory: C:\scripts\StorSimpleSDKTools\AutomationModule

    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----       10/18/2017   8:48 AM                Microsoft.Azure.Management.StorSimple8000Series
    ```

    ```powershell
    Copy-Item "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Microsoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft.Rest.ClientRuntime*.dll" $moduleDir
    Copy-Item "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9-preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\lib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir
    #Don't change the name of the Archive
    compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Management.StorSimple8000Series.zip
    ```

### <a name="import-publish-and-run-automation-runbook"></a>Automation Runbook をインポート、発行、および実行する

1. Azure Portal で、Azure 実行アカウントを Automation として作成します。 そのためには、 **[Azure Marketplace] > [すべて]** に移動し、**Automation** を検索します。 **[Automation アカウント]** を選択します。

    ![search-automation](./media/storsimple-8000-automation-azurerm-runbook/automation1.png)

2. **[Automation アカウントの追加]** ブレードで、次の操作を実行します。

   1. Automation アカウントの **名前** を指定します。
   2. StorSimple デバイス マネージャー サービスにリンクされている **サブスクリプション** を選択します。
   3. 新しいリソース グループを作成するか、既存のリソース グループを選択します。
   4. **場所** (可能な場合は、サービスが実行されている場所と同じ場所) を選択します。
   5. **[Create Run As account]\(実行アカウントの作成\)** オプションは既定値の選択状態のままにします。
   6. オプションで、 **[ダッシュボードにピン留めする]** をオンにします。 **Create** をクリックしてください。

       ![[Automation アカウント] ウィンドウのスクリーンショット。[サブスクリプション]、[Create Run As account]\(実行アカウントの作成\)、[作成] 2 に値が入力されます。](./media/storsimple-8000-automation-azurerm-runbook/create-automation-account.png)

      Automation アカウントが正常に作成されると、その旨が通知されます。 Automation アカウントの作成方法の詳細については、[実行アカウントの作成](../automation/manage-runas-account.md)に関するページを参照してください。

3. 作成した Automation アカウントで StorSimple デバイス マネージャー サービスにアクセスできるようにするには、適切な権限を Automation アカウントに割り当てる必要があります。 StorSimple デバイス マネージャー サービスの **[アクセス制御]** に移動します。 **[+ 追加]** をクリックし、Azure Automation アカウントの名前を入力します。 設定を **保存** します。

    ![add-permissions-automation-account](./media/storsimple-8000-automation-azurerm-runbook/goto-add-roles.png)

4. 新しく作成されたアカウントで、 **[共有リソース] > [モジュール]** に移動し、 **[+ モジュールの追加]** をクリックします。

5. **[モジュールの追加]** ブレードで、zip 形式のモジュールの場所を参照し、モジュールを選択して開きます。 **[OK]** をクリックします。

    ![[モジュールの追加] ウィンドウのスクリーンショット。[ファイルのアップロード] 値と [OK] を確認できます](./media/storsimple-8000-automation-azurerm-runbook/add-module.png)

6. **[プロセス オートメーション] > [Runbook] に移動し、[+ Runbook の追加] をクリックします**。 **[Runbook の追加]** ブレードで、 **[既存の Runbook のインポート]** をクリックします。 **[Runbook ファイル]** で、Windows PowerShell スクリプト ファイルを参照します。 Runbook の種類が自動的に選択されます。 Runbook の名前と説明 (オプション) を入力します。 **Create** をクリックしてください。

    ![add-module](./media/storsimple-8000-automation-azurerm-runbook/import-runbook.png)

7. Runbook が Runbook の一覧に追加されます。 この Runbook を選択してクリックします。

    ![click-new-runbook](./media/storsimple-8000-automation-azurerm-runbook/verify-runbook-created.png)

8. Runbook を編集し、 **[テスト ウィンドウ]** をクリックします。 StorSimple デバイス マネージャー サービスの名前、StorSimple デバイスの名前、サブスクリプションなどのパラメーターを入力します。 テストを **開始** します。 実行が完了すると、レポートが生成されます。 詳細については、[Runbook のテスト方法](../automation/learn/automation-tutorial-runbook-textual-powershell.md#step-3---test-the-runbook)に関するセクションを参照してください。

    ![[テスト] ウィンドウのスクリーンショット。パラメーター値を入力し、テストを開始できます](./media/storsimple-8000-automation-azurerm-runbook/test-runbook.png)

9. [テスト] ウィンドウで Runbook の出力を確認します。 問題がなければ、ウィンドウを閉じます。 **[発行]** をクリックします。確認を求められたら確定して Runbook を発行します。

    ![[PowerShell Runbook の編集] ウィンドウのスクリーンショット。Runbook の発行に進むか問われます](./media/storsimple-8000-automation-azurerm-runbook/publish-runbook.png)

## <a name="next-steps"></a>次のステップ

[StorSimple デバイス マネージャー サービスを使用した StorSimple デバイスの管理](storsimple-8000-manager-service-administration.md)。