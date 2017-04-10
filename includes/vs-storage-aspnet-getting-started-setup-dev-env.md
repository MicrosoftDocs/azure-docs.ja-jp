## <a name="set-up-the-development-environment"></a>開発環境を設定する

このセクションでは、ASP.NET MVC アプリの作成、接続済みサービスの接続の追加、コントローラーの追加、必要な名前空間ディレクティブの指定など、開発環境の設定方法について説明します。

### <a name="create-an-aspnet-mvc-app-project"></a>ASP.NET MVC アプリ プロジェクトを作成する

1. Visual Studio を開きます。

1. メイン メニューで、**[ファイル]、[新規作成]、[プロジェクト]** の順に選択します。

1. **[新しいプロジェクト]** ダイアログ ボックスで、次の図で強調表示されているようにオプションを指定します。

    ![ASP.NET プロジェクトの作成](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-1.png)

1. **[OK]**を選択します。

1. **[新しい ASP.NET プロジェクト]** ダイアログ ボックスで、次の図で強調表示されているようにオプションを指定します。

    ![MVC の指定](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-2.png)

1. **[OK]**を選択します。

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>接続済みサービスを使用して Azure ストレージ アカウントに接続する

1. **ソリューション エクスプローラー**でプロジェクトを右クリックし、コンテキスト メニューで **[追加]、[接続済みサービス]** の順に選択します。

1. **[接続済みサービスの追加]** ダイアログで **[Azure Storage]** を選択し、**[構成]** を選択します。

    ![[接続済みサービス] ダイアログ](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-3.png)

1. **[Azure Storage]** ダイアログで目的の Azure ストレージ アカウントを選択し、**[追加]** を選択します。


<!--HONumber=Dec16_HO3-->


