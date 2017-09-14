アプリケーションでポリシーを編集できるようにするには、プロファイル編集ポリシーを作成する必要があります。 このポリシーは、プロファイル編集中のコンシューマーのエクスペリエンスと、正常に完了したときにアプリケーションが受け取るトークンのコンテンツを記述します。

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

設定のポリシー セクションで **[プロファイルの編集ポリシー]** を選択し、**[+ 追加]** をクリックします。

![[プロファイルの編集ポリシー] を選択して [追加] をクリック](media/active-directory-b2c-create-profile-editing-policy/add-b2c-editing-policy.png)

参照するアプリケーションのポリシーの**名前**を入力します。 たとえば、「 `SiPe`」のように入力します。

**[ID プロバイダー]** を選択し、**[ローカル アカウント サインイン]** チェック ボックスをオンにします。 既に構成されている場合は、ソーシャル ID プロバイダーを選択することもできます。 **[OK]**をクリックします。

![ID プロバイダーとして [ローカル アカウント サインイン] を選択して [OK] をクリック](media/active-directory-b2c-create-profile-editing-policy/add-b2c-editing-identity-providers.png)

**[プロファイルの属性]** を選択します。 コンシューマーがそのプロファイル内で表示したり編集したりすることのできる属性を選択します。 たとえば、**[国/リージョン]**、**[表示名]**、**[郵便番号]** の各チェック ボックスをオンにします。 **[OK]**をクリックします。

![属性を選択して [OK] をクリック](media/active-directory-b2c-create-profile-editing-policy/add-b2c-editing-attributes.png)

**[アプリケーション クレーム]** を選択します。 プロファイル編集エクスペリエンスの成功後にアプリケーションに戻される承認トークンで返される要求を選択します。 たとえば、**[表示名]** および **[郵便番号]** を選択します。

![アプリケーション要求を選択して [OK] をクリック](media/active-directory-b2c-create-profile-editing-policy/add-b2c-editing-application-claims.png)

**[作成]** をクリックして、ポリシーを追加します。 ポリシーが **B2C_1_SiPe** として表示されます。 名前には **B2C_1_** というプレフィックスが追加されます。

**[B2C_1_SiPe]** を選択することによってポリシーを開きます。 テーブルに指定されている設定を確認し、**[今すぐ実行]** をクリックします。

![ポリシーを選択して実行](media/active-directory-b2c-create-profile-editing-policy/run-b2c-editing-policy.png)

| 設定      | 値  |
| ------------ | ------ |
| **アプリケーション** | Contoso B2C アプリ |
| **応答 URL の選択** | `https://localhost:44316/` |

新しいブラウザー タブが開き、構成したプロファイル編集のコンシューマー エクスペリエンスを確認できます。

> [!NOTE]
> ポリシーの作成と更新が有効になるまで、最大で 1 分間かかります。
>