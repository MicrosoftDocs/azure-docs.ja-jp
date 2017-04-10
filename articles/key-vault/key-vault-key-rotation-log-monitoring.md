---
title: "エンド ツー エンドのキー ローテーションと監査で Azure Key Vault を設定する | Microsoft Docs"
description: "キー ローテーションとキー コンテナー ログの監視による設定について説明します。"
services: key-vault
documentationcenter: 
author: swgriffith
manager: mbaldwin
tags: 
ms.assetid: 9cd7e15e-23b8-41c0-a10a-06e6207ed157
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: jodehavi;stgriffi
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: cc872e8d9bc0662f46d5f394f9c98885e34fe67a
ms.lasthandoff: 01/20/2017


---
# <a name="set-up-azure-key-vault-with-end-to-end-key-rotation-and-auditing"></a>エンド ツー エンドのキー ローテーションと監査で Azure Key Vault を設定する
## <a name="introduction"></a>はじめに
Key Vault を作成したら、そのコンテナーを使って、キーとシークレットを保存できます。 アプリケーションでキーやシークレットを保持する必要がなくなりました。キーやシークレットは、必要に応じてキー コンテナーに要求します。 これによりアプリケーションの動作に影響を与えずにキーとシークレットを更新できるため、キーおよびシークレット管理を取り巻く可能性の幅が広がります。

この記事では、Azure Key Vault を使ってシークレット (ここでは、アプリケーションがアクセスする Azure Storage アカウント キー) を格納する例について説明します。 また、そのストレージ アカウント キーのスケジュールされたローテーションの実装も紹介します。 最後に、キー コンテナー監査ログを監視し、予期しない要求が行われたときにアラートを生成する方法のデモを見ていきます。

> [!NOTE]
> このチュートリアルの目的は、Key Vault の初期設定について詳しく説明することではありません。 詳細については、「 [Azure Key Vault の概要](key-vault-get-started.md)」を参照してください。 クロスプラットフォーム コマンド ライン インターフェイスの手順については、「[Manage Key Vault using CLI](key-vault-manage-with-cli.md)」(CLI を使用した Key Vault の管理) を参照してください。
>
>

## <a name="set-up-key-vault"></a>Key Vault の設定
アプリケーションが Key Vault からシークレットを取得できるようにするには、最初にシークレットを作成し、それをコンテナーにアップロードする必要があります。 これを行うため、Azure PowerShell セッションを開始し、次のコマンドで Azure アカウントにサインインします。

```powershell
Login-AzureRmAccount
```

ポップアップ ブラウザー ウィンドウで、Azure アカウントのユーザー名とパスワードを入力します。 PowerShell は、このアカウントに関連付けられているすべてのサブスクリプションを取得します。 PowerShell は既定で最初のサブスクリプションを使用します。

複数のサブスクリプションをお持ちの場合は、Key Vault を作成するときに使用した&1; つを指定することが必要なことがあります。 アカウントのサブスクリプションを確認するには、次を入力します。

```powershell
Get-AzureRmSubscription
```

ログ記録する Key Vault に関連付けられているサブスクリプションを指定するには、次を入力します。

```powershell
Set-AzureRmContext -SubscriptionId <subscriptionID>
```

この記事では、ストレージ アカウント キーをシークレットとして格納しているため、そのストレージ アカウント キーを取得する必要があります。

```powershell
Get-AzureRmStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

自分のシークレット (ここではストレージ アカウント キー) を取得したら、それをセキュリティで保護された文字列に変換し、その値を使ってシークレットをキー コンテナーに作成します。

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzureKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```
次に、作成したシークレットの URI を取得します。 これは、後で Key Vault を呼び出して、自分のシークレットを取得するときに使用します。 次の PowerShell コマンドを実行し、ID の値をメモします。これが、シークレットの URI です。

```powershell
Get-AzureKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>アプリケーションの設定
シークレットを格納したところで、コードを使用して、シークレットを取得して使用できます。 これを実現するために必要ないくつかの手順があります。 最も重要なのは、アプリケーションを Azure Active Directory に登録し、Key Vault にアプリケーション情報を指定して、Key Vault がアプリケーションからの要求を許可できるようにすることです。

> [!NOTE]
> アプリケーションは、Key Vault と同じ Azure Active Directory テナントに作成する必要があります。
>
>

Azure Active Directory の [アプリケーション] タブを開きます。

![Azure Active Directory の [アプリケーション] を開く](./media/keyvault-keyrotation/AzureAD_Header.png)

**[追加]** を選択して、アプリケーションを Azure Active Directory に追加します。

![[追加] を選択する](./media/keyvault-keyrotation/Azure_AD_AddApp.png)

アプリケーションの種類は **[Web アプリケーションや Web API]** のままにして、アプリケーション名を入力します。

![アプリケーションに名前を付ける](./media/keyvault-keyrotation/AzureAD_NewApp1.png)

アプリケーションの **[サインオン URL]** と **[アプリケーション ID/URI]** を指定します。 このデモでは、任意の値を入力できます。この値は、必要に応じて後で変更できます。

![必要な URI を指定する](./media/keyvault-keyrotation/AzureAD_NewApp2.png)

アプリケーションが Azure Active Directory に追加されると、アプリケーション ページが表示されます。 **[構成]** タブをクリックし、**[クライアント ID]** の値を見つけてコピーします。 後で使用できるように、クライアント ID をメモします。

次に、アプリケーションのキーを生成して、Azure Active Directory とやり取りできるようにします。 これは、**[構成]** タブの **[キー]** セクションで作成できます。 後で使用できるように、Azure Active Directory アプリケーションから新しく生成されたキーをメモします。

![Azure Active Directory アプリケーション キー](./media/keyvault-keyrotation/Azure_AD_AppKeys.png)

Key Vault へのアプリケーションからの呼び出しを確立する前に、アプリケーションとそのアクセス許可について Key Vault に指定する必要があります。 次のコマンドは、Azure Active Directory アプリからコンテナーの名前とクライアント ID を取得し、アプリケーションのキー コンテナーに対する **Get** アクセスを許可します。

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

この時点で、アプリケーション呼び出しの構築をいつでも開始できます。 アプリケーションでは、Azure Key Vault および Azure Active Directory との対話に必要な NuGet パッケージをインストールする必要があります。 Visual Studio パッケージ マネージャー コンソールで、次のコマンドを入力します。 この記事が書かれた時点で、 Azure Active Directory パッケージの最新バージョンは 3.10.305231913 です。したがって、現在の最新のバージョンを確認し、必要に応じて更新してください。

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

アプリケーション コードで、 Azure Active Directory 認証用のメソッドを保持するクラスを作成します。 この例では、そのクラスは **Utils** と呼ばれます。 次の using ステートメントを追加します。

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

次に、以下のメソッドを追加して、Azure Active Directory から JWT トークンを取得します。 保守容易性のために、ハードコーディングされた文字列の値を Web またはアプリケーション構成に移動できます。

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

必要なコードを追加して Key Vault を呼び出し、シークレット値を取得できます。 まず、次の using ステートメントを追加する必要があります。

```csharp
using Microsoft.Azure.KeyVault;
```

メソッド呼び出しを追加して Key Vault を呼び出し、シークレットを取得します。 このメソッドでは、前の手順で保存したシークレットの URI を提供します。 以前に作成した **Utils** クラスから **GetToken** メソッドを使用している点に注意してください。

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

アプリケーションを実行するときに、Azure Active Directory への認証を行い、Azure Key Vault からシークレット値を取得しているはずです。

## <a name="key-rotation-using-azure-automation"></a>Azure Automation を使用したキー ローテーション
Azure Key Vault シークレットとして格納する値のローテーション戦略は、さまざまな方法で実装できます。 シークレットのローテーションは、手動プロセスの一環として行ったり、API 呼び出しを利用してプログラムによって実行したりできます。また、自動化スクリプトを使うこともできます。 この記事では、Azure PowerShell と Azure Automation を組み合わせて使用して、Azure Storage アカウントのアクセス キーを変更します。 その後、その新しいキーでキー コンテナーを更新します。

Azure Automation でキー コンテナーのシークレット値を設定できるようにするには、Azure Automation インスタンスを確立したときに作成された、AzureRunAsConnection という接続のクライアント ID を取得する必要があります。 この ID を取得するには、Azure Automation インスタンスから **[アセット]** を選択します。 そこで **[接続]**、**[AzureRunAsConnection]** サービス プリンシパルの順に選択します。 **[アプリケーション ID]** をメモしてください。

![Azure Automation クライアント ID](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

**[アセット]** で **[モジュール]** を選択します。 **[モジュール]** では、**[ギャラリー]** を選択し、次の各モジュールの更新バージョンを検索して、**[インポート]** を実行します。

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage


> [!NOTE]
> この記事が書かれた時点では、以下のスクリプトで更新が必要なのは上記のモジュールだけです。 自動化ジョブが失敗した場合は、必要なモジュールとその依存関係がすべてインポートされていることを確認してください。
>
>

Azure Automation 接続のアプリケーション ID を取得したら、このアプリケーションにコンテナー内のシークレットを更新するアクセス権があることを、Key Vault に通知する必要があります。 これを行うには、次の PowerShell コマンドを使用します。

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

次に、Azure Automation インスタンスで **[Runbook]** を選択し、**[Runbook の追加]** を選択します。 **[簡易作成]**を選択します。 Runbook に名前を付けて、Runbook の種類として **[PowerShell]** を選択します。 説明を追加するオプションがあります。 最後に、**[作成]** をクリックします。

![Runbook を作成する](./media/keyvault-keyrotation/Create_Runbook.png)

新しい Runbook のエディター ペインに、次の PowerShell スクリプトを貼り付けます。

```powershell
$connectionName = "AzureRunAsConnection"
try
{
    # Get the connection "AzureRunAsConnection "
    $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

    "Logging in to Azure..."
    Add-AzureRmAccount `
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

#Optionally you may set the following as parameters
$StorageAccountName = <storageAccountName>
$RGName = <storageAccountResourceGroupName>
$VaultName = <keyVaultName>
$SecretName = <keyVaultSecretName>

#Key name. For example key1 or key2 for the storage account
New-AzureRmStorageAccountKey -ResourceGroupName $RGName -StorageAccountName $StorageAccountName -KeyName "key2" -Verbose
$SAKeys = Get-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName

$secretvalue = ConvertTo-SecureString $SAKeys[1].Value -AsPlainText -Force

$secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secretvalue
```

エディター ペインで **[テスト ウィンドウ]** を選択して、スクリプトをテストします。 エラーなしでスクリプトが実行されると、**[発行]** を選択できます。その後、Runbook 構成ウィンドウに戻って、Runbook のスケジュールを適用できます。

## <a name="key-vault-auditing-pipeline"></a>Key Vault 監査のパイプライン
Key Vault を設定するときに、監査をオンにして、Key Vault へのアクセス要求のログを収集できます。 これらのログは、指定された Azure Storage アカウントに保存され、抽出、監視、および分析することができます。 次のシナリオでは、Azure Functions、Azure Logic Apps、および Key Vault の監査ログを利用してパイプラインを作成し、Web アプリのアプリ ID と一致するアプリがコンテナーからシークレットを取得したときに、電子メールを送信します。

最初に、Key Vault へのログオンを有効にする必要があります。 これを行うには、次の PowerShell コマンドを使用します (詳細については、[key-vault-logging](key-vault-logging.md) をご覧ください)。

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzureRmKeyVault -VaultName '<vaultName>'
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent
```

ログオンを有効にすると、監査ログによって、指定されたストレージ アカウントへの収集が開始されます。 こうしたログには、いつ、だれが、どのような方法で Key Vault にアクセスしたかに関するイベントが含まれます。

> [!NOTE]
> Key Vault の操作を行ってから 10 分後には、ログ情報にはアクセスできます。 通常はこれよりも早くアクセスできます。
>
>

次は、[Azure Service Bus キューを作成](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)します。 ここにキー コンテナーの監査ログがプッシュされます。 監査ログ メッセージがキューにあるときは、ロジック アプリはそれらを選択して処理します。 次の手順で Service Bus を作成します。

1. Service Bus 名前空間を作成します (使用できるものが既にある場合は、手順 2 に進みます)。
2. Azure ポータルで Service Bus に移動して、キューを作成する名前空間を選択します。
3. **[新規]** を選択し、**[Service Bus] > [キュー]** の順に選択して、必要な情報を入力します。
4. 名前空間を選択し、**[接続情報]** をクリックして、Service Bus 接続情報を取得します。 この情報は、次のセクションで必要です。

次に、[Azure 関数を作成](../azure-functions/functions-create-first-azure-function.md)して、ストレージ アカウント内で Key Vault ログをポーリングし、新しいイベントを取得します。 これが、スケジュールでトリガーされる関数です。

Azure 関数を作成するには、Azure ポータルで **[新規] > [Function App]** を選択します。 作成中、既存のホスティング プランを使用するか、新しいプランを作成できます。 動的ホスティングを選択することもできます。 関数のホスティング オプションの詳細については、「[Azure Functions のスケーリング方法](../azure-functions/functions-scale.md)」をご覧ください。

Azure 関数を作成するときに、この画面にアクセスしてタイマー関数と C\# を選択します。次に **[この関数を作成する]** をクリックします。

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

            //required to order by time as they may not be in the file
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
> 上記のコードの変数を、Key Vault ログが書き込まれたストレージ アカウント、以前に作成した Service Bus、およびキー コンテナーのストレージ ログへの特定のパスを指すように置き換えます。
>
>

関数は Key Vault ログが書き込まれたストレージ アカウントから最新のログ ファイルを取得します。その後、そのファイルから最新のイベントを取得して、Service Bus キューにプッシュします。 1 つのファイルに複数のイベントが保持されている可能性があるため、sync.txt ファイルを作成します。関数はこのファイルも参照して、取得された最後のイベントのタイム スタンプを確認します。 これにより、同じイベントが複数回プッシュされなくなります。 この sync.txt ファイルには、最後に発生したイベントのタイムスタンプが含まれます。 ログは、読み込まれたときに、タイムスタンプに基づいて正しく並べ替える必要があります。

この関数では、Azure Functions ですぐには使用できない追加ライブラリをいくつか参照します。 ライブラリを追加するには、Azure Functions で、NuGet を使用してそのライブラリをプルするがあります。 **[ファイルを表示]** オプションを選択します。

![[ファイルの表示] オプション](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

次に、以下のコンテンツが含まれる project.json というファイルを追加します。

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
**[保存]** すると、Azure Functions によって必要なバイナリがダウンロードされます。

**[統合]** タブに切り替えて、関数内で使用するタイマー パラメーターにわかりやすい名前を付けます。 上記のコードでは、タイマーは *myTimer*と呼ばれます。 [CRON 式](../app-service-web/web-sites-create-web-jobs.md#CreateScheduledCRON)を次のように指定します: 0 \* \* \* \* \* (1 分ごとに関数を実行するタイマーの場合)。

同じ **[統合]** タブで、種類が **Azure Blob Storage** の入力を追加します。 これは sync.txt ファイルを指します。このファイルには、関数によって参照される最後のイベントのタイムスタンプが含まれます。 これは関数内でパラメーター名によって使用できます。 上記のコードの Azure Blob Storage 入力では、パラメーター名は *inputBlob* です。 sync.txt ファイルが存在するストレージ アカウントを選択します (同じストレージ アカウントにすることも別のストレージ アカウントにすることもできます)。 パス フィールドには、ファイルが存在するパスを {container-name}/path/to/sync.txt の形式で指定します。

種類が *Azure Blob Storage* の出力を追加します。 これは、入力で定義した sync.txt ファイルを指します。 これは、参照される最後のイベントのタイムスタンプを書き込むときに、関数によって使用されます。 上記のコードでは、このパラメーターは *outputBlob* と呼ばれます。

これで関数の準備ができました。 **[開発]** タブに切り替えて、コードを 保存します。 出力ウィンドウでコンパイル エラーがないかどうかを確認し、エラーがある場合は修正します。 コードをコンパイルすると、コードは&1; 分ごとに Key Vault ログを確認し、新しいイベントすべてを定義済み Service Bus キューにプッシュします。 関数がトリガーされるたびに、ログ情報がログ ウィンドウに書き出されることがわかります。

### <a name="azure-logic-app"></a>Azure Logic App
次に、Azure Logic App を作成する必要があります。このアプリは、関数が Service Bus キューにプッシュするイベントを取得し、コンテンツを解析して、一致する条件に基づいて電子メールを送信します。

**[新規] > [Logic App]** の順に移動し、[Logic App を作成](../logic-apps/logic-apps-create-a-logic-app.md)します。

Logic App が作成されたら、そのアプリに移動し、**[編集]** を選択します。 Logic App エディターで、**[Service Bus キュー]** を選択し、Service Bus 資格情報を入力して、キューに接続します。

![Azure Logic App Service Bus](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

次に、**[条件を追加]** を選択します。 条件で高度なエディターに切り替えて次のコードを入力し、APP_ID を、Web アプリの実際の APP_ID に置き換えます。

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

この式は、基本的に、受信イベント (Service Bus メッセージの本文) の *appid* が、アプリの *appid* でない場合に、**false** を返します。

ここで、**[If no, do nothing (いいえの場合は、何もしない)]** でアクションを作成します。

![Azure Logic App 選択アクション](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

アクションで、[Office 365 - send email (Office 365 - メール送信)] ****を選択します。 フィールドに入力して、定義された条件が **false** を返したときに送信する電子メールを作成します。 Office 365 がない場合は、同じ結果を実現するために他の方法を確認してください。

この時点では、1 分ごとに新しい Key Vault 監査ログを確認するエンド ツー エンドのパイプラインがあります。 これは検出した新しいログを Service Bus キューにプッシュします。 新しいメッセージがキューに届くと、ロジック アプリがトリガーされます。 イベント内の *appid* が呼び出し元アプリケーションのアプリケーション ID と一致しない場合、電子メールが送信されます。

