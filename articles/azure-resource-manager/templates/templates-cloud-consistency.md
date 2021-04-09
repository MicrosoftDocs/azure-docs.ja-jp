---
title: クラウド間でテンプレートを再利用する
description: 異なるクラウド環境で一貫して動作する Azure Resource Manager テンプレート (ARM テンプレート) を開発します。 Azure Stack 用のテンプレートを新規作成するか、既存のテンプレートを更新します。
author: marcvaneijk
ms.topic: conceptual
ms.date: 12/09/2018
ms.author: mavane
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 120e98ebebec1436c9171f6076baccd35be9b843
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104888823"
---
# <a name="develop-arm-templates-for-cloud-consistency"></a>クラウドの一貫性を実現する ARM テンプレートを開発する

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

Azure の主な利点は一貫性です。 ある場所に対する開発投資を、別の場所で再利用できます。 Azure Resource Manager テンプレート (ARM テンプレート) を使用すると、グローバル Azure、Azure ソブリン クラウド、Azure Stack などの環境にわたってデプロイが一貫性を持ち、再現可能となります。 ただし、異なるクラウドでテンプレートを再利用するには、このガイドで説明するように、クラウド固有の依存関係を考慮する必要があります。

Microsoft は、次のように、インテリジェントでエンタープライズ対応のクラウド サービスをさまざまな場所で提供しています。

* Microsoft が管理するデータセンターのネットワークを世界中のリージョンに拡大することでサポートされるグローバル Azure プラットフォーム。
* Azure Germany、Azure Government、Azure China 21Vianet など、分離されたソブリン クラウド。 ソブリン クラウドは、グローバル Azure ユーザーがアクセスできるものと同じ優れた機能の大部分と一貫性があるプラットフォームを提供します。
* ユーザーが組織のデータセンターから Azure サービスを提供できるようにするハイブリッド クラウド プラットフォームである Azure Stack。 企業は、自社のデータセンターに Azure Stack を設定することも、(ホストされたリージョンとも呼ばれる) 自社の施設で Azure Stack を運用するサービス プロバイダーから Azure サービスを利用することもできます。

すべてのクラウドの中核では、Azure Resource Manager が提供する API により、さまざまなユーザー インターフェイスが Azure プラットフォームと通信できます。 この API は、コードとしてのインフラストラクチャの強力な機能を提供します。 Azure Resource Manager では、Azure クラウド プラットフォームで利用可能なすべての種類のリソースを展開して構成できます。 1 つのテンプレートを使って、完全なアプリケーションを展開して構成し、最終的な運用状態にすることができます。

![Azure 環境](./media/templates-cloud-consistency/environments.png)

グローバル Azure、ソブリン クラウド、ホストされたクラウド、ユーザーのデータセンター内のクラウドが一貫していると、Azure Resource Manager からのメリットを得られやすくなります。 テンプレート ベースのリソースの展開と構成を設定するときに、これらのクラウド間で開発投資を再利用することができます。

ただし、グローバル クラウド、ソブリン クラウド、ホストされたクラウド、ハイブリッド クラウドが一貫性のあるサービスを提供する場合でも、すべてのクラウドが同じであるわけではありません。 そのため、特定のクラウドでのみ使用できる機能に対する依存関係を持つテンプレートを作成できます。

このガイドの残りの部分では、Azure Stack 用の ARM テンプレートの新規開発または既存更新を計画するときに考慮すべき事柄について説明します。 一般に、チェック リストには次のものを含める必要があります。

* テンプレートの関数、エンドポイント、サービス、その他のリソースが展開先の場所で利用できることを確認します。
* 入れ子になったテンプレートと構成成果物を、異なるクラウドからアクセス可能な場所に格納します。
* リンクと要素をハード コーディングするのではなく動的参照を使用します。
* 使用するテンプレート パラメーターが対象のクラウドで動作することを確認します。
* リソース固有のプロパティが対象のクラウドで使用できることを確認します。

ARM テンプレートの概要については、[テンプレートのデプロイ](overview.md)に関するページを参照してください。

## <a name="ensure-template-functions-work"></a>テンプレート関数を確実に動作させる

ARM テンプレートの基本的な構文は JSON です。 テンプレートは JSON のスーパーセットを使用しており、式と関数について構文が拡張されています。 テンプレートの言語プロセッサは、追加されたテンプレート関数をサポートするために頻繁に更新されます。 使用可能なテンプレート関数の詳細については、「[ARM テンプレート関数](template-functions.md)」を参照してください。

ソブリン クラウドまたは Azure Stack では、Azure Resource Manager に導入された新しいテンプレート関数をすぐに利用することはできません。 テンプレートを正常に展開するには、テンプレートで参照されているすべての関数を展開先のクラウドで使用できる必要があります。

Azure Resource Manager の機能は、常にグローバル Azure に最初に導入されます。 次の PowerShell スクリプトを使用すると、新しく導入されたテンプレート関数を Azure Stack でも使用できるかどうかを確認できます。

1. GitHub リポジトリ [https://github.com/marcvaneijk/arm-template-functions](https://github.com/marcvaneijk/arm-template-functions) の複製を作成します。

1. リポジトリのローカルな複製を作成したら、PowerShell で対象の Azure Resource Manager に接続します。

1. psm1 モジュールをインポートし、Test-AzureRmTemplateFunctions コマンドレットを実行します。

   ```powershell
   # Import the module
   Import-module <path to local clone>\AzTemplateFunctions.psm1

   # Execute the Test-AzureRmTemplateFunctions cmdlet
   Test-AzureRmTemplateFunctions -path <path to local clone>
   ```

このスクリプトでは、それぞれが固有のテンプレート関数のみを含む、複数の最小化されたテンプレートが展開されます。 スクリプトの出力では、サポートされているテンプレート関数と、使用できないテンプレート関数が報告されます。

## <a name="working-with-linked-artifacts"></a>リンクされた成果物の操作

テンプレートは、リンクされた成果物への参照、および別のテンプレートにリンクしている展開リソースを含むことができます。 リンクされたテンプレート (入れ子になったテンプレートとも呼ばれます) は、Resource Manager によって実行時に取得されます。 テンプレートは、仮想マシン (VM) 拡張機能に対する成果物への参照も含むことができます。 これらの成果物は、テンプレートの展開中に VM 拡張機能を構成するため、VM 内で実行している VM 拡張機能によって取得されます。

次のセクションでは、メインのデプロイ テンプレートの外部にある成果物を含むテンプレートを開発するときのクラウドの一貫性に関する考慮事項について説明します。

### <a name="use-nested-templates-across-regions"></a>入れ子になったテンプレートをリージョン間で使用する

テンプレートは、それぞれが特定の目的を持ち、異なる展開シナリオで再利用できる、小さい再利用可能なテンプレートに分解することができます。 展開を実行するには、メイン テンプレートまたはマスター テンプレートと呼ばれる 1 つのテンプレートを指定します。 そこでは、仮想ネットワーク、VM、Web アプリなど、展開するリソースが指定されています。 また、メイン テンプレートは別のテンプレートへのリンクを含むこともでき、これはテンプレートを入れ子にできることを意味します。 同様に、入れ子になったテンプレートも、他のテンプレートへのリンクを含むことができます。 最大 5 レベルの深さまで入れ子にできます。

次のコードでは、templateLink パラメーターが入れ子になったテンプレートを参照する方法を示します。

```json
"resources": [
  {
     "type": "Microsoft.Resources/deployments",
     "apiVersion": "2020-10-01",
     "name": "linkedTemplate",
     "properties": {
       "mode": "incremental",
       "templateLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/vNet.json",
          "contentVersion":"1.0.0.0"
       }
     }
  }
]
```

Azure Resource Manager は、実行時にメイン テンプレートを評価し、入れ子になった各テンプレートを取得して評価します。 入れ子になったすべてのテンプレートが取得された後、テンプレートはフラット化されて、後続の処理が開始されます。

### <a name="make-linked-templates-accessible-across-clouds"></a>リンクされたテンプレートをクラウド間でアクセスできるようにする

使用するリンクされたテンプレートを格納する場所と方法を検討します。 実行時に、Azure Resource Manager はリンクされたテンプレートをすべてフェッチするので、リンクされたテンプレートに直接アクセスする必要があります。 一般的な方法としては、GitHub を使用して入れ子になったテンプレートを格納します。 GitHub リポジトリは、URL を使ってパブリックにアクセスできるファイルを含むことができます。 パブリック クラウドとソブリン クラウドの場合はこの方法で問題ありませんが、Azure Stack 環境は、企業ネットワーク上または切断されたリモートの場所に配置されていて、インターネットに送信アクセスできないことがあります。 このような場合、Azure Resource Manager は入れ子になったテンプレートを取得できません。

クロスクラウド デプロイでのよりよい方法は、ターゲット クラウドがアクセスできる場所にリンクされたテンプレートを格納することです。 理想的には、すべての展開成果物を、継続的インテグレーション/継続的デリバリー (CI/CD) パイプラインで保持し、そこから展開します。 または、入れ子になったテンプレートを BLOB ストレージ コンテナーに格納することもでき、Azure Resource Manager はそこからテンプレートを取得できます。

各クラウド上の BLOB ストレージは異なるエンドポイントの完全修飾ドメイン名 (FQDN) を使用するので、リンクされたテンプレートの場所を 2 つのパラメーターで指定してテンプレートを構成します。 パラメーターは、展開時にユーザー入力を受け取ることができます。 通常、テンプレートは複数のユーザーによって作成されて共有されるため、ベスト プラクティスはこれらのパラメーターに標準的な名前を使うことです。 名前付け規則を設けると、異なるリージョン、クラウド、作成者の間でテンプレートをいっそう再利用しやすくなります。

次のコードで、`_artifactsLocation` はすべての展開関連アイテムを含む 1 つの場所を示すために使用されています。 既定値が提供されていることに注意してください。 展開時に `_artifactsLocation` の入力値が定されていない場合は、既定値が使用されます。 `sasToken` に対する入力としては `_artifactsLocationSasToken` が使用されます。 パブリック GitHub リポジトリのような `_artifactsLocation` がセキュリティで保護されていないシナリオでは、既定値を空の文字列にする必要があります。

```json
"parameters": {
  "_artifactsLocation": {
    "type": "string",
    "metadata": {
      "description": "The base URI where artifacts required by this template are located."
    },
    "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-custom-script-windows/"
  },
  "_artifactsLocationSasToken": {
    "type": "securestring",
    "metadata": {
      "description": "The sasToken required to access _artifactsLocation."
    },
    "defaultValue": ""
  }
}
```

テンプレート全体で、リンクは成果物の相対パスのベース URI (`_artifactsLocation` パラメーターから) と `_artifactsLocationSasToken` を組み合わせることで生成されます。 次のコードでは、uri テンプレート関数を使って入れ子になったテンプレートへのリンクを指定する方法を示します。

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2020-10-01",
    "name": "shared",
    "properties": {
      "mode": "Incremental",
      "templateLink": {
        "uri": "[uri(parameters('_artifactsLocation'), concat('nested/vnet.json', parameters('_artifactsLocationSasToken')))]",
        "contentVersion": "1.0.0.0"
      }
    }
  }
]
```

この方法を使用することにより、`_artifactsLocation` パラメーターの既定値が使用されます。 リンクされたテンプレートを別の場所から取得する必要がある場合は、展開時にパラメーター入力を使用して、既定値をオーバーライドできます。テンプレート自体を変更する必要はありません。

### <a name="use-_artifactslocation-instead-of-hardcoding-links"></a>リンクをハードコーディングするのではなく _artifactsLocation を使用する

入れ子になったテンプレートに使われるだけでなく、`_artifactsLocation` パラメーターの URL は、展開テンプレートの関連するすべての成果物のベースとしても使われます。 一部の VM 拡張機能には、テンプレートの外部に格納されているスクリプトへのリンクが含まれます。 これらの拡張機能では、リンクをハードコーディングしないようにする必要があります。 たとえば、カスタム スクリプトと PowerShell DSC 拡張機能では、次に示すように、GitHub 上の外部スクリプトにリンクすることがあります。

```json
"properties": {
  "publisher": "Microsoft.Compute",
  "type": "CustomScriptExtension",
  "typeHandlerVersion": "1.9",
  "autoUpgradeMinorVersion": true,
  "settings": {
    "fileUris": [
      "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
    ]
  }
}
```

スクリプトへのリンクをハードコーディングすると、テンプレートを別の場所に正常に展開できない可能性があります。 VM リソースの構成時に、VM 内で実行されている VM エージェントは、VM 拡張機能でリンクされているすべてのスクリプトのダウンロードを開始し、VM のローカル ディスクにスクリプトを格納します。 このアプローチは、前の「入れ子になったテンプレートをリージョン間で使用する」セクションで説明した入れ子になったテンプレートのリンクと同じように機能します。

Resource Manager は入れ子になったテンプレートを実行時に取得します。 VM 拡張機能の場合、外部の成果物の取得は VM エージェントによって実行されます。 成果物の取得を開始するものを除くと、テンプレートの定義でのソリューションは同じです。 (VM 拡張機能のスクリプトを含む) すべての成果物が格納される基本パスの既定値を含む _artifactsLocation パラメーターと、sasToken の入力に対する `_artifactsLocationSasToken` パラメーターを使用します。

```json
"parameters": {
  "_artifactsLocation": {
    "type": "string",
    "metadata": {
      "description": "The base URI where artifacts required by this template are located."
    },
    "defaultValue": "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/"
  },
  "_artifactsLocationSasToken": {
    "type": "securestring",
    "metadata": {
      "description": "The sasToken required to access _artifactsLocation."
    },
    "defaultValue": ""
  }
}
```

成果物の絶対 URI を作成する推奨される方法は、concat テンプレート関数ではなく、uri テンプレート関数を使用することです。 VM 拡張機能内のスクリプトへのハードコーディングされたリンクを、uri テンプレート関数に置き換えることで、テンプレートのこの機能は、クラウドの一貫性を維持するように構成されます。

```json
"properties": {
  "publisher": "Microsoft.Compute",
  "type": "CustomScriptExtension",
  "typeHandlerVersion": "1.9",
  "autoUpgradeMinorVersion": true,
  "settings": {
    "fileUris": [
      "[uri(parameters('_artifactsLocation'), concat('scripts/configure-music-app.ps1', parameters('_artifactsLocationSasToken')))]"
    ]
  }
}
```

このアプローチでは、構成スクリプトを含むすべての展開成果物を、テンプレート自体と同じ場所に格納できます。 すべてのリンクの場所を変更する場合は、_artifactsLocation_ パラメーターに異なるベース URL を指定するだけで済みます。

## <a name="factor-in-differing-regional-capabilities"></a>異なるリージョン機能での要因

Azure に導入される更新されたサービスと新規サービスのアジャイル開発および継続的なフローにより、使用できるサービスまたは更新プログラムは[リージョンによって異なる](https://azure.microsoft.com/regions/services/)場合があります。 厳格な内部テストの後、新しいサービスや既存サービスの更新プログラムは、通常、検証プログラムに参加している小規模な対象ユーザーに導入されます。 顧客による検証で問題がなければ、サービスや更新プログラムは、Azure リージョンのサブセットで使用できるようにされた後、より多くのリージョンに導入され、ソブリン クラウドにロールアウトされて、Azure Stack の顧客も使用できるようになる可能性があります。

Azure のリージョンやクラウドによって使用できるサービスが異なることを理解していれば、テンプレートに関していくつかのことを事前に決定できます。 手始めとしては、クラウドで使用可能なリソース プロバイダーを確認するのが適切です。 リソース プロバイダーにより、Azure サービスで利用可能な一連のリソースと操作がわかります。

テンプレートは、リソースを展開して構成します。 リソースの種類は、リソース プロバイダーによって提供されます。 たとえば、コンピューティング リソース プロバイダー (Microsoft.Compute) は、virtualMachines や availabilitySets などの複数のリソースの種類を提供します。 各リソース プロバイダーは、共通のコントラクトによって定義されている API を Azure Resource Manager に提供し、すべてのリソース プロバイダーで一貫性があり統合されたオーサリング エクスペリエンスを可能にします。 ただし、グローバル Azure で利用可能なリソース プロバイダーは、ソブリン クラウドまたは Azure Stack リージョンでは利用できないことがあります。

![リソース プロバイダー](./media/templates-cloud-consistency/resource-providers.png)

特定のクラウドで使用可能なリソース プロバイダーを確認するには、Azure コマンド ライン インターフェイス ([CLI](/cli/azure/install-azure-cli)) で次のスクリプトを実行します。

```azurecli-interactive
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

また、次の PowerShell コマンドレットを使って、使用可能なリソース プロバイダーを確認することもできます。

```azurepowershell-interactive
Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

### <a name="verify-the-version-of-all-resource-types"></a>すべてのリソースの種類のバージョンを確認する

プロパティのセットはすべてのリソースの種類で共通ですが、各リソースには固有のプロパティもあります。 新しい API バージョンにより、新機能および関連するプロパティが既存のリソースの種類に追加されることがあります。 テンプレート内のリソースには、独自の API バージョン プロパティ `apiVersion` があります。 このバージョン管理により、テンプレート内の既存のリソース構成が、プラットフォームでの変更によって影響を受けないことが保証されます。

グローバル Azure で既存のリソースの種類に導入された新しい API バージョンは、すべてのリージョン、ソブリン クラウド、または Azure Stack ですぐに使用できるようにならないことがあります。 クラウドで使用可能なリソース プロバイダー、リソースの種類、および API バージョンの一覧を見るには、Azure portal でリソース エクスプローラーを使用します。 [すべてのサービス] メニューでリソース エクスプローラーを検索します。 リソース エクスプローラーで [プロバイダー] ノードを展開すると、そのクラウドで利用可能なすべてのリソース プロバイダー、リソースの種類、および API バージョンが表示されます。

Azure CLI で特定のクラウドのすべてのリソースの種類で利用可能な API バージョンを一覧表示するには、次のスクリプトを実行します。

```azurecli-interactive
az provider list --query "[].{namespace:namespace, resourceType:resourceType[]}"
```

次の PowerShell コマンドレットを使うこともできます。

```azurepowershell-interactive
Get-AzureRmResourceProvider | select-object ProviderNamespace -ExpandProperty ResourceTypes | ft ProviderNamespace, ResourceTypeName, ApiVersions
```

### <a name="refer-to-resource-locations-with-a-parameter"></a>パラメーターでリソースの場所を参照する

テンプレートは、常に、リージョン内に存在するリソース グループに展開されます。 展開自体とは別に、テンプレート内の各リソースにも、展開するリージョンを指定するために使用する場所プロパティがあります。 各 Azure Stack は一意の場所の名前を含むことができるため、クラウドの一貫性のためのテンプレートを開発するには、リソースの場所を動的に参照する方法が必要です。 通常、リソースはリソース グループと同じリージョンに展開されますが、クロスリージョン アプリケーションの可用性などのシナリオをサポートするには、リージョン間にリソースを分散すると便利な場合があります。

テンプレートでリソース プロパティを指定するときはリージョン名をハードコーディングできますが、このアプローチでは、他の Azure Stack 環境にテンプレートを展開できる保証はありません。なぜなら、他の Azure Stack 環境にはリージョン名がほぼ間違いなく存在しないためです。

異なるリージョンに対応するには、既定値を持つ入力パラメーター location をテンプレートに追加します。 展開時に値が指定されない場合は、既定値が使われます。

テンプレート関数 `[resourceGroup()]` は、次のキー/値ペアを含むオブジェクトを返します。

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

入力パラメーターの defaultValue 内のオブジェクトの location キーを参照することにより、Azure Resource Manager は、実行時に、`[resourceGroup().location]` テンプレート関数をテンプレート展開先リソース グループの場所の名前に置き換えます。

```json
"parameters": {
  "location": {
    "type": "string",
    "metadata": {
      "description": "Location the resources will be deployed to."
    },
    "defaultValue": "[resourceGroup().location]"
  }
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2015-06-15",
    "name": "storageaccount1",
    "location": "[parameters('location')]",
    ...
```

このテンプレート関数を使うと、リージョン名が事前にわからなくても、クラウドにテンプレートを展開できます。 さらに、テンプレート内の特定のリソースの場所は、リソース グループの場所と異なっていてもかまいません。 この場合、その特定のリソースに対する追加入力パラメーターを使うことによってそれを構成でき、同じテンプレート内の他のリソースは最初の場所入力パラメーターを引き続き使います。

### <a name="track-versions-using-api-profiles"></a>API プロファイルを使用してバージョンを追跡する

Azure Stack に存在するすべての利用可能なリソース プロバイダーと関連 API バージョンを追跡するのは非常に困難なことがあります。 たとえば、記述時に Azure の **Microsoft.Compute/availabilitySets** の最新 API バージョンが `2018-04-01` であっても、Azure と Azure Stack で共通に利用可能な API バージョンは `2016-03-30` です。 Azure と Azure Stack のすべての場所で共有される **Microsoft.Storage/storageAccounts** の共通 API バージョンは `2016-01-01` ですが、Azure での最新の API バージョンは `2018-02-01` です。

このため、Resource Manager ではテンプレートに API プロファイルの概念が導入されました。 API プロファイルがない場合、テンプレート内の各リソースは、その特定のリソースの API バージョンが記述されている `apiVersion` 要素で構成されます。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "metadata": {
          "description": "Location the resources will be deployed to."
      },
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-01-01",
      "name": "mystorageaccount",
      "location": "[parameters('location')]",
      "properties": {
        "accountType": "Standard_LRS"
      }
    },
    {
      "type": "Microsoft.Compute/availabilitySets",
      "apiVersion": "2016-03-30",
      "name": "myavailabilityset",
      "location": "[parameters('location')]",
      "properties": {
        "platformFaultDomainCount": 2,
        "platformUpdateDomainCount": 2
      }
    }
  ],
  "outputs": {}
}
```

API プロファイルのバージョンは、Azure と Azure Stack に共通するリソースの種類ごとの 1 つの API バージョンのエイリアスとして機能します。 テンプレート内のリソースごとに API バージョンを指定する代わりに、`apiProfile` と呼ばれる新しいルート要素で API プロファイルのバージョンのみを指定し、個々のリソースの `apiVersion` 要素は省略します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "apiProfile": "2018–03-01-hybrid",
    "parameters": {
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location the resources will be deployed to."
            },
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "mystorageaccount",
            "location": "[parameters('location')]",
            "properties": {
                "accountType": "Standard_LRS"
            }
        },
        {
            "type": "Microsoft.Compute/availabilitySets",
            "name": "myavailabilityset",
            "location": "[parameters('location')]",
            "properties": {
                "platformFaultDomainCount": 2,
                "platformUpdateDomainCount": 2
            }
        }
    ],
    "outputs": {}
}
```

API プロファイルにより、異なる場所で API バージョンを使用できることが保証されるので、特定の場所で使用可能な apiVersion を手動で確認する必要はありません。 API プロファイルによって参照されている API バージョンが Azure Stack 環境に存在することを保証するため、Azure Stack オペレーターはサポートのポリシーに基づいてソリューションを最新の状態に保つ必要があります。 システムが 6 か月以上期限が切れている場合、準拠していないものと見なされ、環境を更新する必要があります。

API プロファイルは、テンプレートの必須要素ではありません。 要素を追加した場合でも、`apiVersion` が指定されていないリソースにのみ使われます。 この要素は段階的な変更に対応しますが、既存のテンプレートを変更する必要はありません。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "apiProfile": "2018–03-01-hybrid",
    "parameters": {
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location the resources will be deployed to."
            },
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2016-01-01",
            "name": "mystorageaccount",
            "location": "[parameters('location')]",
            "properties": {
                "accountType": "Standard_LRS"
            }
        },
        {
            "type": "Microsoft.Compute/availabilitySets",
            "name": "myavailabilityset",
            "location": "[parameters('location')]",
            "properties": {
                "platformFaultDomainCount": 2,
                "platformUpdateDomainCount": 2
            }
        }
    ],
    "outputs": {}
}
```

## <a name="check-endpoint-references"></a>エンドポイント参照を確認する

リソースは、プラットフォーム上の他のサービスへの参照を持つことができます。 たとえば、パブリック IP アドレスには、パブリック DNS 名を割り当てることができます。 パブリック クラウド、ソブリン クラウド、および Azure Stack ソリューションは、独自に個別のエンドポイント名前空間を持っています。 ほとんどの場合、リソースはテンプレートでの入力としてプレフィックスのみを必要とします。 実行時に、Azure Resource Manager はそれにエンドポイントの値を追加します。 いくつかのエンドポイントの値を、テンプレートで明示的に指定する必要があります。

> [!NOTE]
> クラウドの一貫性のためのテンプレートを開発するには、エンドポイントの名前空間をハードコーディングしないでください。

次の 2 つの例は、リソースを作成するときに明示的に指定する必要がある共通のエンドポイント名前空間です。

* ストレージ アカウント (BLOB、キュー、テーブル、ファイル)
* データベースおよび Azure Cache for Redis の接続文字列

エンドポイントの名前空間は、展開完了時にユーザーへの情報としてテンプレートの出力で使うこともできます。 一般的な例を次に示します。

* ストレージ アカウント (BLOB、キュー、テーブル、ファイル)
* 接続文字列 (MySql、SQLServer、SQLAzure、Custom、NotificationHub、ServiceBus、EventHub、ApiHub、DocDb、RedisCache、PostgreSQL)
* Traffic Manager
* パブリック IP アドレスの domainNameLabel
* クラウド サービス

一般に、テンプレートにエンドポイントをハードコーディングすることは避けます。 ベスト プラクティスは、参照テンプレート関数を使ってエンドポイントを動的に取得することです。 たとえば、最もよくハードコーディングされるエンドポイントは、ストレージ アカウントのエンドポイント名前空間です。 各ストレージ アカウントは、ストレージ アカウントの名前とエンドポイントの名前空間を連結して作成される一意の FQDN を持っています。 mystorageaccount1 という名前の BLOB ストレージ アカウントは、クラウドによって異なる FQDN になります。

* `mystorageaccount1.blob.core.windows.net`: グローバル Azure クラウドで作成されたとき。
* `mystorageaccount1.blob.core.chinacloudapi.cn`: Azure China 21Vianet クラウドで作成されたとき。

次の参照テンプレート関数は、ストレージ リソース プロバイダーからエンドポイントの名前空間を取得します。

```json
"diskUri":"[concat(reference(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))).primaryEndpoints.blob, 'container/myosdisk.vhd')]"
```

ハードコーディングされたストレージ アカウント エンドポイントの値を `reference` テンプレート関数に置き換えることで、エンドポイント参照を変更することなく、同じテンプレートを使って、異なる環境に正常に展開できます。

### <a name="refer-to-existing-resources-by-unique-id"></a>一意の ID で既存のリソースを参照する

同じクラウド内の同じテナント内の同じサブスクリプション内または別のサブスクリプション内の同じリソース グループまたは別のリソース グループから既存のリソースを参照することもできます。 リソースのプロパティを取得するには、リソース自体の一意識別子を使う必要があります。 次のコードに示すように、`resourceId` テンプレート関数は、SQL Server などのリソースの一意 ID を取得します。

```json
"outputs": {
  "resourceId":{
    "type": "string",
    "value": "[resourceId('otherResourceGroup', 'Microsoft.Sql/servers', parameters('serverName'))]"
  }
}
```

その後、`reference` テンプレート関数の内部で `resourceId` 関数を使って、データベースのプロパティを取得できます。 返されるオブジェクトには、エンドポイントの完全な値を保持する `fullyQualifiedDomainName` プロパティが含まれています。 この値は、実行時に取得され、クラウド環境に固有のエンドポイントの名前空間を提供します。 エンドポイントの名前空間をハードコーディングせずに、接続文字列を定義するには、次に示すように、返されたオブジェクトのプロパティを接続文字列で直接参照することができます。

```json
"[concat('Server=tcp:', reference(resourceId('sql', 'Microsoft.Sql/servers', parameters('test')), '2015-05-01-preview').fullyQualifiedDomainName, ',1433;Initial Catalog=', parameters('database'),';User ID=', parameters('username'), ';Password=', parameters('pass'), ';Encrypt=True;')]"
```

## <a name="consider-resource-properties"></a>リソースのプロパティについて考慮する

Azure Stack 環境内の特定のリソースは、テンプレートで考慮する必要がある一意のプロパティを持っています。

### <a name="ensure-vm-images-are-available"></a>VM イメージを使用できるようにする

Azure では、VM イメージの豊富な選択肢が提供されています。 これらのイメージは、Microsoft とパートナーによる展開用に作成および準備されています。 イメージは、プラットフォームでの VM の基盤を形成します。 しかし、クラウドの一貫性のためのテンプレートでは、使用可能なパラメーターのみを参照する必要があります。具体的には、グローバル Azure、Azure ソブリン クラウド、または Azure Stack ソリューションで使用可能な VM イメージのパブリッシャー、オファー、および SKU です。

ある場所で利用可能な VM イメージの一覧を取得するには、次の Azure CLI コマンドを実行します。

```azurecli-interactive
az vm image list -all
```

Azure PowerShell コマンドレット [Get-AzureRmVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) を使用し、`-Location` パラメーターで目的の場所を指定して、同じ一覧を取得できます。 次に例を示します。

```azurepowershell-interactive
Get-AzureRmVMImagePublisher -Location "West Europe" | Get-AzureRmVMImageOffer | Get-AzureRmVMImageSku | Get-AzureRmVMImage
```

このコマンドを使ってグローバル Azure クラウドの西ヨーロッパ リージョンで利用可能なすべてのイメージを取得するには数分かかります。

これらの VM イメージを Azure Stack で使用できるようにする場合は、使用可能なすべてのストレージが消費されます。 最小のスケール ユニットでも対応するため、Azure Stack では環境に追加するイメージを選択できます。

次のコード サンプルでは、ARM テンプレートでパブリッシャー、オファー、SKU パラメーターを参照するための一貫した方法を示します。

```json
"storageProfile": {
    "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "2016-Datacenter",
    "version": "latest"
    }
}
```

### <a name="check-local-vm-sizes"></a>ローカル VM サイズを確認する

クラウドの一貫性のためのテンプレートを開発するには、必要な VM サイズをすべてのターゲット環境で使用できることを確認する必要があります。 VM サイズは、パフォーマンス特性と機能のグループです。 一部の VM サイズは、VM が実行されるハードウェアに依存します。 たとえば、GPU 最適化 VM を展開する場合、ハイパーバイザーを実行するハードウェアはハードウェア GPU を備えている必要があります。

Microsoft が特定のハードウェアに依存する新しいサイズの VM を導入するときは、通常、最初に Azure クラウド内のリージョンの小さなサブセットでその VM サイズを利用できるようにします。 その後、他のリージョンおよびクラウドで利用できるようにします。 展開先の各クラウドに VM サイズが存在することを確認するには、次の Azure CLI コマンドで利用可能なサイズを取得できます。

```azurecli-interactive
az vm list-sizes --location "West Europe"
```

Azure PowerShell では、次を使用します。

```azurepowershell-interactive
Get-AzureRmVMSize -Location "West Europe"
```

使用可能なサービスの完全なリストについては、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?cdn=disable)」をご覧ください。

### <a name="check-use-of-azure-managed-disks-in-azure-stack"></a>Azure Stack での Azure Managed Disks の使用を確認する

マネージド ディスクは、Azure テナント用のストレージを処理します。 明示的にストレージ アカウントを作成して仮想ハード ディスク (VHD) の URI を指定する代わりに、VM を展開するときにマネージド ディスクを使ってこれらの操作を暗黙的に実行できます。 マネージド ディスクは、同じ可用性セット内の VM のすべてのディスクを異なるストレージ ユニットに配置することで、可用性を強化します。 さらに、非常に短いダウンタイムで、既存の VHD を Standard ストレージから Premium ストレージに変換できます。

マネージド ディスクは Azure Stack のロードマップ上にありますが、現在はサポートされていません。 サポートされるようになるまでは、次に示すように、VM リソース用テンプレートの `vhd` 要素を使って VHD を明示的に指定することで、Azure Stack 用のクラウド一貫性テンプレートを開発できます。

```json
"storageProfile": {
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "[parameters('windowsOSVersion')]",
    "version": "latest"
  },
  "osDisk": {
    "name": "osdisk",
    "vhd": {
      "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, 'vhds/osdisk.vhd')]"
    },
    "caching": "ReadWrite",
    "createOption": "FromImage"
  }
}
```

一方、テンプレートでマネージド ディスクの構成を指定するには、ディスク構成から `vhd` 要素を削除します。

```json
"storageProfile": {
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "[parameters('windowsOSVersion')]",
    "version": "latest"
  },
  "osDisk": {
    "caching": "ReadWrite",
    "createOption": "FromImage"
  }
}
```

同じ変更が、[データ ディスク](../../virtual-machines/using-managed-disks-template-deployments.md)にも適用されます。

### <a name="verify-that-vm-extensions-are-available-in-azure-stack"></a>VM 拡張機能が Azure Stack で使用できることを確認する

クラウドの一貫性のためのもう 1 つの考慮事項は、VM の内部でリソースを構成するための[仮想マシン拡張機能](../../virtual-machines/extensions/features-windows.md)の使用です。 Azure Stack では使用できない VM 拡張機能があります。 テンプレートでは、VM 拡張機能に専用のリソースを指定して、テンプレート内に依存関係と条件を作成できます。

たとえば、Microsoft SQL Server を実行する VM を構成する場合、VM 拡張機能はテンプレートの展開の一部として SQL Server を構成できます。 SQL Server を実行する VM 上にデータベースを作成するように構成されたアプリケーション サーバーも展開テンプレートに含まれる場合、何が起きるかを考慮します。 アプリケーション サーバーに対する VM 拡張機能の使用だけでなく、正常に返された SQL Server VM 拡張機能リソースに対してアプリケーション サーバーの依存関係を構成できます。 このアプローチは、SQL Server を実行する VM が構成され、アプリケーション サーバーがデータベースを作成するように指示されたときに使用できることを保証します。

テンプレートの宣言的方法では、リソースの最終状態とそれらの間の依存関係を定義できますが、依存関係に必要なロジックはプラットフォームが処理します。

#### <a name="check-that-vm-extensions-are-available"></a>VM 拡張機能が利用できることを確認する

多くの種類の VM 拡張機能があります。 クラウドの一貫性のためのテンプレートを開発する場合は、テンプレートの対象となるすべてのリージョンで利用できる拡張機能のみを使うようにします。

特定のリージョン (この例では `myLocation`) で利用可能な VM 拡張機能の一覧を取得するには、次の Azure CLI コマンドを実行します。

```azurecli-interactive
az vm extension image list --location myLocation
```

また、Azure PowerShell の [Get-AzureRmVmImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) コマンドレットを実行し、`-Location` を使って仮想マシン イメージの場所を指定することもできます。 次に例を示します。

```azurepowershell-interactive
Get-AzureRmVmImagePublisher -Location myLocation | Get-AzureRmVMExtensionImageType | Get-AzureRmVMExtensionImage | Select Type, Version
```

#### <a name="ensure-that-versions-are-available"></a>バージョンを使用できることを確認する

VM 拡張機能はファースト パーティ Resource Manager リソースであるため、独自の API バージョンがあります。 次のコードで示すように、VM 拡張機能の種類は、Microsoft.Compute リソース プロバイダーの入れ子になったリソースです。

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "apiVersion": "2015-06-15",
    "name": "myExtension",
    "location": "[parameters('location')]",
    ...
```

VM 拡張機能リソースの API バージョンは、テンプレートの対象になるすべての場所に存在する必要があります。 場所の依存関係は、前の「すべてのリソースの種類のバージョンを確認する」セクションで説明したリソース プロバイダーの API バージョンの可用性と同じように動作します。

VM 拡張機能リソースで利用可能な API バージョンの一覧を取得するには、次に示すように、**Microsoft.Compute** リソース プロバイダーで [Get-AzureRmResourceProvider](/powershell/module/az.resources/get-azresourceprovider) コマンドレットを使います。

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Compute" | Select-Object -ExpandProperty ResourceTypes | Select ResourceTypeName, Locations, ApiVersions | where {$_.ResourceTypeName -eq "virtualMachines/extensions"}
```

仮想マシン スケール セットで VM 拡張機能を使うこともできます。 同じ場所条件が適用されます。 クラウドの一貫性のためのテンプレートを開発するには、展開先のすべての場所で API バージョンが使用できることを確認します。 スケール セットの VM 拡張機能リソースの API バージョンを取得するには、前と同じコマンドレットを使用しますが、次に示すように、仮想マシン スケール セットのリソースの種類を指定します。

```azurepowershell-interactive
Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Compute" | Select-Object -ExpandProperty ResourceTypes | Select ResourceTypeName, Locations, ApiVersions | where {$_.ResourceTypeName -eq "virtualMachineScaleSets/extensions"}
```

特定の各拡張機能もバージョン管理されます。 このバージョンは、VM 拡張機能の `typeHandlerVersion` プロパティで示されます。 テンプレートの VM 拡張機能の `typeHandlerVersion` 要素で指定されているバージョンが、テンプレート展開先の場所で使用できることを確認します。 たとえば、次のコードではバージョン 1.7 を指定しています。

```json
{
    "type": "extensions",
    "apiVersion": "2016-03-30",
    "name": "MyCustomScriptExtension",
    "location": "[parameters('location')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]"
    ],
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.7",
        ...
```

特定の VM 拡張機能で使用可能なバージョンの一覧を取得するには、[Get-AzureRmVMExtensionImage](/powershell/module/az.compute/get-azvmextensionimage) コマンドレットを使います。 次の例では、PowerShell DSC (Desired State Configuration) VM 拡張機能で使用可能なバージョンを **myLocation** から取得しています。

```azurepowershell-interactive
Get-AzureRmVMExtensionImage -Location myLocation -PublisherName Microsoft.PowerShell -Type DSC | FT
```

パブリッシャーの一覧を取得するには、[Get-AzureRmVmImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) コマンドを使用します。 種類を要求するには、[Get-AzureRmVMExtensionImageType](/powershell/module/az.compute/get-azvmextensionimagetype) コマンドを使用します。

## <a name="tips-for-testing-and-automation"></a>テストと自動化に関するヒント

テンプレートを作成する間に関連するすべての設定、機能、制限事項を追跡するのは困難です。 一般的な方法は、他の場所を対象にする前に、1 つのクラウドに対してテンプレートを開発してテストすることです。 ただし、作成プロセスの早い段階でそのテストを実行すると、開発チームが行う必要のあるトラブルシューティングとコードの書き直しが減ります。 場所の依存関係のために失敗する展開のトラブルシューティングには時間がかかることがあります。 そのため、作成サイクルのできるだけ早期に自動テストを行うことをお勧めします。 最終的に、必要な開発時間とリソースが減り、クラウド一貫成果物の価値が高くなります。

次の図では、統合開発環境 (IDE) を使用するチームの開発プロセスの典型的な例を示します。 タイムラインの異なる段階で、異なる種類のテストが実行されます。 ここでは 2 人の開発者が同じソリューションで作業していますが、このシナリオは単独開発者や大規模なチームにも等しく適用されます。 通常、各開発者は中央リポジトリのローカル コピーを作成し、同じファイルの作業をしている可能性がある他の開発者に影響を与えることなく、ローカルのコピーを使用して作業できます。

![ローカル IDE での並列の 2 つのセットの単体テストと統合テストを示す図。これは、CI/CD 開発フローにマージされ、単体テスト、統合テスト、テスト デプロイ、デプロイの順に遷移します。](./media/templates-cloud-consistency/workflow.png)

テストと自動化については次のヒントを考慮してください。

* テスト ツールを利用します。 たとえば、Visual Studio Code と Visual Studio には、テンプレートの検証を支援できる IntelliSense や他の機能が含まれます。
* ローカル IDE での開発時にコードの品質を向上させるには、単体テストと統合テストで静的コード分析を実行します。
* 初期開発時のエクスペリエンスをさらによくするには、単体テストと統合テストでは問題が見つかってテストを続行するときにのみ警告する必要があります。 そうすることで、対処する問題を特定して、変更の優先順位を決めることができます。これはテスト駆動型展開 (TDD) とも呼ばれます。
* 一部のテストは Azure Resource Manager に接続しなくても実行できることに注意してください。 テンプレート展開テストなど、他のテストには、オフラインで実行できない特定のアクションを実行するために Resource Manager が必要です。
* 検証 API に対する展開テンプレートのテストは、実際の展開と同じではありません。 また、ローカル ファイルからテンプレートを展開する場合でも、テンプレート内の入れ子になったテンプレートへの参照は Resource Manager によって直接取得され、VM 拡張機能によって参照される成果物は、展開される VM 内で実行されている VM エージェントによって取得されます。

## <a name="next-steps"></a>次のステップ

* [Azure Resource Manager テンプレートに関する考慮事項](/azure-stack/user/azure-stack-develop-templates)
* [ARM テンプレートのベスト プラクティス](template-syntax.md)
