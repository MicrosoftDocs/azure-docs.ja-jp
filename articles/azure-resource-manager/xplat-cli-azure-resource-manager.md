---
title: "Azure CLI を使用したリソースの管理 | Microsoft Docs"
description: "Azure コマンド ライン インターフェイス (CLI) を使用して Azure のリソースとグループを管理します。"
editor: 
manager: timlt
documentationcenter: 
author: tfitzmac
services: azure-resource-manager
ms.assetid: bb0af466-4f65-4559-ac3a-43985fa096ff
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 08/22/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: bd6f81ee12a7bb655166cf059236175bfb9994e5
ms.lasthandoff: 03/21/2017


---
# <a name="use-the-azure-cli-to-manage-azure-resources-and-resource-groups"></a>Azure CLI を使用して Azure のリソースとリソース グループを管理する
> [!div class="op_single_selector"]
> * [ポータル](resource-group-portal.md) 
> * [Azure CLI](xplat-cli-azure-resource-manager.md)
> * [Azure PowerShell](powershell-azure-resource-manager.md)
> * [REST API](resource-manager-rest-api.md)
> 
> 

Azure コマンド ライン インターフェイス (Azure CLI) は、Resource Manager を使用したリソースのデプロイと管理に使用できるツールの&1; つです。 この記事では、Resource Manager モードで Azure CLI を使用して、Azure のリソースとリソース グループを管理する一般的な方法を紹介します。 CLI を使用したリソースのデプロイの詳細については、「[Resource Manager テンプレートと Azure CLI を使用したリソースのデプロイ](resource-group-template-deploy-cli.md)」を参照してください。 Azure リソースと Resource Manager に関する背景については、「[Azure Resource Manager の概要](resource-group-overview.md)」を参照してください。

> [!NOTE]
> Azure CLI を使用して Azure リソースを管理するには、[Azure CLI をインストール](../cli-install-nodejs.md)し、`azure login` コマンドを使用して [Azure にログイン](../xplat-cli-connect.md)する必要があります。 CLI が Resource Manager モードになっていることを確認します (`azure config mode arm` を実行します)。 これらの操作が完了したら、準備は OK です。
> 
> 

## <a name="get-resource-groups-and-resources"></a>リソース グループとリソースの取得
### <a name="resource-groups"></a>リソース グループ
サブスクリプションのすべてのリソース グループとその場所の一覧を取得するには、次のコマンドを実行します。

    azure group list


### <a name="resources"></a>リソース
 グループ内 (この例では *testRG* という名前のグループ) のすべてのリソースの一覧を表示するには、次のコマンドを使用します。

    azure resource list testRG

*MyUbuntuVM* という名前の VM など、グループ内の個別のリソースを表示するには、次のコマンドを使用します。

    azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15"

**Microsoft.Compute/virtualMachines** パラメーターに注意してください。 このパラメーターは、情報要求の対象となるリソースの種類を示します。

> [!NOTE]
> **list** コマンド以外の **azure resource** コマンドを使用する場合、リソースの API バージョンを **-o** パラメーターで指定する必要があります。 API バージョンが不明な場合は、テンプレート ファイルでリソースの apiVersion フィールドを確認してください。 Resource Manager の API バージョンの詳細については、「[Resource Manager のプロバイダー、リージョン、API のバージョン、およびスキーマ](resource-manager-supported-services.md)」を参照してください。
> 
> 

リソースの詳細を表示するとき、多くの場合、`--json` パラメーターを使用すると便利です。 このパラメーターを使用すると、一部の値が入れ子構造やコレクションになるので、出力が読みやすくなります。 次の入力例は、**show** コマンドの結果を JSON ドキュメントとして返す場合を示しています。

    azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15" --json

> [!NOTE]
> &gt; 文字を使用して出力をファイルに転送することで、JSON データをファイルに保存できます。 次に例を示します。
> 
> `azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15" --json > myfile.json`
> 
> 

### <a name="tags"></a>タグ
[!INCLUDE [resource-manager-tag-resources-cli](../../includes/resource-manager-tag-resources-cli.md)]

## <a name="manage-resources"></a>リソースの管理
ストレージ アカウントなどのリソースをリソース グループに追加するには、次のようなコマンドを実行します。

    azure resource create testRG MyStorageAccount "Microsoft.Storage/storageAccounts" "westus" -o "2015-06-15" -p "{\"accountType\": \"Standard_LRS\"}"

**-o** パラメーターでリソースの API バージョンを指定するほか、**-p** パラメーターで必須または追加のプロパティを指定して JSON で書式設定された文字列を渡します。

仮想マシンのリソースなど、既存のリソースを削除するには、次のようなコマンドを使用します。

    azure resource delete testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15"

既存のリソースを別のリソース グループまたはサブスクリプションに移動するには、 **azure resource move** コマンドを使用します。 次の例は、Redis Cache を新しいリソース グループに移動する方法を示しています。 **-i** パラメーターには、移動するリソース ID のコンマ区切りリストを指定します。

    azure resource move -i "/subscriptions/{guid}/resourceGroups/OldRG/providers/Microsoft.Cache/Redis/examplecache" -d "NewRG"

## <a name="control-access-to-resources"></a>リソースへのアクセスの制御
Azure CLI を使用して、Azure リソースへのアクセスを制御するポリシーを作成し、管理することができます。 ポリシーの定義とリソースへのポリシーの割り当てに関する背景については、「[ポリシーを使用したリソース管理とアクセス制御](resource-manager-policy.md)」を参照してください。

たとえば、米国西部または米国中北部以外の場所からのすべての要求を拒否する次のポリシーを定義し、ポリシー定義ファイル policy.json に保存してみます。

    {
    "if" : {
        "not" : {
        "field" : "location",
        "in" : ["westus" ,  "northcentralus"]
        }
    },
    "then" : {
        "effect" : "deny"
    }
    }

次に、**policy definition create** コマンドを実行します。

    azure policy definition create MyPolicy -p c:\temp\policy.json

このコマンドの出力は次のようになります。

    + Creating policy definition MyPolicy data:    PolicyName:             MyPolicy data:    PolicyDefinitionId:     /subscriptions/########-####-####-####-############/providers/Microsoft.Authorization/policyDefinitions/MyPolicy

    data:    PolicyType:             Custom data:    DisplayName:            undefined data:    Description:            undefined data:    PolicyRule:             field=location, in=[westus, northcentralus], effect=deny

 目的のスコープにポリシーを割り当てるには、前のコマンドで返された **PolicyDefinitionId** を使用します。 次の例では、スコープはサブスクリプションになっていますが、リソース グループや個々のリソースをスコープにすることもできます。

    azure policy assignment create MyPolicyAssignment -p /subscriptions/########-####-####-####-############/providers/Microsoft.Authorization/policyDefinitions/MyPolicy -s /subscriptions/########-####-####-####-############/

**policy definition show**、**policy definition set**、**policy definition delete** の各コマンドを使用して、ポリシー定義を取得、変更、削除することができます。

同様に、**policy assignment show**、**policy assignment set**、**policy assignment delete** の各コマンドを使用して、ポリシーの割り当てを取得、変更、削除することができます。

## <a name="export-a-resource-group-as-a-template"></a>テンプレートとしてのリソース グループのエクスポート
既存のリソース グループがある場合は、そのリソース グループの Resource Manager テンプレートを表示できます。 テンプレートのエクスポートには、2 つの利点があります。

1. ソリューションの将来のデプロイを簡単に自動化できます。すべてのインフラストラクチャがテンプレートに定義されているためです。
2. ソリューションを表す JSON を見ることでテンプレートの構文について理解を深めることができます。

Azure CLI を使用して、リソース グループの現在の状態を表すテンプレートをエクスポートするか、特定のデプロイに使用されたテンプレートをダウンロードできます。

* **リソース グループのテンプレートのエクスポート** - これは、リソース グループを変更した後で、現在の状態の JSON 表現を取得する必要があるときに便利です。 しかしながら、生成されたテンプレートには、最小数のパラメーターのみが含まれ、変数はありません。 テンプレートの大半の値はハードコーディングされています。 生成されたテンプレートをデプロイする前に、さまざまな環境に合わせてデプロイをカスタマイズできるように、多くの値をパラメーターに変換しておくと便利です。
  
    リソース グループのテンプレートをローカル ディレクトリにエクスポートするには、次の例に示すように、`azure group export` コマンドを実行します  (ローカル ディレクトリはお使いのオペレーティング システム環境に合わせて置き換えてください)。
  
        azure group export testRG ~/azure/templates/
* **特定のデプロイのテンプレートのダウンロード** -- これは、リソースのデプロイに使用された実際のテンプレートを表示する必要があるときに便利です。 テンプレートには、元のデプロイに定義されていたすべてのパラメーターと変数が含まれます。 ただし、組織のだれかがテンプレートに定義されている以外のリソース グループを変更した場合、そのテンプレートはそのリソース グループの現在の状態を表しません。
  
    特定のデプロイに使用されたテンプレートをローカル ディレクトリにダウンロードするには、`azure group deployment template download` コマンドを実行します。 次に例を示します。
  
        azure group deployment template download TestRG testRGDeploy ~/azure/templates/downloads/

> [!NOTE]
> テンプレートのエクスポート機能はプレビューの段階にあり、現在のところ、一部の種類のリソースでは、テンプレートをエクスポートできません。 テンプレートのエクスポートを試行したとき、一部のリソースがエクスポートされなかったというエラーが表示されることがあります。 必要に応じて、ダウンロード後、エクスポートされなかったリソースをテンプレート内で手動で定義します。
> 
> 

## <a name="next-steps"></a>次のステップ
* Azure CLI を使用したデプロイ操作とデプロイ エラーのトラブルシューティングの詳細については、[デプロイ操作の表示](resource-manager-deployment-operations.md)に関する記事を参照してください。
* CLI を使用してリソースにアクセスするアプリケーションやスクリプトを設定する場合は、「[リソースにアクセスするためのサービス プリンシパルを Azure CLI で作成する](resource-group-authenticate-service-principal-cli.md)」を参照してください。
* 企業が Resource Manager を使用してサブスクリプションを効果的に管理する方法については、「[Azure enterprise scaffold - prescriptive subscription governance (Azure エンタープライズ スキャフォールディング - サブスクリプションの規範的な管理)](resource-manager-subscription-governance.md)」を参照してください。


