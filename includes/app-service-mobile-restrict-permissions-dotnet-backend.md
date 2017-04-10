
既定では、Mobile Apps バックエンドの API は匿名で呼び出すことができます。 次に、認証されたクライアントのみにアクセスを制限する必要があります。  

* **Node.js バックエンド (Azure Portal 経由)**:  

    Mobile Apps の設定で **[Easy Tables]** をクリックし、目的のテーブルを選択します。 **[アクセス許可の変更]** をクリックし、すべてのアクセス許可に対して **[Authenticated access only (認証済みアクセスのみ)]** を選択し、**[保存]** をクリックします。
* **.NET バックエンド (C#)**:  

    サーバー プロジェクトで、**[コントローラー]** > **[TodoItemController.cs]** の順に移動します。 次のように、 `[Authorize]` 属性を **TodoItemController** クラスに追加します。 アクセスを特定のメソッドのみに制限するには、この属性を、クラスではなく、そのメソッドのみに適用するだけです。 サーバー プロジェクトを発行します。

        [Authorize]
        public class TodoItemController : TableController<TodoItem>

* **Node.js バックエンド (Node.js コード経由)** :  

    テーブルへのアクセスに対して認証を要求するには、Node.js サーバー スクリプトに次の行を追加します。

        table.access = 'authenticated';

    詳細については、「[方法: テーブルへのアクセスに認証を要求する](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-auth)」を参照してください。 自社サイトからクイック スタート コード プロジェクトをダウンロードする方法については、「 [方法: Git を使用して Node.js バックエンド クイック スタート コード プロジェクトをダウンロードする](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart)」を参照してください。


<!--HONumber=Dec16_HO2-->


