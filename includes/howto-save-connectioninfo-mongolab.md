MongoLab URI をコードに貼り付けることはできますが、管理しやすくするため環境内で構成することをお勧めします。 このようにすると、URI が変化した場合、コードに手を加えなくても Azure ポータルで変更できます。

1. Azure ポータルで、 **[Web Apps]**を選択します。
2. Web Apps の一覧に表示されている Web アプリの名前をクリックします。  
   ![WebAppEntry][entry-website]  
   Web アプリのダッシュボードが表示されます。
3. メニュー バーで **[構成]** をクリックします。  
   ![WebAppDashboardConfig][focus-mongolab-websitedashboard-config]
4. 下方向へ [接続文字列] セクションまでスクロールします。  
   ![WebAppConnectionStrings][focus-mongolab-websiteconnectionstring]
5. **[名前]** に、「MONGOLAB_URI」と入力します。
6. **[値]**に、先にコピーした接続文字列を貼り付けます。
7. **[種類]** ボックスの一覧で、既定値の **[SQLAzure]** の代わりに **[カスタム]** を選択します。
8. ツール バーの **[保存]** をクリックします。  
   ![SaveWebApp][button-website-save]

**注:** Azure では、この変数に **CUSTOMCONNSTR\_** というプレフィックスが追加されます。そのため上のコードでは **CUSTOMCONNSTR\_MONGOLAB_URI** を参照しています。

[entry-website]: ./media/howto-save-connectioninfo-mongolab/entry-website.png
[focus-mongolab-websitedashboard-config]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websitedashboard-config.png
[focus-mongolab-websiteconnectionstring]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websiteconnectionstring.png
[button-website-save]: ./media/howto-save-connectioninfo-mongolab/button-website-save.png


<!--HONumber=Jan17_HO3-->


