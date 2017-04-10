MongoLab データベースをプロビジョニングすると、MongoLab により Azure に MongoDB の標準接続文字列形式で URI が送信されます。 この値は、選択した MongoDB ドライバーをとおして MongoDB 接続を初期化するために使用されます。 接続文字列の詳細については、mongodb.org の [接続に関するページ](http://www.mongodb.org/display/DOCS/Connections) を参照してください。

**この URI は、データベース ユーザー名とパスワードを含んでいます。機密情報として扱い、他人と共有しないでください。**

この URI は、Azure ポータルで次のステップを使用して取得できます。

1. **[アドオン]**を選択します。  
   ![AddonsButton][button-addons]
2. アドオンの一覧で MongoLab サービスを見つけます。  
   ![MongolabEntry][entry-mongolabaddon]
3. アドオンの名前をクリックして、アドオン ページに移動します。
4. **[接続文字列]**をクリックします。  
   ![ConnectionInfoButton][button-connectioninfo]  
   MongoLab の URI が表示されます。  
   ![ConnectionInfoScreen][screen-connectioninfo]  
5. MONGOLAB_URI 値の右側のクリップボード ボタンをクリックし、値全体をクリップボードにコピーします。

[entry-mongolabaddon]: ./media/howto-get-connectioninfo-mongolab/entry-mongolabaddon.png
[button-connectioninfo]: ./media/howto-get-connectioninfo-mongolab/button-connectioninfo.png
[screen-connectioninfo]: ./media/howto-get-connectioninfo-mongolab/dialog-mongolab_connectioninfo.png
[button-addons]: ./media/howto-get-connectioninfo-mongolab/button-addons.png


<!--HONumber=Jan17_HO3-->


