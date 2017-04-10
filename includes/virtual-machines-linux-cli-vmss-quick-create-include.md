## <a name="prerequisites"></a>前提条件

まだない場合は、[Azure サブスクリプションの無料試用版](https://azure.microsoft.com/pricing/free-trial/)を入手し、[Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2) をインストールします。

## <a name="create-the-scale-set"></a>スケール セットを作成する

まず、スケール セットをデプロイするリソース グループを作成します。

```azurecli
az group create --location westus --name myResourceGroup
```

次に、`az vmss create` コマンドを使ってスケール セットを作成します。 次の例では、`myrg` という名前のリソース グループに、`myvmss` という名前の Linux スケール セットを作成します。

```azurecli
az vmss create --resource-group myResourceGroup --name myVmss \
    --image UbuntuLTS --admin-username azureuser \
    --authentication-type password --admin-password P4$$w0rd
```

次の例では、同じ構成で Windows スケール セットを作成します。

```azurecli
az vmss create --resource-group myResourceGroup --name myVmss \
    --image Win2016Datacenter --admin-username azureuser \
    --authentication-type password --admin-password P4$$w0rd
```

別の OS イメージを選択する場合は、`az vm image list` コマンドまたは `az vm image list --all` コマンドを使用することで利用できるイメージを表示できます。 スケール セットの VM の接続情報を表示するには、`az vmss list_instance_connection_info` コマンドを使用します。

```azurecli
az vmss list_instance_connection_info --resource-group myResourceGroup --name myVmss
```