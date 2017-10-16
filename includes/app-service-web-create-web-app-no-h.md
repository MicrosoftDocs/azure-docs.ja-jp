Cloud Shell で [az webapp create](/cli/azure/webapp#create) コマンドを使って、`myAppServicePlan` App Service プランに [Web アプリ](../articles/app-service/app-service-web-overview.md)を作成します。 

Web アプリによって目的のコード用のホスト領域が提供され、デプロイされたアプリを表示するための URL が提供されます。

次のコマンドで、*\<app_name>* を一意の名前 (有効な文字は `a-z`、`0-9`、および `-`) に置き換えます。 `<app_name>` が一意でない場合は、"指定された名前 <app_name> の Web サイトは既に存在します" というエラー メッセージが表示されます。 Web アプリの既定の URL は、`https://<app_name>.azurewebsites.net` です。 

```azurecli-interactive
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan --deployment-local-git
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

サイトを参照して、新たに作成された Web アプリを表示します。

```bash
http://<app_name>.azurewebsites.net
```
