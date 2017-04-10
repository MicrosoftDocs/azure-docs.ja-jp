AzureRm.Resources モジュールのバージョン 3.0 には、タグの操作方法に関する大幅な変更が含まれています。 次に進む前にバージョンを確認してください。

```powershell
Get-Module -ListAvailable -Name AzureRm.Resources | Select Version
```

モジュールのバージョンが 3.0 以降であれば、このトピックの例はお使いの環境で動作します。 お使いのバージョンが 3.0 以降でない場合は、このトピックに進む前に PowerShell ギャラリーまたは Web Platform Installer を使用して[バージョンを更新](/powershell/azureps-cmdlets-docs/)してください。

```powershell
Version
-------
3.5.0
```

リソースまたはリソース グループにタグを適用するたびに、そのリソースまたはリソース グループの既存のタグが上書きされます。 したがって、リソースまたはリソース グループに保持する必要がある既存のタグがあるかどうかに基づいて、異なるアプローチを使用する必要があります。 その方法を次に示します。

* タグのないリソース グループにタグを追加する。

  ```powershell
  Set-AzureRmResourceGroup -Name TagTestGroup -Tag @{ Dept="IT"; Environment="Test" }
  ```

* 既存のタグがあるリソース グループにタグを追加する。

  ```powershell
  $tags = (Get-AzureRmResourceGroup -Name TagTestGroup).Tags
  $tags += @{Status="Approved"}
  Set-AzureRmResourceGroup -Tag $tags -Name TagTestGroup
  ```

* タグのないリソースにタグを追加する。

  ```powershell
  Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceName storageexample -ResourceGroupName TagTestGroup -ResourceType Microsoft.Storage/storageAccounts
  ```

* 既存のタグがあるリソースにタグを追加する。

  ```powershell
  $tags = (Get-AzureRmResource -ResourceName storageexample -ResourceGroupName TagTestGroup).Tags
  $tags += @{Status="Approved"}
  Set-AzureRmResource -Tag $tags -ResourceName storageexample -ResourceGroupName TagTestGroup -ResourceType Microsoft.Storage/storageAccounts
  ```

**リソースにある既存のタグを保持せずに**、リソース グループのすべてのタグをリソースに適用するには、次のスクリプトを使用します。

```powershell
$groups = Get-AzureRmResourceGroup
foreach ($g in $groups) 
{
    Find-AzureRmResource -ResourceGroupNameEquals $g.ResourceGroupName | ForEach-Object {Set-AzureRmResource -ResourceId $_.ResourceId -Tag $g.Tags -Force } 
}
```

**リソースにある重複しない既存のタグを保持して**、リソース グループのすべてのタグをリソースに適用するには、次のスクリプトを使用します。

```powershell
$groups = Get-AzureRmResourceGroup
foreach ($g in $groups) 
{
    if ($g.Tags -ne $null) {
        $resources = Find-AzureRmResource -ResourceGroupNameEquals $g.ResourceGroupName 
        foreach ($r in $resources)
        {
            $resourcetags = (Get-AzureRmResource -ResourceId $r.ResourceId).Tags
            foreach ($key in $g.Tags.Keys)
            {
                if ($resourcetags.ContainsKey($key)) { $resourcetags.Remove($key) }
            }
            $resourcetags += $g.Tags
            Set-AzureRmResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
        }
    }
}
```

すべてのタグを削除するには、空のハッシュ テーブルを渡します。

```powershell
Set-AzureRmResourceGroup -Tag @{} -Name TagTestGgroup
```

特定のタグを持つリソース グループを取得するには、`Find-AzureRmResourceGroup` コマンドレットを使用します。

```powershell
(Find-AzureRmResourceGroup -Tag @{ Dept="Finance" }).Name 
```

特定のタグと値を持つすべてのリソースを取得するには、`Find-AzureRmResource` コマンドレットを使用します。

```powershell
(Find-AzureRmResource -TagName Dept -TagValue Finance).Name
```

