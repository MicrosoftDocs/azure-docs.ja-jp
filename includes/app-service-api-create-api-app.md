
[az webapp create](/cli/azure/appservice/web#create) コマンドを使って、`myAppServicePlan` App Service プランにアプリを作成します。 

Web アプリによって API 用のホスト領域が提供され、デプロイされたアプリを表示するための URL が提供されます。

次のコマンドで、 *\<app_name >*を一意の名前に置き換えます。 `<app_name>` が一意でない場合は、"指定された名前 <app_name> の Web サイトは既に存在します" というエラー メッセージが表示されます。 Web アプリの既定の URL は、`https://<app_name>.azurewebsites.net` です。 

```azurecli-interactive
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan
```

Web アプリが作成されると、Azure CLI によって次の例のような情報が表示されます。

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "<app_name>.azurewebsites.net",
    "<app_name>.scm.azurewebsites.net"
  ],
  "gatewaySiteName": null,
  "hostNameSslStates": [
    {
      "hostType": "Standard",
      "name": "<app_name>.azurewebsites.net",
      "sslState": "Disabled",
      "thumbprint": null,
      "toUpdate": null,
      "virtualIp": null
    }
    < JSON data removed for brevity. >
}
```