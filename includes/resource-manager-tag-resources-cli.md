リソース グループにタグを追加するには、**azure group set** を使用します。 リソース グループにタグがない場合には、追加するタグを渡します。

```azurecli
azure group set -n tag-demo-group -t Dept=Finance
```

タグは全体として更新されます。 タグが既に存在するリソース グループにタグを追加する場合には、タグをすべて渡します。 

```azurecli
azure group set -n tag-demo-group -t Dept=Finance;Environment=Production;Project=Upgrade
```

リソース グループ内のリソースがタグを継承することはありません。 リソースにタグを追加するには、**azure resource set** を使用します。 タグの追加先となるリソースの種類について、API のバージョン番号を渡します。 API のバージョンを取得する必要がある場合には、設定しようとしているリソースの種類のリソース プロバイダーで以下のコマンドを使用します。

```azurecli
azure provider show -n Microsoft.Storage --json
```

結果で、タグの追加先のリソースの種類を探します。

```azurecli
"resourceTypes": [
{
  "resourceType": "storageAccounts",
  ...
  "apiVersions": [
    "2016-01-01",
    "2015-06-15",
    "2015-05-01-preview"
  ]
}
...
```

以上が終わったら、API のバージョン、リソース グループ名、リソース名、リソースの種類、タグ値をパラメーターとして指定します。

```azurecli
azure resource set -g tag-demo-group -n storagetagdemo -r Microsoft.Storage/storageAccounts -t Dept=Finance -o 2016-01-01
```

タグはリソースやリソース グループ上に直接存在します。 既存のタグを表示するには、 **azure group show** を使用してリソース グループとそのリソースを取得します。

```azurecli
azure group show -n tag-demo-group --json
```

これにより、リソース グループに関するメタデータが、適用されているすべてのタグと共に返されます。

```azurecli
{
  "id": "/subscriptions/4705409c-9372-42f0-914c-64a504530837/resourceGroups/tag-demo-group",
  "name": "tag-demo-group",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "location": "southcentralus",
  "tags": {
    "Dept": "Finance",
    "Environment": "Production",
    "Project": "Upgrade"
  },
  ...
}
```

特定のリソースのタグを表示するには、 **azure resource show**を使用します。

```azurecli
azure resource show -g tag-demo-group -n storagetagdemo -r Microsoft.Storage/storageAccounts -o 2016-01-01 --json
```

タグ値が設定されているすべてのリソースを取得するには、以下を使用します。

```azurecli
azure resource list -t Dept=Finance --json
```

タグ値が設定されているすべてのリソース グループを取得するには、以下を使用します。

```azurecli
azure group list -t Dept=Finance
```

サブスクリプション内の既存のタグを表示するには、以下のコマンドを使用します。

```azurecli
azure tag list
```
