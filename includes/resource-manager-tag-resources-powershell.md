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

*リソース グループ*の既存のタグを表示するには、次のコマンドを使用します。

```powershell
(Get-AzureRmResourceGroup -Name examplegroup).Tags
```

このスクリプトは次の形式を返します。

```powershell
Name                           Value
----                           -----
Dept                           IT
Environment                    Test
```

*特定のリソース ID に該当するリソース*の既存のタグを表示するには、次のコマンドを使用します。

```powershell
(Get-AzureRmResource -ResourceId {resource-id}).Tags
```

*特定の名前とリソース グループに該当するリソース*の既存のタグを表示するには、次のコマンドを使用します。

```powershell
(Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup).Tags
```

*特定のタグが付いたリソース グループ*を取得するには、次のコマンドを使用します。

```powershell
(Find-AzureRmResourceGroup -Tag @{ Dept="Finance" }).Name 
```

*特定のタグが付いたリソース*を取得するには、次のコマンドを使用します。

```powershell
(Find-AzureRmResource -TagName Dept -TagValue Finance).Name
```

リソースまたはリソース グループにタグを適用するたびに、そのリソースまたはリソース グループの既存のタグが上書きされます。 したがって、リソースまたはリソース グループに既存のタグがあるかどうかに基づいて、異なるアプローチを使用する必要があります。 

*既存のタグのないリソース グループ*にタグを追加するには、次のコマンドを使用します。

```powershell
Set-AzureRmResourceGroup -Name examplegroup -Tag @{ Dept="IT"; Environment="Test" }
```

*既存のタグがあるリソース グループ*にタグを追加するには、既存のタグを取得して新しいタグを追加し、タグを適用し直します。

```powershell
$tags = (Get-AzureRmResourceGroup -Name examplegroup).Tags
$tags += @{Status="Approved"}
Set-AzureRmResourceGroup -Tag $tags -Name examplegroup
```

*既存のタグのないリソース*にタグを追加するには、次のコマンドを使用します。

```powershell
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceName examplevnet -ResourceGroupName examplegroup
```

*既存のタグがあるリソース*にタグを追加するには、次のコマンドを使用します。

```powershell
$tags = (Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup).Tags
$tags += @{Status="Approved"}
Set-AzureRmResource -Tag $tags -ResourceName examplevnet -ResourceGroupName examplegroup
```

*リソースにある既存のタグを保持せずに*、リソース グループのすべてのタグをリソースに適用するには、次のスクリプトを使用します。

```powershell
$groups = Get-AzureRmResourceGroup
foreach ($g in $groups) 
{
    Find-AzureRmResource -ResourceGroupNameEquals $g.ResourceGroupName | ForEach-Object {Set-AzureRmResource -ResourceId $_.ResourceId -Tag $g.Tags -Force } 
}
```

*リソースにある重複しない既存のタグを保持して*、リソース グループのすべてのタグをリソースに適用するには、次のスクリプトを使用します。

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
Set-AzureRmResourceGroup -Tag @{} -Name examplegroup
```



