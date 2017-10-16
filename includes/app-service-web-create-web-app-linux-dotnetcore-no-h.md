Cloud Shell で [az webapp create](/cli/azure/webapp#create) コマンドを使って、`myAppServicePlan` App Service プランに [Web アプリ](../articles/app-service/containers/app-service-linux-intro.md)を作成します。 `<app name>` を固有のアプリ名に置き換えることを忘れないでください。

次のコマンドのランタイムは、`dotnetcore|1.1` に設定されます。 サポートされているすべてのランタイムを確認するには、[az webapp list-runtimes](/cli/azure/webapp#list-runtimes) を実行してください。 

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --runtime "dotnetcore|1.1" --deployment-local-git
```

Web アプリが作成されると、Azure CLI によって次の例のような出力が表示されます。

```json
Local git is configured with url of 'https://<username>@<app name>.scm.azurewebsites.net/<app name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app name>.scm.azurewebsites.net/<app name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

Git デプロイを有効にして、空の新しい Web アプリを Linux コンテナーに作成しました。

> [!NOTE]
> Git リモートの URL は `deploymentLocalGitUrl` プロパティに `https://<username>@<app name>.scm.azurewebsites.net/<app name>.git` 形式で出力されます。 この URL は後で必要になるので保存しておいてください。
>
