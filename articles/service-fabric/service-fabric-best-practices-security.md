---
title: Azure Service Fabric セキュリティに関するベスト プラクティス
description: Azure Service Fabric クラスターとアプリケーションを安全に保つためのベストプラクティスと設計上の考慮事項。
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: b7af0a4c26a47644973e936eb37e221853d74c03
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98784665"
---
# <a name="azure-service-fabric-security"></a>Azure Service Fabric のセキュリティ 

[Azure のセキュリティに関するベスト プラクティス](../security/index.yml)について詳しくは、「[Azure Service Fabric セキュリティに関するベスト プラクティス](../security/fundamentals/service-fabric-best-practices.md)」をご覧ください

## <a name="key-vault"></a>Key Vault

[Azure Key Vault](../key-vault/index.yml) は、Azure Service Fabric のアプリケーションとクラスターに対して推奨されるシークレット管理サービスです。
> [!NOTE]
> キー コンテナーからの証明書/シークレットが仮想マシン スケール セット シークレットとして仮想マシン スケール セットにデプロイされる場合は、キー コンテナーと仮想マシン スケール セットを同じ場所に置く必要があります。

## <a name="create-certificate-authority-issued-service-fabric-certificate"></a>証明機関発行の Service Fabric 証明書を作成する

Azure Key Vault 証明書は、作成するか、またはキー コンテナーにインポートすることができます。 Key Vault 証明書を作成すると、秘密キーはキー コンテナー内に作成され、証明書の所有者に公開されることはありません。 Key Vault で証明書を作成する方法は次のとおりです。

- 自己署名証明書を作成して公開/秘密キー ペアを作成して、証明書と関連付けます。 証明書は、独自のキーで署名されます。 
- 新しい証明書を手動で作成して公開/秘密キー ペアを作成し、X.509 証明書署名要求を生成します。 署名要求には、登録機関または証明機関が署名できます。 署名された x509 証明書を保留中のキー ペアとマージして、Key Vault の KV 証明を完成できます。 この方法では必要な手順が増えますが、秘密キーが作成されて Key Vault に限定されるため、セキュリティが強化されます。 これを次の図で説明します。 

詳しくは、[Azure Key Vault 証明書の作成方法](../key-vault/certificates/create-certificate.md)に関する記事をご覧ください。

## <a name="deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets"></a>Service Fabric クラスターの仮想マシン スケール セットに Key Vault 証明書をデプロイする

併置されているキー コンテナーから仮想マシン スケール セットに証明書をデプロイするには、仮想マシン スケール セットの [osProfile](/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) を使用します。 Resource Manager テンプレートのプロパティを次に示します。

```json
"secrets": [
   {
       "sourceVault": {
           "id": "[parameters('sourceVaultValue')]"
       },
       "vaultCertificates": [
          {
              "certificateStore": "[parameters('certificateStoreValue')]",
              "certificateUrl": "[parameters('certificateUrlValue')]"
          }
       ]
   }
]
```

> [!NOTE]
> コンテナーで Resource Manager テンプレートのデプロイが有効になっている必要があります。

## <a name="apply-an-access-control-list-acl-to-your-certificate-for-your-service-fabric-cluster"></a>Service Fabric クラスター用の証明書にアクセス制御リスト (ACL) を適用する

ノードのセキュリティを構成するには、[仮想マシン スケール セット拡張機能](/cli/azure/vmss/extension)のパブリッシャー Microsoft.Azure.ServiceFabric を使用します。
Service Fabric クラスター プロセスに対する証明書に ACL を適用するには、次の Resource Manager テンプレートのプロパティを使用します。

```json
"certificate": {
   "commonNames": [
       "[parameters('certificateCommonName')]"
   ],
   "x509StoreName": "[parameters('certificateStoreValue')]"
}
```

## <a name="secure-a-service-fabric-cluster-certificate-by-common-name"></a>共通名を使用して Service Fabric クラスターの証明書をセキュリティで保護する

証明書の `Common Name` を使用して Service Fabric クラスターをセキュリティ保護するには、Resource Manager テンプレートのプロパティ [certificateCommonNames](/rest/api/servicefabric/sfrp-model-clusterproperties#certificatecommonnames) を次のように使用します。

```json
"certificateCommonNames": {
    "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprint')]"
        }
    ],
    "x509StoreName": "[parameters('certificateStoreValue')]"
}
```

> [!NOTE]
> Service Fabric クラスターでは、ホストの証明書ストアで最初に見つかった有効な証明書が使用されます。 Windows では、これは共通名と発行者のサムプリントが一致し、有効期限が最も遅い証明書になります。

*\<YOUR SUBDOMAIN\>.cloudapp.azure.com や \<YOUR SUBDOMAIN\>.trafficmanager.net などの Azure ドメインは、Microsoft によって所有されています。 証明機関では、承認されていないユーザーにはドメインの証明書は発行されません。 証明機関にその一般名で証明書を発行してもらうには、ほとんどのユーザーは、レジストラーからドメインを購入するか、承認されたドメイン管理者になる必要があります。

自分のドメインが Microsoft の IP アドレスに解決されるように DNS サービスを構成する方法について詳しくは、[ドメインをホストするための Azure DNS](../dns/dns-delegate-domain-azure-dns.md) の構成方法に関する記事をご覧ください。

> [!NOTE]
> ドメイン ネーム サーバーを Azure DNS ゾーンのネーム サーバーに委任した後、DNS ゾーンに次の 2 つのレコードを追加します。
> - カスタム ドメインが解決されるすべての IP アドレスに対する `Alias record set` ではないドメイン APEX の "A" レコード。
> - ユーザーがプロビジョニングした、`Alias record set` ではないMicrosoft サブドメインの "C" レコード。 たとえば、Traffic Manager または Load Balancer の DNS 名を使用できます。

Service Fabric クラスター `"managementEndpoint"` のカスタム DNS 名が表示されるようにポータルを更新するには、Service Fabric クラスターの Resource Manager テンプレートの次のプロパティを更新します。

```json
 "managementEndpoint": "[concat('https://<YOUR CUSTOM DOMAIN>:',parameters('nt0fabricHttpGatewayPort'))]",
```

## <a name="encrypting-service-fabric-package-secret-values"></a>Service Fabric パッケージのシークレット値を暗号化する

Service Fabric パッケージで暗号化される一般的な値は、Azure Container Registry (ACR) の資格情報、環境変数、設定、および Azure ボリューム プラグインのストレージ アカウント キーなどです。

[Windows クラスターで暗号化証明書を設定してシークレットを暗号化する](./service-fabric-application-secret-management-windows.md)には:

シークレットを暗号化するための自己署名証明書を生成します。

```powershell
New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
```

「[Service Fabric クラスターの仮想マシン スケール セットに Key Vault 証明書をデプロイする](#deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets)」の説明に従って、Service Fabric クラスターの仮想マシン スケール セットに Key Vault 証明書をデプロイします。

次の PowerShell コマンドを使ってシークレットを暗号化した後、暗号化された値で Service Fabric のアプリケーション マニフェストを更新します。

``` powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

[Linux クラスターで暗号化証明書を設定してシークレットを暗号化する](./service-fabric-application-secret-management-linux.md)には:

シークレットを暗号化するための自己署名証明書を生成します。

```bash
user@linux:~$ openssl req -newkey rsa:2048 -nodes -keyout TestCert.prv -x509 -days 365 -out TestCert.pem
user@linux:~$ cat TestCert.prv >> TestCert.pem
```

「[Service Fabric クラスターの仮想マシン スケール セットに Key Vault 証明書をデプロイする](#deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets)」の説明に従って、Service Fabric クラスターの仮想マシン スケール セットに Key Vault 証明書をデプロイします。

次のコマンドを使ってシークレットを暗号化した後、暗号化された値で Service Fabric のアプリケーション マニフェストを更新します。

```bash
user@linux:$ echo "Hello World!" > plaintext.txt
user@linux:$ iconv -f ASCII -t UTF-16LE plaintext.txt -o plaintext_UTF-16.txt
user@linux:$ openssl smime -encrypt -in plaintext_UTF-16.txt -binary -outform der TestCert.pem | base64 > encrypted.txt
```

保護対象の値を暗号化した後、[Service Fabric アプリケーションで暗号化されたシークレットを指定](./service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application)し、[サービス コードから暗号化されたシークレットを暗号化解除](./service-fabric-application-secret-management.md#decrypt-encrypted-secrets-from-service-code)します。

## <a name="include-certificate-in-service-fabric-applications"></a>Service Fabric アプリケーションに証明書を含める

アプリケーションがシークレットにアクセスできるようにするには、アプリケーション マニフェストに **SecretsCertificate** 要素を追加することにより証明書を含めます。

```xml
<ApplicationManifest … >
  ...
  <Certificates>
    <SecretsCertificate Name="MyCert" X509FindType="FindByThumbprint" X509FindValue="[YourCertThumbrint]"/>
  </Certificates>
</ApplicationManifest>
```
## <a name="authenticate-service-fabric-applications-to-azure-resources-using-managed-service-identity-msi"></a>マネージド サービス ID (MSI) を使用して Azure リソースに対して Service Fabric アプリケーションを認証する

Azure リソースに対するマネージド ID について詳しくは、「[Azure リソースのマネージド ID とは](../active-directory/managed-identities-azure-resources/overview.md)」をご覧ください。
Azure Service Fabric クラスターは、[マネージド サービス ID](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources) をサポートする仮想マシン スケール セットでホストされています。
MSI を認証に使用できるサービスの一覧を取得するには、「[Azure AD 認証をサポートしている Azure サービス](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)」をご覧ください。


仮想マシン スケール セットの作成時に、または既存の仮想マシン スケール セットで、システム割り当てのマネージド ID を有効にするには、次の `"Microsoft.Compute/virtualMachinesScaleSets"` プロパティを宣言します。

```json
"identity": { 
    "type": "SystemAssigned"
}
```
詳しくは、「[Azure リソースのマネージド ID とは](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vmss.md#system-assigned-managed-identity)」をご覧ください。

[ユーザー割り当てのマネージド ID](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity) を作成した場合は、テンプレートで次のリソースを宣言して、仮想マシン スケール セットに割り当てます。 `\<USERASSIGNEDIDENTITYNAME\>` は、作成したユーザー割り当てのマネージド ID の名前に置き換えます。

```json
"identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
        "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
    }
}
```

Service Fabric アプリケーションでマネージド ID を使用するには、その前に、認証が必要な Azure リソースに対するアクセス許可を付与する必要があります。
次のコマンドでは、Azure リソースへのアクセスが許可されます。

```bash
principalid=$(az resource show --id /subscriptions/<YOUR SUBSCRIPTON>/resourceGroups/<YOUR RG>/providers/Microsoft.Compute/virtualMachineScaleSets/<YOUR SCALE SET> --api-version 2018-06-01 | python -c "import sys, json; print(json.load(sys.stdin)['identity']['principalId'])")

az role assignment create --assignee $principalid --role 'Contributor' --scope "/subscriptions/<YOUR SUBSCRIPTION>/resourceGroups/<YOUR RG>/providers/<PROVIDER NAME>/<RESOURCE TYPE>/<RESOURCE NAME>"
```

Service Fabric アプリケーションのコードで、次のような REST 呼び出しを行って、Azure Resource Manager に対する[アクセス トークンを取得](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md#get-a-token-using-http)します。

```bash
access_token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true | python -c "import sys, json; print json.load(sys.stdin)['access_token']")

```

その後は、Service Fabric アプリでアクセス トークンを使用して、Active Directory をサポートする Azure リソースへの認証を行うことができます。
次の例では、Cosmos DB リソースに対してこれを行う方法を示します。

```bash
cosmos_db_password=$(curl 'https://management.azure.com/subscriptions/<YOUR SUBSCRIPTION>/resourceGroups/<YOUR RG>/providers/Microsoft.DocumentDB/databaseAccounts/<YOUR ACCOUNT>/listKeys?api-version=2016-03-31' -X POST -d "" -H "Authorization: Bearer $access_token" | python -c "import sys, json; print(json.load(sys.stdin)['primaryMasterKey'])")
```
## <a name="windows-security-baselines"></a>Windows のセキュリティ ベースライン
[ベースラインを自分で作成するのではなく、Microsoft セキュリティ ベースラインのように、広く知られており、十分にテストされている業界標準の構成を実装することをお勧めします](/windows/security/threat-protection/windows-security-baselines)。これらを仮想マシン スケール セットにプロビジョニングするための 1 つの方法は、Azure Desired State Configuration (DSC) 拡張ハンドラーを使用して、VM をオンラインになるときに構成し、運用ソフトウェアが実行されるようにすることです。

## <a name="azure-firewall"></a>Azure Firewall
[Azure Firewall は、Azure Virtual Network リソースを保護する、クラウドベースのマネージド ネットワーク セキュリティ サービスです。これは、組み込みの高可用性とクラウドによる無制限のスケーラビリティを備えた、完全にステートフルなサービスとしてのファイアウォールです。](../firewall/overview.md)これによって、アウトバウンド HTTP/S トラフィックを、ワイルド カードを含む完全修飾ドメイン名 (FQDN) の指定した一覧に制限できます。 この機能に TLS/SSL 終了は必要ありません。 Windows Update に [Azure Firewall FQDN タグ](../firewall/fqdn-tags.md)を利用し、Microsoft Windows Update エンドポイントへのネットワーク トラフィックがファイアウォールを通過できるようにすることをお勧めします。 「[テンプレートを使用して Azure Firewall をデプロイする](../firewall/deploy-template.md)」では、Microsoft.Network/azureFirewalls リソース テンプレート定義のサンプルが提供されています。 Service Fabric アプリケーションに共通するファイアウォール規則では、クラスターの仮想ネットワークで以下が許可されます。

- *download.microsoft.com
- *servicefabric.azure.com
- *.core.windows.net

これらのファイアウォール規則は、許可されている送信ネットワーク セキュリティ グループを補完するものであり、お使いの仮想ネットワークから許可される宛先として ServiceFabric と Storage が含まれます。

## <a name="tls-12"></a>TLS 1.2

Microsoft Azure は、すべてのお客様がトランスポート層セキュリティ (TLS) 1.2 をサポートするソリューションへの移行を完了し、TLS 1.2 が既定で使用されることを確認することを[お勧めします](https://azure.microsoft.com/updates/azuretls12/)。

[Service Fabric](https://techcommunity.microsoft.com/t5/azure-service-fabric/microsoft-azure-service-fabric-6-3-refresh-release-cu1-notes/ba-p/791493) を含む Azure サービスでは、TLS 1.0/1.1 プロトコルの依存関係を削除するエンジニアリング作業を完了し、TLS 1.2 接続のみを受け入れて開始するようにワークロードを構成する必要があるお客様に完全なサポートを提供します。

お客様は、Azure でホストされるワークロードとオンプレミスのアプリケーションを Azure サービスと対話するように構成して、既定で TLS 1.2 を使用する必要があります。 特定の TLS バージョンを使用するには、[Service Fabric クラスター ノードとアプリケーションを構成する方法](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides/blob/master/Security/TLS%20Configuration.md)に関するページを参照してください。

## <a name="windows-defender"></a>Windows Defender 

Windows Server 2016 には、Windows Defender ウイルス対策が既定でインストールされます。 詳しくは、「[Windows Server 2016 の Windows Defender ウイルス対策](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016)」をご覧ください。 一部の SKU ではユーザー インターフェイスが既定でインストールされますが、必須ではありません。 Windows Defender によって発生するパフォーマンスへの影響とリソース消費のオーバーヘッドを小さくするには、セキュリティ ポリシーでオープン ソース ソフトウェアに対するプロセスとパスの除外が許可されている場合は、仮想マシン スケール セット拡張機能の Resource Manager テンプレートの次のプロパティを宣言して、Service Fabric クラスターをスキャンから除外します。


```json
 {
    "name": "[concat('VMIaaSAntimalware','_vmNodeType0Name')]",
    "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "IaaSAntimalware",
        "typeHandlerVersion": "1.5",
        "settings": {
            "AntimalwareEnabled": "true",
            "Exclusions": {
                "Paths": "[concat(parameters('svcFabData'), ';', parameters('svcFabLogs'), ';', parameters('svcFabRuntime'))]",
                "Processes": "Fabric.exe;FabricHost.exe;FabricInstallerService.exe;FabricSetup.exe;FabricDeployer.exe;ImageBuilder.exe;FabricGateway.exe;FabricDCA.exe;FabricFAS.exe;FabricUOS.exe;FabricRM.exe;FileStoreService.exe"
            },
            "RealtimeProtectionEnabled": "true",
            "ScheduledScanSettings": {
                "isEnabled": "true",
                "scanType": "Quick",
                "day": "7",
                "time": "120"
            }
        },
        "protectedSettings": null
    }
}
```

> [!NOTE]
> Windows Defender を使用していない場合は、お使いのマルウェア対策のドキュメントで構成ルールを参照してください。 Windows Defender は、Linux ではサポートされていません。

## <a name="platform-isolation"></a>プラットフォームの分離
既定では、Service Fabric アプリケーションには Service Fabric ランタイムそのものへのアクセスが許可され、それらのマニフェストの形式は異なります。ホストのファイル パスを指す[環境変数](service-fabric-environment-variables-reference.md)はアプリケーションと Fabric ファイルに相当し、内部プロセス通信エンドポイントはアプリケーション固有の要求を受け取り、クライアント証明書はアプリケーションが自身を認証するために使用すると Fabric が想定しているものです。 サービス自体が信頼できないコードをホストしている場合は、明示的に必要でない限り、SF ランタイムへのアクセスを無効にすることをお勧めします。 ランタイムへのアクセスは、アプリケーション マニフェストのポリシー セクション内の次の宣言を使用して削除されます。 

```xml
<ServiceManifestImport>
    <Policies>
        <ServiceFabricRuntimeAccessPolicy RemoveServiceFabricRuntimeAccess="true"/>
    </Policies>
</ServiceManifestImport>

```

## <a name="next-steps"></a>次のステップ

* Windows Server を実行している VM またはコンピューター上にクラスターを作成する:[Windows Server 用の Service Fabric クラスターの作成](service-fabric-cluster-creation-for-windows-server.md)。
* Linux を実行している VM またはコンピューター上にクラスターを作成する:[Linux クラスターの作成](service-fabric-cluster-creation-via-portal.md)。
* [Service Fabric のサポート オプション](service-fabric-support.md)について学びます。

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
