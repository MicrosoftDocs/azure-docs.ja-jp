Azure CLI を使って API アプリのリモート デプロイの URL を取得します。 次のコマンドで、 *\<app_name >* をお使いの Web アプリの名前に置き換えます。

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group myResourceGroup --query url --output tsv
```

リモートにプッシュできるようにローカル Git のデプロイを構成します。

```bash
git remote add azure <URI from previous step>
```

アプリをデプロイするために、Azure リモートにプッシュします。 前にデプロイ ユーザーを作成したときに作成したパスワードを入力するよう求められます。 Azure Portal へのログインに使用するパスワードではなく、クイック スタートの前の手順で作成したパスワードを入力するようにしてください。

```bash
git push azure master
```
