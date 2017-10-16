1. Azure Portal の左上にある **[新規]** ボタンをクリックします。

1. **[Compute]** > **[Function App]** の順にクリックします。 次に、テーブルに指定されている Function App の設定を使用します。

    ![Azure Portal での Function App の作成](./media/functions-create-function-app-portal/function-app-create-flow.png)

    | 設定      | 推奨値  | 説明                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **アプリ名** | グローバルに一意の名前 | 新しい Function App を識別する名前。 | 
    | **サブスクリプション** | 該当するサブスクリプション | この新しい Function App が作成されるサブスクリプション。 | 
    | **[リソース グループ](../articles/azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Function App を作成するための新しいリソース グループの名前。 | 
    | **[ホスティング プラン](../articles/azure-functions/functions-scale.md)** |   従量課金プラン | Function App にどのようにリソースが割り当てられるかを定義するホスティング プラン。 既定の **[従量課金プラン]** では、リソースは関数の必要に応じて動的に追加されます。 ユーザーは、関数が実行された時間に対してだけ支払います。   |
    | **場所** | 西ヨーロッパ | ユーザーに近い場所、または関数がアクセスする他のサービスの近くの場所を選択します。 |
    | **[ストレージ アカウント](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account)** |  グローバルに一意の名前 |  Function App によって使用される新しいストレージ アカウントの名前。 ストレージ アカウント名の長さは 3 ～ 24 文字で、数字と小文字のみを使用できます。 既存のアカウントを使用することもできます。 |

1. **[作成]** をクリックして、新しい Function App をプロビジョニングし、デプロイします。
