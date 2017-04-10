Azure ユーザーは 1 か月あたり 25,000 通の電子メールを無料で利用できます。 毎月 25,000 通の無料電子メールにより、高度なレポートおよび分析と [すべての API][all APIs] (Web、SMTP、イベント、解析など) を利用できるようになります。 SendGrid の提供するその他のサービスについては、[SendGrid のソリューション][SendGrid Solutions]に関するページにアクセスしてください。

### <a name="to-sign-up-for-a-sendgrid-account"></a>SendGrid アカウントにサインアップするには
1. [Azure 管理ポータル][Azure Management Portal]にログインします。
2. 左側のメニューで **[新規]** をクリックします。

    ![command-bar-new][command-bar-new]
3. **[アドオン]**、**[SendGrid Email Delivery (SendGrid 電子メール配信)]** の順にクリックします。

    ![sendgrid-store][sendgrid-store]
4. サインアップ フォームに入力し、**[作成]** を選択します。

    ![sendgrid-create][sendgrid-create]
5. Azure 設定で SendGrid サービスを特定する**名前**を入力します。 名前は 1 ～ 100 文字にする必要があります。また、名前に含めることができるのは英数字、ダッシュ、ピリオド、およびアンダースコアのみです。 この名前は、Azure ストアのサブスクライブ項目の一覧で一意になっている必要があります。
6. **パスワード**を入力し、確認します。
7. **サブスクリプション**を選択します。
8. 新しい**リソース グループ**を作成するか、既存のリソース グループを使用します。
9. **[価格レベル]** セクションで、サインアップする SendGrid プランを選択します。

    ![sendgrid-pricing][sendgrid-pricing]
10. 所有している場合は、**プロモーション コード**を入力します。
11. **連絡先情報**を入力します。
12. **法律条項**を確認し、同意します。
13. 購入を確認すると、**[デプロイメントに成功しました]** ポップアップが表示され、**[すべてのリソース]** セクションにアカウントが表示されます。

    ![all-resources][all-resources]

    購入を完了して **[管理]** をクリックし、電子メール検証プロセスが始まると、SendGrid からアカウントの確認を求める電子メールが届きます。 この電子メールを受信していない場合やアカウントの検証に問題がある場合は、この FAQ を参照してください。

    ![manage][manage]

    **アカウントが確認されるまでは、1 日に送信できる電子メールの数は最大 100 件です。**

    サブスクリプション プランを変更するか、SendGrid 連絡先設定を表示するには、SendGrid サービスの名前をクリックし、SendGrid Marketplace ダッシュボードを開きます。

    ![[設定]][settings]

    SendGrid を使用して電子メールを送信するには、API キーを指定する必要があります。

### <a name="to-find-your-sendgrid-api-key"></a>SendGrid API キーを確認するには
1. **[管理]**をクリックします。

    ![manage][manage]
2. SendGrid のダッシュボードの左側のメニューで、**[設定]**、**[API キー]** の順に選択します。

    ![api-keys][api-keys]

3. **[API キーの作成]** ドロップダウンをクリックして、**[General API Key (一般的な API キー)]** を選択します。

    ![general-api-key][general-api-key]
4. 少なくとも、**このキーの名前**を指定して**電子メールの送信**のフル アクセスを提供し、**[保存]** を選択します。

    ![access][access]
5. この時点で API が&1; 回表示されます。 必ず安全に保管してください。

### <a name="to-find-your-sendgrid-credentials"></a>SendGrid の資格情報を確認するには
1. キー アイコンをクリックして、自分の**ユーザー名**を検索します。

    ![key][key]
2. パスワードは、セットアップ時に選択したものです。 変更するには、**[パスワードの変更]** または **[パスワードのリセット]** を選択します。

電子メール配信を管理するには、**[管理]** ボタンをクリックします。 これで、SendGrid のダッシュボードにリダイレクトされます。

    ![manage][manage]

    For more information on sending email through SendGrid, visit the [Email API Overview][Email API Overview].

<!--images-->

[command-bar-new]: ./media/sendgrid-sign-up/new-addon.png
[sendgrid-store]: ./media/sendgrid-sign-up/sendgrid-store.png
[sendgrid-create]: ./media/sendgrid-sign-up/sendgrid-create.png
[sendgrid-pricing]: ./media/sendgrid-sign-up/sendgrid-pricing.png
[all-resources]: ./media/sendgrid-sign-up/all-resources.png
[manage]: ./media/sendgrid-sign-up/manage.png
[settings]: ./media/sendgrid-sign-up/settings.png
[api-keys]: ./media/sendgrid-sign-up/api-keys.png
[general-api-key]: ./media/sendgrid-sign-up/general-api-key.png
[access]: ./media/sendgrid-sign-up/access.png
[key]: ./media/sendgrid-sign-up/key.png

<!--Links-->

[SendGrid Solutions]: https://sendgrid.com/solutions
[Azure Management Portal]: https://manage.windowsazure.com
[SendGrid Getting Started]: http://sendgrid.com/docs
[SendGrid Provisioning Process]: https://support.sendgrid.com/hc/articles/200181628-Why-is-my-account-being-provisioned-
[all APIs]: https://sendgrid.com/docs/API_Reference/index.html
[Email API Overview]: https://sendgrid.com/docs/API_Reference/Web_API_v3/Mail/index.html
