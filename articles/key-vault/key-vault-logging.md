---
title: Azure Key Vault のログ記録 | Microsoft Docs
description: このチュートリアルを使用すれば、Azure Key Vault のログ記録を容易に開始できます。
services: key-vault
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: barclayn
ms.openlocfilehash: 0ae977215e52883e190ad3859eefac9e97462968
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67205944"
---
# <a name="azure-key-vault-logging"></a>Azure Key Vault のログ記録

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1 つまたは複数のキー コンテナーを作成したら、いつ、どのように、誰によってキー コンテナーがアクセスされるのかを監視するのが一般的です。 監視を行うには、Azure Key Vault のログ記録を有効にします。これにより、指定した Azure ストレージ アカウントに情報が保存されます。 指定したストレージ アカウント用の **insights-logs-auditevent** という名前の新しいコンテナーが自動的に作成されます。 このストレージ アカウントを使用して複数のキー コンテナーのログを収集することができます。

キー コンテナーの操作を行ってから最大 10 分後には、ログ情報にアクセスできます。 ほとんどの場合は、これよりも早く確認できます。  ストレージ アカウントでのログの管理はお客様に委ねられます。

* ログにアクセスできるユーザーを制限することでログのセキュリティを保護するには、標準的な Azure アクセス制御方法を使用します。
* ストレージ アカウントに保持する必要がなくなったログは削除します。

このチュートリアルを使用すれば、Azure Key Vault のログ記録を容易に開始できます。 ストレージ アカウントを作成し、ログを有効にしてから、収集されたログ情報を解釈します。  

> [!NOTE]
> Key Vault、キー、またはシークレットの作成方法については、このチュートリアルに含まれていません。 この情報については、「[Azure Key Vault とは](key-vault-overview.md)」を参照してください。 また、クロスプラットフォーム Azure CLI の手順については、[対応するチュートリアル](key-vault-manage-with-cli2.md)をご覧ください。
>
> この記事では、Azure PowerShell を使用して診断ログを更新する方法を説明します。 また、Azure portal の **[診断ログ]** セクションで、Azure Monitor を使用して診断ログを更新することもできます。 
>

Key Vault の概要については、「[Azure Key Vault とは](key-vault-whatis.md)」を参照してください。 Key Vault が使用可能な場所については、[価格に関するページ](https://azure.microsoft.com/pricing/details/key-vault/)をご覧ください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

* 使用している既存の Key Vault。  
* Azure PowerShell 1.0.0 以降のバージョン。 Azure PowerShell をインストールして、Azure サブスクリプションに関連付けるには、「 [Azure PowerShell のインストールおよび構成方法](/powershell/azure/overview)」を参照してください。 Azure PowerShell をインストール済みで、バージョンがわからない場合は、Azure PowerShell コンソールで「`$PSVersionTable.PSVersion`」と入力します。  
* Azure 上に確保された Key Vault のログを格納するための十分なストレージ。

## <a id="connect"></a>Key Vault サブスクリプションに接続する

キーのログ記録の最初の設定手順は、ログに記録するキー コンテナーに Azure PowerShell をポイントすることです。

Azure PowerShell セッションを開始し、次のコマンドで Azure アカウントにサインインします。  

```powershell
Connect-AzAccount
```

ポップアップ ブラウザー ウィンドウで、Azure アカウントのユーザー名とパスワードを入力します。 Azure PowerShell は、このアカウントに関連付けられているすべてのサブスクリプションを取得します。 既定では、PowerShell は最初のサブスクリプションを使用します。

キー コンテナーを作成するときに使用したサブスクリプションを指定することが必要な場合があります。 アカウントのサブスクリプションを確認するには、次のコマンドを入力します。

```powershell
Get-AzSubscription
```

ログ記録するキー コンテナーに関連付けられているサブスクリプションを指定するには、次を入力します。

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

PowerShell を適切なサブスクリプションにポイントすることは、アカウントに複数のサブスクリプションが関連付けられている場合は特に重要な手順です。 Azure PowerShell の詳細については、「 [How to install and configure Azure PowerShell (Azure PowerShell のインストールと構成の方法)](/powershell/azure/overview)」をご覧ください。

## <a id="storage"></a>ログ用のストレージ アカウントを作成する

既存のストレージ アカウントをログのために使用できますが、Key Vault のログ専用にストレージ アカウントを作成することにします。 後でこれを指定することが必要になる場合に備えて、詳細情報を **sa** という名前の変数に格納します。

また、管理を容易にするために、キー コンテナーが含まれているリソース グループと同じリソース グループを使用します。 [入門チュートリアル](key-vault-get-started.md)に基づいて、このリソース グループには **ContosoResourceGroup** という名前を付け、東アジアの場所を引き続き使用します。 これらの値は、必要に応じて実際の値に置き換えてください。

```powershell
 $sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup -Name contosokeyvaultlogs -Type Standard_LRS -Location 'East Asia'
```

> [!NOTE]
> 既存のストレージ アカウントを使う場合は、キー コンテナーと同じサブスクリプションを使う必要があります。 また、クラシック デプロイ モデルではなく Azure Resource Manager デプロイ モデルを使う必要があります。
>
>

## <a id="identify"></a>ログに対する Key Vault を識別する

[入門チュートリアル](key-vault-get-started.md)では、キー コンテナー名は **ContosoKeyVault** でした。 この名前を引き続き使用して、**kv** という名前の変数の詳細情報を格納します。

```powershell
$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'
```

## <a id="enable"></a>ログの有効化

Key Vault のログ記録を有効にするために、**Set-AzDiagnosticSetting** コマンドレットを、新しいストレージ アカウントおよび Key Vault 用に作成した変数と組み合わせて使用します。 また、 **-Enabled** フラグを **$true** に設定し、カテゴリを **AuditEvent** (Key Vault ログ記録の唯一のカテゴリ) に設定します。

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

出力は次のようになります。

    StorageAccountId   : /subscriptions/<subscription-GUID>/resourceGroups/ContosoResourceGroup/providers/Microsoft.Storage/storageAccounts/ContosoKeyVaultLogs
    ServiceBusRuleId   :
    StorageAccountName :
        Logs
        Enabled           : True
        Category          : AuditEvent
        RetentionPolicy
        Enabled : False
        Days    : 0

この出力を見れば、ログ記録がキー コンテナーに対して有効になっていることと、ストレージ アカウントに情報が保存されることを確認できます。

必要に応じて、ログのアイテム保持ポリシーを、古いログが自動的に削除されるように設定することができます。 たとえば、 **-RetentionEnabled** フラグを **$true** に設定し、 **-RetentionInDays** パラメーターを **90** に設定した保持ポリシーを設定すると、90 日より前のログが自動的に削除されます。

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

ログ記録の内容:

* 認証されたすべての REST API 要求。これには、アクセス許可がないため、システム エラーのため、または不正な要求の結果として、失敗した要求が含まれます。
* キー コンテナー自体に関する操作。これには、作成、削除、キー コンテナーのアクセス ポリシーの設定、キー コンテナー属性 (タグなど) の更新が含まれます。
* 次の操作を含む、キー コンテナーのキーとシークレットに関する操作。
  * これらのキーまたはシークレットの作成、変更、または削除。
  * 署名、確認、暗号化、復号化、キーのラップとラップ解除、シークレットの取得、およびキーとシークレット (およびそのバージョン) の一覧表示。
* 結果として 401 応答が発生する、認証されていない要求。 たとえば、ベアラー トークンを持たない要求、形式が正しくない要求、有効期限切れの要求、または無効なトークンを持つ要求です。  

## <a id="access"></a>ログへのアクセス

Key Vault のログは、指定したストレージ アカウント内の **insights-logs-auditevent** コンテナーに格納されます。 ログを表示するには、BLOB をダウンロードする必要があります。

最初に、コンテナー名の変数を作成します。 この変数は、チュートリアルの残りの部分で使用します。

```powershell
$container = 'insights-logs-auditevent'
```

このコンテナー内のすべての BLOB を一覧表示するには、次のように入力します。

```powershell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

出力の例を次に示します。

```
Container Uri: https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent

Name

---
resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json
```

この出力からわかるように、BLOB は次の命名規則に従います。`resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

日付と時刻の値には UTC が使用されます。

同じストレージ アカウントを使用して複数のリソースのログを収集することができるので、必要な BLOB のみにアクセスしたり、ダウンロードしたりする場合には、BLOB 名に完全なリソース ID を使用すると便利です。 その前に、すべての BLOB をダウンロードする方法を説明します。

フォルダーを作成して BLOB をダウンロードします。 例:

```powershell 
New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force
```

すべての BLOB の一覧を取得します。  

```powershell
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context
```

**Get-AzStorageBlobContent** を介してこの一覧をパイプして、BLOB を宛先フォルダーにダウンロードします。

```powershell
$blobs | Get-AzStorageBlobContent -Destination C:\Users\username\ContosoKeyVaultLogs'
```

この 2 番目のコマンドを実行すると、BLOB 名に含まれる **/** 区切り記号によって、宛先フォルダーの下にフォルダー構造全体が作成されます。 この構造は、BLOB をファイルとしてダウンロードし、保存するために使用します。

BLOB を選択的にダウンロードするには、ワイルドカードを使用します。 例:

* 複数の Key Vault を持っている場合に、CONTOSOKEYVAULT3 という名前の Key Vault のみについてログをダウンロードするには、次のようにします。

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
  ```

* 複数のリソース グループを持っている場合、1 つのリソース グループのみについてログをダウンロードするには、次のように `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`を使用します。

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
  ```

* 2019 年 1 月のすべてのログをダウンロードする場合は、次のように `-Blob '*/year=2019/m=01/*'` を使用します。

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'
  ```

これで、ログの内容を検討する準備が整いました。 ただし、検討に移る前に、他の 2 つのコマンドを知っておく必要があります。

* Key Vault リソースの診断設定の状態のクエリを実行するためのパラメーター: `Get-AzDiagnosticSetting -ResourceId $kv.ResourceId`
* Key Vault リソースのログ記録を無効にするためのパラメーター: `Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Category AuditEvent`

## <a id="interpret"></a>Key Vault のログを解釈する

個々の BLOB はテキストとして格納されます (JSON BLOB 形式)。 ログ エントリの例を見てみましょう。 次のコマンドを実行します。

```powershell
Get-AzKeyVault -VaultName 'contosokeyvault'`
```

次のようなログ エントリが返されます。

```json
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
```

次の表にフィールド名と説明を示します。

| フィールド名 | 説明 |
| --- | --- |
| **time** |日付と時刻 (UTC)。 |
| **resourceId** |Azure Resource Manager リソース ID。 Key Vault のログの場合は、常に Key Vault リソース ID となります。 |
| **operationName** |次の表に示すような操作の名前です。 |
| **operationVersion** |クライアントによって要求された REST API バージョン。 |
| **category** |結果の種類。 Key Vault のログの場合、**AuditEvent** は使用可能な単一の値です。 |
| **resultType** |REST API 要求の結果です。 |
| **resultSignature** |HTTP の状態です。 |
| **resultDescription** |結果に関する追加の説明です (使用可能な場合)。 |
| **durationMs** |REST API 要求を処理するのにかかった時間 (ミリ秒単位) です。 これにネットワーク待機時間は含まれません。したがって、クライアント側で測定する時間はこの時間と一致しない場合があります。 |
| **callerIpAddress** |要求を行ったクライアントの IP アドレスです。 |
| **correlationId** |オプションの GUID であり、クライアント側のログとサービス側の (Key Vault) ログを対応付ける場合に渡します。 |
| **identity** |REST API 要求に提示されたトークンからの ID です。 これは、通常、Azure PowerShell コマンドレットの実行結果として生じる要求の場合と同様に、"user"、"service principal"、または組み合わせ "user+appId" となります。 |
| **properties** |操作によって異なる情報です (**operationName**)。 ほとんどの場合、このフィールドには、クライアント情報 (クライアントから渡されたユーザー エージェント文字列)、正確な REST API 要求 URI、および HTTP 状態コードが含まれます。 さらに、要求 (**KeyCreate** または **VaultGet** など) を行った結果としてオブジェクトが返される場合は、キーの URI ("id" として)、資格情報コンテナーの URI、またはシークレットの URI も含まれます。 |

**operationName** フィールドの値は、*ObjectVerb* 形式となります。 例:

* キー コンテナーに関するすべての操作は、`Vault<action>` 形式となります (`VaultGet` や `VaultCreate` など)。
* キーに関するすべての操作は、`Key<action>` 形式となります (`KeySign` や `KeyList` など)。
* シークレットに関するすべての操作は、`Secret<action>` 形式となります (`SecretGet` や `SecretListVersions` など)。

次の表に、**operationName** の値と、対応する REST API コマンドを示します。

| operationName | REST API コマンド |
| --- | --- |
| **認証** |Azure Active Directory エンドポイント経由で認証します |
| **VaultGet** |[キー コンテナーに関する情報を取得します](https://msdn.microsoft.com/library/azure/mt620026.aspx) |
| **VaultPut** |[キー コンテナーを作成または更新します](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultDelete** |[キー コンテナーを削除します](https://msdn.microsoft.com/library/azure/mt620022.aspx) |
| **VaultPatch** |[Key Vault を更新します](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultList** |[リソース グループ内のすべてのキー コンテナーを一覧表示します](https://msdn.microsoft.com/library/azure/mt620027.aspx) |
| **KeyCreate** |[キーを作成します](https://msdn.microsoft.com/library/azure/dn903634.aspx) |
| **KeyGet** |[キーに関する情報を取得します](https://msdn.microsoft.com/library/azure/dn878080.aspx) |
| **KeyImport** |[コンテナーにキーをインポートします](https://msdn.microsoft.com/library/azure/dn903626.aspx) |
| **KeyBackup** |[キーをバックアップします](https://msdn.microsoft.com/library/azure/dn878058.aspx) |
| **KeyDelete** |[キーを削除します](https://msdn.microsoft.com/library/azure/dn903611.aspx) |
| **KeyRestore** |[キーを復元します](https://msdn.microsoft.com/library/azure/dn878106.aspx) |
| **KeySign** |[キーで署名します](https://msdn.microsoft.com/library/azure/dn878096.aspx) |
| **KeyVerify** |[キーで確認します](https://msdn.microsoft.com/library/azure/dn878082.aspx) |
| **KeyWrap** |[キーをラップします](https://msdn.microsoft.com/library/azure/dn878066.aspx) |
| **KeyUnwrap** |[キーのラップを解除します](https://msdn.microsoft.com/library/azure/dn878079.aspx) |
| **KeyEncrypt** |[キーで暗号化します](https://msdn.microsoft.com/library/azure/dn878060.aspx) |
| **KeyDecrypt** |[キーで復号化します](https://msdn.microsoft.com/library/azure/dn878097.aspx) |
| **KeyUpdate** |[キーを更新します](https://msdn.microsoft.com/library/azure/dn903616.aspx) |
| **KeyList** |[コンテナー内のキーを一覧表示します](https://msdn.microsoft.com/library/azure/dn903629.aspx) |
| **KeyListVersions** |[キーのバージョンを一覧表示します](https://msdn.microsoft.com/library/azure/dn986822.aspx) |
| **SecretSet** |[シークレットを作成します](https://msdn.microsoft.com/library/azure/dn903618.aspx) |
| **SecretGet** |[シークレットを取得します](https://msdn.microsoft.com/library/azure/dn903633.aspx) |
| **SecretUpdate** |[シークレットを更新します](https://msdn.microsoft.com/library/azure/dn986818.aspx) |
| **SecretDelete** |[シークレットを削除します](https://msdn.microsoft.com/library/azure/dn903613.aspx) |
| **SecretList** |[コンテナー内のシークレットを一覧表示します](https://msdn.microsoft.com/library/azure/dn903614.aspx) |
| **SecretListVersions** |[シークレットのバージョンを一覧表示します](https://msdn.microsoft.com/library/azure/dn986824.aspx) |

## <a id="loganalytics"></a>Azure Monitor ログの使用

Azure Monitor ログの Key Vault ソリューションを使用して、Key Vault の **AuditEvent** ログを調査することができます。 Azure Monitor ログでは、ログ クエリを使用してデータを分析し、必要な情報を取得します。 

詳細については、[Azure Monitor ログの Azure Key Vault ソリューション](../azure-monitor/insights/azure-key-vault.md)に関するページをご覧ください。 この記事では、Azure Monitor ログのプレビューで提供された以前の Key Vault ソリューションから移行する必要がある場合の手順について説明しました。つまり、最初にログを Azure ストレージ アカウントにルーティングし、そこから読み取ることができるよう Azure Monitor ログを構成しました。

## <a id="next"></a>次のステップ

.NET Web アプリケーションでの Azure Key Vault の使用方法に関するチュートリアルについては、「[Web アプリケーションからの Azure Key Vault の使用](tutorial-net-create-vault-azure-web-app.md)」を参照してください。

プログラミング リファレンスについては、「 [Azure Key Vault 開発者ガイド](key-vault-developers-guide.md)」を参照してください。

Azure Key Vault の Azure PowerShell 1.0 のコマンドレットの一覧については、[Azure Key Vault コマンドレット](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)に関するページを参照してください。

Azure Key Vault を使用したキーのローテーションとログの監査のチュートリアルについては、[エンド ツー エンドのキー ローテーションと監査での Key Vault の設定](key-vault-key-rotation-log-monitoring.md)に関するページを参照してください。
