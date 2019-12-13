---
title: PowerShell を使用してブループリント定義のインポートとエクスポートを行う
description: ブループリント定義をコードとして使用する方法について説明します。 export コマンドと import コマンドを使用して、それらの共有、ソース管理、および管理を行います。
ms.date: 09/03/2019
ms.topic: how-to
ms.openlocfilehash: fc7b9818072665d79deaf8a456868943e8428730
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873201"
---
# <a name="import-and-export-blueprint-definitions-with-powershell"></a>PowerShell を使用したブループリント定義のインポートとエクスポート

Azure Blueprints は、Azure portal で完全に管理できます。 組織が Blueprints の使用を進める場合、ブループリント定義をマネージド コードとして考える必要があります。 この概念は、多くの場合、Infrastructure as Code (IaC) と呼ばれます。 ブループリント定義をコードとして扱うことは、Azure portal の機能にはない利点があります。 次のような利点があります。

- ブループリント定義の共有
- ブループリント定義のバックアップ
- 異なるテナントまたはサブスクリプションでのブループリント定義の再利用
- ソース管理へのブループリント定義の配置
  - テスト環境でのブループリント定義の自動テスト
  - 継続的インテグレーションと継続的デプロイ (CI/CD) パイプラインのサポート

理由にかかわらず、ブループリント定義をコードとして管理することには利点があります。 この記事では、[Az.Blueprint](https://powershellgallery.com/packages/Az.Blueprint/) モジュールで `Import-AzBlueprintWithArtifact` および `Export-AzBlueprintWithArtifact` コマンドを使用する方法について説明します。

## <a name="prerequisites"></a>前提条件

この記事では、Azure Blueprints に関する実用的な知識があることを前提としています。 そうでない場合は、次の記事を参照してください。

- [ポータルでブループリントを作成する](../create-blueprint-portal.md)
- [デプロイ ステージ](../concepts/deployment-stages.md)と[ブループリントのライフサイクル](../concepts/lifecycle.md)について確認する
- PowerShell を使用したブループリントの定義と割り当ての[作成](../create-blueprint-powershell.md)と[管理](./manage-assignments-ps.md)

まだインストールされていない場合は、「[Az.Blueprint モジュールを追加する](./manage-assignments-ps.md#add-the-azblueprint-module)」の手順に従って、PowerShell ギャラリーから **Az.Blueprint** モジュールをインストールして検証します。

## <a name="folder-structure-of-a-blueprint-definition"></a>ブループリント定義のフォルダー構造

ブループリントのエクスポートとインポートについて確認する前に、ブループリント定義を構成するファイルがどのような構造であるかを見てみましょう。 ブループリント定義は、専用のフォルダーに格納するようにします。

> [!IMPORTANT]
> `Import-AzBlueprintWithArtifact` コマンドレットの **Name** パラメーターに値が渡されない場合は、ブループリント定義が格納されているフォルダーの名前が使用されます。

ブループリント定義 (`blueprint.json` という名前を付ける必要があります) と共に、ブループリント定義を構成する成果物があります。 各成果物は、必ず `artifacts` という名前のサブフォルダー内に存在します。
まとめると、ブループリント定義の構造は、フォルダー内の JSON ファイルとして、次のようになります。

```text
.
|
|- MyBlueprint/  _______________ # Root folder name becomes default name of blueprint definition
|  |- blueprint.json  __________ # The blueprint definition. Fixed name.
|
|  |- artifacts/  ______________ # Subfolder for all blueprint artifacts. Fixed name.
|     |- artifact.json  ________ # Blueprint artifact as JSON file. Artifact named from file.
|     |- ...
|     |- more-artifacts.json

```

## <a name="export-your-blueprint-definition"></a>ブループリント定義をエクスポートする

ブループリント定義をエクスポートする手順は簡単です。 ブループリント定義をエクスポートすると、共有、バックアップ、またはソース管理への配置に便利な場合があります。

- **Blueprint** [必須]
  - ブループリント定義を指定します
  - `Get-AzBlueprint` を使用して参照オブジェクトを取得します
- **OutputPath** [必須]
  - ブループリント定義の JSON ファイルを保存するパスを指定します
  - 出力ファイルは、ブループリント定義の名前を含むサブフォルダー内にあります
- **Version** (省略可能)
  - **Blueprint** 参照オブジェクトに複数のバージョンへの参照が含まれている場合に出力するバージョンを指定します。

1. `{subId}` のように表されるサブスクリプションからエクスポートするブループリント定義への参照を取得します。

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   # Get version '1.1' of the blueprint definition in the specified subscription
   $bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'MyBlueprint' -Version '1.1'
   ```

1. `Export-AzBlueprintWithArtifact` コマンドレットを使用して、指定したブループリント定義をエクスポートします。

   ```azurepowershell-interactive
   Export-AzBlueprintWithArtifact -Blueprint $bpDefinition -OutputPath 'C:\Blueprints'
   ```

## <a name="import-your-blueprint-definition"></a>ブループリント定義をインポートする

[エクスポートしたブループリント定義](#export-your-blueprint-definition)があるか、[必要なフォルダー構造](#folder-structure-of-a-blueprint-definition)に手動で作成したブループリント定義がある場合は、そのブループリント定義を別の管理グループまたはサブスクリプションにインポートすることができます。

組み込みのブループリント定義の例については、[Azure Blueprint の GitHub リポジトリ](https://github.com/Azure/azure-blueprints/tree/master/samples/builtins)を参照してください。

- **Name** [必須]
  - 新しいブループリント定義の名前を指定します
- **InputPath** [必須]
  - ブループリント定義の作成に使用するパスを指定します
  - [必要なフォルダー構造](#folder-structure-of-a-blueprint-definition)と一致する必要があります
- **ManagementGroupId** (省略可能)
  - 現在のコンテキストが既定値でない場合にブループリント定義を保存する管理グループ ID
  - **ManagementGroupId** または **SubscriptionId** を指定する必要があります
- **SubscriptionId** (省略可能)
  - 現在のコンテキストが既定値でない場合にブループリント定義を保存するサブスクリプション ID
  - **ManagementGroupId** または **SubscriptionId** を指定する必要があります

1. `Import-AzBlueprintWithArtifact` コマンドレットを使用して、指定したブループリント定義をインポートします。

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   Import-AzBlueprintWithArtifact -Name 'MyBlueprint' -ManagementGroupId 'DevMG' -InputPath 'C:\Blueprints\MyBlueprint'
   ```

ブループリント定義をインポートしたら、[PowerShell を使用して割り当てます](./manage-assignments-ps.md#create-blueprint-assignments)。

高度なブループリント定義の作成の詳細については、次の記事を参照してください。

- [静的および動的なパラメーター](../concepts/parameters.md)を使用する。
- [ブループリントの優先順位](../concepts/sequencing-order.md)をカスタマイズする。
- [ブループリント リソースのロック](../concepts/resource-locking.md)を使用してデプロイを保護する。
- [ブループリントをコードとして管理する](https://github.com/Azure/azure-blueprints/blob/master/README.md)。

## <a name="next-steps"></a>次の手順

- [ブループリントのライフサイクル](../concepts/lifecycle.md)を参照する。
- [静的および動的パラメーター](../concepts/parameters.md)の使用方法を理解する。
- [ブループリントの優先順位](../concepts/sequencing-order.md)のカスタマイズを参照する。
- [ブループリントのリソース ロック](../concepts/resource-locking.md)の使用方法を調べる。
- ブループリントの割り当て時の問題を[一般的なトラブルシューティング](../troubleshoot/general.md)で解決する。