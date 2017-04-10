デプロイ中にリソースにタグを付けるには、デプロイするリソースに `tags` 要素を追加します。 タグの名前と値を指定します。

### <a name="apply-literal-value-to-tag-name"></a>タグ名へのリテラル値の適用
次の例は、2 つのタグ (`Dept` と `Environment`) をリテラル値に設定するストレージ アカウントを示しています。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat('storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```

### <a name="apply-object-to-tag-element"></a>タグ要素へのオブジェクトの適用
複数のタグを格納するオブジェクト パラメーターを定義し、そのオブジェクトをタグ要素に適用できます。 オブジェクト内の各プロパティがリソースの個々のタグになります。 次の例は、タグ要素に適用される `tagValues` という名前のパラメーターを示しています。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "tagValues": {
      "type": "object",
      "defaultValue": {
        "Dept": "Finance",
        "Environment": "Production"
      }
    }
  },
  "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat('storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "tags": "[parameters('tagValues')]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {}
    }
  ]
}
```

### <a name="apply-json-string-to-tag-name"></a>タグ名への JSON 文字列の適用

1 つのタグに複数の値を格納するには、値を表す JSON 文字列を適用します。 JSON 文字列全体は、1 つのタグとして格納されます。256 文字以下にする必要があります。 次の例は、JSON 文字列で複数の値を格納する `CostCenter` という名前の&1; つのタグを示しています。  

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat('storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "tags": {
        "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```

<!--HONumber=Feb17_HO1-->


