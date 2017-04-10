
---
title: "Azure Portal での Service Fabric クラスターの作成 | Microsoft Docs"
description: "この記事では、Azure ポータルと Azure Key Vault を使用して Azure でセキュリティ保護された Service Fabric クラスターを設定する方法について説明します。"
services: service-fabric
documentationcenter: .net
author: chackdan
manager: timlt
editor: vturecek
ms.assetid: 426c3d13-127a-49eb-a54c-6bde7c87a83b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/21/2017
ms.author: chackdan
translationtype: Human Translation
ms.sourcegitcommit: bb27d279396aa7b670187560cebe2ed074576bad
ms.openlocfilehash: c413f415cb056f079ed30cf444af4edbe20364ea
ms.lasthandoff: 01/25/2017


---
# <a name="create-a-service-fabric-cluster-in-azure-using-the-azure-portal"></a>Azure ポータルを使用して Azure で Service Fabric クラスターを作成する
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure ポータル](service-fabric-cluster-creation-via-portal.md)
> 
> 

これは、Azure ポータルを使用して Azure でセキュリティで保護された Service Fabric クラスターのセットアップの手順を説明するステップ バイ ステップ ガイドです。 このガイドでは、次の手順について説明します。

* クラスターのセキュリティのためのキーを管理する Key Vault を設定します。
* Azure ポータルを使用して Azure でセキュリティで保護されたクラスターを作成します。
* 証明書を使って管理者を認証します。

> [!NOTE]
> Azure Active Directory を使用したユーザー認証、アプリケーション セキュリティ用の証明書の設定など、詳細なセキュリティ オプションについては、[Azure Resource Manager を使用したクラスターの作成][create-cluster-arm]に関する記事を参照してください。
> 
> 

セキュリティで保護されたクラスターとは、管理操作に対する未承認のアクセスを防止するクラスターで、この操作には、アプリケーション、サービス、また格納されたデータのデプロイ、アップグレード、削除が含まれます。 セキュリティで保護されていないクラスターとは、だれでも管理操作にいつでも接続し、実行できるクラスターを指します。 セキュリティで保護されていないクラスターを作成することもできますが、**セキュリティで保護されたクラスターを作成することを強くお勧めします**。 セキュリティで保護されていないクラスターを**後でセキュリティで保護することはできません**。新しいクラスターを作成する必要があります。

セキュリティで保護されたクラスターの作成については、Linux クラスターであれ Windows クラスターであれ、考え方は同じです。 セキュリティで保護された Linux クラスターの作成に関する詳しい情報とヘルパー スクリプトについては、[「セキュリティで保護されたクラスターを Linux 上に作成する」](service-fabric-cluster-creation-via-arm.md#secure-linux-cluster)をご覧ください。 提供されるヘルパー スクリプトから得られるパラメーターは、「 [Azure ポータルでのクラスターの作成](#create-cluster-portal)」セクションの説明に従ってポータルに直接入力できます。

## <a name="log-in-to-azure"></a>Azure へのログイン
このガイドでは [Azure PowerShell][azure-powershell] を使用します。 新しい PowerShell セッションを開始した場合、Azure アカウントにログインし、Azure のコマンドを実行する前にサブスクリプションを選択します。

Azure アカウントにログインします。

```powershell
Login-AzureRmAccount
```

サブスクリプションを選択します。

```powershell
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="set-up-key-vault"></a>Key Vault の設定
ガイドのこのセクションでは、Azure Service Fabric クラスターおよび Service Fabric アプリケーション用の Key Vault を作成する手順を説明します。 Key Vault の完全なガイドについては、[Azure Key Vault の概要][key-vault-get-started]に関する記事を参照してください。

Service Fabric では X.509 証明書を使用して、クラスターをセキュリティ保護します。 Azure Key Vault を使用して、Azure で Service Fabric クラスター用の証明書を管理します。 Azure にクラスターがデプロイされると、Service Fabric クラスターの作成担当 Azure リソース プロバイダーにより Key Vault から証明書が取得されてクラスター VM にインストールされます。

次の図は、Key Vault、Service Fabric クラスター、そしてクラスターの作成時に Key Vault に格納された証明書を使用する Azure リソース プロバイダー間の関係を示しています。

![証明書のインストール][cluster-security-cert-installation]

### <a name="create-a-resource-group"></a>リソース グループの作成
最初の手順として、Key Vault 専用の新しいリソース グループを作成します。 Key Vault を独自のリソース グループに配置することをお勧めします。これにより、コンピューティングおよびストレージ リソース グループ (Service Fabric クラスターを持つリソース グループなど) をキーとシークレットを紛失することなく削除できます。 Key Vault を持つリソース グループは、それを使用するクラスターと同じリージョンにある必要があります。

```powershell

    PS C:\Users\vturecek> New-AzureRmResourceGroup -Name mycluster-keyvault -Location 'West US'
    WARNING: The output object type of this cmdlet will be modified in a future release.

    ResourceGroupName : mycluster-keyvault
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/<guid>/resourceGroups/mycluster-keyvault

```

### <a name="create-key-vault"></a>Key Vault の作成
新しいリソース グループに、Key Vault を作成します。 Key Vault を **デプロイ用に有効にして** 、Service Fabric リソース プロバイダーがそこから証明書を取得し、クラスター ノードにインストールできるようにする必要があります。

```powershell

    PS C:\Users\vturecek> New-AzureRmKeyVault -VaultName 'myvault' -ResourceGroupName 'mycluster-keyvault' -Location 'West US' -EnabledForDeployment


    Vault Name                       : myvault
    Resource Group Name              : mycluster-keyvault
    Location                         : West US
    Resource ID                      : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault
    Vault URI                        : https://myvault.vault.azure.net
    Tenant ID                        : <guid>
    SKU                              : Standard
    Enabled For Deployment?          : False
    Enabled For Template Deployment? : False
    Enabled For Disk Encryption?     : False
    Access Policies                  :
                                       Tenant ID                :    <guid>
                                       Object ID                :    <guid>
                                       Application ID           :
                                       Display Name             :    
                                       Permissions to Keys      :    get, create, delete, list, update, import, backup, restore
                                       Permissions to Secrets   :    all


    Tags                             :
```

Key Vault が既にある場合は、Azure CLI を使用してそれをデプロイ用に有効にできます。

```cli
> azure login
> azure account set "your account"
> azure config mode arm 
> azure keyvault list
> azure keyvault set-policy --vault-name "your vault name" --enabled-for-deployment true
```


## <a name="add-certificates-to-key-vault"></a>証明書の Key Vault への追加
Service Fabric では証明書を使用して、クラスターとそのアプリケーションのさまざまな側面をセキュリティで保護するための認証および暗号化を指定します。 Service Fabric での証明書の使用方法の詳細については、「[Service Fabric クラスターのセキュリティに関するシナリオ][service-fabric-cluster-security]」をご覧ください。

### <a name="cluster-and-server-certificate-required"></a>クラスターとサーバーの証明書 (必須)
この証明書はクラスターをセキュリティで保護し、クラスターに対する未承認のアクセスを防ぐために必要です。 証明書により、クラスター セキュリティが次のような方法で提供されます。

* **クラスター認証:** クラスター フェデレーション用のノード間通信を認証します。 この証明書で自分の ID を証明できたノードだけがクラスターに参加できます。
* **サーバー認証:** 管理クライアントに対するクラスター管理エンドポイントを認証します。これで、管理クライアントにより、実際のクラスターと通信していることが認識されるようになります。 この証明書は、HTTPS 管理 API および HTTPS 経由の Service Fabric Explorer に対して SSL も提供します。

この目的のため、証明書は次の要件を満たす必要があります。

* 証明書は秘密キーを含む必要があります。
* 証明書はキー交換のために作成され、Personal Information Exchange (.pfx) ファイルにエクスポートできる必要があります。
* 証明書の件名は Service Fabric クラスターへのアクセスに使用されるドメインと一致する必要があります。 これは、HTTPS 管理エンドポイントと Service Fabric Explorer 用の SSL を提供するために必要です。 証明機関 (CA) から `.cloudapp.azure.com` ドメインの SSL 証明書を取得することはできません。 クラスターのカスタム ドメイン名を取得します。 CA に証明書を要求するときは、証明書の件名がクラスターに使用するカスタム ドメイン名と一致している必要があります。

### <a name="client-authentication-certificates"></a>クライアント認証証明書
その他のクライアント証明書は、クラスター管理タスクに対して管理者を認証します。 Service Fabric には、**admin** および **read-only user** という&2; つのアクセス レベルがあります。 管理アクセスについて、少なくとも&1; つの証明書を使用する必要があります。 追加のユーザー レベル アクセスとして、別の証明書を指定する必要があります。 アクセス ロールの詳細については、「[ロールベースのアクセス制御 (Service Fabric クライアント用)][service-fabric-cluster-security-roles]」を参照してください。

Service Fabric を操作するために、クライアント認証証明書を Key Vault にアップロードする必要はありません。 この証明書は、クラスター管理を許可されているユーザーにのみ指定する必要があります。 

> [!NOTE]
> クライアントのクラスター管理操作を認証するには、Azure Active Directory を使用することをお勧めします。 Azure Active Directory を使用するには、[Azure Resource Manager を使用してクラスターを作成する][create-cluster-arm]必要があります。
> 
> 

### <a name="application-certificates-optional"></a>アプリケーション証明書 (省略可能)
アプリケーション セキュリティの目的で、任意の数の追加の証明書をクラスターにインストールできます。 クラスターを作成する前に、ノードにインストールする証明書を必要とするアプリケーション セキュリティ シナリオについて考慮します。これには次のようなものがあります。

* アプリケーション構成値の暗号化と復号化
* レプリケーション中のノード間のデータの暗号化 

Azure ポータルを使用してクラスターを作成した場合、アプリケーション証明書を構成することはできません。 クラスターのセットアップ時にアプリケーション証明書を構成するには、[Azure Resource Manager を使用してクラスターを作成する][create-cluster-arm]必要があります。 作成後に、アプリケーション証明書をクラスターに追加することもできます。

### <a name="formatting-certificates-for-azure-resource-provider-use"></a>Azure リソース プロバイダー用の証明書の書式設定
秘密キー ファイル (.pfx) を追加し、Key Vault を使用して直接使用できます。 ただし、Azure リソース プロバイダーは、base-64 でエンコードされた文字列としての .pfx と、秘密キーのパスワードを含む特別な JSON 形式にキーを格納する必要があります。 これらの要件に対応するには、キーを JSON 文字列に配置して、 *シークレット* として Key Vault に格納する必要があります。

このプロセスをわかりやすくするために、PowerShell モジュールを [GitHub から入手][service-fabric-rp-helpers]できます。 モジュールを使用するには、次の手順を実行します。

1. リポジトリの内容全体をローカル ディレクトリにダウンロードします。 
2. モジュールを PowerShell ウィンドウにインポートします。

```powershell
  PS C:\Users\vturecek> Import-Module "C:\users\vturecek\Documents\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"
```

この PowerShell モジュールの `Invoke-AddCertToKeyVault` コマンドは、証明書の秘密キーを JSON 文字列に自動的にフォーマットし、Key Vault にアップロードします。 これを使用してクラスター証明書と追加のアプリケーション証明書 (ある場合) を Key Vault に追加します。 必要に応じて、クラスターにインストールする追加の証明書についてこの手順を繰り返します。

```powershell
PS C:\Users\vturecek> Invoke-AddCertToKeyVault -SubscriptionId <guid> -ResourceGroupName mycluster-keyvault -Location "West US" -VaultName myvault -CertificateName mycert -Password "<password>" -UseExistingCertificate -ExistingPfxFilePath "C:\path\to\mycertkey.pfx"

    Switching context to SubscriptionId <guid>
    Ensuring ResourceGroup mycluster-keyvault in West US
    WARNING: The output object type of this cmdlet will be modified in a future release.
    Using existing valut myvault in West US
    Reading pfx file from C:\path\to\key.pfx
    Writing secret to myvault in vault myvault


Name  : CertificateThumbprint
Value : <value>

Name  : SourceVault
Value : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault

Name  : CertificateURL
Value : https://myvault.vault.azure.net:443/secrets/mycert/4d087088df974e869f1c0978cb100e47

```

ノードの認証、管理エンドポイントのセキュリティおよび認証、X.509 証明書を使用する追加のアプリケーション セキュリティ機能用の証明書をインストールする Service Fabric クラスターの Resource Manager テンプレートを構成するためのすべての Key Vault 前提条件について説明しました。 この時点で、Azure で以下の設定が完了しています。

* Key Vault リソース グループ
  * Key Vault
    * クラスター サーバー認証証明書

</a "create-cluster-portal" ></a>

## <a name="create-cluster-in-the-azure-portal"></a>Azure ポータルでのクラスターの作成
### <a name="search-for-the-service-fabric-cluster-resource"></a>Service Fabric クラスター リソースの検索
![Azure ポータルで Service Fabric クラスター テンプレートを検索します。][SearchforServiceFabricClusterTemplate]

1. [Azure Portal][azure-portal] にサインインします。
2. **[新規]** をクリックして、新しいリソース テンプレートを追加します。 **[Marketplace]** の **[すべて]** で、Service Fabric クラスター テンプレートを検索します。
3. 一覧から **[Service Fabric クラスター]** を選択します。
4. **[Service Fabric クラスター]** ブレードに移動し、**[作成]** をクリックします。
5. **[Service Fabric クラスターの作成]** ブレードには次の&4; つの手順があります。

#### <a name="1-basics"></a>1.基本
![新しいリソース グループを作成するスクリーン ショット。][CreateRG]

[基本] ブレードでは、クラスターの基本情報を提供する必要があります。

1. クラスターの名前を入力します。
2. VM 向けリモート デスクトップの**ユーザー名**と**パスワード**を入力します。
3. クラスターをデプロイする **サブスクリプション** を選択します (特に、複数のサブスクリプションがある場合)。
4. **新しいリソース グループ**を作成します。 クラスター名と同じにすると、後で検索が楽になります。デプロイメントを変更したり、クラスターを削除したりするときに特に便利です。
   
   > [!NOTE]
   > 既存のリソース グループを使用することもできますが、新しいリソース グループを作成することをお勧めします。 こうすることで、不要になったクラスターを簡単に削除できます。
   > 
   > 
5. クラスターを作成する **リージョン** を選択します。 Key Vault が存在するのと同じリージョンを使用する必要があります。

#### <a name="2-cluster-configuration"></a>2.クラスター構成
![ノード タイプの作成][CreateNodeType]

クラスター ノードを構成します。 ノードのタイプには、VM のサイズ、VM の数、プロパティが定義されています。 クラスターには複数のノードのタイプを指定できますが、プライマリ ノードのタイプ (ポータルに最初に定義したノード) には、少なくとも&5; つの VM が必要です。これが Service Fabric システム サービスが配置されるノードのタイプになります。 "NodeTypeName" の既定の配置プロパティは自動的に追加されるため、**[配置プロパティ]** を構成しないでください。

> [!NOTE]
> 複数のノードのタイプの一般的なシナリオは、フロントエンド サービスとバックエンド サービスを含むアプリケーションです。 フロントエンド サービスを小規模の VM (D2 のような VM サイズ) に配置します。この小規模 VM では、インターネットにポートを開いています。ただし、バックエンド サービスはインターネットに接続されたポートが開いていない大規模な VM (D4、D6、D15 などの VM サイズ) に配置します。
> 
> 

1. ノードのタイプの名前を選択します (英字と数字のみを含む 1 ～ 12 文字)。
2. プライマリ ノード タイプの最小 VM **サイズ**は、クラスターに選択した**耐久性**レベルによって決まります。 既定の耐久性レベルはブロンズです。 耐久性の詳細については、[Service Fabric クラスターの信頼性と耐久性の選択方法][service-fabric-cluster-capacity]に関する記事を参照してください。
3. VM サイズと価格レベルを選択します。 D シリーズ VM には SSD ドライブが備わっており、ステートフルなアプリケーションに最適です。 部分的なコアを持つ VM SKU や使用可能なディスク容量が 7 GB 未満の VM SKU は使用しないでください。 
4. プライマリ ノード タイプの最低 VM **数**は、選択した**信頼性**レベルによって決まります。 既定の信頼性レベルは Silver です。 信頼性の詳細については、[Service Fabric クラスターの信頼性と耐久性の選択方法][service-fabric-cluster-capacity]に関する記事を参照してください。
5. ノードのタイプの VM 数を選択します。 後でノード タイプの VM 数を増減できますが、プライマリのノード タイプの場合、選択した信頼性レベルによって最小値が決まります。 他のノード タイプには、VM 数に最小値の 1 を設定できます。
6. カスタム エンドポイントを構成します。 このフィールドでは、アプリケーション用にパブリック インターネットに Azure Load Balancer 経由で公開するポートのコンマ区切りのリストを入力できます。 たとえば、Web アプリケーションをクラスターにデプロイする予定がある場合は、「80」と入力して、クラスターへのポート 80 でのトラフィックを許可します。 エンドポイントの詳細については、[アプリケーションとの通信][service-fabric-connect-and-communicate-with-services]に関する記事を参照してください。
7. クラスター **診断**を構成します。 既定では、問題のトラブルシューティングのためクラスターで診断が有効になります。 診断を無効にするには、**[ステータス]** を **[オフ]** に切り替えます。 診断をオフにすることは **推奨されません** 。
8. クラスターを設定するファブリック アップグレード モードを選択します。 使用可能な最新バージョンをシステムで自動的に選択し、クラスターをそのバージョンにアップグレード場合は、 **[自動]**を選択します。 サポートされるバージョンを選択する場合は、 **マニュアル**モードに設定します。

> [!NOTE]
> サポートされるバージョンの Service Fabric を実行しているクラスターのみがサポート対象になります。 **マニュアル** モードを選択すると、自身の責任でクラスターをサポートされるバージョンにアップグレードする必要があります。 Fabric のアップグレード モードの詳細については、[service-fabric-cluster-upgrade ドキュメント][service-fabric-cluster-upgrade]を参照してください。
> 
> 

#### <a name="3-security"></a>手順&3;.セキュリティ
![Azure ポータルのセキュリティ構成のスクリーン ショット。][SecurityConfigs]

最後の手順では、前の手順で作成した Key Vault と証明書情報を使用してクラスターをセキュリティで保護するための証明書情報を指定します。

* `Invoke-AddCertToKeyVault` PowerShell コマンドを使って**クラスター証明書**を Key Vault にアップロードして取得した出力をプライマリ証明書フィールドに設定します。

```powershell
Name  : CertificateThumbprint
Value : <value>

Name  : SourceVault
Value : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault

Name  : CertificateURL
Value : https://myvault.vault.azure.net:443/secrets/mycert/4d087088df974e869f1c0978cb100e47
```

* **[詳細設定の構成]** ボックスをオンにして、**管理用クライアント**と**読み取り専用クライアント**のクライアント証明書を入植します。 該当する場合は、これらのフィールドに管理用クライアント証明書の拇印と、読み取り専用ユーザー クライアント証明書の拇印を入力します。 管理者がクラスターに接続しようとした場合、ここに入力した拇印値に一致する拇印を持つ証明書がある場合にのみアクセスが許可されます。  

#### <a name="4-summary"></a>4.概要
!["Service Fabric クラスターのデプロイ中" が表示されているスタート画面のスクリーン ショット。 ][Notifications]

クラスターの作成を完了するには、**[概要]** をクリックして設定した構成を確認するか、クラスターのデプロイに使用する Azure Resource Manager テンプレートをダウンロードします。 必須の設定を指定すると、**[OK]** ボタンが緑色になります。このボタンをクリックして、クラスター作成プロセスを開始できます。

通知には作成の進行状況が表示されます (画面の右上にあるステータス バーの近くの "ベル" アイコンをクリックします)。クラスターの作成中に **[スタート画面にピン留めする]** をクリックした場合、**[Deploying Service Fabric Cluster (Service Fabric クラスターのデプロイ)]** が**[スタート]** 画面にピン留めされます。

### <a name="view-your-cluster-status"></a>クラスターの状態を表示する
![ダッシュボードのクラスターの詳細のスクリーン ショット。][ClusterDashboard]

クラスターの作成後、ポータルでクラスターを検査できます。

1. **[参照]** に移動し、**[Service Fabric クラスター]** をクリックします。
2. クラスターを探してクリックします。
3. これにより、クラスターのパブリック エンドポイント、Service Fabric Explorer へのリンクなどのクラスターに関する詳細が、ダッシュボードに表示されます。

クラスターのダッシュボード ブレードの **[ノード モニター]** セクションには、正常な VM 数と正常ではない VM 数が表示されます。 クラスターの正常性の詳細については、[Service Fabric の正常性モデルの概要][service-fabric-health-introduction]に関するページを参照してください。

> [!NOTE]
> Service Fabric クラスターが常に稼働状態になって可用性を維持し、その状態を保持するには、一定数のノードが常にアップしている必要があります。これは、「維持クォーラム」と呼ばれます。 そのため、最初に[状態の完全バックアップ][service-fabric-reliable-services-backup-restore]を実行していない限り、クラスター内のすべてのコンピューターをシャットダウンするのは一般に安全ではありません。
> 
> 

## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>仮想マシン スケール セット インスタンスまたはクラスター ノードにリモート接続する
クラスターで指定する NodeType ごとに、VM スケール セットがセットアップされます。 詳細については、[VM スケール セットのインスタンスへのリモート接続][remote-connect-to-a-vm-scale-set]に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ
この時点で、管理の認証に証明書を使用したセキュリティで保護されたクラスターがあります。 次に、[クラスターに接続](service-fabric-connect-to-secure-cluster.md)して、[アプリケーション シークレットを管理](service-fabric-application-secret-management.md)する方法を説明します。  また、[Service Fabric のサポート オプション](service-fabric-support.md)について学びます。

<!-- Links -->
[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[service-fabric-rp-helpers]: https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers
[azure-portal]: https://portal.azure.com/
[key-vault-get-started]: ../key-vault/key-vault-get-started.md
[create-cluster-arm]: service-fabric-cluster-creation-via-arm.md
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[service-fabric-cluster-security-roles]: service-fabric-cluster-security-roles.md
[service-fabric-cluster-capacity]: service-fabric-cluster-capacity.md
[service-fabric-connect-and-communicate-with-services]: service-fabric-connect-and-communicate-with-services.md
[service-fabric-health-introduction]: service-fabric-health-introduction.md
[service-fabric-reliable-services-backup-restore]: service-fabric-reliable-services-backup-restore.md
[remote-connect-to-a-vm-scale-set]: service-fabric-cluster-nodetypes.md#remote-connect-to-a-vm-scale-set-instance-or-a-cluster-node
[service-fabric-cluster-upgrade]: service-fabric-cluster-upgrade.md

<!--Image references-->
[SearchforServiceFabricClusterTemplate]: ./media/service-fabric-cluster-creation-via-portal/SearchforServiceFabricClusterTemplate.png
[CreateRG]: ./media/service-fabric-cluster-creation-via-portal/CreateRG.png
[CreateNodeType]: ./media/service-fabric-cluster-creation-via-portal/NodeType.png
[SecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/SecurityConfigs.png
[Notifications]: ./media/service-fabric-cluster-creation-via-portal/notifications.png
[ClusterDashboard]: ./media/service-fabric-cluster-creation-via-portal/ClusterDashboard.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png

