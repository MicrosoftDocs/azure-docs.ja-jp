---
title: "Azure Key Vault のログ記録 | Microsoft Docs"
description: "このチュートリアルを使用すれば、Azure Key Vault のログ記録を容易に開始できます。"
services: key-vault
documentationcenter: 
author: cabailey
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 43f96a2b-3af8-4adc-9344-bc6041fface8
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/07/2017
ms.author: cabailey
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 51732acdad74dd6dbfc47fae62efc87df6ce5c15
ms.lasthandoff: 03/14/2017


---
# <a name="azure-key-vault-logging"></a>Azure Key Vault のログ記録
Azure Key Vault は、ほとんどのリージョンで使用できます。 詳細については、 [Key Vault の価格のページ](https://azure.microsoft.com/pricing/details/key-vault/)を参照してください。

## <a name="introduction"></a>はじめに
1 つまたは複数の Key Vault を作成したら、いつ、どのように、誰によって Key Vault がアクセスされるのかを監視するのが一般的です。 監視を行うには、Key Vault のログ記録を有効にします。これにより、指定した Azure ストレージ アカウントに情報が保存されます。 指定したストレージ アカウントに対して **insights-logs-auditevent** という名前の新しいコンテナーが自動的に作成されます。このストレージ アカウントを使用して複数の Key Vault のログを収集することができます。

Key Vault の操作を行ってから、遅くとも 10 分後には、ログ情報にはアクセスできます。 ほとんどの場合は、これよりも早く確認できます。  ストレージ アカウントでのログの管理はお客様に委ねられます。

* ログにアクセスできるユーザーを制限することでログのセキュリティを保護するには、標準的な Azure アクセス制御方法を使用します。
* ストレージ アカウントに保持する必要がなくなったログは削除します。

このチュートリアルでは、Azure Key Vault のログ記録を開始する際に役立つ情報を提供し、ストレージ アカウントを作成し、ログ記録を有効にし、収集されたログ情報を解釈します。  

> [!NOTE]
> Key Vault、キー、またはシークレットの作成方法については、このチュートリアルに含まれていません。 詳細については、「 [Azure Key Vault の概要](key-vault-get-started.md)」を参照してください。 また、クロスプラットフォーム コマンドライン インターフェイスの手順については、 [対応するチュートリアル](key-vault-manage-with-cli.md)をご覧ください。
> 
> 現時点では、Azure ポータルで Azure Key Vault を構成できません。 代わりに、Azure PowerShell 命令を使用します。
> 
> 

Azure Key Vault の概要については、「 [Azure Key Vault とは](key-vault-whatis.md)

## <a name="prerequisites"></a>前提条件
このチュートリアルを完了するには、以下が必要です。

* 使用している既存の Key Vault。  
* Azure PowerShell **1.0.1 以降のバージョン**。 Azure PowerShell をインストールして、Azure サブスクリプションに関連付けるには、「 [Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs)」を参照してください。 Azure PowerShell をインストール済みで、バージョンがわからない場合は、Azure PowerShell コンソールで「 `(Get-Module azure -ListAvailable).Version`」と入力します。  
* Azure 上に確保された Key Vault のログを格納するための十分なストレージ。

## <a id="connect"></a>サブスクリプションへの接続
Azure PowerShell セッションを開始し、次のコマンドで Azure アカウントにサインインします。  

    Login-AzureRmAccount

ポップアップ ブラウザー ウィンドウで、Azure アカウントのユーザー名とパスワードを入力します。 Azure PowerShell は、このアカウントに関連付けられているすべてのサブスクリプションを取得し、既定で最初のサブスクリプションを使用します。

複数のサブスクリプションをお持ちの場合は、Azure Key Vault を作成するときに使用した特定の&1; つを指定することが必要なことがあります。 アカウントのサブスクリプションを確認するには、次を入力します。

    Get-AzureRmSubscription

ログ記録する Key Vault に関連付けられているサブスクリプションを指定するには、次を入力します。

    Set-AzureRmContext -SubscriptionId <subscription ID>

> [!NOTE]
> これは重要な手順で、アカウントに複数のサブスクリプションが関連付けられている場合に特に便利です。 この手順をスキップすると、Microsoft.Insights を登録する際にエラーが発生する可能性があります。 
>   
>

Azure PowerShell の構成の詳細については、「[Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs)」をご覧ください。

## <a id="storage"></a>ログ用に新しいストレージ アカウントを作成する
既存のストレージ アカウントをログのために使用できますが、Key Vault のログ専用に新しいストレージ アカウントを作成することにします。 後でこれを指定することが必要になる場合に備えて、詳細情報を **sa**という名前の変数に格納します。

また、管理を容易にするために、当該 Key Vault が含まれているリソース グループと同じリソース グループを使用します。 [入門チュートリアル](key-vault-get-started.md)に基づいて、このリソース グループには **ContosoResourceGroup** という名前を付け、東アジアの場所を引き続き使用します。 これらの値は、必要に応じて実際の値に置き換えてください。

    $sa = New-AzureRmStorageAccount -ResourceGroupName ContosoResourceGroup -Name contosokeyvaultlogs -Type Standard_LRS -Location 'East Asia'


> [!NOTE]
> 既存のストレージ アカウントを使用する場合、そのストレージ アカウントでは、目的の Key Vault と同じサブスクリプションを使用する必要があります。また、クラシック デプロイメント モデルではなく、リソース マネージャー デプロイメント モデルを使用する必要があります。
> 
> 

## <a id="identify"></a>ログに対する Key Vault を識別する
入門チュートリアルで、Key Vault の名前は **ContosoKeyVault** としたので、この名前を引き続き使用し、詳細情報を **kv** という名前の変数に格納します。

    $kv = Get-AzureRmKeyVault -VaultName 'ContosoKeyVault'


## <a id="enable"></a>ログの有効化
Key Vault のログ記録を有効にするために、AzureRmDiagnosticSetting コマンドレットを、新しいストレージ アカウントおよび当該 Key Vault 用に作成した変数と組み合わせて使用します。 また、**-Enabled** フラグを **$true** に設定し、カテゴリを AuditEvent (Key Vault ログ記録専用のカテゴリ) に設定します。

    Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent

この出力は次のとおりです。

    StorageAccountId   : /subscriptions/<subscription-GUID>/resourceGroups/ContosoResourceGroup/providers/Microsoft.Storage/storageAccounts/ContosoKeyVaultLogs
    ServiceBusRuleId   :
    StorageAccountName :
        Logs
        Enabled           : True
        Category          : AuditEvent
        RetentionPolicy
        Enabled : False
        Days    : 0


これを見れば、ログ記録が Key Vault に対して有効になっていること、ストレージ アカウントに情報が保存されることを確認できます。

必要に応じて、ログのリテンション期間ポリシーを使用して、古いログが自動的に削除されるように設定することもできます。 たとえば、**-RetentionEnabled** フラグを **$true** に設定し、**-RetentionInDays** パラメーターを **90** に設定したリテンション期間ポリシーを設定すると、90 日より前のログが自動的に削除されます。

    Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent -RetentionEnabled $true -RetentionInDays 90

ログ記録の内容:

* 認証されたすべての REST API 要求がログ記録されます。これには、アクセス許可がないため、システム エラーのため、または不正な要求の結果として、失敗した要求が含まれます。
* Key Vault 自体に関する操作。これには、作成、削除、Key Vault アクセス ポリシーの設定、Key Vault 属性 (タグなど) の更新が含まれます。
* Key Vault 内のキーおよびシークレットに関する操作。これには、キーまたはシークレットの作成、変更、または削除といった操作に加えて、キーの署名、確認、暗号化、複合化、ラップ、およびラップ解除、シークレットの取得、キーとシークレットとこれらのバージョンの一覧表示などの操作も含まれます。
* 結果として 401 応答が発生する、認証されていない要求。 たとえば、ベアラー トークンを持たない要求、形式が正しくない要求、有効期限切れの要求、または無効なトークンを持つ要求です。  

## <a id="access"></a>ログへのアクセス
Key Vault のログは、指定したストレージ アカウント内の **insights-logs-auditevent** コンテナーに格納されます。 このコンテナー内のすべての BLOB を一覧表示するには、次のように入力します。

最初に、コンテナー名の変数を作成します。 この変数は、チュートリアルの残りの部分で使用します。

    $container = 'insights-logs-auditevent'

このコンテナー内のすべての BLOB を一覧表示するには、次のように入力します。

    Get-AzureStorageBlob -Container $container -Context $sa.Context
出力は次のようになります。

**Container Uri: https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent**

**名前**

- - -
**resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json**

**resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json**

**resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json****

この出力からわかるように、BLOB は次の命名規則に従います。**resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json**

日付と時刻の値には UTC が使用されます。

同じストレージ アカウントを使用して複数のリソースのログを収集することができるので、必要な BLOB のみにアクセスしたり、ダウンロードしたりする場合には、BLOB 名の完全なリソース ID を使用すると便利です。 その前に、すべての BLOB をダウンロードする方法を説明します。

まず、フォルダーを作成して BLOB をダウンロードします。 For example:

    New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force

すべての BLOB の一覧を取得します。  

    $blobs = Get-AzureStorageBlob -Container $container -Context $sa.Context

'Get-AzureStorageBlobContent' を介してこの一覧をパイプして、BLOB を宛先フォルダーにダウンロードします。

    $blobs | Get-AzureStorageBlobContent -Destination 'C:\Users\username\ContosoKeyVaultLogs'

この&2; 番目のコマンドを実行すると、BLOB 名に含まれる **/** 区切り記号によって、宛先フォルダーの下にフォルダー構造全体が作成されます。このフォルダー構造は、BLOB をファイルとしてダウンロードし、保存するために使用されます。

BLOB を選択的にダウンロードするには、ワイルドカードを使用します。 For example:

* 複数の Key Vault を持っている場合に、CONTOSOKEYVAULT3 という名前の Key Vault のみについてログをダウンロードするには、次のようにします。
  
        Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
* 複数のリソース グループを持っている場合、1 つのリソース グループのみについてログをダウンロードするには、次のように `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`を使用します。
  
        Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
* 2016 年 1 月のすべてのログをダウンロードする場合は、次のように `-Blob '*/year=2016/m=01/*'`を使用します。
  
        Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'

これで、ログの内容を検討する準備が整いました。 ただし、ログの内容の検討に入る前に、Get-AzureRmDiagnosticSetting 用のさらに&2; つのパラメーターを把握しておく必要があります。

* Key Vault リソースの診断設定の状態のクエリを実行するためのパラメーター: `Get-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId`
* Key Vault リソースのログ記録を無効にするためのパラメーター: `Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories AuditEvent`

## <a id="interpret"></a>Key Vault のログを解釈する
個々の BLOB はテキストとして格納されます (JSON BLOB 形式)。 `Get-AzureRmKeyVault -VaultName 'contosokeyvault'`を実行して得られたログ エントリの例を次に示します。

    {
        "records":
        [
            {
                "time": "2016-01-05T01:32:01.2691226Z",
                "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
                "operationName": "VaultGet",
                "operationVersion": "2015-06-01",
                "category": "AuditEvent",
                "resultType": "Success",
                "resultSignature": "OK",
                "resultDescription": "",
                "durationMs": "78",
                "callerIpAddress": "104.40.82.76",
                "correlationId": "",
                "identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
                "properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
            }
        ]
    }


次の表にフィールド名と説明を示します。

| フィールド名 | Description |
| --- | --- |
| time |日付と時刻 (UTC)。 |
| resourceId |Azure リソース マネージャー リソース ID。 Key Vault のログの場合は、常に Key Vault リソース ID となります。 |
| operationName |次の表に示すような操作の名前です。 |
| operationVersion |クライアントによって要求される REST API バージョンです。 |
| カテゴリ |Key Vault のログの場合、AuditEvent は使用可能な単一の値です。 |
| resultType |REST API 要求の結果です。 |
| resultSignature |HTTP の状態です。 |
| resultDescription |結果に関する追加の説明です (使用可能な場合)。 |
| durationMs |REST API 要求を処理するのにかかった時間 (ミリ秒単位) です。 これにネットワーク待機時間は含まれません。したがって、クライアント側で測定する時間はこの時間と一致しない場合があります。 |
| callerIpAddress |要求を行ったクライアントの IP アドレスです。 |
| correlationId |オプションの GUID であり、クライアント側のログとサービス側の (Key Vault) ログを対応付ける場合に渡します。 |
| ID |REST API 要求を行う場合に提示されたトークンからの ID です。 これは、通常、Azure PowerShell コマンドレットの実行結果として生じる要求の場合と同様に、"user"、"service principal"、または組み合わせ "user+appId" となります。 |
| プロパティ |このフィールドには、操作に基づくさまざまな情報が含まれます (operationName)。 ほとんどの場合は、クライアント情報 (クライアントから渡された useragent 文字列)、正確な REST API 要求 URI、および HTTP 状態コードが含まれます。 さらに、要求 (KeyCreate または VaultGet など) を行った結果としてオブジェクトが返される場合は、キーの URI ("id" として)、資格情報コンテナーの URI、またはシークレットの URI も含まれます。 |

**operationName** フィールドの値は、ObjectVerb 形式となります。 次に例を示します。

* Key Vault に関するすべての操作は、"Vault`<action>`" 形式となります (`VaultGet` や `VaultCreate` など)。
* キーに関するすべての操作は、"Key`<action>`" 形式となります (`KeySign` や `KeyList` など)。
* シークレットに関するすべての操作は、"Secret`<action>`" 形式となります (`SecretGet` や `SecretListVersions` など)。

次の表に、operationName と、対応する REST API コマンドを一覧します。

| operationName | REST API コマンド |
| --- | --- |
| 認証 |Azure Active Directory エンドポイント経由 |
| VaultGet |[キー コンテナーに関する情報を取得します](https://msdn.microsoft.com/en-us/library/azure/mt620026.aspx) |
| VaultPut |[キー コンテナーを作成または更新します](https://msdn.microsoft.com/en-us/library/azure/mt620025.aspx) |
| VaultDelete |[キー コンテナーを削除します](https://msdn.microsoft.com/en-us/library/azure/mt620022.aspx) |
| VaultPatch |[Key Vault を更新します](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| VaultList |[リソース グループ内のすべてのキー コンテナーを一覧表示します](https://msdn.microsoft.com/en-us/library/azure/mt620027.aspx) |
| KeyCreate |[キーを作成します](https://msdn.microsoft.com/en-us/library/azure/dn903634.aspx) |
| KeyGet |[キーに関する情報を取得します](https://msdn.microsoft.com/en-us/library/azure/dn878080.aspx) |
| KeyImport |[コンテナーにキーをインポートします](https://msdn.microsoft.com/en-us/library/azure/dn903626.aspx) |
| KeyBackup |[キーをバックアップします](https://msdn.microsoft.com/en-us/library/azure/dn878058.aspx) |
| KeyDelete |[キーを削除します](https://msdn.microsoft.com/en-us/library/azure/dn903611.aspx) |
| KeyRestore |[キーを復元します](https://msdn.microsoft.com/en-us/library/azure/dn878106.aspx) |
| KeySign |[キーで署名します](https://msdn.microsoft.com/en-us/library/azure/dn878096.aspx) |
| KeyVerify |[キーで確認します](https://msdn.microsoft.com/en-us/library/azure/dn878082.aspx) |
| KeyWrap |[キーをラップします](https://msdn.microsoft.com/en-us/library/azure/dn878066.aspx) |
| KeyUnwrap |[キーのラップを解除します](https://msdn.microsoft.com/en-us/library/azure/dn878079.aspx) |
| KeyEncrypt |[キーで暗号化します](https://msdn.microsoft.com/en-us/library/azure/dn878060.aspx) |
| KeyDecrypt |[キーで復号化します](https://msdn.microsoft.com/en-us/library/azure/dn878097.aspx) |
| KeyUpdate |[キーを更新します](https://msdn.microsoft.com/en-us/library/azure/dn903616.aspx) |
| KeyList |[コンテナー内のキーを一覧表示します](https://msdn.microsoft.com/en-us/library/azure/dn903629.aspx) |
| KeyListVersions |[キーのバージョンを一覧表示します](https://msdn.microsoft.com/en-us/library/azure/dn986822.aspx) |
| SecretSet |[シークレットを作成します](https://msdn.microsoft.com/en-us/library/azure/dn903618.aspx) |
| SecretGet |[シークレットを取得します](https://msdn.microsoft.com/en-us/library/azure/dn903633.aspx) |
| SecretUpdate |[シークレットを更新します](https://msdn.microsoft.com/en-us/library/azure/dn986818.aspx) |
| SecretDelete |[シークレットを削除します](https://msdn.microsoft.com/en-us/library/azure/dn903613.aspx) |
| SecretList |[コンテナー内のシークレットを一覧表示します](https://msdn.microsoft.com/en-us/library/azure/dn903614.aspx) |
| SecretListVersions |[シークレットのバージョンを一覧表示します](https://msdn.microsoft.com/en-us/library/azure/dn986824.aspx) |

## <a id="loganalytics"></a>Log Analytics を使用する

Log Analytics の Azure Key Vault ソリューションを使用して、Azure Key Vault の AuditEvent ログを調査することができます。 詳細については、[Log Analytics の Azure Key Vault ソリューション](../log-analytics/log-analytics-azure-key-vault.md)に関するページをご覧ください。 この記事では、Log Analytics プレビューで提供された以前の Key Vault ソリューションから移行する必要がある場合の手順について説明しました。つまり、最初にログを Azure ストレージ アカウントにルーティングし、そこから読み取ることができるよう Log Analytics を構成しました。

## <a id="next"></a>次のステップ
Web アプリケーションでの Azure Key Vault の使用方法に関するチュートリアルについては、「 [Web アプリケーションからの Azure Key Vault の使用](key-vault-use-from-web-application.md)」を参照してください。

プログラミング リファレンスについては、「 [Azure Key Vault 開発者ガイド](key-vault-developers-guide.md)」を参照してください。

Azure Key Vault の Azure PowerShell 1.0 のコマンドレットの一覧については、「 [Azure Key Vault Cmdlets (Azure Key Vault コマンドレット)](https://msdn.microsoft.com/library/azure/dn868052.aspx)」を参照してください。

Azure Key Vault を使用したキーのローテーションとログの監査のチュートリアルについては、「 [エンド ツー エンドのキーのローテーションと監査で Key Vault を設定する方法](key-vault-key-rotation-log-monitoring.md)」を参照してください。


