#### <a name="to-create-a-new-service"></a>新しいサービスを作成するには
1. Microsoft アカウント資格情報を使用して、この URL ( [https://manage.windowsazure.com/](https://manage.windowsazure.com/)) から Azure クラシック ポータルにログオンします。 Government ポータルにデバイスをデプロイする場合は、[https://manage.windowsazure.us/](https://manage.windowsazure.us/) にログインします。
2. ポータルで、**[新規]、[Data Services]、[StorSimple Manager]、[簡易作成]** の順にクリックします。
3. 表示されるフォームで、次の手順を実行します。
   
   1. サービスの一意の **[名前]** を指定します。 これは、サービスの識別に使用できるフレンドリ名です。 名前の長さは 2 ～ 50 文字とし、文字、数字、ハイフンを含めることができます。 名前の最初と最後は、文字か数字とする必要があります。
   2. StorSimple 仮想デバイスを管理するサービスの **[管理されているデバイスの種類]** ボックスの一覧で、**[仮想デバイス シリーズ]** を選択します。
   3. サービスの **[場所]** を指定します。 場所は、デバイスをデプロイする地理的領域です。
      
      * Azure 内の既存のワークロードを StorSimple デバイスにもデプロイする場合、そのデータセンターを使用する必要があります。
      * StorSimple Manager と Azure Storage は別々の場所に置くことができます。 その場合、StorSimple Manager と Azure Storage のアカウントを別々に作成する必要があります。 Azure Storage のアカウントを作成するには、ポータルで Azure Storage サービスに移動し、「[ストレージ アカウントの作成](../articles/storage/storage-create-storage-account.md#create-a-storage-account)」に記載されている手順を実行します。 アカウントが作成されたら、StorSimple Manager サービスに追加します。この手順については、「[サービスの新しいストレージ アカウントを構成する](#optional-step-configure-a-new-storage-account-for-the-service)」をご覧ください。
      * Government ポータルに仮想デバイスをデプロイする場合、米国アイオワ州と米国バージニア州で StorSimple Manager サービスを使用できます。
   4. **[サブスクリプション]** ボックスの一覧で、サブスクリプションを選択します。 サブスクリプションは、課金アカウントにリンクされます。 このフィールドは、保有するサブスクリプションが 1 つだけの場合は表示されません。
   5. **[新しい Azure ストレージ アカウントを作成する]** をオンにすると、サービスの作成時にストレージ アカウントが自動的に作成されます。 このストレージ アカウントは、"storsimplebwv8c6dcnf" などの特別な名前になります。 別の場所でデータが必要になる場合、このボックスをオフにします。
   6. **[StorSimple Manager の作成]** をクリックしてサービスを作成します。
      
      ![](./media/storsimple-ova-create-new-service/image1m-include.png)
   
   **[サービス]** ランディング ページが表示されます。 サービスの作成には数分かかります。 サービスが正常に作成されると通知が表示されます。
   
   ![](./media/storsimple-ova-create-new-service/image2-include.png)
   
   サービスの状態が **"アクティブ"**に変わります。



<!--HONumber=Nov16_HO3-->


