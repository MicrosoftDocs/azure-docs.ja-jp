### <a name="prerequisites"></a>前提条件
* [SFTP](https://en.wikipedia.org/wiki/SSH_File_Transfer_Protocol) アカウント  

ロジック アプリで SFTP アカウントを使用するには、SFTP アカウントに接続するロジック アプリを承認する必要があります。 これは、Azure ポータルのロジック アプリ内から簡単に実行できます。  

SFTP アカウントに接続するロジック アプリを承認する手順を次に示します。  

1. SFTP への接続を作成するには、ロジック アプリ デザイナーのドロップダウン リストから **[Microsoft が管理している API を表示]** を選択し、検索ボックスに「*SFTP*」と入力します。 **[SFTP - When a file is added or modified (SFTP - ファイルが追加または変更されたとき)]** トリガーを選択します。  
   ![SFTP オンライン接続イメージ 1](./media/connectors-create-api-sftp/sftp-1.png)  
2. これまでに SFTP への接続を作成したことがない場合は、SFTP の資格情報の入力を求められます。 この資格情報を使用して、接続するロジック アプリの承認と、SFTP アカウントのデータへのアクセスが行われます。  
   ![SFTP オンライン接続イメージ 2](./media/connectors-create-api-sftp/sftp-2.png)  
3. 接続が作成されたら、ロジック アプリで他の手順を自由に実行できるようになります。   
   ![SFTP オンライン接続イメージ 3](./media/connectors-create-api-sftp/sftp-3.png) 



<!--HONumber=Nov16_HO3-->


