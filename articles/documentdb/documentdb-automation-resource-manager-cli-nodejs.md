---
title: DocumentDB Automation - Resource Manager - Azure CLI 1.0 | Microsoft Docs
description: "Azure リソース マネージャーのテンプレートまたは CLI を使用し、DocumentDB データベース アカウントをデプロイします。 DocumentDB は、JSON データ用のクラウドベースの NoSQL データベースです。"
services: documentdb
author: mimig1
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: eae5eec6-0e27-442c-abfc-ef6b7fd3f8d2
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 4725f4200446434bfcb8754aac9bf0d99f8a7526
ms.lasthandoff: 03/21/2017


---
# <a name="automate-documentdb-account-creation-using-azure-cli-10-and-azure-resource-manager-templates"></a>Azure CLI 1.0 と Azure Resource Manager テンプレートを使用して DocumentDB アカウントを自動作成する
> [!div class="op_single_selector"]
> * [Azure ポータル](documentdb-create-account.md)
> * [Azure CLI 1.0](documentdb-automation-resource-manager-cli-nodejs.md)
> * [Azure CLI 2.0](documentdb-automation-resource-manager-cli.md)
> * [Azure PowerShell](documentdb-manage-account-with-powershell.md)

この記事では、Azure Resource Manager のテンプレートを使用して、または Azure CLI 1.0 (コマンド ライン インターフェイス) で直接的に、Azure DocumentDB アカウントを作成する方法について説明します。 Azure ポータルを使用して DocumentDB アカウントを作成する方法については、「 [DocumentDB データベース アカウントの作成](documentdb-create-account.md)」を参照してください。

現在のところ、DocumentDB データベース アカウントは、Resource Manager テンプレートと Azure CLI 1.0 で作成できる唯一の DocumentDB リソースです。

## <a name="getting-ready"></a>開発の準備
Azure リソース グループで Azure CLI 1.0 を使用するには、適切なバージョンと Azure アカウントを用意する必要があります。 Azure CLI 1.0 をインストールしていない場合は、 [インストールします](../cli-install-nodejs.md)。

### <a name="update-your-azure-cli-10-version"></a>Azure CLI 1.0 のバージョンを更新する
コマンド プロンプトで「`azure --version`」と入力し、バージョン 0.10.4 以降が既にインストールされているかどうかを確認します。 この手順で Microsoft Azure CLI のデータ収集に参加するように求められる場合があります。y または n を選択して、オプトインまたはオプトアウトできます。

    azure --version
    0.10.4 (node: 4.2.4)

バージョンが 0.10.4 以降ではない場合、[Azure CLI 1.0 をインストール](../cli-install-nodejs.md)するか、いずれかのネイティブ インストーラーまたは **npm** で更新する必要があります。更新する場合は「`npm update -g azure-cli`」と入力し、インストールする場合は「`npm install -g azure-cli`」と入力します。

### <a name="set-your-azure-account-and-subscription"></a>Azure アカウントとサブスクリプションを設定する
Azure サブスクリプションをまだ持っていない場合でも、Visual Studio サブスクリプションがあれば、 [Visual Studio サブスクライバー向けの特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)をアクティブ化できます。 または、 [無料試用版](https://azure.microsoft.com/pricing/free-trial/)にサインアップできます。

Azure リソース管理テンプレートを使用するには、職場のアカウント、学校のアカウント、Microsoft アカウントのいずれかの ID を所有している必要があります。 いずれかのアカウントを所有している場合、次のコマンドを入力します。

    azure login

次の出力が生成されます。

    info:    Executing command login
    |info:    To sign in, use a web browser to open the page https://aka.ms/devicelogin.
    Enter the code E1A2B3C4D to authenticate.

> [!NOTE]
> Azure アカウントを持っていない場合、別の種類のアカウントが必要であることを示すエラー メッセージが表示されます。 現在の Azure アカウントから作成する方法については、「 [Azure Active Directory で職場または学校の ID を作成する](../virtual-machines/virtual-machines-windows-create-aad-work-id.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。
>
>

ブラウザーで [https://aka.ms/devicelogin](https://aka.ms/devicelogin) を開き、コマンド出力で生成されたコードを入力します。

![Microsoft Azure CLI 1.0 のデバイス ログイン画面のスクリーンショット](media/documentdb-automation-resource-manager-cli/azure-cli-login-code.png)

コードを入力したら、ブラウザーで使用する ID を選択し、必要に応じてユーザー名とパスワードを入力します。

![使用する Azure サブスクリプションに関連付けられている Microsoft ID アカウントを選択する場所のスクリーンショット](media/documentdb-automation-resource-manager-cli/identity-cli-login.png)

ログインすると、次の確認画面が表示されます。表示されたら、ブラウザーのウィンドウを閉じることができます。

![Microsoft Azure クロスプラットフォーム コマンド ライン インターフェイスへのログインを確認するスクリーンショット](media/documentdb-automation-resource-manager-cli/login-confirmation.png)

コマンド シェルでは、次の出力も表示されます。

    /info:    Added subscription Visual Studio Ultimate with MSDN
    info:    Setting subscription "Visual Studio Ultimate with MSDN" as default
    +
    info:    login command OK

ここで説明する対話式のログイン方法以外に、Azure CLI 1.0 でログインする方法もあります。 他の方法と複数のサブスクリプションを処理する方法に関する詳細については、「[Azure コマンド ライン インターフェイス (Azure CLI 1.0) から Azure サブスクリプションに接続する](../xplat-cli-connect.md)」を参照してください。

### <a name="switch-to-azure-cli-10-resource-group-mode"></a>Azure CLI 1.0 リソース グループ モードに切り替える
既定では、Azure CLI 1.0 はサービス管理モード (**asm** モード) で起動します。 以下を入力してリソース グループ モードに切り替えます。

    azure config mode arm

次の出力が生成されます。

    info:    Executing command config mode
    info:    New mode is arm
    info:    config mode command OK

必要に応じて、「 `azure config mode asm`」と入力することで、既定のコマンド セットに戻すことができます。

### <a name="create-or-retrieve-your-resource-group"></a>リソース グループを作成または取得する
DocumentDB アカウントを作成するには、最初にリソース グループを用意する必要があります。 使用するリソース グループの名前がわかっている場合、 [手順 2](#create-documentdb-account-cli)に移動してください。

現在のすべてのリソース グループの一覧を確認するには、次のコマンドを実行し、使用するリソース グループの名前をメモします。

    azure group list

リソース グループを作成するには、作成する新しいリソース グループの名前とリソース グループを作成するリージョンを指定します。

    azure group create <resourcegroupname> <resourcegrouplocation>

* `<resourcegroupname>` に使用できる文字は、英数字、ピリオド、アンダースコア、「-」、かっこのみです。末尾にピリオドを指定することはできません。
* `<resourcegrouplocation>` には、DocumentDB が一般公開されているリージョンのいずれかを指定します。 最新のリージョン一覧については、 [Azure のリージョン ページ](https://azure.microsoft.com/regions/#services)を参照してください。

入力例:

    azure group create new_res_group westus

次の出力が生成されます。

    info:    Executing command group create
    + Getting resource group new_res_group
    + Creating resource group new_res_group
    info:    Created resource group new_res_group
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/new_res_group
    data:    Name:                new_res_group
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:
    info:    group create command OK

エラーが発生した場合、「 [トラブルシューティング](#troubleshooting)」を参照してください。

## <a name="understanding-resource-manager-templates-and-resource-groups"></a>Resource Manager テンプレートおよびリソース グループについて
大部分のアプリケーションは、異なる種類のリソースの組み合わせ (1 つ以上の DocumentDB アカウント、ストレージ アカウント、仮想ネットワーク、コンテンツ配信ネットワークなど) から構築されます。 既定の Azure サービス管理 API と Azure ポータルでは、サービス単位のアプローチを使用してこれらの項目を表していました。 この方法では、個々のサービスを&1; つの論理的なデプロイ単位としてではなく、個別にデプロイ、管理 (またはこのことを実行するその他のツールを検索) する必要があります。

*Azure リソース マネージャー テンプレート* では、これらの異なるリソースを&1; つの論理的なデプロイ単位として、宣言型の方法でデプロイし、管理することが可能になります。 何をデプロイするのかを Azure に&1; コマンドずつ命令するのではなく、JSON ファイル内にデプロイ全体、つまりすべてのリソースと、関連する構成およびデプロイ パラメーターを記述し、Azure にそれらのリソースを&1; つのグループとしてデプロイするよう指示します。

Azure リソース グループとその機能の詳細については、「[Azure Resource Manager の概要](../azure-resource-manager/resource-group-overview.md)」を参照してください。 テンプレートの作成に興味がある場合は、「 [Azure リソース マネージャーのテンプレートの作成](../azure-resource-manager/resource-group-authoring-templates.md)」を参照してください。

## <a id="quick-create-documentdb-account"></a>作業: 単一リージョンの DocumentDB アカウントを作成する
単一リージョンの DocumentDB アカウントを作成する場合は、このセクションの指示に従います。 この作業は、Resource Manager テンプレートの有無にかかわらず、Azure CLI 1.0 を使用して行うことができます。

### <a id="create-single-documentdb-account-cli-arm"></a>Azure CLI 1.0 を使用して Resource Manager テンプレートを使わずに単一リージョンの DocumentDB アカウントを作成する
コマンド プロンプトで次のコマンドを入力し、新規または既存のリソース グループで DocumentDB アカウントを作成します。

> [!TIP]
> Azure PowerShell または Windows PowerShell でこのコマンドを実行すると、予想外のトークンに関するエラーが発生します。 代わりに、Windows PowerShell コマンド プロンプトでこのコマンドを実行します。
>
>

    azure resource create -g <resourcegroupname> -n <databaseaccountname> -r "Microsoft.DocumentDB/databaseAccounts" -o 2015-04-08 -l <resourcegrouplocation> -p "{\"databaseAccountOfferType\":\"Standard\",\"ipRangeFilter\":\"<ip-range-filter>\",\"locations\":["{\"locationName\":\"<databaseaccountlocation>\",\"failoverPriority\":\"<failoverPriority>\"}"]}"

* `<resourcegroupname>` に使用できる文字は、英数字、ピリオド、アンダースコア、「-」、かっこのみです。末尾にピリオドを指定することはできません。
* `<resourcegrouplocation>` は現在のリソース グループのリージョンです。
* `<ip-range-filter>` 特定のデータベース アカウントのクライアント IP の許可リストとして追加する一連の IP アドレスまたは IP アドレス範囲を CIDR 形式で指定する必要があります。 IP アドレス/範囲は、コンマで区切る必要があり、スペースを含めることはできません。 詳細については、「[DocumentDB のファイアウォール サポート](documentdb-firewall-support.md)」を参照してください。
* `<databaseaccountname>` に使用できる文字は、英小文字、数字、「-」のみです。文字数は 3 ～ 50 文字にする必要があります。
* `<databaseaccountlocation>` には、DocumentDB が一般公開されているリージョンのいずれかを指定します。 最新のリージョン一覧については、 [Azure のリージョン ページ](https://azure.microsoft.com/regions/#services)を参照してください。

入力例:

    azure resource create -g new_res_group -n samplecliacct -r "Microsoft.DocumentDB/databaseAccounts" -o 2015-04-08 -l westus -p "{\"databaseAccountOfferType\":\"Standard\",\"ipRangeFilter\":\"\",\"locations\":["{\"locationName\":\"westus\",\"failoverPriority\":\"0\"}"]}"

新しいアカウントがプロビジョニングされるとき、次の出力が生成されます。

    info:    Executing command resource create
    + Getting resource samplecliacct
    + Creating resource samplecliacct
    info:    Resource samplecliacct is updated
    data:
    data:    Id:        /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/new_res_group/providers/Microsoft.DocumentDB/databaseAccounts/samplecliacct
    data:    Name:      samplecliacct
    data:    Type:      Microsoft.DocumentDB/databaseAccounts
    data:    Parent:
    data:    Location:  West US
    data:    Tags:
    data:
    info:    resource create command OK

エラーが発生した場合、「 [トラブルシューティング](#troubleshooting)」を参照してください。

コマンドが終了すると、アカウントは数分間 "**作成中**" の状態になります。その後、"**オンライン**" の状態に変化し、使用する準備ができます。 [Azure Portal](https://portal.azure.com) の **[DocumentDB アカウント]** ブレードでアカウントの状態を確認できます。

### <a id="create-single-documentdb-account-cli-arm"></a>Azure CLI 1.0 を使用して Resource Manager テンプレートを使用して単一リージョンの DocumentDB アカウントを作成する
このセクションの手順では、Azure Resource Manager (ARM) テンプレートとオプションのパラメーター ファイル (いずれも JSON ファイル) を使用して DocumentDB アカウントを作成する方法について説明します。 テンプレートを利用すると、誤りなく必要なものを正確に表現し、それを繰り返すことができます。

次の内容でローカル テンプレート ファイルを作成します。 azuredeploy.json ファイルに名前を付けます。

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "type": "string"
            },
            "locationName1": {
                "type": "string"
            }
        },
        "variables": {},
        "resources": [
            {
                "apiVersion": "2015-04-08",
                "type": "Microsoft.DocumentDb/databaseAccounts",
                "name": "[parameters('databaseAccountName')]",
                "location": "[resourceGroup().location]",
                "properties": {
                    "databaseAccountOfferType": "Standard",
                    "ipRangeFilter": "",
                    "locations": [
                        {
                            "failoverPriority": 0,
                            "locationName": "[parameters('locationName1')]"
                        }
                    ]
                }
            }
        ]
    }

これは単一リージョンのアカウントなので、failoverPriority を 0 に設定する必要があります。 failoverPriority が 0 であるということは、このリージョンが [DocumentDB アカウントの書き込みリージョン][scaling-globally]として保持されることを表します。
コマンド ラインで値を入力するか、パラメーター ファイルを作成して値を指定できます。

パラメーター ファイルを作成するには、新しいファイルに次の内容をコピーし、azuredeploy.parameters.json ファイルに名前を付けます。 コマンド プロンプトでデータベース アカウント名を指定する場合、このファイルを作成せずに続行できます。

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "value": "samplearmacct"
            },
            "locationName1": {
                "value": "westus"
            }
        }
    }

azuredeploy.parameters.json ファイルで、`"samplearmacct"` という値を、使用するデータベースの名前に変更し、ファイルを保存します。 `"databaseAccountName"` に使用できる文字は、英小文字、数字、「-」のみです。文字数は 3 ～ 50 文字にする必要があります。 `"locationName1"` という値フィールドを、DocumentDB アカウントを作成するリージョンに変更します。

リソース グループで DocumentDB アカウントを作成するには、次のコマンドを実行し、テンプレート ファイルのパス、パラメーター ファイルまたはパラメーター値のパス、デプロイメントするリソース グループの名前、デプロイメント名を指定します (-n は任意です)。

パラメーター ファイルを使用するには:

    azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g <resourcegroupname> -n <deploymentname>

* `<PathToTemplate>` は、手順 1 で作成した azuredeploy.json ファイルのパスです。 パス名にスペースが含まれる場合は、二重引用符でこのパラメーターを囲みます。
* `<PathToParameterFile>` は、手順 1 で作成した azuredeploy.parameters.json ファイルのパスです。 パス名にスペースが含まれる場合は、二重引用符でこのパラメーターを囲みます。
* `<resourcegroupname>` は、DocumentDB データベース アカウントを追加する既存のリソース グループ名です。
* `<deploymentname>` は、デプロイメント名 (省略可能) です。

入力例:

    azure group deployment create -f azuredeploy.json -e azuredeploy.parameters.json -g new_res_group -n azuredeploy

あるいは、パラメーター ファイルなしでデータベース アカウント名パラメーターを指定し、代わりに値の入力を求めさせるには、次のコマンドを実行します。

    azure group deployment create -f <PathToTemplate> -g <resourcegroupname> -n <deploymentname>

入力例 ("samplearmacct" というデータベース アカウントのプロンプトとエントリが表示されます):

    azure group deployment create -f azuredeploy.json -g new_res_group -n azuredeploy
    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    databaseAccountName: samplearmacct

アカウントがプロビジョニングされると、次の情報を受け取ります。

    info:    Executing command group deployment create
    + Initializing template configurations and parameters
    + Creating a deployment
    info:    Created template deployment "azuredeploy"
    + Waiting for deployment to complete
    +
    +
    info:    Resource 'new_res_group' of type 'Microsoft.DocumentDb/databaseAccounts' provisioning status is Running
    +
    info:    Resource 'new_res_group' of type 'Microsoft.DocumentDb/databaseAccounts' provisioning status is Succeeded
    data:    DeploymentName     : azuredeploy
    data:    ResourceGroupName  : new_res_group
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : 2015-11-30T18:50:23.6300288Z
    data:    Mode               : Incremental
    data:    CorrelationId      : 4a5d4049-c494-4053-bad4-cc804d454700
    data:    DeploymentParameters :
    data:    Name                 Type    Value
    data:    -------------------  ------  ------------------
    data:    databaseAccountName  String  samplearmacct
    data:    locationName1        String  westus
    info:    group deployment create command OK

エラーが発生した場合、「 [トラブルシューティング](#troubleshooting)」を参照してください。  

コマンドが終了すると、アカウントは数分間 "**作成中**" の状態になります。その後、"**オンライン**" の状態に変化し、使用する準備ができます。 [Azure Portal](https://portal.azure.com) の **[DocumentDB アカウント]** ブレードでアカウントの状態を確認できます。

## <a id="quick-create-documentdb-with-mongodb-api-account"></a>タスク: 単一リージョンの DocumentDB を作成する: MongoDB アカウント用の API
単一リージョンの MongoDB アカウント用 API を作成する場合は、このセクションの指示に従います。 この操作は、Azure CLI 1.0 で Resource Manager テンプレートを使用して実行できます。

### <a id="create-single-documentdb-with-mongodb-api-account-cli-arm"></a>Azure CLI 1.0 と Resource Manager テンプレートを使用して単一リージョンの MongoDB アカウントを作成する
このセクションの手順では、Azure Resource Manager (ARM) テンプレートとオプションのパラメーター ファイル (いずれも JSON ファイル) を使用して MongoDB アカウント用の API を作成する方法について説明します。 テンプレートを利用すると、誤りなく必要なものを正確に表現し、それを繰り返すことができます。

次の内容でローカル テンプレート ファイルを作成します。 azuredeploy.json ファイルに名前を付けます。

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "type": "string"
            },
            "locationName1": {
                "type": "string"
            }
        },
        "variables": {},
        "resources": [
            {
                "apiVersion": "2015-04-08",
                "type": "Microsoft.DocumentDb/databaseAccounts",
                "name": "[parameters('databaseAccountName')]",
                "location": "[resourceGroup().location]",
                "kind": "MongoDB",
                "properties": {
                    "databaseAccountOfferType": "Standard",
                    "ipRangeFilter": "",
                    "locations": [
                        {
                            "failoverPriority": 0,
                            "locationName": "[parameters('locationName1')]"
                        }
                    ]
                }
            }
        ]
    }

kind には MongoDB を設定して、このアカウントが MongoDB API をサポートすることを指定する必要があります。 kind プロパティが指定されない場合の既定値は、ネイティブの DocumentDB アカウントになります。

これは単一リージョンのアカウントなので、failoverPriority を 0 に設定する必要があります。 failoverPriority が 0 であるということは、このリージョンが [DocumentDB アカウントの書き込みリージョン][scaling-globally]として保持されることを表します。
コマンド ラインで値を入力するか、パラメーター ファイルを作成して値を指定できます。

パラメーター ファイルを作成するには、新しいファイルに次の内容をコピーし、azuredeploy.parameters.json ファイルに名前を付けます。 コマンド プロンプトでデータベース アカウント名を指定する場合、このファイルを作成せずに続行できます。

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "value": "samplearmacct"
            },
            "locationName1": {
                "value": "westus"
            }
        }
    }

azuredeploy.parameters.json ファイルで、`"samplearmacct"` という値を、使用するデータベースの名前に変更し、ファイルを保存します。 `"databaseAccountName"` に使用できる文字は、英小文字、数字、「-」のみです。文字数は 3 ～ 50 文字にする必要があります。 `"locationName1"` という値フィールドを、DocumentDB アカウントを作成するリージョンに変更します。

リソース グループで DocumentDB アカウントを作成するには、次のコマンドを実行し、テンプレート ファイルのパス、パラメーター ファイルまたはパラメーター値のパス、デプロイメントするリソース グループの名前、デプロイメント名を指定します (-n は任意です)。

パラメーター ファイルを使用するには:

    azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g <resourcegroupname> -n <deploymentname>

* `<PathToTemplate>` は、手順 1 で作成した azuredeploy.json ファイルのパスです。 パス名にスペースが含まれる場合は、二重引用符でこのパラメーターを囲みます。
* `<PathToParameterFile>` は、手順 1 で作成した azuredeploy.parameters.json ファイルのパスです。 パス名にスペースが含まれる場合は、二重引用符でこのパラメーターを囲みます。
* `<resourcegroupname>` は、DocumentDB データベース アカウントを追加する既存のリソース グループ名です。
* `<deploymentname>` は、デプロイメント名 (省略可能) です。

入力例:

    azure group deployment create -f azuredeploy.json -e azuredeploy.parameters.json -g new_res_group -n azuredeploy

あるいは、パラメーター ファイルなしでデータベース アカウント名パラメーターを指定し、代わりに値の入力を求めさせるには、次のコマンドを実行します。

    azure group deployment create -f <PathToTemplate> -g <resourcegroupname> -n <deploymentname>

入力例 ("samplearmacct" というデータベース アカウントのプロンプトとエントリが表示されます):

    azure group deployment create -f azuredeploy.json -g new_res_group -n azuredeploy
    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    databaseAccountName: samplearmacct

アカウントがプロビジョニングされると、次の情報を受け取ります。

    info:    Executing command group deployment create
    + Initializing template configurations and parameters
    + Creating a deployment
    info:    Created template deployment "azuredeploy"
    + Waiting for deployment to complete
    +
    +
    info:    Resource 'new_res_group' of type 'Microsoft.DocumentDb/databaseAccounts' provisioning status is Running
    +
    info:    Resource 'new_res_group' of type 'Microsoft.DocumentDb/databaseAccounts' provisioning status is Succeeded
    data:    DeploymentName     : azuredeploy
    data:    ResourceGroupName  : new_res_group
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : 2015-11-30T18:50:23.6300288Z
    data:    Mode               : Incremental
    data:    CorrelationId      : 4a5d4049-c494-4053-bad4-cc804d454700
    data:    DeploymentParameters :
    data:    Name                 Type    Value
    data:    -------------------  ------  ------------------
    data:    databaseAccountName  String  samplearmacct
    data:    locationName1        String  westus
    info:    group deployment create command OK

エラーが発生した場合、「 [トラブルシューティング](#troubleshooting)」を参照してください。  

コマンドが終了すると、アカウントは数分間 "**作成中**" の状態になります。その後、"**オンライン**" の状態に変化し、使用する準備ができます。 [Azure Portal](https://portal.azure.com) の **[DocumentDB アカウント]** ブレードでアカウントの状態を確認できます。

## <a id="create-multi-documentdb-account"></a>作業: 複数リージョンの DocumentDB アカウントを作成する
DocumentDB には、さまざまな [Azure リージョン](https://azure.microsoft.com/regions/#services)にわたって[データをグローバルに分散する][distribute-globally]機能が備わっています。 DocumentDB アカウントを作成するときに、サービスを存在させるリージョンを指定できます。 複数リージョンの DocumentDB アカウントを作成する場合は、このセクションの指示に従います。 この作業は、Resource Manager テンプレートの有無にかかわらず、Azure CLI 1.0 を使用して行うことができます。

### <a id="create-multi-documentdb-account-cli"></a>Azure CLI 1.0 を使用して Resource Manager テンプレートなしで複数リージョンの DocumentDB アカウントを作成する
コマンド プロンプトで次のコマンドを入力し、新規または既存のリソース グループで DocumentDB アカウントを作成します。

> [!TIP]
> Azure PowerShell または Windows PowerShell でこのコマンドを実行すると、予想外のトークンに関するエラーが発生します。 代わりに、Windows PowerShell コマンド プロンプトでこのコマンドを実行します。
>
>

    azure resource create -g <resourcegroupname> -n <databaseaccountname> -r "Microsoft.DocumentDB/databaseAccounts" -o 2015-04-08 -l <resourcegrouplocation> -p "{\"databaseAccountOfferType\":\"Standard\",\"ipRangeFilter\":\"<ip-range-filter>\",\"locations\":["{\"locationName\":\"<databaseaccountlocation1>\",\"failoverPriority\":\"<failoverPriority1>\"},{\"locationName\":\"<databaseaccountlocation2>\",\"failoverPriority\":\"<failoverPriority2>\"}"]}"

* `<resourcegroupname>` に使用できる文字は、英数字、ピリオド、アンダースコア、「-」、かっこのみです。末尾にピリオドを指定することはできません。
* `<resourcegrouplocation>` は現在のリソース グループのリージョンです。
* `<ip-range-filter>` 特定のデータベース アカウントのクライアント IP の許可リストとして追加する一連の IP アドレスまたは IP アドレス範囲を CIDR 形式で指定する必要があります。 IP アドレス/範囲は、コンマで区切る必要があり、スペースを含めることはできません。 詳細については、「[DocumentDB のファイアウォール サポート](documentdb-firewall-support.md)」を参照してください。
* `<databaseaccountname>` に使用できる文字は、英小文字、数字、「-」のみです。文字数は 3 ～ 50 文字にする必要があります。
* `<databaseaccountlocation1>` および `<databaseaccountlocation2>` には、DocumentDB が一般公開されているリージョンを指定します。 最新のリージョン一覧については、 [Azure のリージョン ページ](https://azure.microsoft.com/regions/#services)を参照してください。

入力例:

    azure resource create -g new_res_group -n samplecliacct -r "Microsoft.DocumentDB/databaseAccounts" -o 2015-04-08 -l westus -p "{\"databaseAccountOfferType\":\"Standard\",\"ipRangeFilter\":\"\",\"locations\":["{\"locationName\":\"westus\",\"failoverPriority\":\"0\"},{\"locationName\":\"eastus\",\"failoverPriority\":\"1\"}"]}"

新しいアカウントがプロビジョニングされるとき、次の出力が生成されます。

    info:    Executing command resource create
    + Getting resource samplecliacct
    + Creating resource samplecliacct
    info:    Resource samplecliacct is updated
    data:
    data:    Id:        /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/new_res_group/providers/Microsoft.DocumentDB/databaseAccounts/samplecliacct
    data:    Name:      samplecliacct
    data:    Type:      Microsoft.DocumentDB/databaseAccounts
    data:    Parent:
    data:    Location:  West US
    data:    Tags:
    data:
    info:    resource create command OK

エラーが発生した場合、「 [トラブルシューティング](#troubleshooting)」を参照してください。

コマンドが終了すると、アカウントは数分間 "**作成中**" の状態になります。その後、"**オンライン**" の状態に変化し、使用する準備ができます。 [Azure Portal](https://portal.azure.com) の **[DocumentDB アカウント]** ブレードでアカウントの状態を確認できます。

### <a id="create-multi-documentdb-account-cli-arm"></a>Azure CLI 1.0 を使用して Resource Manager テンプレートを使用した複数リージョンの DocumentDB アカウントを作成する
このセクションの手順では、Azure Resource Manager (ARM) テンプレートとオプションのパラメーター ファイル (いずれも JSON ファイル) を使用して DocumentDB アカウントを作成する方法について説明します。 テンプレートを利用すると、誤りなく必要なものを正確に表現し、それを繰り返すことができます。

次の内容でローカル テンプレート ファイルを作成します。 azuredeploy.json ファイルに名前を付けます。

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "type": "string"
            },
            "locationName1": {
                "type": "string"
            },
            "locationName2": {
                "type": "string"
            }
        },
        "variables": {},
        "resources": [
            {
                "apiVersion": "2015-04-08",
                "type": "Microsoft.DocumentDb/databaseAccounts",
                "name": "[parameters('databaseAccountName')]",
                "location": "[resourceGroup().location]",
                "properties": {
                    "databaseAccountOfferType": "Standard",
                    "ipRangeFilter": "",
                    "locations": [
                        {
                            "failoverPriority": 0,
                            "locationName": "[parameters('locationName1')]"
                        },
                        {
                            "failoverPriority": 1,
                            "locationName": "[parameters('locationName2')]"
                        }
                    ]
                }
            }
        ]
    }

上記のテンプレート ファイルを使用すると、2 つのリージョンを持つ DocumentDB アカウントを作成できます。 さらに多くのリージョンを持つアカウントを作成するには、リージョンを "locations" 配列に追加し、対応するパラメーターを追加します。

いずれかのリージョンの failoverPriority 値は 0 にする必要があります。これは、このリージョンが [DocumentDB アカウントの書き込みリージョン][scaling-globally]として保持されることを表します。 フェールオーバー優先度の値は、各場所間で一意である必要があり、フェールオーバー優先度の最大値は、リージョンの合計数よりも小さい必要があります。 コマンド ラインで値を入力するか、パラメーター ファイルを作成して値を指定できます。

パラメーター ファイルを作成するには、新しいファイルに次の内容をコピーし、azuredeploy.parameters.json ファイルに名前を付けます。 コマンド プロンプトでデータベース アカウント名を指定する場合、このファイルを作成せずに続行できます。

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "value": "samplearmacct"
            },
            "locationName1": {
                "value": "westus"
            },
            "locationName2": {
                "value": "eastus"
            }
        }
    }

azuredeploy.parameters.json ファイルで、`"samplearmacct"` という値を、使用するデータベースの名前に変更し、ファイルを保存します。 `"databaseAccountName"` に使用できる文字は、英小文字、数字、「-」のみです。文字数は 3 ～ 50 文字にする必要があります。 `"locationName1"` と `"locationName2"` の値フィールドを、DocumentDB アカウントを作成するリージョンに変更します。

リソース グループで DocumentDB アカウントを作成するには、次のコマンドを実行し、テンプレート ファイルのパス、パラメーター ファイルまたはパラメーター値のパス、デプロイメントするリソース グループの名前、デプロイメント名を指定します (-n は任意です)。

パラメーター ファイルを使用するには:

    azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g <resourcegroupname> -n <deploymentname>

* `<PathToTemplate>` は、手順 1 で作成した azuredeploy.json ファイルのパスです。 パス名にスペースが含まれる場合は、二重引用符でこのパラメーターを囲みます。
* `<PathToParameterFile>` は、手順 1 で作成した azuredeploy.parameters.json ファイルのパスです。 パス名にスペースが含まれる場合は、二重引用符でこのパラメーターを囲みます。
* `<resourcegroupname>` は、DocumentDB データベース アカウントを追加する既存のリソース グループ名です。
* `<deploymentname>` は、デプロイメント名 (省略可能) です。

入力例:

    azure group deployment create -f azuredeploy.json -e azuredeploy.parameters.json -g new_res_group -n azuredeploy

あるいは、パラメーター ファイルなしでデータベース アカウント名パラメーターを指定し、代わりに値の入力を求めさせるには、次のコマンドを実行します。

    azure group deployment create -f <PathToTemplate> -g <resourcegroupname> -n <deploymentname>

入力例 ("samplearmacct" というデータベース アカウントのプロンプトとエントリが表示されます):

    azure group deployment create -f azuredeploy.json -g new_res_group -n azuredeploy
    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    databaseAccountName: samplearmacct

アカウントがプロビジョニングされると、次の情報を受け取ります。

    info:    Executing command group deployment create
    + Initializing template configurations and parameters
    + Creating a deployment
    info:    Created template deployment "azuredeploy"
    + Waiting for deployment to complete
    +
    +
    info:    Resource 'new_res_group' of type 'Microsoft.DocumentDb/databaseAccounts' provisioning status is Running
    +
    info:    Resource 'new_res_group' of type 'Microsoft.DocumentDb/databaseAccounts' provisioning status is Succeeded
    data:    DeploymentName     : azuredeploy
    data:    ResourceGroupName  : new_res_group
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : 2015-11-30T18:50:23.6300288Z
    data:    Mode               : Incremental
    data:    CorrelationId      : 4a5d4049-c494-4053-bad4-cc804d454700
    data:    DeploymentParameters :
    data:    Name                 Type    Value
    data:    -------------------  ------  ------------------
    data:    databaseAccountName  String  samplearmacct
    data:    locationName1        String  westus
    data:    locationName2        String  eastus
    info:    group deployment create command OK

エラーが発生した場合、「 [トラブルシューティング](#troubleshooting)」を参照してください。  

コマンドが終了すると、アカウントは数分間 "**作成中**" の状態になります。その後、"**オンライン**" の状態に変化し、使用する準備ができます。 [Azure Portal](https://portal.azure.com) の **[DocumentDB アカウント]** ブレードでアカウントの状態を確認できます。

## <a name="troubleshooting"></a>トラブルシューティング
リソース グループまたはデータベース アカウントの作成中に `Deployment provisioning state was not successful` のようなエラーが発生する場合、いくつかのトラブルシューティング方法があります。

> [!NOTE]
> データベース アカウント名に正しくない文字を指定するか、DocumentDB を使用できない場所を指定すると、デプロイメント エラーが発生します。 データベース アカウント名に使用できる文字は、英小文字、数字、「-」のみです。文字数は 3 ～ 50 文字にする必要があります。 有効なデータベース アカウントの場所一覧については、[Azure リージョンのページ](https://azure.microsoft.com/regions/#services)を参照してください。
>
>

* 出力に次の `Error information has been recorded to C:\Users\wendy\.azure\azure.err`が含まれている場合は、azure.err ファイルでエラー情報を確認してください。
* このログ ファイルには、リソース グループの役立つ情報が記録されている場合があります。 ログ ファイルを表示するには、次のコマンドを実行します。

        azure group log show <resourcegroupname> --last-deployment

    入力例:

        azure group log show new_res_group --last-deployment

    追加情報については、「 [Azure のリソース グループ デプロイのトラブルシューティング](../azure-resource-manager/resource-manager-common-deployment-errors.md) 」を参照してください。
* 次のスクリーンショットのように、エラー情報は Azure Portal でも確認できます。 エラー情報に移動するには: ジャンプバーで [リソース グループ] をクリックし、エラーが発生したリソース グループを選択します。次に、[リソース グループ] ブレードの [Essentials] 領域で [前回のデプロイ] の日付をクリックし、[デプロイ履歴] ブレードで失敗したデプロイを選択し、[デプロイ] ブレードで赤い感嘆符が付いた [操作の詳細] 詳細をクリックします。 失敗したデプロイメントの状態メッセージが [操作の詳細] ブレードに表示されます。

    ![デプロイメント エラー メッセージに移動する方法を示す Azure ポータルのスクリーンショット](media/documentdb-automation-resource-manager-cli/portal-troubleshooting-deploy.png)

## <a name="next-steps"></a>次のステップ
DocumentDB アカウントを作成できたら、次の手順として DocumentDB データベースを作成します。 データベースを作成するには、次のいずれかを使用します。

* Azure Portal。[Azure Portal を使用した DocumentDB コレクションとデータベースの作成](documentdb-create-collection.md)に関する記事をご覧ください。
* GitHub の [azure-documentdb-dotnet](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples) リポジトリの [DatabaseManagement](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/DatabaseManagement) プロジェクトにある C# .NET のサンプル。
* [DocumentDB SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx)。 DocumentDB には、.NET、Java、Python、Node.js、および JavaScript API の SDK があります。

データベースを作成した後に、データベースに [1 つまたは複数のコレクションを追加](documentdb-create-collection.md)し、それらのコレクションに[ドキュメントを追加する](documentdb-view-json-document-explorer.md)必要があります。

コレクションにドキュメントを用意した後で、ポータルの[クエリ エクスプローラー](documentdb-query-collections-query-explorer.md)、[REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx)、またはいずれかの [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx) を使用することで、[DocumentDB SQL](documentdb-sql-query.md) を使用してドキュメントに対して[クエリを実行](documentdb-sql-query.md#ExecutingSqlQueries)できます。

DocumentDB の詳細については、以下のリソースを参照してください。

* [DocumentDB のラーニング パス](https://azure.microsoft.com/documentation/learning-paths/documentdb/)
* [DocumentDB のリソース モデルと概念](documentdb-resources.md)

使用できる他のテンプレートについては、「 [Azure クイックスタート テンプレート](https://azure.microsoft.com/documentation/templates/)」を参照してください。

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[distribute-globally]: https://azure.microsoft.com/en-us/documentation/articles/documentdb-distribute-data-globally
[scaling-globally]: https://azure.microsoft.com/en-us/documentation/articles/documentdb-distribute-data-globally/#scaling-across-the-planet

