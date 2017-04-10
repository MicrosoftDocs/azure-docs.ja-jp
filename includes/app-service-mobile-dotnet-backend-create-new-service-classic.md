1. [Azure ポータル]にログインします。
2. **[+ 新規]** > **[Web + モバイル]** > **[モバイル アプリ]** の順にクリックし、モバイル アプリ バックエンドの名前を入力します。
3. **[リソース グループ]**で、既存のリソース グループを選択するか、新しく作成します (アプリと同じ名前を使用)。 
   
    別の App Service プランを選択するか、または新しいプランを作成することもできます。 App Services プランの詳細と、さまざまな価格レベルおよび目的の場所で新しいプランを作成する方法については、「 [Azure App Service プランの詳細な概要](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)」を参照してください。
4. **[App Service プラン]**には既定のプラン ( [Standard レベル](https://azure.microsoft.com/pricing/details/app-service/)) が選択されています。 別のプランを選択することも、[新しいプランを作成](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md#create-an-app-service-plan)することもできます。 App Service プランの設定により、アプリに関連付けられる [場所、機能、コスト、コンピューティング リソース](https://azure.microsoft.com/pricing/details/app-service/) が決まります。 
   
    プランを決定したら、 **[作成]**をクリックします。 モバイル アプリ バックエンドが作成されます。 
5. 新しいモバイル アプリ バックエンドの **[設定]** ブレードで、**[クイック スタート]**、お使いのクライアント アプリ プラットフォーム、**[Connect a database (データベースに接続する)]** の順にクリックします。 
   
    ![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-data-connection.png)
6. **[Add data connection (データ接続の追加)]** ブレードで、**[SQL Database]** > **[新しいデータベースの作成]** の順にクリックします。データベース名を **[名前]** に入力し、価格レベルを設定して、**[サーバー]** をクリックします。  この新しいデータベースは再利用できます。 同じ場所に既にデータベースを所有している場合は、代わりに **[既存のデータベースの使用]**を選択することもできます。 別の場所にあるデータベースを使用することは、帯域幅コストと待機時間が増加するため、お勧めしません。
   
    ![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)
7. **[新しいサーバー]** ブレードで、**[サーバー名]** フィールドに一意のサーバー名を入力して、ログインとパスワードを指定し、**[Azure サービスにサーバーへのアクセスを許可する]** をオンにして、**[OK]** をクリックします。 新しいデータベースが作成されます。
8. **[Add data connection (データ接続の追加)]** ブレードに戻り、**[接続文字列]** をクリックし、データベースのログインとパスワードを入力して、**[OK]** をクリックします。 データベースが正常にデプロイされるまで数分待ってから次の手順に進んでください。

<!-- URLs. -->
[Azure ポータル]: https://portal.azure.com/


<!--HONumber=Nov16_HO3-->


