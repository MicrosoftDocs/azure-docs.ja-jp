Azure Marketplace には、Microsoft、サード パーティ企業、およびオープン ソース ソフトウェア活動によって開発された多種多様な人気の Web アプリが用意されています。 Azure Marketplace から作成した Web アプリは、 [Azure Preview ポータル](http://go.microsoft.com/fwlink/?LinkId=529715)への接続に使用するブラウザー以外に、他のソフトウェアをインストールする必要はありません。 

このチュートリアルでは、次のことについて説明します。

* Azure Marketplace を通じて新しい web アプリを作成する方法。
* Azure Preview ポータルを使用して Web アプリをデプロイする方法。

既定のテンプレートを使用する WordPress ブログを構築します。 次の図に、完成したアプリケーションを示します。

![Wordpress blog][13]

> [!NOTE]
> Azure アカウントにサインアップする前に Azure App Service の使用を開始したい場合は、「[Azure App Service アプリケーションの作成](https://azure.microsoft.com/try/app-service/)」を参照してください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。
> 
> 

## <a name="create-a-web-app-in-the-portal"></a>ポータルで Web アプリを作成する
1. Azure プレビュー ポータルにログインします。
2. **Marketplace** アイコンをクリックして、Azure Marketplace を開きます。
   
    ![Marketplace icon][marketplace]
   
    または、ダッシュボードの右上の**[新規]** アイコンをクリックして、一覧の一番下にある**[Marketplace]** を選択します。
   
    ![新規作成][5]
3. **[Web + Mobile]**を選択します。 **WordPress** を検索し、**[WordPress]** アイコンをクリックします。
   
    ![WordPress リスト][7]
4. WordPress アプリの説明を読んだら、 **[作成]**を選択します。
5. **[Web アプリ]**をクリックし、Web アプリを構成するために必要な値を指定します。
   
    ![configure your app][8]
6. **[データベース]**をクリックし、MySQL データベースを構成するために必要な値を指定します。 
   
    ![configure database][database]
7. 新しいリソース グループの名前を指定します。
   
    ![Set resource group][groupname]
8. 必要に応じて、 **[サブスクリプション]**をクリックし、使用するサブスクリプションを指定します。 
9. Web アプリの定義が完了したら、 **[作成]**をクリックし、新しい Web アプリが作成されるのを待機します。
   
   Web アプリが作成されると、Web アプリとデータベースを含む新しいリソース グループが表示されます。
   
   ![show group][resourcegroup]

## <a name="launch-and-manage-your-wordpress-web-app"></a>WordPress Web アプリの起動と管理
1. アプリの詳細を表示するには、新しい Web アプリをクリックします。
   
    ![launch dashboard][10]
2. **[Essentials]** ページで、**[参照]** をクリックするか、**[URL]** の下のリンクをクリックして Web アプリのウェルカム ページを開きます。
   
    ![site URL][browse]
3. WordPress をインストールしていない場合は、WordPress に必要となる適切な構成情報を入力し、 **[WordPress のインストール]** をクリックして構成を完了し、Web アプリのログイン ページを開きます。
4. **[ログイン]** をクリックして、資格情報を入力します。  
5. 新しい WordPress Web アプリは次の Web アプリのようになります。    
   
    ![your WordPress site][13]

[5]: ./media/website-from-gallery/start-marketplace.png
[6]: ./media/website-from-gallery/wordpressgallery-02.png
[7]: ./media/website-from-gallery/search-web-app.png
[8]: ./media/website-from-gallery/set-web-app.png
[9]: ./media/website-from-gallery/wordpressgallery-05.png
[10]: ./media/website-from-gallery/select-web.png
[13]: ./media/website-from-gallery/wordpressgallery-09.png
[webapps]: ./media/website-from-gallery/selectwebapps.png
[database]: ./media/website-from-gallery/set-db.png
[resourcegroup]: ./media/website-from-gallery/show-rg.png
[browse]: ./media/website-from-gallery/browse-web.png
[marketplace]: ./media/website-from-gallery/marketplace-icon.png
[groupname]: ./media/website-from-gallery/set-rg.png


<!--HONumber=Jan17_HO3-->


