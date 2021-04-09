---
author: georgewallace
ms.service: multiple
ms.topic: include
ms.date: 11/25/2018
ms.author: gwallace
ms.openlocfilehash: 5bc00f4de95d22eec71f9b1b2504b00f506232dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99213746"
---
### <a name="to-sign-up-for-a-sendgrid-account"></a>SendGrid アカウントにサインアップするには
1. [Azure portal][Azure portal] にサインインする
2. Azure portal メニューまたは [ホーム] ページで、 **[リソースの作成]** を選択します。

    ![[リソースの作成] オプションが選択されている Azure portal メニューのスクリーンショット。][command-bar-new]
3. **[SendGrid]** を探して選択します。

    ![検索ボックスに「SendGr」と入力され、検索結果で [SendGrid] が選択されている、Azure portal の [Marketplace] 画面のスクリーンショット。][sendgrid-store]
4. サインアップ フォームに入力し、 **[作成]** を選択します。

    ![[名前]、[パスワード]、[サブスクリプション]、[リソースグループ] の各フィールドが入力された [新しい SendGrid アカウントの作成] ダイアログのスクリーンショット。][sendgrid-create]
5. Azure 設定で SendGrid サービスを特定する **名前** を入力します。 名前は 1 ～ 100 文字にする必要があります。また、名前に含めることができるのは英数字、ダッシュ、ピリオド、およびアンダースコアのみです。 この名前は、Azure ストアのサブスクライブ項目の一覧で一意になっている必要があります。
6. **パスワード** を入力し、確認します。
7. **サブスクリプション** を選択します。
8. 新しい **リソース グループ** を作成するか、既存のリソース グループを使用します。
9. **[価格レベル]** セクションで、サインアップする SendGrid プランを選択します。

    ![[価格レベルの選択] セクションが開いている、[新しい SendGrid アカウントの作成] ダイアログのスクリーンショット][sendgrid-pricing]
10. 所有している場合は、**プロモーション コード** を入力します。
11. **連絡先情報** を入力します。
12. **法律条項** を確認し、同意します。
13. 購入が確認されると、 **[デプロイに成功しました]** ポップアップが表示され、お使いのアカウントが一覧に表示されます。

    ![新しいアカウントの ContosoSendGrid が一覧表示されている [SendGrid アカウント] ページのスクリーンショット。][all-resources]

    購入を完了して **[Manage]** をクリックし、電子メール検証プロセスが始まると、SendGrid からアカウントの確認を求める電子メールが届きます。 この電子メールを受信していない場合やアカウントの検証に問題がある場合は、Microsoft の FAQ を参照してください。

    ![[管理] ボタンが強調表示されている [ContosoSendGrid] アカウント ページのスクリーンショット。][manage]

    **アカウントが確認されるまでは、1 日に送信できる電子メールの数は最大 100 件です。**

    サブスクリプション プランを変更するか、SendGrid 連絡先設定を表示するには、SendGrid サービスの名前をクリックし、SendGrid Marketplace ダッシュボードを開きます。

    ![[ContosoSendGrid] アカウント ページで [すべての設定] を選択したことで、ContosoSendGrid アカウントの [設定] ページが開かれたことを示すスクリーンショット。][settings]

    SendGrid を使用して電子メールを送信するには、API キーを指定する必要があります。

### <a name="to-find-your-sendgrid-api-key"></a>SendGrid API キーを確認するには
1. **Manage** をクリックします。

    ![[管理] ボタンが強調表示されている [ContosoSendGrid] アカウント ページのスクリーンショット。][manage]
2. SendGrid のダッシュボードの左側のメニューで、 **[Settings]** 、 **[API Keys]** の順に選択します。

    ![[設定] ドロップダウンが開き、[API キー] が選択されている、SendGrid のダッシュボードのスクリーンショット。][api-keys]

3. **[API キーの作成]** をクリックします。

    ![[API キーの作成] ボタンが選択されている [API キー] 画面のスクリーンショット。][general-api-key]
4. 少なくとも、**このキーの名前** を指定して **電子メールの送信** のフル アクセスを提供し、 **[保存]** を選択します。

    ![[メールの送信] が [フルアクセス] に設定され、[スケジュールされた送信] が [アクセスなし] に設定されて、[保存] ボタンが強調表示されている、[新しい一般的な API キーの追加] 画面のスクリーンショット。][access]
5. この時点で API が 1 回表示されます。 必ず安全に保管してください。

### <a name="to-find-your-sendgrid-credentials"></a>SendGrid の資格情報を確認するには
1. キー アイコンをクリックして、自分の **ユーザー名** を検索します。

    ![キー アイコンが強調表示されている ContosoSendGrid アカウント ページのスクリーンショット。][key]
2. パスワードは、セットアップ時に選択したものです。 変更するには、 **[パスワードの変更]** または **[パスワードのリセット]** を選択します。

電子メール配信を管理するには、**管理** ボタンをクリックします。 これで、SendGrid のダッシュボードにリダイレクトされます。

![[管理] ボタンが強調表示されている [ContosoSendGrid] アカウント ページのスクリーンショット。][manage]

SendGrid 経由での電子メールの送信の詳細については、[電子メール API の概要][Email API Overview]に関するページを参照してください。

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
[Azure portal]: https://portal.azure.com
[SendGrid Getting Started]: http://sendgrid.com/docs
[SendGrid Provisioning Process]: https://support.sendgrid.com/hc/articles/200181628-Why-is-my-account-being-provisioned-
[all APIs]: https://sendgrid.com/docs/API_Reference/index.html
[Email API Overview]: https://sendgrid.com/docs/API_Reference/Web_API_v3/Mail/index.html
