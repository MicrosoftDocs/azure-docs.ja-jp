[az webapp deployment source config-local-git](/cli/azure/webapp/deployment/source#config-local-git) コマンドを使って、Web アプリにローカルの Git デプロイを構成します。

App Service では、FTP、ローカル Git、GitHub、Visual Studio Team Services、Bitbucket など、Web アプリにコンテンツをデプロイするいくつかの方法がサポートされています。 このクイック スタートでは、ローカル Git を使用してデプロイします。 つまり、Git コマンドを使用してローカル リポジトリから Azure 内のリポジトリにプッシュすることでデプロイします。 

次のコマンドで、 *\<app_name >* をお使いの Web アプリの名前に置き換えます。

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group myResourceGroup --query url --output tsv
```

出力の形式は次のとおりです。

```bash
https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
```

`<username>` は、前の手順で作成した[デプロイ ユーザー](#configure-a-deployment-user)です。

表示された URI をコピーします。これは、次の手順で使用します。
