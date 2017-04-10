## <a name="generate-an-api-app-client"></a>API アプリ クライアントの生成
Visual Studio の API アプリ ツールでは、デスクトップ アプリ、ストア アプリ、モバイル アプリから Azure API Apps を呼び出す C# コードを簡単に生成できます。 

1. Visual Studio で、[API アプリの作成](../articles/app-service-api/app-service-dotnet-create-api-app.md)に関するトピックの API アプリが含まれているソリューションを開きます。 
2. **ソリューション エクスプローラー**で、ソリューションを右クリックし、**[追加]** > **[新しいプロジェクト]** の順に選択します。
   
    ![新しいプロジェクトの追加](./media/app-service-dotnet-debug-api-app-gen-api-client/01-add-new-project-v3.png)
3. **[新しいプロジェクトの追加]** ダイアログで、次の手順に従います。
   
   1. **[Windows デスクトップ]** カテゴリを選択します。
   2. **[コンソール アプリケーション]** プロジェクト テンプレートを選択します。
   3. プロジェクトに名前を付けます。
   4. **[OK]** をクリックして、既存のソリューションで新しいプロジェクトを生成します。
      
      ![新しいプロジェクトの追加](./media/app-service-dotnet-debug-api-app-gen-api-client/02-contact-list-console-project-v3.png)
4. 新しく作成されたコンソール アプリケーション プロジェクトを右クリックし、**[追加]** > **[Azure API App Client (Azure API アプリ クライアント)]** の順に選択します。 
   
    ![新しいクライアントの追加](./media/app-service-dotnet-debug-api-app-gen-api-client/03-add-azure-api-client-v3.png)
5. **[Microsoft Azure API アプリ クライアントの追加]** ダイアログで、次の手順に従います。 
   
   1. **[ダウンロード]** オプションを選択します。 
   2. ドロップダウン リストから、先ほど作成した API アプリを選択します。 
   3. **[OK]**をクリックします。 
      
      ![生成画面](./media/app-service-dotnet-debug-api-app-gen-api-client/04-select-the-api-v3.png)
      
      ウィザードによって API メタデータ ファイルがダウンロードされ、API アプリを呼び出すための型指定されたインターフェイスが生成されます。
      
      ![生成中を示す画面](./media/app-service-dotnet-debug-api-app-gen-api-client/05-metadata-downloading-v3.png)
      
      コードの生成が完了すると、API アプリの名前が付いた新しいフォルダーがソリューション エクスプローラーに表示されます。 このフォルダーには、クライアントとデータ モデルを実装するコードが含まれています。 
      
      ![生成完了](./media/app-service-dotnet-debug-api-app-gen-api-client/06-code-gen-output-v3.png)
6. プロジェクトのルートから **Program.cs** ファイルを開き、**Main** メソッドを次のコードに置き換えます。 
   
        static void Main(string[] args)
        {
            var client = new ContactsList();
   
            // Send GET request.
            var contacts = client.Contacts.Get();
            foreach (var c in contacts)
            {
                Console.WriteLine("{0}: {1} {2}",
                    c.Id, c.Name, c.EmailAddress);
            }
   
            // Send POST request.
            client.Contacts.Post(new Models.Contact
            {
                EmailAddress = "lkahn@contoso.com",
                Name = "Loretta Kahn",
                Id = 4
            });
   
            Console.WriteLine("Finished");
            Console.ReadLine();
        }

## <a name="test-the-api-app-client"></a>API アプリ クライアントのテスト
API アプリのコーディングが終わったら、次はコードをテストします。

1. **ソリューション エクスプローラー**を開きます。
2. 前のセクションで作成したコンソール アプリケーションを右クリックします。
3. コンソール アプリケーションのコンテキスト メニューから、**[デバッグ]、[新しいインスタンスを開始]** の順に選択します。 
4. コンソール ウィンドウが開き、すべての連絡先が表示されます。 
   
    ![コンソール アプリの実行](./media/app-service-dotnet-debug-api-app-gen-api-client/running-console-app.png)
5. **Enter** キーを押してコンソール ウィンドウを閉じます。          



<!--HONumber=Jan17_HO3-->


