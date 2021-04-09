---
title: テンプレートのベスト プラクティス
description: Azure Resource Manager テンプレート (ARM テンプレート) を作成するための推奨されるアプローチについて説明します。 テンプレートを使用する場合の一般的な問題を回避するための推奨事項を示します。
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: 583a113df9cdb1951daf1002dd69531f050cfb54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99257999"
---
# <a name="arm-template-best-practices"></a>ARM テンプレートのベスト プラクティス

この記事では、Azure Resource Manager テンプレート (ARM テンプレート) を構築する際に推奨プラクティスを使用する方法について説明します。 これらの推奨事項は、ARM テンプレートを使用したソリューションのデプロイに関する一般的な問題を回避するうえで役立ちます。

## <a name="template-limits"></a>テンプレートの制限

テンプレートのサイズを 4 MB に、各パラメーター ファイルのサイズを 64 KB に制限します。 4 MB の制限は、反復的なリソースの定義と変数およびパラメーターの値で拡張された後のテンプレートの最終的な状態に適用されます。

また、以下のように制限されます。

* パラメーター 256 個
* 変数 256 個
* リソース (コピー数を含む) 800 個
* 出力値 64 個
* テンプレート式内で 24,576 文字

入れ子になったテンプレートを使用すると、一部のテンプレートの制限を超過することができます。 詳細については、「[Azure リソース デプロイ時のリンクされたテンプレートおよび入れ子になったテンプレートの使用](linked-templates.md)」を参照してください。 パラメーター、変数、出力の数を減らすために、いくつかの値を 1 つのオブジェクトに結合することができます。 詳しくは、[パラメーターとしてのオブジェクト](/azure/architecture/guide/azure-resource-manager/advanced-templates/objects-as-parameters)に関する記事をご覧ください。

## <a name="resource-group"></a>Resource group

リソース グループにリソースをデプロイすると、リソースに関するメタデータがリソース グループに格納されます。 メタデータは、リソース グループの場所に格納されます。

リソース グループのリージョンが一時的に使用できない場合は、メタデータが使用できないため、リソース グループ内のリソースを更新できません。 他のリージョン内のリソースは通常どおり機能しますが、それらを更新することはできません。 リスクを最小限に抑えるため、リソース グループとリソースは同じリージョンに配置するようにしてください。

## <a name="parameters"></a>パラメーター

このセクションの情報は、[パラメーター](template-parameters.md)を使用するときに役に立ちます。

### <a name="general-recommendations-for-parameters"></a>パラメーターに関する一般的な推奨事項

* パラメーターの使用を最小限に抑えます。 代わりに、デプロイ時に指定する必要のないプロパティの変数またはリテラル値を使用してください。

* パラメーター名にキャメル ケースを使用します。

* SKU、サイズ、容量など、環境に応じて異なる設定のパラメーターを使用します。

* 特定しやすいように指定するリソース名のパラメーターを使用します。

* メタデータですべてのパラメーターを説明します。

    ```json
    "parameters": {
      "storageAccountType": {
        "type": "string",
        "metadata": {
          "description": "The type of the new storage account created to store the VM disks."
        }
      }
    }
    ```

* 重要ではないパラメーターの既定値を定義します。 既定値を指定することで、テンプレートをデプロイしやすくなり、ご自身のテンプレートのユーザーに適切な値の例が表示されます。 パラメーターの既定値は、既定のデプロイ構成のすべてのユーザーに対して有効である必要があります。

    ```json
    "parameters": {
      "storageAccountType": {
        "type": "string",
        "defaultValue": "Standard_GRS",
        "metadata": {
          "description": "The type of the new storage account created to store the VM disks."
        }
      }
    }
    ```

* オプションのパラメーターを指定するために、空の文字列を既定値として使用しないでください。 代わりに、リテラル値または言語式を使用して値を構成します。

   ```json
   "storageAccountName": {
     "type": "string",
     "defaultValue": "[concat('storage', uniqueString(resourceGroup().id))]",
     "metadata": {
       "description": "Name of the storage account"
     }
   }
   ```

* `allowedValues` は慎重に使用してください。 許可されているオプションに、ある値が含まれていないことを確認する必要がある場合にのみ使用します。 `allowedValues` を盛大に使用しすぎると、ご自身の一覧が最新の状態に保たれなくなり、有効なデプロイをブロックしてしまう可能性があります。

* お使いのテンプレートのパラメーター名と PowerShell デプロイ コマンドのパラメーターが同じ場合は、Resource Manager によって、ポストフィックス **FromTemplate** がテンプレート パラメーターに追加され、この名前の競合が解決されます。 たとえば、**ResourceGroupName** という名前のパラメーターをテンプレートに追加した場合、[New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) コマンドレットの **ResourceGroupName** パラメーターと競合します。 デプロイ中、**ResourceGroupNameFromTemplate** に値を指定するように求められます。

### <a name="security-recommendations-for-parameters"></a>パラメーターに関するセキュリティの推奨事項

* ユーザー名とパスワード (またはシークレット) に対して必ずパラメーターを使用します。

* すべてのパスワードおよびシークレットに `securestring` を使用します。 JSON オブジェクトに機密データを渡す場合は、`secureObject` 型を使用します。 secure string 型または secure object 型を含むテンプレート パラメーターをリソースのデプロイ後に読み取ることはできません。

    ```json
    "parameters": {
      "secretValue": {
        "type": "securestring",
        "metadata": {
          "description": "The value of the secret to store in the vault."
        }
      }
    }
    ```

* ユーザー名、パスワード、または `secureString` 型を必要とする任意の値には既定値を指定しないでください。

* アプリケーションの攻撃対象となる領域が増えるプロパティには既定値を指定しないでください。

### <a name="location-recommendations-for-parameters"></a>パラメーターに関する場所の推奨事項

* パラメーターを使用して、リソースの場所を指定し、既定値を `resourceGroup().location` に設定します。 場所パラメーターを指定することにより、テンプレートのユーザーが、自身がリソースをデプロイするアクセス許可を持つ場所を指定できます。

   ```json
   "parameters": {
     "location": {
       "type": "string",
       "defaultValue": "[resourceGroup().location]",
       "metadata": {
         "description": "The location in which the resources should be deployed."
       }
     }
   }
   ```

* 場所パラメーターに `allowedValues` を指定しないでください。 指定する場所がすべてのクラウドで使用できるとは限りません。

* 場所パラメーター値は、同じ場所に所属する可能性が高いリソースに対して使用します。 この方法により、ユーザーが場所情報の入力を求められる回数を最小限に抑えることができます。

* 一部の場所で使用できないリソースについては、個別のパラメーターを使用するか、場所のリテラル値を指定します。

## <a name="variables"></a>変数

[変数](template-variables.md)を使用する場合は、次の情報を活用してください。

* 変数名にはキャメル ケースを使用します。

* テンプレート内で複数回使用する必要のある値には、変数を使用してください。 1 回しか使用しない値は、ハードコーディングすることでテンプレートが読みやすくなります。

* テンプレート関数を複雑に組み合わせて作成する値に対して変数を使用してください。 複雑な式が変数内にのみ現れるようにすると、テンプレートが読みやすくなります。

* テンプレートの `variables` セクションでは [reference](template-functions-resource.md#reference) 関数は使用できません。 `reference` 関数は、リソースのランタイム状態からその値を取得します。 ただし、変数が解決されるのは、テンプレートの初期解析時です。 `reference` 関数を必要とする値は、テンプレートの `resources` セクションまたは `outputs` セクションに直接作成してください。

* リソース名を表す変数は、一意である必要があります。

* [変数のコピー ループ](copy-variables.md)を使用して、JSON オブジェクトの繰り返しパターンを作成します。

* 未使用の変数を削除します。

## <a name="api-version"></a>API バージョン

リソースの種類に対してハードコーディングされた API バージョンを `apiVersion` プロパティに設定します。 新しいテンプレートを作成するときは、リソースの種類に最新の API バージョンを使用することをお勧めします。 使用可能な値を確認するには、[テンプレートのリファレンス](/azure/templates/)に関する記事をご覧ください。

テンプレートが想定どおりに動作する場合は、同じ API バージョンを使用し続けることをお勧めします。 同じ API バージョンを使用することで、新しいバージョンで導入される可能性がある破壊的変更について気にする必要はなくなります。

API バージョンにパラメーターを使用しないでください。 リソースのプロパティおよび値は、API バージョンによって異なる可能性があります。 パラメーターに API バージョンが設定されると、コード エディターの IntelliSense が適切なスキーマを決定できなくなります。 テンプレート内のプロパティと一致しない API バージョンを渡した場合、デプロイは失敗します。

API バージョンに対しては変数を使用しないでください。 特に、デプロイ時に API バージョンを動的に取得するために、[プロバイダー関数](template-functions-resource.md#providers)を使用しないでください。 動的に取得された API バージョンが、テンプレート内のプロパティと一致しない可能性があります。

## <a name="resource-dependencies"></a>リソースの依存関係

どのような[依存関係](define-resource-dependency.md)を設定するかを決めるときは、次のガイドラインを使用してください。

* プロパティを共有する必要があるリソース間に暗黙的な依存関係を設定するには、`reference` 関数を使用して、リソース名を渡します。 暗黙的な依存関係を既に定義してある場合は、明示的な `dependsOn` 要素を追加しないでください。 これにより、不要な依存関係が設定されるリスクを減らすことができます。 暗黙の依存関係を設定する例については、「[reference 関数と list 関数](define-resource-dependency.md#reference-and-list-functions)」を参照してください。

* 子リソースは、その親リソースに依存するように設定します。

* [condition 要素](conditional-resource-deployment.md)が false に設定されているリソースは、依存関係の順序から自動的に削除されます。 依存関係は、リソースが常にデプロイされているかのように設定してください。

* 明示的に設定しなくても連鎖的な依存関係になるようにします。 たとえば、仮想マシンが仮想ネットワーク インターフェイスに依存し、その仮想ネットワーク インターフェイスは仮想ネットワークとパブリック IP アドレスに依存しているとします。 この場合、仮想マシンは、この 3 つのリソースすべての後にデプロイされますが、この仮想マシンを 3 つのリソースすべてに依存するものとして明示的に設定しないでください。 これにより依存関係の順序が明確になり、後でテンプレートを変更するのも簡単になります。

* デプロイの前に値を指定できる場合は、依存関係なしでリソースをデプロイしてみます。 たとえば、構成値に他のリソースの名前を必要とする場合は、依存関係が不要なことがあります。 ただし、これは必ずしも有効であるとは限りません。リソースによっては、他のリソースが存在するかどうかを確認することがあるためです。 エラーが発生したら、依存関係を追加してください。

## <a name="resources"></a>リソース

[リソース](template-syntax.md#resources)を使用する場合は、次の情報を活用してください。

* 他の共同作業者にリソースの用途がわかるように、テンプレート内の各リソースに `comments` を指定してください。

    ```json
    "resources": [
      {
        "name": "[variables('storageAccountName')]",
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2019-06-01",
        "location": "[resourceGroup().location]",
        "comments": "This storage account is used to store the VM disks.",
          ...
      }
    ]
    ```

* テンプレートで "*パブリック エンドポイント*" (Azure Blob Storage のパブリック エンドポイントなど) を使用する場合、名前空間は "*ハードコーディングしない*" でください。 名前空間を動的に取得するには、`reference` 関数を使用します。 そうすることで、テンプレートのエンドポイントを手作業で変更することなく、別のパブリック名前空間環境にテンプレートをデプロイできます。 API バージョンは、テンプレートのストレージ アカウントで使用するものと同じバージョンに設定します。

    ```json
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": "true",
        "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').primaryEndpoints.blob]"
      }
    }
    ```

   ストレージ アカウントが、作成しているものと同じテンプレートにデプロイされ、そのストレージ アカウントの名前がテンプレート内の別のリソースと共有されない場合、リソースを参照するときにプロバイダーの名前空間または `apiVersion` を指定する必要はありません。 簡単な構文の例を次に示します。

    ```json
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": "true",
        "storageUri": "[reference(variables('storageAccountName')).primaryEndpoints.blob]"
      }
    }
    ```

   また、別のリソース グループにある既存のストレージ アカウントを参照することもできます。

    ```json
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": "true",
        "storageUri": "[reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('existingStorageAccountName')), '2019-06-01').primaryEndpoints.blob]"
      }
    }
    ```

* 仮想マシンにパブリック IP アドレスを割り当てるのは、アプリケーションで必要な場合のみにしてください。 デバッグや各種管理のために仮想マシン (VM) に接続するには、受信 NAT 規則、仮想ネットワーク ゲートウェイ、またはジャンプボックスを使用してください。

     仮想マシンへの接続の詳細については、以下の記事を参照してください。

   * [Azure で N 層アーキテクチャの VM を実行する](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)
   * [Azure Resource Manager の VM の WinRM アクセスを設定する](../../virtual-machines/windows/winrm.md)
   * [Azure Portal を使用して VM への外部アクセスを許可する](../../virtual-machines/windows/nsg-quickstart-portal.md)
   * [PowerShell を使用して VM への外部アクセスを許可する](../../virtual-machines/windows/nsg-quickstart-powershell.md)
   * [Azure CLI を使用して Linux VM への外部アクセスを許可する](../../virtual-machines/linux/nsg-quickstart.md)

* パブリック IP アドレスの `domainNameLabel` プロパティは一意である必要があります。 `domainNameLabel` の値は、3 文字以上 63 文字以下で、正規表現 `^[a-z][a-z0-9-]{1,61}[a-z0-9]$` で指定された規則に従う必要があります。 `uniqueString` 関数は 13 文字の文字列を生成するため、`dnsPrefixString` パラメーターは 50 文字に制限されます。

    ```json
    "parameters": {
      "dnsPrefixString": {
        "type": "string",
        "maxLength": 50,
        "metadata": {
          "description": "The DNS label for the public IP address. It must be lowercase. It should match the following regular expression, or it will raise an error: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$"
        }
      }
    },
    "variables": {
      "dnsPrefix": "[concat(parameters('dnsPrefixString'),uniquestring(resourceGroup().id))]"
    }
    ```

* カスタム スクリプト拡張機能にパスワードを追加する場合は、`protectedSettings` プロパティで `commandToExecute` プロパティを使用してください。

    ```json
    "properties": {
      "publisher": "Microsoft.Azure.Extensions",
      "type": "CustomScript",
      "typeHandlerVersion": "2.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "fileUris": [
          "[concat(variables('template').assets, '/lamp-app/install_lamp.sh')]"
        ]
      },
      "protectedSettings": {
        "commandToExecute": "[concat('sh install_lamp.sh ', parameters('mySqlPassword'))]"
      }
    }
    ```

   > [!NOTE]
   > シークレット情報を VM と拡張機能にパラメーターとして渡すときに暗号化されるように、関連する拡張機能の `protectedSettings` プロパティを使用する必要があります。

* 時間の経過と共に変わる可能性のある既定値を持つプロパティに対して明示的な値を指定します。 たとえば、AKS クラスターをデプロイする場合、`kubernetesVersion` プロパティは指定することも省略することもできます。 これを指定しない場合、[クラスターは既定では N-1 マイナー バージョンと最新のパッチ](../../aks/supported-kubernetes-versions.md#azure-portal-and-cli-versions)に設定されます。 ARM テンプレートを使用してクラスターをデプロイする場合、この既定の動作は想定どおりにならないことがあります。 ご利用のテンプレートを再デプロイすると、クラスターが予期せずに新しい Kubernetes バージョンにアップグレードされる可能性があります。 このため、ご利用のクラスターをアップグレードする準備ができたら、明示的なバージョン番号を指定して、手動で変更することを検討してください。

## <a name="use-test-toolkit"></a>テスト ツールキットの使用

ARM テンプレート テスト ツールキットは、推奨される方法がテンプレートで使用されているかどうかを確認するスクリプトです。 テンプレートが推奨されるプラクティスに準拠していない場合は、推奨される変更を含む警告の一覧が返されます。 テスト ツールキットは、テンプレートにベストプラクティスを実装する方法を理解するのに役立ちます。

テンプレートが完成したら、テスト ツールキットを実行して、その実装を改善する方法があるかどうかを確認します。 詳細については、「[ARM テンプレート テスト ツールキットを使用する](test-toolkit.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

* テンプレート ファイルの構造については、「[ARM テンプレートの構造と構文の詳細](template-syntax.md)」を参照してください。
* すべての Azure クラウド環境で動作するテンプレートを作成する方法に関する推奨事項については、[クラウドの一貫性のための ARM テンプレートの開発](templates-cloud-consistency.md)に関するページをご覧ください。
