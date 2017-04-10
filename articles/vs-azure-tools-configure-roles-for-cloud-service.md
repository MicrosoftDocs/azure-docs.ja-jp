---
title: "Visual Studio で Azure クラウド サービスのロールを構成する | Microsoft Docs"
description: "Visual Studio を使用し、Azure クラウド サービスのロールをセットアップして構成する方法について説明します。"
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: d397ef87-64e5-401a-aad5-7f83f1022e16
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 03/21/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 9e79f275b8de045892517ec33581b72195a81f32
ms.lasthandoff: 03/22/2017


---
# <a name="configure-azure-cloud-service-roles-with-visual-studio"></a>Visual Studio で Azure クラウド サービスのロールを構成する
Azure クラウド サービスには、worker ロールまたは Web ロールを割り当てることができます。 それぞれのロールについて、そのセットアップ方法を定義すると共に、実行方法を構成する必要があります。 クラウド サービスのロールの詳細については、 [Azure Cloud Services の概要](https://channel9.msdn.com/Series/Windows-Azure-Cloud-Services-Tutorials/Introduction-to-Windows-Azure-Cloud-Services)を紹介した動画をご覧ください。 

クラウド サービスに関する情報は、次のファイルに格納されます。

- **ServiceDefinition.csdef**: サービス定義ファイルは、必要なロール、エンドポイント、仮想マシンのサイズを含む、クラウド サービスのランタイム設定を定義します。 `ServiceDefinition.csdef` に格納されたデータはいずれも、ロールの実行中には変更できません。
- **ServiceConfiguration.cscfg**: サービス構成ファイルでは、実行されるロールのインスタンス数とロールに対して定義される設定の値を構成します。 `ServiceConfiguration.cscfg` に格納されたデータは、ロールの実行中に変更できます。

ロールの実行方法を制御する設定にさまざまな値を指定して使い分けられるように、サービス構成は複数定義することができます。 デプロイメント環境ごとに異なるサービス構成を使用できます。 たとえば、ローカル サービス構成で、ローカルの Azure ストレージ エミュレーターを使用するようにストレージ アカウントの接続文字列を設定する一方、クラウドで Azure ストレージを使用するためのサービス構成を別途作成することができます。

Visual Studio で Azure クラウド サービスを作成すると、2 つのサービス構成が自動的に作成されて Azure プロジェクトに追加されます。

- `ServiceConfiguration.Cloud.cscfg`
- `ServiceConfiguration.Local.cscfg`

## <a name="configure-an-azure-cloud-service"></a>Azure クラウド サービスを構成する
次の手順に示すように、Azure クラウド サービスの構成は、Visual Studio のソリューション エクスプローラーから行うことができます。

1. Visual Studio で Azure クラウド サービス プロジェクトを開くか新たに作成します。

1. **ソリューション エクスプローラー**でそのプロジェクトを右クリックし、コンテキスト メニューから **[プロパティ]** を選択します。
   
    ![ソリューション エクスプローラーのプロジェクトのコンテキスト メニュー](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-project-context-menu.png)

1. プロジェクトのプロパティ ページで **[開発]** タブを選択します。 

    ![プロジェクトのプロパティ ページ - [開発] タブ](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-development-tab.png)

1. **[サービスの構成]** ボックスの一覧で、編集するサービス構成の名前を選択します  (このロールのすべてのサービス構成を変更する場合は、**[すべての構成]** を選択してください)。
   
    > [!IMPORTANT]
    > 設定の対象が "すべての構成" に限定されるプロパティが一部存在します。特定のサービス構成を選択した場合、それらのプロパティは無効になります。 これらのプロパティを編集するには、**[すべての構成]** を選択する必要があります。
    > 
    > 
   
    ![Azure クラウド サービスのサービス構成の一覧](./media/vs-azure-tools-configure-roles-for-cloud-service/cloud-service-service-configuration-property.png)

## <a name="change-the-number-of-role-instances"></a>ロール インスタンス数の変更
クラウド サービスのパフォーマンスを高めるには、実行するロールのインスタンスの数を、ユーザー数や特定のロールに必要な負荷に応じて変更します。 Azure でクラウド サービスを実行すると、ロールのインスタンスごとに仮想マシンが作成されます。 このことが、そのクラウド サービスのデプロイの料金に影響します。 課金の詳細については、「[Microsoft Azure の課金内容の確認](billing/billing-understand-your-bill.md)」を参照してください。

1. Visual Studio で Azure クラウド サービス プロジェクトを開くか新たに作成します。

1. **ソリューション エクスプローラー**でプロジェクト ノードを展開します。 更新するロールを **[ロール]** ノードで右クリックし、コンテキスト メニューから **[プロパティ]** を選択します。

    ![ソリューション エクスプローラーの Azure ロールのコンテキスト メニュー](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-azure-role-context-menu.png)

1. **[構成]** タブを選択します。

    ![[構成] タブ](./media/vs-azure-tools-configure-roles-for-cloud-service/role-configuration-properties-page.png)

1. **[サービスの構成]** の一覧で、更新するサービス構成を選択します。
   
    ![サービス構成の一覧](./media/vs-azure-tools-configure-roles-for-cloud-service/role-configuration-properties-page-select-configuration.png)

1. **[インスタンス数]** テキスト ボックスに、このロールで開始するインスタンスの数を入力します。 Azure にクラウド サービスを発行すると、それぞれのインスタンスが個別の仮想マシンで実行されます。

    ![インスタンス数の更新](./media/vs-azure-tools-configure-roles-for-cloud-service/role-configuration-properties-page-instance-count.png)

1. Visual Studio のツール バーの **[保存]** を選択します。

## <a name="manage-connection-strings-for-storage-accounts"></a>ストレージ アカウント用の接続文字列の管理
接続文字列は、サービス構成で追加、削除、変更することができます。 たとえば、 `UseDevelopmentStorage=true`という値を持つローカル サービス構成には、ローカル接続文字列を使用します。 それに加えて、Azure のストレージ アカウントを使用するクラウド サービス構成も必要になることが考えられます。

> [!WARNING]
> ストレージ アカウント接続文字列の Azure ストレージ アカウント キー情報を入力したとき、その情報はローカルのサービス構成ファイルに格納されます。 ただしこの情報は現在、暗号化されたテキストとして保存されません。
> 
> 

サービス構成ごとに値を使い分ければ、クラウド サービスを Azure に発行する段階でコードに変更を加えたり、複数の異なる接続文字列をクラウド サービスで使用したりせずに済みます。 コード内の接続文字列には同じ名前を使用できます。クラウド サービスをビルドまたは発行するときに選択したサービス構成によって異なる値が適用されます。

1. Visual Studio で Azure クラウド サービス プロジェクトを開くか新たに作成します。

1. **ソリューション エクスプローラー**でプロジェクト ノードを展開します。 更新するロールを **[ロール]** ノードで右クリックし、コンテキスト メニューから **[プロパティ]** を選択します。

    ![ソリューション エクスプローラーの Azure ロールのコンテキスト メニュー](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-azure-role-context-menu.png)

1. **[設定]** タブを選択します。

    ![Settings tab](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab.png)

1. **[サービスの構成]** の一覧で、更新するサービス構成を選択します。

    ![サービス構成](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-select-configuration.png)

1. 接続文字列を追加するには、**[設定の追加]** を選択します。

    ![接続文字列の追加](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-add-setting.png)

1. 新しい設定が一覧に追加されたら、必要な情報でその行を更新します。

    ![新しい接続文字列](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-add-setting-new-setting.png)

    - **[名前]**: 接続文字列に使用する名前を入力します。
    - **[種類]**: ドロップダウン リストから **[接続文字列]** を選択します。
    - **[値]**: 接続文字列は **[値]** セルに直接入力するか、省略記号 (...) を選択して **[ストレージ接続文字列の作成]** ダイアログで作成することができます。  

1. **[ストレージ接続文字列の作成]** ダイアログで **[接続方法]** のオプションを選択します。 選択したオプションに応じて次の手順に従います。

    - **[Microsoft Azure ストレージ エミュレーター]**: このオプションを選択した場合、ダイアログ上の他の設定は Azure にのみ該当するため無効になります。 **[OK]**を選択します。
    - **[サブスクリプション]**: このオプションを選択した場合、ドロップダウン リストから Microsoft アカウントを選択してサインインするか、または Microsoft アカウントを追加してください。 Azure サブスクリプションとストレージ アカウントを選択します。 **[OK]**を選択します。
    - **[手動で入力された資格情報]**: ストレージ アカウント名を入力し、プライマリ キーまたはセカンダリ キーを入力します。 **[接続]** のオプションを選択します (通常は HTTPS を推奨)。**[OK]**を選択します。

1. 接続文字列を削除するには、接続文字列を選択し、**[設定の削除]** を選択します。

1. Visual Studio のツール バーの **[保存]** を選択します。

## <a name="programmatically-access-a-connection-string"></a>接続文字列にプログラムからアクセスする

次の手順では、C# を使って接続文字列にプログラムからアクセスする方法について説明します。

1. 設定を使う予定のある C# ファイルに次の using ディレクティブを追加します。

    ```csharp
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.ServiceRuntime;
    ```

1. 次のコードは、接続文字列にアクセスする方法の例を示しています。 &lt;ConnectionStringName> プレースホルダーは、適切な値に置き換えてください。 

    ```csharp
    // Setup the connection to Azure Storage
    var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("<ConnectionStringName>"));
    ```

## <a name="add-custom-settings-to-use-in-your-azure-cloud-service"></a>Azure クラウド サービスで使用するカスタム設定を追加する
特定のサービス構成に使用する文字列の名前と値は、サービス構成ファイルのカスタム設定で追加できます。 コードの中でカスタム設定の値を読み取ってその値を基にロジックを制御することで、クラウド サービスの機能を構成することができます。 これらのサービス構成の値を変更するために、サービス パッケージをリビルドする必要はありません。クラウド サービスの実行中に値を変更することもできます。 設定が変更されたことは、その通知をコードで受け取ることによって確認できます。 「[RoleEnvironment.Changing イベント](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changing.aspx)」を参照してください。

カスタム設定は、目的のサービス構成を対象に追加、削除、変更することができます。 文字列の値はサービス構成ごとに使い分けることができます。

サービス構成ごとに値を使い分ければ、クラウド サービスを Azure に発行する段階でコードに変更を加えたり、複数の異なる文字列をクラウド サービスで使用したりせずに済みます。 コード内の文字列には同じ名前を使用できます。クラウド サービスをビルドまたは発行するときに選択したサービス構成によって異なる値が適用されます。

1. Visual Studio で Azure クラウド サービス プロジェクトを開くか新たに作成します。

1. **ソリューション エクスプローラー**でプロジェクト ノードを展開します。 更新するロールを **[ロール]** ノードで右クリックし、コンテキスト メニューから **[プロパティ]** を選択します。

    ![ソリューション エクスプローラーの Azure ロールのコンテキスト メニュー](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-azure-role-context-menu.png)

1. **[設定]** タブを選択します。

    ![Settings tab](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab.png)

1. **[サービスの構成]** の一覧で、更新するサービス構成を選択します。

    ![サービス構成の一覧](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-select-configuration.png)

1. カスタム設定を追加するには、**[設定の追加]** を選択します。

    ![カスタム設定の追加](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-add-setting.png)

1. 新しい設定が一覧に追加されたら、必要な情報でその行を更新します。

    ![新しいカスタム設定](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-add-setting-new-setting.png)

    - **[名前]**: 設定の名前を入力します。
    - **[種類]**: ドロップダウン リストから **[文字列]** を選択します。
    - **[値]**: 文字列の値を入力します。 この値は **[値]** セルに直接入力するか、省略記号 (...) を選択して **[文字列の編集]** ダイアログで入力することができます。  

1. カスタム設定を削除するには、設定を選択し、**[設定の削除]** を選択します。

1. Visual Studio のツール バーの **[保存]** を選択します。

## <a name="programmatically-access-a-custom-settings-value"></a>カスタム設定の値にプログラムからアクセスする
 
次の手順では、C# を使ってカスタム設定にプログラムからアクセスする方法について説明します。

1. 設定を使う予定のある C# ファイルに次の using ディレクティブを追加します。

    ```csharp
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.ServiceRuntime;
    ```

1. 次のコードは、カスタム設定にアクセスする方法の例を示しています。 &lt;SettingName> プレースホルダーは、適切な値に置き換えてください。 
    
    ```csharp
    var settingValue = RoleEnvironment.GetConfigurationSettingValue("<SettingName>");
    ```

## <a name="manage-local-storage-for-each-role-instance"></a>各ロール インスタンスのローカル ストレージの管理
ローカル ファイル システム ストレージは、ロールのインスタンスごとに追加できます。 このストレージに保存されるデータには、それを保存するロールのインスタンス以外からアクセスしたり、他のロールからアクセスしたりすることはできません。  

1. Visual Studio で Azure クラウド サービス プロジェクトを開くか新たに作成します。

1. **ソリューション エクスプローラー**でプロジェクト ノードを展開します。 更新するロールを **[ロール]** ノードで右クリックし、コンテキスト メニューから **[プロパティ]** を選択します。

    ![ソリューション エクスプローラーの Azure ロールのコンテキスト メニュー](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-azure-role-context-menu.png)

1. **[ローカル ストレージ]** タブを選択します。

    ![[ローカル ストレージ] タブ](./media/vs-azure-tools-configure-roles-for-cloud-service/role-local-storage-tab.png)

1. このローカル ストレージの設定はすべてのサービス構成が対象となります。**[サービス構成]** リストでは必ず **[すべての構成]** を選択してください。 それ以外の値を選択すると、このページのすべての入力フィールドが無効になります。 

    ![サービス構成の一覧](./media/vs-azure-tools-configure-roles-for-cloud-service/role-local-storage-tab-service-configuration.png)

1. ローカル ストレージのエントリを追加するには、**[ローカル ストレージの追加]** を選択します。

    ![ローカル ストレージの追加](./media/vs-azure-tools-configure-roles-for-cloud-service/role-local-storage-tab-add-local-storage.png)

1. 新しいローカル ストレージのエントリが一覧に追加されたら、必要な情報でその行を更新します。

    ![新しいローカル ストレージのエントリ](./media/vs-azure-tools-configure-roles-for-cloud-service/role-local-storage-tab-new-local-storage.png)

    - **[名前]**: 新しいローカル ストレージに使用する名前を入力します。
    - **[サイズ (MB)]**: 新しいローカル ストレージに必要なサイズを MB 単位で入力します。
    - **[ロールのリサイクル時に消去]**: このロール用の仮想マシンがリサイクルされたときに、新しいローカル ストレージのデータを削除するには、このオプションを選択します。

1. ローカル ストレージのエントリを削除するには、そのエントリを選択し、**[ローカル ストレージの削除]** を選択します。

1. Visual Studio のツール バーの **[保存]** を選択します。

## <a name="programmatically-accessing-local-storage"></a>ローカル ストレージにプログラムからアクセスする

このセクションでは、C# を使ったテスト用テキスト ファイル (`MyLocalStorageTest.txt`) の書き込みを通じて、ローカル ストレージにプログラムからアクセスする方法について説明します。  

### <a name="write-a-text-file-to-local-storage"></a>ローカル ストレージにテキスト ファイルを書き込む

以下のコード例は、ローカル ストレージにテキスト ファイルを書き込む方法を示しています。 &lt;LocalStorageName> プレースホルダーは、適切な値に置き換えてください。 

    ```csharp
    // Retrieve an object that points to the local storage resource
    LocalResource localResource = RoleEnvironment.GetLocalResource("<LocalStorageName>");
    
    //Define the file name and path
    string[] paths = { localResource.RootPath, "MyLocalStorageTest.txt" };
    String filePath = Path.Combine(paths);
    
    using (FileStream writeStream = File.Create(filePath))
    {
        Byte[] textToWrite = new UTF8Encoding(true).GetBytes("Testing Web role storage");
        writeStream.Write(textToWrite, 0, textToWrite.Length);
    }

    ```

### <a name="find-a-file-written-to-local-storage"></a>ローカル ストレージに書き込まれたファイルを探す

前のセクションのコードによって作成されたファイルを確認するには、次の手順を実行します。
    
1.  Windows 通知領域で Azure アイコンを右クリックし、コンテキスト メニューから **[Show Compute Emulator UI (コンピューティング エミュレーター UI の表示)]** を選択します。 

    ![コンピューティング エミュレーターの表示](./media/vs-azure-tools-configure-roles-for-cloud-service/show-compute-emulator.png)

1. Web ロールを選択します。

    ![Azure コンピューティング エミュレーター](./media/vs-azure-tools-configure-roles-for-cloud-service/compute-emulator.png)

1. **[Microsoft Azure コンピューティング エミュレーター]** メニューの **[ツール]** > **[Open local store (ローカル ストアを開く)]** を選択します。

    ![ローカル ストアを開くためのメニュー項目](./media/vs-azure-tools-configure-roles-for-cloud-service/compute-emulator-open-local-store-menu.png)

1. Windows エクスプローラー ウィンドウが表示されたら、**[検索]** ボックスに「`MyLocalStorageTest.txt``」と入力し、**Enter** キーを押して検索を開始します。 

## <a name="next-steps"></a>次のステップ
Visual Studio における Azure プロジェクトの詳細については、「 [Azure プロジェクトの構成](vs-azure-tools-configuring-an-azure-project.md)」を参照してください。 クラウド サービスのスキーマの詳細については、「 [スキーマ リファレンス](https://msdn.microsoft.com/library/azure/dd179398)」を参照してください。


