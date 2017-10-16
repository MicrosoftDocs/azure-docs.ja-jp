Cloud Shell で [az group create](/cli/azure/group#create) コマンドを使用してリソース グループを作成します。

[!INCLUDE [resource group intro text](resource-group.md)]

次の例では、*myResourceGroup* という名前のリソース グループを "*西ヨーロッパ*" の場所に作成します。

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

通常は、現在地付近の地域にリソース グループおよびリソースを作成します。 Azure Web Apps がサポートされているすべての場所を表示するには、`az appservice list-locations` コマンドを実行します。 