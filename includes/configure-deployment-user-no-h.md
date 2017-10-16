Cloud Shell で [az webapp deployment user set](/cli/azure/webapp/deployment/user#set) コマンドを使用してデプロイ資格情報を作成します。

FTP と、Web アプリへのローカル Git のデプロイには、デプロイ ユーザーが必要です。 ユーザー名とパスワードはアカウント レベルです。 "_Azure サブスクリプションの資格情報とは異なります。_"

次のコマンドで、*\<username>* と *\<password>* を新しいユーザー名とパスワードで置き換えます。 ユーザー名は一意である必要があります。 パスワードは長さが 8 文字以上で、文字、数字、記号のうち 2 つを含む必要があります。 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

` 'Conflict'. Details: 409` エラーが発生した場合は、ユーザー名を変更します。 ` 'Bad Request'. Details: 400` エラーが発生した場合は、より強力なパスワードを使用します。

このデプロイ ユーザーの作成は 1 回だけ実行する必要があります。すべての Azure デプロイでこのユーザーを使用できます。

> [!NOTE]
> ユーザー名とパスワードを記録します。 後で Web アプリをデプロイするときに必要になります。
>
>