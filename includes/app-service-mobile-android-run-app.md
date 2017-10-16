
1. [Azure Portal] にアクセスします。 **[すべて参照]** > **[Mobile Apps]** > 作成したバックエンドの順にクリックします。 モバイル アプリの設定で、**[クイック スタート]** > **[Android]** の順にクリックします。 **[Configure your client application (クライアント アプリケーションの構成)]** で、**[ダウンロード]** をクリックします。 これにより、バックエンドに接続するように事前に構成されたアプリ用の完成した Android プロジェクトがダウンロードされます。 
2. **Android Studio** で **[Import project (Eclipse ADT, Gradle, etc.) (プロジェクトのインポート (Eclipse ADT、Gradle など))]** を使用して、プロジェクトを開きます。 JDK エラーを回避するために、このインポート オプションを必ず選択してください。
3. **[アプリの実行]** を押してプロジェクトをビルドし、Android シミュレーターでアプリを開始します。
4. アプリで、意味のあるテキスト (たとえば、「 *チュートリアルの完了* 」) を入力し、[追加] ボタンをクリックします。 これにより、事前にデプロイした Azure バックエンドに POST 要求が送信されます。 バックエンドは要求から取得したデータを TodoItem SQL テーブルに挿入し、新しく格納されたアイテムに関する情報をモバイル アプリに返します。 モバイル アプリでは、このデータが一覧に表示されます。 
   
    ![](./media/app-service-mobile-android-quickstart/mobile-quickstart-startup-android.png)

[Azure Portal]: https://portal.azure.com/
