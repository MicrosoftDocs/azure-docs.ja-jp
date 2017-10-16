アプリケーションで詳細なパスワード リセットを有効にするには、パスワードのリセット ポリシーを作成する必要があります。 [ここ](../articles/active-directory-b2c/active-directory-b2c-reference-sspr.md)に示されているテナント全体のパスワード リセット オプションに注意してください。 このポリシーは、パスワード リセット中のコンシューマーのエクスペリエンスと、正常に完了したときにアプリケーションが受け取るトークンのコンテンツを記述します。

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

設定のポリシー セクションで **[パスワードのリセット ポリシー]** を選択し、**[+ 追加]** をクリックします。

![サインアップ ポリシーまたはサインイン ポリシーを選択して、[追加] ボタンをクリックします。](media/active-directory-b2c-create-password-reset-policy/add-b2c-password-reset-policy.png)

参照するアプリケーションのポリシーの**名前**を入力します。 たとえば、「 `SSPR`」のように入力します。

**[Identity providers]\(ID プロバイダー\)** を選択し、**[Reset password using email address]\(電子メール アドレスを使用してパスワードをリセットする\)** チェック ボックスをオンにします。 **[OK]**をクリックします。

![ID プロバイダーとして [Reset password using email address]\(電子メール アドレスを使用してパスワードをリセットする\) を選択して [OK] をクリック](media/active-directory-b2c-create-password-reset-policy/add-b2c-password-reset-identity-providers.png)

**[アプリケーション クレーム]** を選択します。 パスワード リセット エクスペリエンスの成功後にアプリケーションに戻される承認トークンで返される要求を選択します。 たとえば、**[User's Object ID] (ユーザーのオブジェクト ID)** を選択します。

![アプリケーション要求を選択して [OK] をクリック](media/active-directory-b2c-create-password-reset-policy/add-b2c-password-reset-application-claims.png)

**[作成]** をクリックして、ポリシーを追加します。 ポリシーが **B2C_1_SSPR** として表示されます。 名前には **B2C_1_** というプレフィックスが追加されます。

**[B2C_1_SSPR]** を選択することによってポリシーを開きます。 テーブルに指定されている設定を確認し、**[今すぐ実行]** をクリックします。

![ポリシーを選択して実行](media/active-directory-b2c-create-password-reset-policy/run-b2c-password-reset-policy.png)

| 設定      | 値  |
| ------------ | ------ |
| **アプリケーション** | Contoso B2C アプリ |
| **応答 URL の選択** | `https://localhost:44316/` |

新しいブラウザー タブが開き、アプリケーションでパスワード リセットのコンシューマー エクスペリエンスを確認できます。

> [!NOTE]
> ポリシーの作成と更新が有効になるまで、最大で 1 分間かかります。
>
