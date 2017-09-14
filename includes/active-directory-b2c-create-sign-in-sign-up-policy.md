アプリケーションにサインインできるようにするには、サインイン ポリシーを作成する必要があります。 このポリシーは、サインイン中のコンシューマーのエクスペリエンスと、サインインが成功したときにアプリケーションが受け取るトークンのコンテンツを記述します。

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

設定のポリシー セクションで **[サインアップまたはサインイン ポリシー]** を選択し、**[+ 追加]** をクリックします。

![サインアップ ポリシーまたはサインイン ポリシーを選択して [追加] をクリック](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-policy.png)

参照するアプリケーションのポリシーの**名前**を入力します。 たとえば、「 `SiUpIn`」のように入力します。

**[ID プロバイダー]** を選択し、**[電子メールのサインアップ]** チェック ボックスをオンにします。 既に構成されている場合は、ソーシャル ID プロバイダーを選択することもできます。 **[OK]**をクリックします。

![ID プロバイダーとして [電子メールのサインアップ] を選択して [OK] をクリック](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-identity-providers.png)

**[サインアップ属性]** を選択します。 サインアップ中にコンシューマーから収集する属性を選択します。 たとえば、**[国/リージョン]**、**[表示名]**、**[郵便番号]** の各チェック ボックスをオンにします。 **[OK]**をクリックします。

![属性を選択して [OK] をクリック](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-sign-up-attributes.png)

**[アプリケーション クレーム]** を選択します。 サインアップまたはサインイン エクスペリエンスの成功後にアプリケーションに戻される承認トークンで返される要求を選択します。 たとえば、**[表示名]**、**[ID プロバイダー]**、**[郵便番号]**、**[User is new]\(ユーザーは新規\)**、および **[User's Object ID]\(ユーザーのオブジェクト ID\)** を選択します。

![アプリケーション要求を選択して [OK] をクリック](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-application-claims.png)

**[作成]** をクリックして、ポリシーを追加します。 ポリシーが **B2C_1_SiUpIn** として表示されます。 名前には **B2C_1_** というプレフィックスが追加されます。

**[B2C_1_SiUpIn]** を選択することによってポリシーを開きます。 テーブルに指定されている設定を確認し、**[今すぐ実行]** をクリックします。

![ポリシーを選択して実行](media/active-directory-b2c-create-sign-in-sign-up-policy/run-b2c-signup-signin-policy.png)

| 設定      | 値  |
| ------------ | ------ |
| **アプリケーション** | Contoso B2C アプリ |
| **応答 URL の選択** | `https://localhost:44316/` |

新しいブラウザー タブが開き、構成したサインアップまたはサインインのコンシューマー エクスペリエンスを確認できます。

> [!NOTE]
> ポリシーの作成と更新が有効になるまで、最大で 1 分間かかります。
>