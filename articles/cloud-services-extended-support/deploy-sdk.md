---
title: クラウド サービス (延長サポート) をデプロイする - SDK
description: Azure SDK を使用して Azure クラウド サービス (延長サポート) をデプロイする
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: cf8d2696732c2947ce86b9509720898fd63c1e16
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/27/2021
ms.locfileid: "98886970"
---
# <a name="deploy-a-cloud-services-extended-support-using-sdk"></a>SDK を使用してクラウド サービス (延長サポート) をデプロイする

この記事では、複数のロール (Web ロールおよび worker ロール) を備え、かつ、リモート デスクトップ拡張機能を追加したクラウド サービス (延長サポート) を、[Azure SDK](https://azure.microsoft.com/downloads/) を使ってデプロイする方法を説明します。 

> [!IMPORTANT]
> Cloud Services (延長サポート) は現在、パブリック プレビュー段階です。 このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="before-you-begin"></a>始める前に

クラウド サービス (延長サポート) の[デプロイの前提条件](deploy-prerequisite.md)を確認し、関連するリソースを作成します。

## <a name="deploy-a-cloud-services-extended-support"></a>クラウド サービス (延長サポート) をデプロイする
1. [Azure Compute SDK NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.Management.Compute/43.0.0-preview)をインストールし、標準の認証メカニズムを使用してクライアントを初期化します。

    ```csharp
        public class CustomLoginCredentials : ServiceClientCredentials
    {
        private string AuthenticationToken { get; set; }
        public override void InitializeServiceClient<T>(ServiceClient<T> client)
           {
               var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenantID}");
               var credential = new ClientCredential(clientId: "{clientID}", clientSecret: "{clientSecret}");
               var result = authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);
               if (result == null) throw new InvalidOperationException("Failed to obtain the JWT token");
               AuthenticationToken = result.Result.AccessToken;
           }
        public override async Task ProcessHttpRequestAsync(HttpRequestMessage request, CancellationToken cancellationToken)
           {
                if (request == null) throw new ArgumentNullException("request");
                if (AuthenticationToken == null) throw new InvalidOperationException("Token Provider Cannot Be Null");
                request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", AuthenticationToken);
                request.Headers.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
                //request.Version = new Version(apiVersion);
                await base.ProcessHttpRequestAsync(request, cancellationToken);
            }
    
        var creds = new CustomLoginCredentials();
        m_subId = Environment.GetEnvironmentVariable("AZURE_SUBSCRIPTION_ID");
        ResourceManagementClient m_ResourcesClient = new ResourceManagementClient(creds);
        NetworkManagementClient m_NrpClient = new NetworkManagementClient(creds);
        ComputeManagementClient m_CrpClient = new ComputeManagementClient(creds);
        StorageManagementClient m_SrpClient = new StorageManagementClient(creds);
        m_ResourcesClient.SubscriptionId = m_subId;
        m_NrpClient.SubscriptionId = m_subId;
        m_CrpClient.SubscriptionId = m_subId;
        m_SrpClient.SubscriptionId = m_subId;
    ```

2. Azure Resource Manager NuGet パッケージをインストールして、新しいリソース グループを作成します。

    ```csharp 
    var resourceGroups = m_ResourcesClient.ResourceGroups;
    var m_location = “East US”;
    var resourceGroupName = "ContosoRG";//provide existing resource group name, if created already
    var resourceGroup = new ResourceGroup(m_location); 
    resourceGroup = await resourceGroups.CreateOrUpdateAsync(resourceGroupName, resourceGroup);
    ```

3. クラウド サービス パッケージ (.cspkg) ファイルとサービス構成 (.cscfg) ファイルの格納に使用するストレージ アカウントとコンテナーを作成します。 [Azure Storage NuGet パッケージ](https://www.nuget.org/packages/Azure.Storage.Common/)をインストールします。 既存のストレージ アカウントを使用する場合は、この手順は省略可能です。 ストレージ アカウントには、一意の名前を使用します。

    ```csharp
    string storageAccountName = “ContosoSAS”
    var stoInput = new StorageAccountCreateParameters
       {
            Location = m_location,
            Kind = Microsoft.Azure.Management.Storage.Models.Kind.StorageV2,
            Sku = new Microsoft.Azure.Management.Storage.Models.Sku(SkuName.StandardRAGRS),
        };
            StorageAccount storageAccountOutput = m_SrpClient.StorageAccounts.Create(rgName,
            storageAccountName, stoInput);
            bool created = false;
            while (!created)
               {
                    Thread.Sleep(600);
                    var stos = m_SrpClient.StorageAccounts.ListByResourceGroup(rgName);
                    created =
                    stos.Any(
                        t =>
                        StringComparer.OrdinalIgnoreCase.Equals(t.Name, storageAccountName));
                }
        
    StorageAccount storageAccountOutput = m_SrpClient.StorageAccounts.GetProperties(rgName, storageAccountName);.
    var accountKeyResult = m_SrpClient.StorageAccounts.ListKeysWithHttpMessagesAsync(rgName, storageAccountName).Result;
    CloudStorageAccount storageAccount = new CloudStorageAccount(new StorageCredentials(storageAccountName, accountKeyResult.Body.Keys.FirstOrDefault(). Value), useHttps: true);
        
    var blobClient = storageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
    container.CreateIfNotExistsAsync().Wait();
    sharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
    sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddDays(-1);
    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddDays(2);
    sasConstraints.Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write;
    ```

4. ストレージ アカウントにクラウド サービス パッケージ (.cspkg) ファイルをアップロードします。 パッケージの URL は、どのストレージ アカウントの Shared Access Signature (SAS) URI にもすることができます。

    ```csharp
    CloudBlockBlob cspkgblockBlob = container.GetBlockBlobReference(“ContosoApp.cspkg”);
    cspkgblockBlob.UploadFromFileAsync(“./ContosoApp/ContosoApp.cspkg”). Wait();
    
    //Generate the shared access signature on the blob, setting the constraints directly on the signature.
    string cspkgsasContainerToken = cspkgblockBlob.GetSharedAccessSignature(sasConstraints);
    
    //Return the URI string for the container, including the SAS token.
    string cspkgSASUrl = cspkgblockBlob.Uri + cspkgsasContainerToken;
    ```

5. ストレージ アカウントにクラウド サービス構成 (.cscfg) をアップロードします。 サービス構成は、文字列 XML または URL のいずれかの形式として指定できます。

    ```csharp
    CloudBlockBlob cscfgblockBlob = container.GetBlockBlobReference(“ContosoApp.cscfg”);
    cscfgblockBlob.UploadFromFileAsync(“./ContosoApp/ContosoApp.cscfg”). Wait();
    
    //Generate the shared access signature on the blob, setting the constraints directly on the signature.
    string sasCscfgContainerToken = cscfgblockBlob.GetSharedAccessSignature(sasConstraints);
    
    //Return the URI string for the container, including the SAS token.
    string cscfgSASUrl = cscfgblockBlob.Uri + sasCscfgContainerToken;
    ```

6. 仮想ネットワークとサブネットを作成します。 [Azure Network NuGet パッケージ](https://www.nuget.org/packages/Azure.ResourceManager.Network/)をインストールします。 既存のネットワークとサブネットを使用する場合は、この手順は省略可能です。

    ```csharp
    VirtualNetwork vnet = new VirtualNetwork(name: vnetName) 
       { 
            AddressSpace = new AddressSpace 
               { 
                    AddressPrefixes = new List<string> { "10.0.0.0/16" } 
               }, 
                    Subnets = new List<Subnet> 
                    { 
                        new Subnet(name: subnetName) 
                        { 
                            AddressPrefix = "10.0.0.0/24" 
                        } 
                    }, 
                    Location = m_location 
               }; 
    m_NrpClient.VirtualNetworks.CreateOrUpdate(resourceGroupName, “ContosoVNet”, vnet);
    ```

7. パブリック IP アドレスを作成し、(必要に応じて) そのパブリック IP アドレスの DNS ラベル プロパティを設定します。 静的 IP を使用する場合は、サービス構成ファイルで予約済み IP として参照する必要があります。

    ```csharp
    PublicIPAddress publicIPAddressParams = new PublicIPAddress(name: “ContosIp”) 
       { 
            Location = m_location, 
            PublicIPAllocationMethod = IPAllocationMethod.Dynamic, 
            DnsSettings = new PublicIPAddressDnsSettings() 
               { 
                    DomainNameLabel = “contosoappdns” 
               } 
       }; 
    PublicIPAddress publicIpAddress = m_NrpClient.PublicIPAddresses.CreateOrUpdate(resourceGroupName, publicIPAddressName, publicIPAddressParams);
    ```

8. ネットワーク プロファイル オブジェクトを作成し、プラットフォームで作成されたロード バランサーのフロントエンドにパブリック IP アドレスを関連付けます。

    ```csharp
    LoadBalancerFrontendIPConfiguration feipConfiguration = new LoadBalancerFrontendIPConfiguration() 
        { 
            Name = “ContosoFe”, 
            Properties = new LoadBalancerFrontendIPConfigurationProperties() 
                { 
                PublicIPAddress = new CM.SubResource() 
                    { 
                        Id = $"/subscriptions/{m_subId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/publicIPAddresses/{publicIPAddressName}", 
                    } 
                } 
        }; 
    
    CloudServiceNetworkProfile cloudServiceNetworkProfile = new CloudServiceNetworkProfile() 
        { 
            LoadBalancerConfigurations = new List<LoadBalancerConfiguration>() 
                { 
                    new LoadBalancerConfiguration() 
                       { 
                        Name  = 'ContosoLB', 
                        Properties = new LoadBalancerConfigurationProperties() 
                            { 
                            FrontendIPConfigurations = new List<LoadBalancerFrontendIPConfiguration>() 
                                { 
                                feipConfig 
                                } 
                            } 
                        } 
                } 
        }; 
    
    ```

9. キー コンテナーを作成する このキー コンテナーは、クラウド サービス (延長サポート) の各種ロールに関連付けられている証明書を格納するために使用します。 このキー コンテナーは、一意の名前を使用したうえで、クラウド サービスと同じリージョンおよびサブスクリプションに配置する必要があります。 詳細については、「[Azure Cloud Services (延長サポート) で証明書を使用する](certificates-and-key-vault.md)」を参照してください。

    ```powershell
    New-AzKeyVault -Name "ContosKeyVault” -ResourceGroupName “ContosoOrg” -Location “East US”
    ```

10. キー コンテナーのアクセス ポリシーを更新し、自分のユーザー アカウントに証明書に関するアクセス許可を付与します。

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosoOrg'      -UserPrincipalName 'user@domain.com' -PermissionsToCertificates create,get,list,delete
    ```

    または、ObjectId を使ってアクセス ポリシーを設定します (ObjectId は、Get-    AzADUser を実行すると取得できます)。

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -     ObjectId 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' -PermissionsToCertificates          create,get,list,delete
    ```

11. この例では、キー コンテナーに自己署名証明書を追加します。 クラウド サービスのロールにデプロイするためには、クラウド サービス構成 (.cscfg) ファイルに証明書の拇印を追加する必要があります。

    ```powershell
    $Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -       SubjectName "CN=contoso.com" -IssuerName "Self" -ValidityInMonths 6 -ReuseKeyOnRenewal 
    Add-AzKeyVaultCertificate -VaultName "ContosKeyVault" -Name "ContosCert" -      CertificatePolicy $Policy
    ```

12. OS プロファイル オブジェクトを作成します。 OS プロファイルは、クラウド サービスのロールに関連付ける証明書を指定するものです。 これは、前の手順で作成したのと同じ証明書になります。

    ```csharp
    CloudServiceOsProfile cloudServiceOsProfile = 
        new CloudServiceOsProfile 
           { 
                Secrets = new List<CloudServiceVaultSecretGroup> 
                   { 
                        New CloudServiceVaultSecretGroup { 
                        SourceVault = <sourceVault>, 
                        VaultCertificates = <vaultCertificates> 
                        } 
                   } 
           };
    ```

13. ロール プロファイル オブジェクトを作成します。 ロール プロファイルは、名前、容量、階層など、ロール SKU 固有のプロパティを定義するものです。 この例では、frontendRole と backendRole の 2 つのロールを定義しています。 ロール プロファイルの情報は、構成 (cscfg) ファイルとサービス定義 (csdef) ファイルの中で定義されているロール構成と一致している必要があります。

    ```csharp
    CloudServiceRoleProfile cloudServiceRoleProfile = new CloudServiceRoleProfile()
       {
            Roles = new List<CloudServiceRoleProfileProperties>();
            
                // foreach role in cloudService
                roles.Add(new CloudServiceRoleProfileProperties()
                    {
                        Name = 'ContosoFrontend',
                        Sku = new CloudServiceRoleSku
                        {
                            Name = 'Standard_D1_v2',
                            Capacity = 2,
                            Tier = 'Standard'
                        }
                    );
    
                roles.Add(new CloudServiceRoleProfileProperties()
                    {
                        Name = 'ContosoBackend',
                        Sku = new CloudServiceRoleSku
                        {
                            Name = 'Standard_D1_v2',
                            Capacity = 2,
                            Tier = 'Standard'
                        }
                    );
    
                    }
                    }
    ```

14. (省略可能) クラウド サービスに追加する拡張機能プロファイル オブジェクトを作成します。 この例では、RDP 拡張機能を追加します。

    ```csharp
    string rdpExtensionPublicConfig = "<PublicConfig>" +
        "<UserName>adminRdpTest</UserName>" +
        "<Expiration>2021-10-27T23:59:59</Expiration>" +
        "</PublicConfig>";
        string rdpExtensionPrivateConfig = "<PrivateConfig>" +
        "<Password>VsmrdpTest!</Password>" +
        "</PrivateConfig>";
    
        Extension rdpExtension = new Extension
                {
                    Name = name,
                    Properties = new CloudServiceExtensionProperties
                    {
                        Publisher = "Microsoft.Windows.Azure.Extensions",
                        Type = "RDP",
                        TypeHandlerVersion = "1.2.1",,
                        AutoUpgradeMinorVersion = true,
                        Settings = rdpExtensionPublicConfig,
                        ProtectedSettings = rdpExtensionPrivateConfig,
                        RolesAppliedTo = [“*”],
                    }
                };
                        
    CloudServiceExtensionProfile cloudServiceExtensionProfile = new CloudServiceExtensionProfile
        {
            Extensions = rdpExtension
        };
    ```

15. クラウド サービスのデプロイを作成します。

    ```csharp
    CloudService cloudService = new CloudService
        {
            Properties = new CloudServiceProperties
                {
                    RoleProfile = cloudServiceRoleProfile
                    Configuration = < Add Cscfg xml content here>,
                    // ConfigurationUrl = <Add you configuration URL here>,
                    PackageUrl = <Add cspkg SAS url here>,
                    ExtensionProfile = cloudServiceExtensionProfile,
                    OsProfile= cloudServiceOsProfile,
                    NetworkProfile = cloudServiceNetworkProfile,
                    UpgradeMode = 'Auto'
                },
                    Location = m_location
                };
    
    CloudService createOrUpdateResponse = m_CrpClient.CloudServices.CreateOrUpdate(“ContosOrg”, “ContosoCS”, cloudService);
    ```

## <a name="next-steps"></a>次のステップ
- Cloud Services (延長サポート) に関して[よく寄せられる質問](faq.md)を確認します。
- [Azure portal](deploy-portal.md)、[PowerShell](deploy-powershell.md)、[テンプレート](deploy-template.md)、または [Visual Studio](deploy-visual-studio.md) を使用してクラウド サービス (延長サポート) をデプロイします。
- [Cloud Services (延長サポート) のサンプル リポジトリ](https://github.com/Azure-Samples/cloud-services-extended-support)を確認します。