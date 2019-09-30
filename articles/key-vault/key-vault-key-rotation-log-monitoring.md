---
title: エンド ツー エンドのキー ローテーションと監査で Azure Key Vault を設定する | Microsoft Docs
description: このハウツー ガイドは、キー ローテーションの設定とキー コンテナー ログの監視を行うために役立ちます。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: ''
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 1f60ce3a23882a48e6008b76c0eedcab99e013b2
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "70883454"
---
# <a name="set-up-azure-key-vault-with-key-rotation-and-auditing"></a>キー ローテーションと監査で Azure Key Vault を設定する

## <a name="introduction"></a>はじめに

キー コンテナーを作成したら、以後はそのキー コンテナーを使用してキーとシークレットを保存できます。 アプリケーションでキーやシークレットを保持する必要がなくなり、必要に応じてコンテナーから要求することができます。 キー コンテナーを使用してアプリケーションの動作に影響を与えずにキーとシークレットを更新できるため、キーとシークレットを管理する可能性の幅が広がります。

>[!IMPORTANT]
> この記事の例は、説明のためにのみ示されています。 運用環境での使用は意図していません。 

この記事で説明する内容は次のとおりです。

- Azure Key Vault を使用してシークレットを保存する例。 この記事で保存するシークレットは、アプリケーションによってアクセスされる Azure ストレージ アカウント キーです。 
- そのストレージ アカウント キーのスケジュールされたローテーションを実装する方法。
- キー コンテナー監査ログを監視し、予期しない要求が行われたときにアラートを生成する方法。

> [!NOTE]
> この記事では、使用するキー コンテナーの初期設定の詳細は説明しません。 この情報については、「[Azure Key Vault とは](key-vault-overview.md)」を参照してください。 クロスプラットフォーム コマンド ライン インターフェイスの手順については、「[Manage Key Vault using Azure CLI (Azure CLI を使用して Key Vault を管理する)](key-vault-manage-with-cli2.md)」を参照してください。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="set-up-key-vault"></a>Key Vault の設定

アプリケーションが Key Vault からシークレットを取得できるようにするには、最初にシークレットを作成し、それをコンテナーにアップロードする必要があります。

Azure PowerShell セッションを開始し、次のコマンドで Azure アカウントにサインインします。

```powershell
Connect-AzAccount
```

ポップアップ ブラウザー ウィンドウで、Azure アカウントのユーザー名とパスワードを入力します。 PowerShell は、このアカウントに関連付けられているすべてのサブスクリプションを取得します。 PowerShell は既定で最初のサブスクリプションを使用します。

複数のサブスクリプションをお持ちの場合は、Key Vault を作成するときに使用した 1 つを指定することが必要なことがあります。 アカウントのサブスクリプションを確認するには、次を入力します。

```powershell
Get-AzSubscription
```

ログを記録するキー コンテナーに関連付けられているサブスクリプションを指定するには、次を入力します。

```powershell
Set-AzContext -SubscriptionId <subscriptionID>
```

この記事では、ストレージ アカウント キーをシークレットとして格納しているため、そのストレージ アカウント キーを取得する必要があります。

```powershell
Get-AzStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

自分のシークレット (ここではストレージ アカウント キー) を取得したら、そのキーをセキュリティで保護された文字列に変換し、その値を使用してシークレットを自分のキー コンテナー内に作成します。

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```

次に、作成したシークレットの URI を取得します。 この URI は、後の手順でキー コンテナーを呼び出して自分のシークレットを取得するために必要です。 次の PowerShell コマンドを実行し、ID の値をメモします。それがシークレットの URI です。

```powershell
Get-AzKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>アプリケーションの設定

シークレットが保存されたので、さらにいくつかの手順を実行した後、コードを使用してそれを取得して使用できます。

まず、アプリケーションを Azure Active Directory に登録する必要があります。 その後、Key Vault にアプリケーションの情報を通知して、アプリケーションからの要求を許可できるようにします。

> [!NOTE]
> アプリケーションは、Key Vault と同じ Azure Active Directory テナントに作成する必要があります。

1. **Azure Active Directory** を開きます。
2. **[アプリの登録]** を選択します。 
3. **[新しいアプリケーションの登録]** を選択して、アプリケーションを Azure Active Directory に追加します。

    ![Azure Active Directory の [アプリケーション] を開く](./media/keyvault-keyrotation/azure-ad-application.png)

4. **[作成]** では、[アプリケーションの種類] は **[Web アプリ/API]** のままにし、アプリケーションに名前を付けます。 アプリケーションの **[サインオン URL]** を指定します。 この URL には、このデモ用の任意の値を指定できます。

    ![アプリケーションの登録を作成する](./media/keyvault-keyrotation/create-app.png)

5. アプリケーションが Azure Active Directory に追加されると、アプリケーション ページが開きます。 **[設定]** を選択し、 **[プロパティ]** を選択します。 **[アプリケーション ID]** の値をコピーします。 これは後の手順で必要になります。

次に、アプリケーション用のキーを生成して、Azure Active Directory と対話できるようにします。 キーを作成するには、 **[設定]** で **[キー]** を選択します。 Azure Active Directory アプリケーション用に新しく生成されたキーをメモします。 後の手順で必要になります。 このキーは、このセクションを離れた後で入手することはできません。 

![Azure Active Directory アプリ キー](./media/keyvault-keyrotation/create-key.png)

アプリケーションからキー コンテナーへの呼び出しを確立する前に、アプリケーションとそのアクセス許可についてキー コンテナーに通知する必要があります。 次のコマンドでは、Azure Active Directory アプリのコンテナー名とアプリケーション ID を使用して、自分のキー コンテナーへのアプリケーションの **Get** アクセスを許可しています。

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

これで、アプリケーション呼び出しの構築を開始する準備ができました。 自分のアプリケーションに、Azure Key Vault と Azure Active Directory との対話に必要な NuGet パッケージをインストールする必要があります。 Visual Studio パッケージ マネージャー コンソールで、次のコマンドを入力します。 この記事が書かれた時点で、Azure Active Directory パッケージの最新バージョンは 3.10.305231913 であるため、現在の最新のバージョンを確認し、必要に応じて更新してください。

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

アプリケーション コードで、 Azure Active Directory 認証用のメソッドを保持するクラスを作成します。 この例では、そのクラスは **Utils** と呼ばれます。 次の `using` ステートメントを追加します。

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

次に、以下のメソッドを追加して、Azure Active Directory から JWT トークンを取得します。 保守容易性のために、ハードコーディングされた文字列の値を Web またはアプリケーション構成の中に移動できます。

```csharp
public async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);

    ClientCredential clientCred = new ClientCredential("<AzureADApplicationClientID>","<AzureADApplicationClientKey>");

    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)

    throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

必要なコードを追加して Key Vault を呼び出し、シークレット値を取得できます。 まず、次の `using` ステートメントを追加する必要があります。

```csharp
using Microsoft.Azure.KeyVault;
```

メソッド呼び出しを追加して Key Vault を呼び出し、シークレットを取得します。 このメソッドでは、前の手順で保存したシークレットの URI を提供します。 前に作成した **Utils** クラスから **GetToken** メソッドが使用されている点に注意してください。

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

アプリケーションを実行するときに、Azure Active Directory への認証を行い、Azure Key Vault からシークレット値を取得しているはずです。

## <a name="key-rotation-using-azure-automation"></a>Azure Automation を使用したキー ローテーション

> [!IMPORTANT]
> Azure Automation Runbook は、`AzureRM` モジュールの使用に引き続き必要です。

Key Vault のシークレットとして保存した値のローテーション戦略を設定する準備が整いました。 シークレットは、いくつかの方法でローテーションできます。

- 手動プロセスの一部として
- API 呼び出しを使用してプログラムで
- Azure Automation スクリプトを通して

この記事では、Azure PowerShell と Azure Automation を組み合わせて使用して、Azure ストレージ アカウントのアクセス キーを変更します。 その後、その新しいキーでキー コンテナーのシークレットを更新します。

Azure Automation でキー コンテナー内にシークレット値を設定できるようにするには、**AzureRunAsConnection** という名前の接続用のクライアント ID を取得する必要があります。 この接続は、Azure Automation インスタンスを確立したときに作成されています。 この ID を取得するには、Azure Automation インスタンスから **[資産]** を選択します。 そこから、 **[接続]** 、 **[AzureRunAsConnection]** サービス プリンシパルの順に選択します。 **[ApplicationId]** の値をメモします。

![Azure Automation クライアント ID](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

**[資産]** で、 **[モジュール]** を選択します。 **[ギャラリー]** を選択し、次の各モジュールの更新バージョンを検索してインポートします。

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage

> [!NOTE]
> この記事が書かれた時点では、以下のスクリプトで更新が必要なのは上記のモジュールだけです。 自動化ジョブが失敗した場合は、必要なモジュールとその依存関係がすべてインポートされていることを確認してください。

Azure Automation 接続用のアプリケーション ID を取得したら、このアプリケーションにコンテナー内のシークレットを更新するアクセス許可があることを自分のキー コンテナーに通知する必要があります。 次の PowerShell コマンドを使用します。

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

次に、Azure Automation インスタンスで **[Runbook]** を選択し、 **[Runbook の追加]** を選択します。 **[簡易作成]** を選択します。 Runbook に名前を付け、Runbook の種類として **[PowerShell]** を選択します。 説明を追加できます。 最後に、 **[作成]** を選択します。

![Runbook を作成する](./media/keyvault-keyrotation/Create_Runbook.png)

新しい Runbook のエディター ペインに、次の PowerShell スクリプトを貼り付けます。

```powershell
$connectionName = "AzureRunAsConnection"
try
{
    # Get the connection "AzureRunAsConnection"
    $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

    "Logging in to Azure..."
    Connect-AzureRmAccount `
        -ServicePrincipal `
        -TenantId $servicePrincipalConnection.TenantId `
        -ApplicationId $servicePrincipalConnection.ApplicationId `
        -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
    "Login complete."
}
catch {
    if (!$servicePrincipalConnection)
    {
        $ErrorMessage = "Connection $connectionName not found."
        throw $ErrorMessage
    } else{
        Write-Error -Message $_.Exception
        throw $_.Exception
    }
}

# Optionally you can set the following as parameters
$StorageAccountName = <storageAccountName>
$RGName = <storageAccountResourceGroupName>
$VaultName = <keyVaultName>
$SecretName = <keyVaultSecretName>

#Key name. For example key1 or key2 for the storage account
New-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName -KeyName "key2" -Verbose
$SAKeys = Get-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName

$secretvalue = ConvertTo-SecureString $SAKeys[1].Value -AsPlainText -Force

$secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secretvalue
```

エディター ウィンドウで **[テスト ウィンドウ]** を選択して、スクリプトをテストします。 エラーなしでスクリプトが実行されたら、 **[発行]** を選択できます。その後、Runbook 構成ウィンドウで Runbook 用のスケジュールを適用できます。

## <a name="key-vault-auditing-pipeline"></a>Key Vault 監査のパイプライン

Key Vault を設定するときに、監査をオンにして、Key Vault へのアクセス要求のログを収集できます。 これらのログは、指定された Azure ストレージ アカウントに保存され、抽出、監視、および分析の対象にすることができます。 次のシナリオでは、Azure Functions、Azure Logic Apps、および Key Vault の監査ログを利用してパイプラインを作成し、Web アプリのアプリ ID と一致しないアプリがコンテナーからシークレットを取得するときに電子メールを送信します。

最初に、Key Vault へのログオンを有効にする必要があります。 次の PowerShell コマンドを使用します (詳細については、[Key Vault のログ記録に関するこちらの記事](key-vault-logging.md)をご覧ください)。

```powershell
$sa = New-AzStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzKeyVault -VaultName '<vaultName>'
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

ログ記録を有効にすると、指定されたストレージ アカウントへの監査ログの保存が開始されます。 こうしたログには、いつ、だれが、どのような方法で Key Vault にアクセスしたかに関するイベントが含まれます。

> [!NOTE]
> Key Vault の操作を行ってから 10 分後には、ログ情報にはアクセスできます。 多くの場合、もっと早く利用できます。

次は、[Azure Service Bus キューを作成](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)します。 このキューは、キー コンテナーの監査ログがプッシュされる場所です。 監査ログ メッセージがキューに存在すると、ロジック アプリによってそれらが取り出されて対応されます。 次の手順で Service Bus インスタンスを作成します。

1. Service Bus 名前空間を作成します (使用できるものが既にある場合は、手順 2 に進みます)。
2. Azure portal で Service Bus インスタンスに移動し、その中にキューを作成する名前空間を選択します。
3. **[リソースの作成]**  >  **[エンタープライズ統合]**  >  **[Service Bus]** を選択し、必要な詳細を入力します。
4. 名前空間を選択し、 **[接続情報]** を選択して、Service Bus 接続情報を見つけます。 この情報は、次のセクションで必要です。

次に、[Azure 関数を作成](../azure-functions/functions-create-first-azure-function.md)して、ストレージ アカウント内のキー コンテナー ログをポーリングし、新しいイベントを取得します。 この関数は、スケジュールに従ってトリガーされます。

Azure 関数アプリを作成するには、 **[リソースの作成]** を選択し、**関数アプリ**用のマーケットプレースで Function App を検索し、 **[作成]** をクリックします。 作成中、既存のホスティング プランを使用するか、新しいプランを作成できます。 動的ホスティングを選択することもできます。 Azure Functions のホスティング オプションの詳細については、「[Azure Functions のスケーリング方法](../azure-functions/functions-scale.md)」をご覧ください。

Azure 関数アプリが作成されたら、それに移動し、シナリオとして **[タイマー]** を、言語として **[C\#]** を選択します。 次に **[この関数を作成する]** を選択します。

![Azure Functions スタート ブレード](./media/keyvault-keyrotation/Azure_Functions_Start.png)

**[開発]** タブで、run.csx コードを次のコードに置き換えます。

```csharp
#r "Newtonsoft.Json"

using System;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.ServiceBus.Messaging;
using System.Text;

public static void Run(TimerInfo myTimer, TextReader inputBlob, TextWriter outputBlob, TraceWriter log)
{
    log.Info("Starting");

    CloudStorageAccount sourceStorageAccount = new CloudStorageAccount(new StorageCredentials("<STORAGE_ACCOUNT_NAME>", "<STORAGE_ACCOUNT_KEY>"), true);

    CloudBlobClient sourceCloudBlobClient = sourceStorageAccount.CreateCloudBlobClient();

    var connectionString = "<SERVICE_BUS_CONNECTION_STRING>";
    var queueName = "<SERVICE_BUS_QUEUE_NAME>";

    var sbClient = QueueClient.CreateFromConnectionString(connectionString, queueName);

    DateTime dtPrev = DateTime.UtcNow;
    if(inputBlob != null)
    {
        var txt = inputBlob.ReadToEnd();

        if(!string.IsNullOrEmpty(txt))
        {
            dtPrev = DateTime.Parse(txt);
            log.Verbose($"SyncPoint: {dtPrev.ToString("O")}");
        }
        else
        {
            dtPrev = DateTime.UtcNow;
            log.Verbose($"Sync point file didnt have a date. Setting to now.");
        }
    }

    var now = DateTime.UtcNow;

    string blobPrefix = "insights-logs-auditevent/resourceId=/SUBSCRIPTIONS/<SUBSCRIPTION_ID>/RESOURCEGROUPS/<RESOURCE_GROUP_NAME>/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/<KEY_VAULT_NAME>/y=" + now.Year +"/m="+now.Month.ToString("D2")+"/d="+ (now.Day).ToString("D2")+"/h="+(now.Hour).ToString("D2")+"/m=00/";

    log.Info($"Scanning:  {blobPrefix}");

    IEnumerable<IListBlobItem> blobs = sourceCloudBlobClient.ListBlobs(blobPrefix, true);

    log.Info($"found {blobs.Count()} blobs");

    foreach(var item in blobs)
    {
        if (item is CloudBlockBlob)
        {
            CloudBlockBlob blockBlob = (CloudBlockBlob)item;

            log.Info($"Syncing: {item.Uri}");

            string sharedAccessUri = GetContainerSasUri(blockBlob);

            CloudBlockBlob sourceBlob = new CloudBlockBlob(new Uri(sharedAccessUri));

            string text;
            using (var memoryStream = new MemoryStream())
            {
                sourceBlob.DownloadToStream(memoryStream);
                text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
            }

            dynamic dynJson = JsonConvert.DeserializeObject(text);

            //Required to order by time as they might not be in the file
            var results = ((IEnumerable<dynamic>) dynJson.records).OrderBy(p => p.time);

            foreach (var jsonItem in results)
            {
                DateTime dt = Convert.ToDateTime(jsonItem.time);

                if(dt>dtPrev){
                    log.Info($"{jsonItem.ToString()}");

                    var payloadStream = new MemoryStream(Encoding.UTF8.GetBytes(jsonItem.ToString()));
                    //When sending to ServiceBus, use the payloadStream and set keeporiginal to true
                    var message = new BrokeredMessage(payloadStream, true);
                    sbClient.Send(message);
                    dtPrev = dt;
                }
            }
        }
    }
    outputBlob.Write(dtPrev.ToString("o"));
}

static string GetContainerSasUri(CloudBlockBlob blob)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();

    sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.Read;

    //Generate the shared access signature on the container, setting the constraints directly on the signature.
    string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

> [!NOTE]
> 上記のコードの変数を、キー コンテナー ログが書き込まれるストレージ アカウント、先ほど作成した Service Bus インスタンス、およびキー コンテナーのストレージ ログへの特定のパスを指すように置き換えます。

関数は Key Vault ログが書き込まれたストレージ アカウントから最新のログ ファイルを取得します。その後、そのファイルから最新のイベントを取得して、Service Bus キューにプッシュします。 

1 つのファイルに複数のイベントが保持されている可能性があるため、sync.txt ファイルを作成する必要があります。関数はこのファイルも参照して、取得された最後のイベントのタイム スタンプを確認します。 このファイルの使用によって、同じイベントが複数回プッシュされることがなくなります。 

この sync.txt ファイルには、最後に発生したイベントのタイム スタンプが含まれます。 ログが読み込まれたら、各自のタイム スタンプに基づいて並べ替えを行って、適切な順序になっていることを確認します。

この関数では、Azure Functions ですぐには利用できない 2 つの追加のライブラリを参照します。 これらのライブラリを追加するには、Azure Functions で NuGet を使用してそれらをプルする必要があります。 **[コード]** ボックスで、 **[ファイルの表示]** を選択します。

![[ファイルの表示] オプション](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

次の内容の project.json という名前のファイルを追加します。

```json
    {
      "frameworks": {
        "net46":{
          "dependencies": {
                "WindowsAzure.Storage": "7.0.0",
                "WindowsAzure.ServiceBus":"3.2.2"
          }
        }
       }
    }
```

**[保存]** を選択すると、Azure Functions によって必要なバイナリがダウンロードされます。

**[統合]** タブに切り替えて、関数内で使用するタイマー パラメーターにわかりやすい名前を付けます。 上記のコードの関数では、*myTimer* という名前のタイマーが必要です。 タイマーの [CRON 式](../app-service/webjobs-create.md#CreateScheduledCRON)を、`0 * * * * *` のように指定します。 この式によって、関数は 1 分に 1 回実行されます。

同じ **[統合]** タブで、種類が **Azure Blob Storage** の入力を追加します。 この入力は sync.txt ファイルを指します。このファイルには、関数によって参照される最後のイベントのタイム スタンプが含まれます。 この入力は、関数内でパラメーター名を使用してアクセスされます。 上記のコードでは、Azure Blob Storage の入力のパラメーター名は *inputBlob* です。 sync.txt ファイルが配置されるストレージ アカウントを選択します (同じストレージ アカウントにすることも別のストレージ アカウントにすることもできます)。 path フィールドに、`{container-name}/path/to/sync.txt` の形式でファイルのパスを入力します。

種類が **Azure Blob Storage** の出力を追加します。 この出力は、入力で定義した sync.txt ファイルを指します。 この出力は、参照される最後のイベントのタイム スタンプを書き込むときに、関数によって使用されます。 上記のコードでは、このパラメーターは *outputBlob* と呼ばれます。

これで、関数が準備できました。 **[開発]** タブに切り替えて、コードを 保存します。 出力ウィンドウでコンパイル エラーを確認し、必要に応じて修正します。 コードをコンパイルすると、1 分ごとにキー コンテナー ログが確認され、すべての新しいイベントが定義済みの Service Bus キューにプッシュされます。 関数がトリガーされるたびに、ログ情報がログ ウィンドウに書き出されることがわかります。

### <a name="azure-logic-app"></a>Azure Logic App

次に、Azure ロジック アプリを作成する必要があります。このアプリは、関数によって Service Bus キューにプッシュされるイベントを取得し、その内容を解析し、一致する条件に基づいて電子メールを送信します。

**[リソースの作成]**  >  **[統合]**  >  **[ロジック アプリ]** を選択して、[ロジック アプリを作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)します。

ロジック アプリが作成されたら、それに移動し、 **[編集]** を選択します。 ロジック アプリ エディターで、 **[Service Bus キュー]** を選択し、Service Bus 資格情報を入力して、それをキューに接続します。

![Azure Logic App Service Bus](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

**[条件の追加]** を選択します。 条件の中で、高度なエディターに切り替え、次のコードを入力します。 *APP_ID* を Web アプリの実際のアプリ ID に置き換えます。

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

この式は、基本的に、受信イベント (Service Bus メッセージの本文) の *appid* がアプリの *appid* でない場合に、**false** を返します。

次に、 **[IF NO, DO NOTHING] \(いいえの場合は何もしない\)** でアクションを作成します。

![Azure Logic Apps - アクションの選択](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

アクションで、 **[Office 365 - send email]\(Office 365 - メール送信\)** を選択します。 フィールドに入力して、定義された条件が **false** を返したときに送信する電子メールを作成します。 Office 365 がない場合は、同じ結果を実現するための他の方法を探してください。

これで、1 分に 1 回、新しいキー コンテナーの監査ログを確認するエンド ツー エンドのパイプラインができました。 それは、検出した新しいログを Service Bus キューにプッシュします。 新しいメッセージがキューに届くと、ロジック アプリがトリガーされます。 イベント内の *appid* が呼び出し元アプリケーションのアプリケーション ID と一致しない場合は、電子メールが送信されます。
