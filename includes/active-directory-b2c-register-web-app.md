[!INCLUDE [active-directory-b2c-portal-add-application](active-directory-b2c-portal-add-application.md)]

Web アプリケーションを登録するには、表に指定された設定を使用してください。

![新しい Web アプリの登録設定の例](./media/active-directory-b2c-register-web-app/b2c-new-app-settings.png)

| 設定      | 値の例  | Description                                        |
| ------------ | ------- | -------------------------------------------------- |
| **名前** | Contoso B2C アプリ | アプリケーションの **[名前]** には、コンシューマーがアプリケーションの機能を把握できるような名前を入力します。 | 
| **Web アプリ/Web API を含める** | あり | Web アプリケーションの場合は **[はい]** を選択します。 |
| **暗黙的フローを許可する** | あり | アプリケーションで [OpenID Connect サインイン](../articles/active-directory-b2c/active-directory-b2c-reference-oidc.md)を使用する場合は、**[はい]** を選択します。 |
| **応答 URL** | `https://localhost:44316` | [応答 URL] は、アプリケーションが要求したトークンを Azure AD B2C が返すエンドポイントです。 [適切な](../articles/active-directory-b2c/active-directory-b2c-app-registration.md#choosing-a-web-app-or-api-reply-url)**応答 URL** を入力します。 この例では、アプリがローカルに存在し、ポート 44316 でリッスンします。 |

**[作成]** をクリックして、アプリケーションを登録します。

新しく登録したアプリケーションが、B2C テナントのアプリケーションの一覧に表示されます。 一覧から Web アプリを選択します。 Web アプリケーションのプロパティ ウィンドウが表示されます。

![Web アプリのプロパティ](./media/active-directory-b2c-register-web-app/b2c-web-app-properties.png)

グローバルに一意となる**アプリケーション クライアント ID** をメモします。 アプリケーションのコードには、この ID を使用します。