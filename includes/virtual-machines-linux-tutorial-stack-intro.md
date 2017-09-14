## <a name="create-a-resource-group"></a>リソース グループの作成

[az group create](/cli/azure/group#create) コマンドでリソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 

次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>仮想マシンの作成

[az vm create](/cli/azure/vm#create) コマンドで VM を作成します。 

次の例では、*myVM* という名前の VM を作成し、既定のキーの場所にまだ SSH キーが存在しない場合は SSH キーを作成します。 特定のキーのセットを使用するには、`--ssh-key-value` オプションを使用します。  

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

VM が作成されると、Azure CLI によって次の例のような情報が表示されます。 `publicIpAddress` を書き留めておきます。 このアドレスは、VM へのアクセスに使用されます。

```azurecli-interactive 
{
  "fqdns": "",
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```



## <a name="open-port-80-for-web-traffic"></a>Web トラフィック用にポート 80 を開く 

Azure にデプロイされている Linux VM に対しては、既定で SSH 接続のみが許可されます。 この VM は Web サーバーとして使用することになるため、インターネットからのポート 80 を開放する必要があります。 [az vm open-port](/cli/azure/vm#open-port) コマンドを使用して、目的のポートを開きます。  
 
```azurecli-interactive 
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```
## <a name="ssh-into-your-vm"></a>VM への SSH 接続


VM のパブリック IP アドレスが未確認である場合は、[az network public-ip list](/cli/azure/network/public-ip#list) コマンドを実行してください。


```azurecli-interactive
az network public-ip list --resource-group myResourceGroup --query [].ipAddress
```

次のコマンドを使用して、仮想マシンとの SSH セッションを作成します。 ご使用の仮想マシンの正しいパブリック IP アドレスに置き換えてください。 この例の IP アドレスは *40.68.254.142* です。

```bash
ssh azureuser@40.68.254.142
```

