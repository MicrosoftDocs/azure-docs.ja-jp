


## <a name="attach-an-empty-disk"></a>Attach an empty disk
空のディスクを接続すると、Azure が .vhd ファイルを作成してそれをストレージ アカウントに保存するため、データ ディスクを簡単に追加できます。

1. **[仮想マシン (クラシック)]** をクリックし、適切な VM を選択します。

2. [設定] メニューで **[ディスク]** をクリックします。

   ![Attach a new empty disk](./media/howto-attach-disk-windows-linux/menudisksattachnew.png)

3. コマンド バーで、**[新しいディスクの接続]** をクリックします。  
    **[新しいディスクの接続]** ダイアログ ボックスが表示されます。

    ![新しいディスクの接続](./media/howto-attach-disk-windows-linux/newdiskdetail.png)

    次の情報を入力します。
    - **[ファイル名]**で、.vhd ファイルのファイル名として既定の名前をそのまま使用するか、別の名前を入力します。 .vhd ファイルの別の名前を入力した場合でも、データ ディスクには自動的に生成された名前が使用されます。
    - データ ディスクの**種類**を選択します。 すべての仮想マシンは Standard ディスクをサポートしています。 多くの仮想マシンは Premium ディスクもサポートしています。
    - データ ディスクの**サイズ (GB)** を選択します。
    - **[キャッシュ ホスト]** では、[なし] または [読み取り専用] を選択します。
    - [OK] をクリックして終了します。

4. データ ディスクが作成されて接続されると、VM のディスク セクションに表示されます。

   ![空の新しいデータ ディスクの接続に成功](./media/howto-attach-disk-windows-linux/newdiskemptysuccessful.png)

> [!NOTE]
> データ ディスクを追加した後、VM にログオンし、ディスクを初期化して使用できるようにする必要があります。

## <a name="how-to-attach-an-existing-disk"></a>方法: 既存のディスクの接続
既存のディスクを接続する場合は、ストレージ アカウントで利用できる .vhd を持っている必要があります。 .vhd ファイルをストレージ アカウントにアップロードするには、 [Add-AzureVhd](https://msdn.microsoft.com/library/azure/dn495173.aspx) コマンドレットを使用します。 .vhd ファイルを作成してアップロードした後は、それを VM に接続できます。

1. **[仮想マシン (クラシック)]** をクリックし、適切な仮想マシンを選択します。

2. [設定] メニューで **[ディスク]** をクリックします。

3. コマンド バーで、**[既存のディスクの接続]** をクリックします。

    ![Attach data disk](./media/howto-attach-disk-windows-linux/menudisksattachexisting.png)

4. **[場所]** をクリックします。 使用できるストレージ アカウントが表示されます。 次に、一覧から適切なストレージ アカウントを選択します。

    ![ディスクのストレージ アカウントの指定](./media/howto-attach-disk-windows-linux/existdiskstorageaccounts.png)

5. **ストレージ アカウント**には、ディスク ドライブ (VHD) を含む&1; つまたは複数のコンテナーが保持されています。 一覧から適切なコンテナーを選択します。

    ![Windows 仮想マシンのコンテナーの指定](./media/howto-attach-disk-windows-linux/existdiskcontainers.png)

6. **[VHD]** パネルに、コンテナーに保持されているディスク ドライブの一覧が表示されます。 いずれかのディスクをクリックし、[選択] をクリックします。

    ![Windows 仮想マシンのディスク イメージの指定](./media/howto-attach-disk-windows-linux/existdiskvhds.png)

7. **[既存のディスクの接続]** パネルがもう一度開き、仮想マシンに追加するストレージ アカウント、コンテナー、選択されたハード ディスク (VHD) を格納している場所が表示されます。

  **[キャッシュ ホスト]** を [なし] または [読み取り専用] に設定し、[OK] をクリックします。

    ![Data disk successfully attached](./media/howto-attach-disk-windows-linux/exisitingdisksuccessful.png)
