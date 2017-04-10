ドメインが必要な場合は、[Azure 管理ポータル](https://portal.azure.com)で直接ドメインを購入できます。 次の手順を使用してドメイン名を購入し、Web アプリに割り当てます。

1. ブラウザーで、[Azure 管理ポータル](https://portal.azure.com)を開きます。
2. **[Web Apps]** タブで Web アプリの名前をクリックし、**[設定]**、**[カスタム ドメインおよび SSL]** の順に選択します。
   
    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)
3. **[カスタム ドメインおよび SSL]** ブレードで **[ドメインの購入]** をクリックします。
   
    ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-1.png)
4. **[ドメインの購入]** ブレードで、購入するドメイン名をテキスト ボックスに入力します。 推奨される使用可能なドメインがテキスト ボックスの下に表示されます。 購入したいドメインを選択します。
   
   ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-2.png)
5. **[連絡先情報]** をクリックし、ドメインの連絡先情報フォームに入力します。
   
   ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-3.png)
6. **[ドメインの購入]** ブレードで **[選択]** をクリックすると、**[購入内容の確認]** ブレードに注文情報が表示されます。 法律条項に同意して **[購入]** をクリックすると、注文が送信され、**[通知]** で購入プロセスを確認できます。
   
   ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-4.png)
   
   ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-5.png)
7. ドメインを正常に購入した場合は、ドメインを管理し、Web アプリに割り当てることができます。 ドメインの右側にある **[...]** をクリックします。 **[購入の取り消し]** または **[ドメインの管理]** を選択できます。 **[ドメインの管理]** をクリックし、**[ドメインの管理]** ブレードで**サブドメイン**を Web アプリにバインドします。
   
    ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-6.png)
   
    構成が完了すると、カスタム ドメイン名が Web アプリの **[ホスト名のバインド]** セクションに表示されます。

この時点でブラウザーにカスタム ドメイン名を入力して、対象の Web アプリに正常にアクセスできることを確認できます。



<!--HONumber=Jan17_HO3-->


