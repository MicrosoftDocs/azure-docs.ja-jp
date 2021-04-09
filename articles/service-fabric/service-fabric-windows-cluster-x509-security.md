---
title: 証明書を使用して Windows 上のクラスターをセキュリティで保護する
description: Azure Service Fabric スタンドアロンまたはオンプレミスのクラスター内での通信と、クライアントとクラスターの間での通信をセキュリティで保護します。
ms.topic: conceptual
ms.date: 10/15/2017
ms.openlocfilehash: d75c644be47ea44f6a8a6ccac91b785af0132833
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791039"
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-x509-certificates"></a>X.509 証明書を使用して Windows 上のスタンドアロン クラスターを保護する
この記事では、スタンドアロン Windows クラスターの多様なノード間で行われる通信をセキュリティで保護する方法について説明します。 また、X.509 証明書を使用して、そのクラスターに接続しているクライアントを認証する方法についても説明します。 認証により、許可されたユーザーのみがクラスターやデプロイ済みアプリケーションにアクセスし、管理タスクを実行できるようになります。 証明書セキュリティは、クラスターの作成時にクラスターで有効にしておく必要があります。  

ノード間のセキュリティ、クライアントとノードの間のセキュリティ、ロールベースのアクセス制御などのクラスター セキュリティについて詳しくは、 [クラスターのセキュリティ シナリオ](service-fabric-cluster-security.md)に関する記事をご覧ください。

## <a name="which-certificates-do-you-need"></a>必要な証明書
まず、クラスターのノードの 1 つに [Windows Server 用の Service Fabric パッケージをダウンロード](service-fabric-cluster-creation-for-windows-server.md#download-the-service-fabric-for-windows-server-package)します。 ダウンロードしたパッケージの中に、ClusterConfig.X509.MultiMachine.json ファイルがあります。 このファイルを開き、次の properties セクションの下にある security のセクションを確認します。

```JSON
"security": {
    "metadata": "The Credential type X509 indicates this cluster is secured by using X509 certificates. The thumbprint format is d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
    "ClusterCredentialType": "X509",
    "ServerCredentialType": "X509",
    "CertificateInformation": {
        "ClusterCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },        
        "ClusterCertificateCommonNames": {
            "CommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint1,Thumbprint2,Thumbprint3,...]"
            }
            ],
            "X509StoreName": "My"
        },
        "ClusterCertificateIssuerStores": [
            {
                "IssuerCommonName": "[IssuerCommonName]",
                "X509StoreNames" : "Root"
            }
        ],
        "ServerCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },
        "ServerCertificateCommonNames": {
            "CommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint1,Thumbprint2,Thumbprint3,...]"
            }
            ],
            "X509StoreName": "My"
        },
        "ServerCertificateIssuerStores": [
            {
                "IssuerCommonName": "[IssuerCommonName]",
                "X509StoreNames" : "Root"
            }
        ],
        "ClientCertificateThumbprints": [
            {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": false
            },
            {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": true
            }
        ],
        "ClientCertificateCommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint1,Thumbprint2,Thumbprint3,...]",
                "IsAdmin": true
            }
        ],
        "ClientCertificateIssuerStores": [
            {
                "IssuerCommonName": "[IssuerCommonName]",
                "X509StoreNames": "Root"
            }
        ]
        "ReverseProxyCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },
        "ReverseProxyCertificateCommonNames": {
            "CommonNames": [
                {
                "CertificateCommonName": "[CertificateCommonName]"
                }
            ],
            "X509StoreName": "My"
        }
    }
},
```

このセクションは、スタンドアロンの Windows クラスターをセキュリティで保護するために必要な証明書について説明します。 クラスター証明書を指定する場合は、ClusterCredentialType の値を _X509_ に設定します。 外部接続用にサーバー証明書を指定する場合は、ServerCredentialType を _X509_ に設定します。 必須ではありませんが、クラスターをセキュリティで適切に保護するには、これらの証明書を両方とも用意することをお勧めします。 これらの値を *X509* に設定する場合は、対応する証明書も指定する必要があります。指定しないと、Service Fabric が例外をスローします。 一部のシナリオでは、_ClientCertificateThumbprints_ または _ReverseProxyCertificate_ のみを指定したほうがよいことがあります。 これらのシナリオでは、_ClusterCredentialType_ や _ServerCredentialType_ を _X509_ に設定する必要はありません。


> [!NOTE]
> [拇印](https://en.wikipedia.org/wiki/Public_key_fingerprint) は、証明書のプライマリ ID です。 作成する証明書の拇印を確認するには、「[証明書のサムプリントを取得する](/dotnet/framework/wcf/feature-details/how-to-retrieve-the-thumbprint-of-a-certificate)」をご覧ください。
> 
> 

次の表は、クラスターのセットアップに必要な証明書の一覧です。

| **CertificateInformation の設定** | **説明** |
| --- | --- |
| ClusterCertificate |テスト環境の場合に推奨されます。 クラスターのノード間の通信をセキュリティで保護するには、この証明書が必要です。 プライマリ証明書とアップグレード用のセカンダリ証明書の、2 つの異なる証明書を使用できます。 プライマリ証明書の拇印は Thumbprint セクションで設定し、セカンダリ証明書の拇印は ThumbprintSecondary 変数で設定します。 |
| ClusterCertificateCommonNames |運用環境の場合に推奨されます。 クラスターのノード間の通信をセキュリティで保護するには、この証明書が必要です。 1 つまたは 2 つのクラスター証明書の共通名を使用することができます。 CertificateIssuerThumbprint は、この証明書の発行者の拇印に対応します。 同じ共通名の証明書が複数使用されている場合、複数の発行者の拇印を指定できます。|
| ClusterCertificateIssuerStores |運用環境の場合に推奨されます。 この証明書はクラスター証明書の発行者と対応します。 ClusterCertificateCommonNames に発行者の拇印を指定する代わりに、このセクションで発行者の共通名とそれに対応するストア名を指定することができます。  これにより、クラスター発行者の証明書のロールオーバーが簡単になります。 複数のクラスター証明書が使用されている場合は、複数の発行者を指定できます。 空の IssuerCommonName では、X509StoreNames で指定されている対応するストア内のすべての証明書が許可されます。|
| ServerCertificate |テスト環境の場合に推奨されます。 この証明書は、クライアントがこのクラスターに接続しようとしたときに、クライアントに提示されます。 利便性を考えて、ClusterCertificate と ServerCertificate に同じ証明書を使用することもできます。 プライマリ証明書とアップグレード用のセカンダリ証明書の、2 つの異なるサーバー証明書を使用できます。 プライマリ証明書の拇印は Thumbprint セクションで設定し、セカンダリ証明書の拇印は ThumbprintSecondary 変数で設定します。 |
| ServerCertificateCommonNames |運用環境の場合に推奨されます。 この証明書は、クライアントがこのクラスターに接続しようとしたときに、クライアントに提示されます。 CertificateIssuerThumbprint は、この証明書の発行者の拇印に対応します。 同じ共通名の証明書が複数使用されている場合、複数の発行者の拇印を指定できます。 利便性を考えて、ClusterCertificateCommonNames と ServerCertificateCommonNames に同じ証明書を使用することもできます。 1 つまたは 2 つのサーバー証明書の共通名を使用することができます。 |
| ServerCertificateIssuerStores |運用環境の場合に推奨されます。 この証明書はサーバー証明書の発行者と対応します。 ServerCertificateCommonNames に発行者の拇印を指定する代わりに、このセクションで発行者の共通名とそれに対応するストア名を指定することができます。  これにより、サーバー発行者の証明書のロールオーバーが簡単になります。 複数のサーバー証明書が使用されている場合は、複数の発行者を指定できます。 空の IssuerCommonName では、X509StoreNames で指定されている対応するストア内のすべての証明書が許可されます。|
| ClientCertificateThumbprints |この証明書のセットを認証されたクライアントにインストールします。 クラスターへのアクセスを許可するコンピューターには、いくつかの異なるクライアント証明書をインストールできます。 各証明書の拇印は CertificateThumbprint 変数で設定します。 IsAdmin を *true* に設定した場合、この証明書がインストールされたクライアントは、クラスターに対して管理者権限による管理操作を実行できるようになります。 IsAdmin が *false* の場合、この証明書がインストールされたクライアントはユーザー アクセス権限 (通常は読み取り専用) で許可される操作のみ実行できます。 ロールの詳細については、「[Service Fabric のロールベースのアクセス制御](service-fabric-cluster-security.md#service-fabric-role-based-access-control)」をご覧ください。 |
| ClientCertificateCommonNames |CertificateCommonName には、最初のクライアント証明書の共通名を設定します。 CertificateIssuerThumbprint は、この証明書の発行者の拇印です。 共通名と発行者について詳しくは、「[証明書の使用](/dotnet/framework/wcf/feature-details/working-with-certificates)」をご覧ください。 |
| ClientCertificateIssuerStores |運用環境の場合に推奨されます。 この証明書は、クライアント証明書の発行者と対応します (管理者ロールと非管理者ロールの両方)。 ClientCertificateCommonNames に発行者の拇印を指定する代わりに、このセクションで発行者の共通名とそれに対応するストア名を指定することができます。  これにより、クライアント発行者の証明書のロールオーバーが簡単になります。 複数のクライアント証明書が使用されている場合は、複数の発行者を指定できます。 空の IssuerCommonName では、X509StoreNames で指定されている対応するストア内のすべての証明書が許可されます。|
| ReverseProxyCertificate |テスト環境の場合に推奨されます。 これは、[リバース プロキシ](service-fabric-reverseproxy.md)をセキュリティで保護する場合に指定できる、オプションの証明書です。 この証明書を使用している場合は、nodeTypes に reverseProxyEndpointPort を設定してください。 |
| ReverseProxyCertificateCommonNames |運用環境の場合に推奨されます。 これは、[リバース プロキシ](service-fabric-reverseproxy.md)をセキュリティで保護する場合に指定できる、オプションの証明書です。 この証明書を使用している場合は、nodeTypes に reverseProxyEndpointPort を設定してください。 |

次に、クラスター、サーバー、およびクライアント証明書が指定されているクラスター構成の例を示します。 クラスター/サーバー/リバース プロキシ証明書の場合、同じ証明書の種類について、拇印と共通名の両方を構成することはできません。

 ```JSON
 {
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "10-2017",
    "nodes": [{
        "nodeName": "vm0",
        "metadata": "Replace the localhost below with valid IP address or FQDN",
        "iPAddress": "10.7.0.5",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD0"
    }, {
        "nodeName": "vm1",
        "metadata": "Replace the localhost with valid IP address or FQDN",
        "iPAddress": "10.7.0.4",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r1",
        "upgradeDomain": "UD1"
    }, {
        "nodeName": "vm2",
        "iPAddress": "10.7.0.6",
        "metadata": "Replace the localhost with valid IP address or FQDN",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r2",
        "upgradeDomain": "UD2"
    }],
    "properties": {
        "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
        },
        "security": {
            "metadata": "The Credential type X509 indicates this cluster is secured by using X509 certificates. The thumbprint format is d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
            "ClusterCredentialType": "X509",
            "ServerCredentialType": "X509",
            "CertificateInformation": {
                "ClusterCertificateCommonNames": {
                  "CommonNames": [
                    {
                      "CertificateCommonName": "myClusterCertCommonName"
                    }
                  ],
                  "X509StoreName": "My"
                },
                "ClusterCertificateIssuerStores": [
                    {
                        "IssuerCommonName": "ClusterIssuer1",
                        "X509StoreNames" : "Root"
                    },
                    {
                        "IssuerCommonName": "ClusterIssuer2",
                        "X509StoreNames" : "Root"
                    }
                ],
                "ServerCertificateCommonNames": {
                  "CommonNames": [
                    {
                      "CertificateCommonName": "myServerCertCommonName",
                      "CertificateIssuerThumbprint": "7c fc 91 97 13 16 8d ff a8 ee 71 2b a2 f4 62 62 00 03 49 0d"
                    }
                  ],
                  "X509StoreName": "My"
                },
                "ClientCertificateThumbprints": [{
                    "CertificateThumbprint": "c4 c18 8e aa a8 58 77 98 65 f8 61 4a 0d da 4c 13 c5 a1 37 6e",
                    "IsAdmin": false
                }, {
                    "CertificateThumbprint": "71 de 04 46 7c 9e d0 54 4d 02 10 98 bc d4 4c 71 e1 83 41 4e",
                    "IsAdmin": true
                }]
            }
        },
        "reliabilityLevel": "Bronze",
        "nodeTypes": [{
            "name": "NodeType0",
            "clientConnectionEndpointPort": "19000",
            "clusterConnectionEndpointPort": "19001",
            "leaseDriverEndpointPort": "19002",
            "serviceConnectionEndpointPort": "19003",
            "httpGatewayEndpointPort": "19080",
            "applicationPorts": {
                "startPort": "20001",
                "endPort": "20031"
            },
            "ephemeralPorts": {
                "startPort": "20032",
                "endPort": "20062"
            },
            "isPrimary": true
        }
         ],
        "fabricSettings": [{
            "name": "Setup",
            "parameters": [{
                "name": "FabricDataRoot",
                "value": "C:\\ProgramData\\SF"
            }, {
                "name": "FabricLogRoot",
                "value": "C:\\ProgramData\\SF\\Log"
            }]
        }]
    }
}
 ```

## <a name="certificate-rollover"></a>証明書のロールオーバー
拇印ではなく証明書共通名を使用する場合、証明書のロールオーバーにクラスター構成のアップグレードは必要ありません。 発行者の拇印をアップグレードする場合、新しい拇印のリストには古いリストとの共通部分が含まれるようにしてください。 まず、新しい発行者の拇印を使用して構成をアップグレードし、その後、ストアに新しい証明書 (クラスター/サーバー証明書および発行者証明書の両方) をインストールする必要があります。 新しい発行者の証明書をインストールした後、最低 2 時間は古い発行者の証明書を証明書ストアに保持してください。
発行者のストアを使用している場合、発行者の証明書のロールオーバーのために構成のアップグレードを実行する必要はありません。 対応する証明書ストアにある、有効期限がより後にある、新しい発行者の証明書をインストールし、数時間経ったら古い発行者の証明書を削除します。

## <a name="acquire-the-x509-certificates"></a>X.509 証明書を取得します。
クラスター内の通信をセキュリティで保護するには、最初にクラスター ノード用の X.509 証明書を取得する必要があります。 さらに、承認されたコンピューターまたはユーザーだけがそのクラスターに接続できるように制限するには、クライアント コンピューター用に証明書を取得し、インストールする必要があります。

運用ワークロードを実行するクラスターの場合、 [証明機関 (CA)](https://en.wikipedia.org/wiki/Certificate_authority) で署名された X.509 証明書を使用してクラスターをセキュリティで保護します。 これらの証明書を取得する方法について詳しくは、[証明書の取得方法](/dotnet/framework/wcf/feature-details/how-to-obtain-a-certificate-wcf)に関する記事をご覧ください。 

証明書が正常に機能するには、次のようないくつかの特性を備えている必要があります。

* 証明書のプロバイダーが **Microsoft Enhanced RSA and AES Cryptographic Provider** でなければならない。

* RSA キーを作成するときは、キーが **2048 ビット** である必要がある。

* キー使用法拡張機能に、**Digital Signature, Key Encipherment (a0)** の値が含まれている。

* 拡張キー使用法拡張機能に、**サーバー認証** (OID: 1.3.6.1.5.5.7.3.1) および **クライアント認証** (OID: 1.3.6.1.5.5.7.3.2) の値が含まれている。

テスト目的で使用するクラスターの場合は、自己署名証明書を選択することができます。

その他の質問については、[証明書に関するよく寄せられる質問](./cluster-security-certificate-management.md#troubleshooting-and-frequently-asked-questions)を参照してください。

## <a name="optional-create-a-self-signed-certificate"></a>省略可能:自己署名証明書の作成
正しく保護できる自己署名証明書を作成する方法の 1 つが、C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\Secure ディレクトリの Service Fabric SDK フォルダーにある CertSetup.ps1 スクリプトを使用する方法です。 このファイルを編集して証明書の既定の名前を変更します。 (値 CN=ServiceFabricDevClusterCert を探します。)このスクリプトを `.\CertSetup.ps1 -Install` として実行します。

次に、保護されたパスワードを含む .pfx ファイルにその証明書をエクスポートします。 まず、証明書の拇印を取得します。 
1. **[スタート]** メニューから、 **[コンピューター証明書の管理]** を実行します。 

2. **Local Computer\Personal** フォルダーに移動して、作成した証明書を探します。 

3. その証明書をダブルクリックして開き、 **[詳細]** タブを選択して、下にスクロールして **[拇印]** を表示します。 

4. その拇印の値を次の PowerShell コマンドにコピーします。スペースは削除してください。 

5. 保護のために `String` 値を適切で安全なパスワードに変更して、PowerShell で次を実行します。

   ```powershell   
   $pswd = ConvertTo-SecureString -String "1234" -Force –AsPlainText
   Get-ChildItem -Path cert:\localMachine\my\<Thumbprint> | Export-PfxCertificate -FilePath C:\mypfx.pfx -Password $pswd
   ```

6. コンピューターにインストールされている証明書の詳細を表示するには、次の PowerShell コマンドを実行できます。

   ```powershell
   $cert = Get-Item Cert:\LocalMachine\My\<Thumbprint>
   Write-Host $cert.ToString($true)
   ```

Azure サブスクリプションがある場合は、「[Azure Resource Manager を使用して Service Fabric クラスターを作成する](service-fabric-cluster-creation-via-arm.md)」に記載された手順に従うこともできます。

## <a name="install-the-certificates"></a>証明書のインストール
証明書を作成した後、それをクラスター ノードにインストールできます。 最新の Windows PowerShell 3.x が既にノードにインストールされている必要があります。 クラスター証明書とサーバー証明書、およびセカンダリ証明書の各ノードで次の手順を繰り返します。

1. .pfx ファイルをノードにコピーします。

2. 管理者として PowerShell ウィンドウを開き、次のコマンドを入力します。 *$pswd* を、この証明書の作成に使用したパスワードに置き換えます。 *$PfxFilePath* を、そのノードにコピーした .pfx の完全なパスに置き換えます。
   
    ```powershell
    $pswd = "1234"
    $PfxFilePath ="C:\mypfx.pfx"
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My -FilePath $PfxFilePath -Password (ConvertTo-SecureString -String $pswd -AsPlainText -Force)
    ```
3. 次に、この証明書に対するアクセス制御を設定し、Network Service アカウントで実行される Service Fabric プロセスが次のスクリプトを実行することでそれを使用できるようにします。 証明書とサービス アカウントの **NETWORK SERVICE** の拇印を指定します。 証明書を **[スタート]**  >  **[コンピューター証明書の管理]** で開いて、 **[すべてのタスク]**  >  **[秘密キーの管理]** を表示することで、証明書の ACL が正しいかどうか確認できます。
   
    ```powershell
    param
    (
    [Parameter(Position=1, Mandatory=$true)]
    [ValidateNotNullOrEmpty()]
    [string]$pfxThumbPrint,
   
    [Parameter(Position=2, Mandatory=$true)]
    [ValidateNotNullOrEmpty()]
    [string]$serviceAccount
    )
   
    $cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object -FilterScript { $PSItem.ThumbPrint -eq $pfxThumbPrint; }
   
    # Specify the user, the permissions, and the permission type
    $permission = "$($serviceAccount)","FullControl","Allow" # "NT AUTHORITY\NetworkService" is the service account
    $accessRule = New-Object -TypeName System.Security.AccessControl.FileSystemAccessRule -ArgumentList $permission
   
    # Location of the machine-related keys
    $keyPath = Join-Path -Path $env:ProgramData -ChildPath "\Microsoft\Crypto\RSA\MachineKeys"
    $keyName = $cert.PrivateKey.CspKeyContainerInfo.UniqueKeyContainerName
    $keyFullPath = Join-Path -Path $keyPath -ChildPath $keyName
   
    # Get the current ACL of the private key
    $acl = (Get-Item $keyFullPath).GetAccessControl('Access')
   
    # Add the new ACE to the ACL of the private key
    $acl.SetAccessRule($accessRule)
   
    # Write back the new ACL
    Set-Acl -Path $keyFullPath -AclObject $acl -ErrorAction Stop
   
    # Observe the access rights currently assigned to this certificate
    get-acl $keyFullPath| fl
    ```
4. 各サーバー証明書について、前の手順を繰り返します。 これらの手順は、クラスターへのアクセスを許可するコンピューターにクライアント証明書をインストールするときにも使用できます。

## <a name="create-the-secure-cluster"></a>セキュリティで保護されたクラスターの作成
ClusterConfig.X509.MultiMachine.json ファイルの security セクションを構成した後は、「[クラスターを作成する](service-fabric-cluster-creation-for-windows-server.md#create-the-cluster)」セクションに進んで、ノードの構成とスタンドアロン クラスターの作成を行います。 クラスターを作成する際は、必ず ClusterConfig.X509.MultiMachine.json ファイルを使用してください。 たとえば、コマンドは次のようになります。

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

セキュリティで保護されたスタンドアロンの Windows クラスターを正常に実行し、認証されたクライアントがそのクラスターに接続できるようにセットアップしたら、「[PowerShell を使用してクラスターに接続する](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-powershell)」を参考にしてクラスターに接続してください。 次に例を示します。

```powershell
$ConnectArgs = @{  ConnectionEndpoint = '10.7.0.5:19000';  X509Credential = $True;  StoreLocation = 'LocalMachine';  StoreName = "MY";  ServerCertThumbprint = "057b9544a6f2733e0c8d3a60013a58948213f551";  FindType = 'FindByThumbprint';  FindValue = "057b9544a6f2733e0c8d3a60013a58948213f551"   }
Connect-ServiceFabricCluster $ConnectArgs
```

これでこのクラスターで動作する他の PowerShell コマンドを実行できます。 たとえば、[Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode) を実行すると、セキュリティで保護されたこのクラスターのノードが一覧表示されます。


クラスターを削除するには、Service Fabric パッケージをダウンロードしたクラスターのノードに接続し、コマンド ラインを開いてパッケージ フォルダーに移動します。 そして次のコマンドを実行します。

```powershell
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

> [!NOTE]
> 証明書の構成が正しくないと、デプロイ中にクラスターを起動できない場合があります。 セキュリティの問題を自己診断するには、イベント ビューアーのグループ **[アプリケーションとサービス ログ]**  >  **[Microsoft Service Fabric]** を参照してください。
> 
> 
