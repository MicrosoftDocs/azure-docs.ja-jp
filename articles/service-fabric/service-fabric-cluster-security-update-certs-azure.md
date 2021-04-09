---
title: Azure Service Fabric クラスターで証明書を管理する
description: Service Fabric クラスターに対して新しい証明書を追加、証明書をロールオーバー、および証明書を削除する方法について説明します。
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: 6dd4440d76bed9d110c13baab9f4e67b3a5c64c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94660918"
---
# <a name="add-or-remove-certificates-for-a-service-fabric-cluster-in-azure"></a>Azure Service Fabric クラスターの証明書の追加と削除
Service Fabric で X.509 証明書がどのように使用されるかを理解するために[クラスターのセキュリティに関するシナリオ](service-fabric-cluster-security.md)を読むことをお勧めします。 先に進む前に、クラスター証明書とは何であり、何の目的で使用されるかを理解しておく必要があります。

Azure Service Fabrics SDK の証明書の既定の読み込み動作では、プライマリとセカンダリの構成定義に関係なく、有効期限が最も先の日付になっている定義済みの証明書がデプロイされて使用されます。 従来の動作に戻すことは推奨されませんが、高度な操作として行うことはできます。このためには、`Fabric.Code` 構成で "UseSecondaryIfNewer" 設定パラメーター値を false に設定する必要があります。

Service Fabric では、クラスターの作成中に証明書セキュリティを構成するときに、クライアントの証明書に加えて 2 つのクラスター証明書 (プライマリとセカンダリ) を指定できます。 作成時にそれらを設定する方法について詳しくは、[ポータルからクラスターを作成する](service-fabric-cluster-creation-via-portal.md)方法に関する記事か、[Azure Resource Manager を使用して Azure クラスターを作成する](service-fabric-cluster-creation-via-arm.md)方法に関する記事をご覧ください。 作成時にクラスター証明書を 1 つだけ指定した場合は、それがプライマリ証明書として使用されます。 クラスターの作成後に、新しい証明書をセカンダリ証明書として追加できます。

> [!NOTE]
> セキュリティ保護されたクラスターでは、常に (失効も期限切れもしていない) 有効なクラスター証明書 (プライマリまたはセカンダリ) を 1 つ以上デプロイする必要があります。デプロイしなかった場合、クラスターの機能が停止します。 有効なすべての証明書が期限切れになる 90 日前に、警告トレースとノードの正常性に関する警告イベントが生成されます。 現時点では、証明書の有効期限について Service Fabric から送信される唯一の通知です。
> 
> 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="add-a-secondary-cluster-certificate-using-the-portal"></a>ポータルを使用してセカンダリのクラスター証明書を追加する
Azure Portal では、セカンダリのクラスター証明書を追加できません。Azure PowerShell を使用してください。 このプロセスはこのドキュメントの後半で説明されています。

## <a name="remove-a-cluster-certificate-using-the-portal"></a>ポータルを使用してクラスター証明書を削除する
セキュリティ保護されたクラスターでは、常に、有効な証明書 (失効も期限切れもしていない) が 1 つ以上必要です。 有効期限が最も先の日付でデプロイされている証明書が使用中になります。その証明書を削除するとクラスターの機能が停止するため、期限切れの証明書または有効期限が最も近い未使用の証明書のみを確実に削除します。

未使用のクラスター セキュリティ証明書を削除するには、[セキュリティ] セクションに移動して、未使用の証明書のコンテキスト メニューから [削除] オプションを選択します。

プライマリとマークされている証明書を削除しようとする場合は、自動ロールオーバーを有効にするために、有効期限がプライマリ証明書よりも先の日付のセカンダリ証明書をデプロイする必要があります。自動ロールオーバーが完了した後で、プライマリ証明書を削除します。

## <a name="add-a-secondary-certificate-using-azure-resource-manager"></a>Azure Resource Manager を使用してセカンダリ証明書を追加する

以下の手順は、Resource Manager の動作方法を理解していること、Resource Manager テンプレートを使用して少なくとも 1 つの Service Fabric クラスターをデプロイしていること、クラスターをセットアップするために使用したテンプレートが手元にあることを前提としています。 また、JSON を使いこなせることを前提としています。

> [!NOTE]
> 操作を理解するため、または作業の出発点とするために利用できるサンプル テンプレートおよびパラメーターをお探しの場合は、[こちらの Git リポジトリ](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Cert-Rollover-Sample)からダウンロードできます。 
> 
> 

### <a name="edit-your-resource-manager-template"></a>Resource Manager テンプレートを編集する

サンプル 5-VM-1-NodeTypes-Secure_Step2.JSON にはこれから行うすべての編集内容が含まれています。 このサンプルは、[Git リポジトリ](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Cert-Rollover-Sample)から入手できます。

**すべての手順に従う**

1. クラスターをデプロイするために使用した Resource Manager テンプレートを開きます。 (前のリポジトリからサンプルをダウンロードした場合、5-VM-1-NodeTypes-Secure_Step1.JSON を使用してセキュリティで保護されたクラスターをデプロイし、その後そのテンプレートを開きます)。

2. 型が "string" の **2 つの新しいパラメーター** "secCertificateThumbprint" と "secCertificateUrlValue" をテンプレートのパラメーター セクションに追加します。 次のコード スニペットをコピーしてテンプレートに追加できます。 テンプレートのソースによっては、これらは既に定義されています。この場合は次の手順に進みます。 
 
    ```json
       "secCertificateThumbprint": {
          "type": "string",
          "metadata": {
            "description": "Certificate Thumbprint"
          }
        },
        "secCertificateUrlValue": {
          "type": "string",
          "metadata": {
            "description": "Refers to the location URL in your key vault where the certificate was uploaded, it is should be in the format of https://<name of the vault>.vault.azure.net:443/secrets/<exact location>"
          }
        },
    
    ```

3. **Microsoft.ServiceFabric/clusters** リソースに変更を加えます。テンプレート内の "Microsoft.ServiceFabric/clusters" リソース定義を検索します。 その定義のプロパティ内に、次の JSON スニペットのような "Certificate" JSON タグが見つかります。
   
    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

    新しいタグ "thumbprintSecondary" を追加し、値 "[parameters('secCertificateThumbprint')]" を指定します。  

    これで、リソース定義は次のようになります (テンプレートのソースによっては、下のスニペットと完全に同じではない場合があります)。 

    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "thumbprintSecondary": "[parameters('secCertificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

    **証明書をロール オーバー** するには、新しい証明書をプライマリに指定し、現在のプライマリをセカンダリに移動します。 この結果、1 つのデプロイ手順で現在のプライマリ証明書が新しい証明書にロールオーバーされます。
    
    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('secCertificateThumbprint')]",
              "thumbprintSecondary": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

4. **すべて** の **Microsoft.Compute/virtualMachineScaleSets** リソース定義を変更します。Microsoft.Compute/virtualMachineScaleSets リソース定義を探します。 "virtualMachineProfile" の下の "publisher": "Microsoft.Azure.ServiceFabric" までスクロールします。

    Service Fabric のパブリッシャーの設定は、次のように表示されます。
    
    ![Json_Pub_Setting1][Json_Pub_Setting1]
    
    新しい証明書のエントリを追加します。
    
    ```json
                   "certificateSecondary": {
                        "thumbprint": "[parameters('secCertificateThumbprint')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                        }
                      },
    
    ```

    その結果、プロパティは次のようになります。
    
    ![Json_Pub_Setting2][Json_Pub_Setting2]
    
    **証明書をロール オーバー** するには、新しい証明書をプライマリに指定し、現在のプライマリをセカンダリに移動します。 この結果、現在の証明書が新しい証明書に 1 つのデプロイ手順でロールオーバーされます。     

    ```json
                   "certificate": {
                       "thumbprint": "[parameters('secCertificateThumbprint')]",
                       "x509StoreName": "[parameters('certificateStoreValue')]"
                         },
                   "certificateSecondary": {
                        "thumbprint": "[parameters('certificateThumbprint')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                        }
                      },
    ```

    その結果、プロパティは次のようになります。    
    ![Json_Pub_Setting3][Json_Pub_Setting3]

5. **すべて** の **Microsoft.Compute/virtualMachineScaleSets** リソース定義を変更します。Microsoft.Compute/virtualMachineScaleSets リソース定義を探します。 "OSProfile" の下にある "vaultCertificates": にスクロールします。 次のように表示されます。

    ![Json_Pub_Setting4][Json_Pub_Setting4]
    
    secCertificateUrlValue を追加します。 次のスニペットを使用します。
    
    ```json
                      {
                        "certificateStore": "[parameters('certificateStoreValue')]",
                        "certificateUrl": "[parameters('secCertificateUrlValue')]"
                      }
    
    ```
    生成される Json は次のようになります。
    ![Json_Pub_Setting5][Json_Pub_Setting5]


> [!NOTE]
> 手順 4 と 5 をテンプレート内のすべての Nodetypes/Microsoft.Compute/virtualMachineScaleSets リソース定義に対して実行します。 いずれか 1 つでも実行されないと、その仮想マシン スケール セットに証明書がインストールされず、(クラスターをセキュリティで保護する有効な証明書がない場合は) クラスターがダウンするなど、クラスターに予期しないエラーが発生します。 そのため、先に進む前にもう一度確認してください。
> 
> 

### <a name="edit-your-template-file-to-reflect-the-new-parameters-you-added-above"></a>追加した新しいパラメーターを反映するようにテンプレート ファイルを編集する
[git-repo](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Cert-Rollover-Sample) のサンプルを使用して操作する場合は、サンプル 5-VM-1-NodeTypes-Secure.parameters_Step2.JSON の変更から始めることができます 

Resource Manager テンプレートのパラメーター ファイルを編集し、secCertificateThumbprint と secCertificateUrlValue に 2 つの新しいパラメーターを追加します。 

```JSON
    "secCertificateThumbprint": {
      "value": "thumbprint value"
    },
    "secCertificateUrlValue": {
      "value": "Refers to the location URL in your key vault where the certificate was uploaded, it is should be in the format of https://<name of the vault>.vault.azure.net:443/secrets/<exact location>"
     },

```

### <a name="deploy-the-template-to-azure"></a>テンプレートを Azure にデプロイする

- これでテンプレートを Azure にデプロイする準備が整いました。 Azure PS のバージョン 1 以降のコマンド プロンプトを開きます。
- ご自分の Azure アカウントにサインインし、特定の Azure サブスクリプションを選択します。 1 つ以上の Azure サブスクリプションにアクセスできるユーザーにとって、これは重要な手順です。

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionId <Subscription ID> 

```

テンプレートをデプロイする前にテストを実行します。 クラスターの現在のデプロイ先であるリソース グループを使用します。

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>

```

そのリソース グループに、テンプレートをデプロイします。 クラスターの現在のデプロイ先であるリソース グループを使用します。 New-AzResourceGroupDeployment コマンドを実行します。 既定値の **incremental** を使用するため、モードを指定する必要はありません。

> [!NOTE]
> モードを [Complete (完了)] に設定すると、テンプレートにないリソースが誤って削除される可能性があります。 このため、このシナリオでは使用しないでください。
> 
> 

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>
```

同じ PowerShell コマンドの入力例は次のとおりです。

```powershell
$ResourceGroup2 = "chackosecure5"
$TemplateFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure_Step2.json"
$TemplateParmFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure.parameters_Step2.json"

New-AzResourceGroupDeployment -ResourceGroupName $ResourceGroup2 -TemplateParameterFile $TemplateParmFile -TemplateUri $TemplateFile -clusterName $ResourceGroup2

```

デプロイが完了したら、新しい証明書を使用してクラスターに接続し、クエリをいくつか実行します。 該当する場合は、 古い証明書を削除できます。 

自己署名証明書を使用している場合は、ローカルの TrustedPeople 証明書ストアにインポートすることを忘れないでください。

```powershell
######## Set up the certs on your local box
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)

```
ここでのクイック リファレンスは、セキュリティ保護されたクラスターに接続するための次のコマンドです: 

```powershell
$ClusterName= "chackosecure5.westus.cloudapp.azure.com:19000"
$CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7SD1D630F8F3" 

Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $CertThumbprint  `
    -FindType FindByThumbprint `
    -FindValue $CertThumbprint `
    -StoreLocation CurrentUser `
    -StoreName My
```
ここでのクイック リファレンスは、クラスターの正常性を取得するための次のコマンドです:

```powershell
Get-ServiceFabricClusterHealth 
```

## <a name="deploying-client-certificates-to-the-cluster"></a>クライアント証明書をクラスターにデプロイする

前の手順 5 で説明した方法を使用して、キー コンテナーからノードに証明書をデプロイできます。 別のパラメーターを定義して使用する必要があるのみです。


## <a name="adding-or-removing-client-certificates"></a>クライアント証明書を追加または削除する

クラスター証明書だけでなく、クライアント証明書を追加して Service Fabric クラスターで管理操作を実行できます。

管理と読み取り専用の 2 つの種類のクライアント証明書を追加できます。 追加した証明書は、クラスターの管理操作およびクエリ操作のアクセスを制御するために使用できます。 既定では、クラスター証明書は許可されている管理証明書の一覧に追加されます。

任意の数のクライアント証明書を指定できます。 追加や削除を実行するたびに、Service Fabric クラスターの構成が更新されます。


### <a name="adding-client-certificates---admin-or-read-only-via-portal"></a>ポータルを使用して管理または読み取り専用のクライアント証明書を追加する

1. [セキュリティ] セクションに移動し、セクション上部の [+ 認証] ボタンを選択します。
2. [認証を追加] セクションで、[認証方法] を [Read-only client (読み取り専用クライアント)] または [Admin client (管理クライアント)] から選択します。
3. 次に承認方法を選択します。 この証明書を検索する際に、サブジェクト名を使用するか拇印を指定するかを Service Fabric に示します。 一般的に、承認方法としてサブジェクト名を使用することはセキュリティ上お勧めしません。 

![クライアント証明書の追加][Add_Client_Cert]

### <a name="deletion-of-client-certificates---admin-or-read-only-using-the-portal"></a>ポータルを使用して管理または読み取り専用のクライアント証明書を削除する

クラスターのセキュリティ保護にセカンダリ証明書が使用されないようにするには、[セキュリティ] セクションに移動して指定の証明書のコンテキスト メニューから [削除] オプションを選択します。

## <a name="adding-application-certificates-to-a-virtual-machine-scale-set"></a>アプリケーション証明書を仮想マシン スケール セットに追加する

アプリケーションに使用する証明書をクラスターに追加する方法については、[こちらの PowerShell サンプル スクリプト](scripts/service-fabric-powershell-add-application-certificate.md)をご覧ください。

## <a name="next-steps"></a>次のステップ
クラスター管理の詳細については、次の記事を参照してください。

* [Service Fabric クラスターのアップグレード プロセスと機能](service-fabric-cluster-upgrade.md)
* [クライアント用のロールベースのアクセスの設定](service-fabric-cluster-security-roles.md)

<!--Image references-->
[Add_Client_Cert]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_13.PNG
[Json_Pub_Setting1]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_14.PNG
[Json_Pub_Setting2]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_15.PNG
[Json_Pub_Setting3]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_16.PNG
[Json_Pub_Setting4]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_17.PNG
[Json_Pub_Setting5]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_18.PNG


