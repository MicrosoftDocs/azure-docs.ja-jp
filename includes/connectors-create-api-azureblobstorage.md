### <a name="prerequisites"></a>前提条件
* Azure アカウント。[無料アカウント](https://azure.microsoft.com/free)を作成できます。
* [Azure BLOB ストレージ アカウント](../articles/storage/storage-create-storage-account.md)。ストレージ アカウント名とアクセス キーが含まれます。 この情報は、Azure ポータルでストレージ アカウントのプロパティに表示されます。 Azure Storage の詳細については、[こちら](../articles/storage/storage-introduction.md)をご覧ください。

ロジック アプリで Azure Blob Storage アカウントを使用する前に、Azure Blob Storage アカウントに接続します。 これは、Azure Portal のロジック アプリ内で簡単に実行できます。  

次の手順に従って、Azure Blob Storage アカウントに接続します。  

1. ロジック アプリを作成します。 Logic Apps デザイナーで、トリガーを追加して、アクションを追加します。 ドロップダウン リストから **[Microsoft が管理している API を表示]** を選択し、検索ボックスに「blob」と入力します。 いずれかの操作を選択します。  
   
    ![Azure BLOB ストレージの接続の作成手順](./media/connectors-create-api-azureblobstorage/azureblobstorage-1.png)  
2. これまで Azure Storage への接続を行っていない場合は、接続の詳細情報を求められます。   
   
    ![Azure BLOB ストレージの接続の作成手順](./media/connectors-create-api-azureblobstorage/connection-details.png)  
3. ストレージ アカウントの詳細を入力します。 アスタリスクが付いているプロパティは必須です。
   
   | プロパティ | 詳細 |
   | --- | --- |
   | 接続名 * |接続の任意の名前を入力します。 |
   | Azure ストレージ アカウント名 * |ストレージ アカウント名を入力します。 ストレージ アカウント名は、Azure ポータルのストレージのプロパティに表示されます。 |
   | Azure Storage Account Access Key (Azure ストレージ アカウント アクセス キー) * |ストレージ アカウント キーを入力します。 アクセス キーは、Azure ポータルのストレージのプロパティに表示されます。 |
   
    これらの資格情報を使用して、接続するロジック アプリの承認と、データへのアクセスが行われます。 
4. **[作成]**を選択します。
5. 接続が作成されたことを確認します。 これで、ロジック アプリで他の手順に進むことができます。 
   
    ![Azure BLOB ストレージの接続の作成手順](./media/connectors-create-api-azureblobstorage/azureblobstorage-3.png)  



<!--HONumber=Nov16_HO3-->


