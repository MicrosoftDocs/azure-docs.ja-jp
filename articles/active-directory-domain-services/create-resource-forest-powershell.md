---
title: Azure PowerShell を使用して Azure AD Domain Services のリソース フォレストを作成する | Microsoft Docs
description: この記事では、Azure Active Directory Domain Services のリソース フォレスト、およびオンプレミスの Active Directory Domain Services 環境への送信フォレストを、Azure PowerShell を使用して作成および構成する方法について説明します。
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/27/2020
ms.author: justinha
ms.openlocfilehash: ebfc2476b7955b926f86094de03973155386eb8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619969"
---
# <a name="create-an-azure-active-directory-domain-services-resource-forest-and-outbound-forest-trust-to-an-on-premises-domain-using-azure-powershell"></a>Azure Active Directory Domain Services のリソース フォレスト、およびオンプレミス ドメインへの送信フォレストの信頼を、Azure PowerShell を使用して作成する

パスワード ハッシュを同期できない環境、またはスマート カードを使用して排他的にサインインするために自分でパスワードがわからないユーザーが含まれる環境では、Azure Active Directory Domain Services (Azure AD DS) でリソース フォレストを使用できます。 リソース フォレストでは、Azure AD DS から 1 つまたは複数のオンプレミスの AD DS 環境への一方向の送信の信頼が使用されます。 この信頼関係により、ユーザー、アプリケーション、およびコンピューターは、Azure AD DS マネージド ドメインから、オンプレミスのドメインに対して認証を行うことができます。 リソース フォレストでは、オンプレミスのパスワード ハッシュが同期されることはありません。

![Azure AD DS からオンプレミスの AD DS へのフォレストの信頼の図](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

この記事では、次の方法について説明します。

> [!div class="checklist"]
> * Azure PowerShell を使用して Azure AD DS リソース フォレストを作成する
> * Azure PowerShell を使用して、マネージド ドメインで一方向の送信フォレストの信頼を作成する
> * マネージド ドメイン接続をサポートするように、オンプレミスの AD DS 環境で DNS を構成する
> * オンプレミスの AD DS 環境で一方向の受信フォレストの信頼を作成する
> * 認証とリソース アクセスのために信頼関係をテストおよび検証する

Azure サブスクリプションをお持ちでない場合は、始める前に[アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。

> [!IMPORTANT]
> マネージド ドメイン リソース フォレストでは、Azure HDInsight および Azure Files は現時点ではサポートされていません。 既定のマネージド ドメイン ユーザー フォレストでは、これらの追加サービスの両方がサポートされています。

## <a name="prerequisites"></a>前提条件

この記事を完了するには、以下のリソースと特権が必要です。

* 有効な Azure サブスクリプション
    * Azure サブスクリプションをお持ちでない場合は、[アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。
* ご利用のサブスクリプションに関連付けられた Azure Active Directory テナント (オンプレミス ディレクトリまたはクラウド専用ディレクトリと同期されていること)。
    * 必要に応じて、[Azure Active Directory テナントを作成][create-azure-ad-tenant]するか、[ご利用のアカウントに Azure サブスクリプションを関連付け][associate-azure-ad-tenant]ます。

* Azure PowerShell のインストールおよび構成。
    * 必要であれば、手順に従って、[Azure PowerShell モジュールをインストールし、Azure サブスクリプションに接続](/powershell/azure/install-az-ps)します。
    * 必ず [Connect-AzAccount][Connect-AzAccount] コマンドレットを使用して Azure サブスクリプションにサインインしてください。
* Azure AD PowerShell をインストールして構成します。
    * 必要であれば、手順に従って、[Azure AD PowerShell モジュールをインストールして Azure AD に接続](/powershell/azure/active-directory/install-adv2)します。
    * 必ず [Connect-AzureAD][Connect-AzureAD] コマンドレットを使用して Azure AD テナントにサインインしてください。
* Azure AD DS を有効にするには、Azure AD テナントに "*全体管理者*" 特権が必要です。
* 必要な Azure AD DS リソースを作成するためには、ご利用の Azure サブスクリプションに "*共同作成者*" 特権が必要です。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

この記事では、Azure portal を使用してマネージド ドメインからの送信フォレストの信頼を作成して構成します。 最初に、[Azure portal](https://portal.azure.com) にサインインします。

## <a name="deployment-process"></a>デプロイ プロセス

これは、マネージド ドメイン リソース フォレストを、次いでオンプレミスの AD DS への信頼関係を作成するための複数パートから成るプロセスです。 次の大まかな手順で、信頼されたハイブリッド環境を構築します。

1. マネージド ドメイン サービス プリンシパルを作成する。
1. マネージド ドメイン リソース フォレストを作成する。
1. サイト間 VPN または ExpressRoute を使用してハイブリッド ネットワーク接続を作成する。
1. 信頼関係のマネージド ドメイン側を作成する。
1. 信頼関係のオンプレミス AD DS 側を作成する。

開始する前に、[ネットワークに関する考慮事項、フォレストの名前作成、DNS の要件](tutorial-create-forest-trust.md#networking-considerations)について理解しておく必要があります。 マネージド ドメインのフォレスト名は、一度デプロイすると変更できません。

## <a name="create-the-azure-ad-service-principal"></a>Azure AD サービス プリンシパルを作成する

Azure AD DS には、Azure AD からのサービス プリンシパル同期データが必要です。 このプリンシパルは、マネージド ドメイン リソース フォレストを作成するより前に、Azure AD テナントに作成する必要があります。

Azure AD DS による通信や、それ自身の認証が行えるようにするため、Azure AD サービス プリンシパルを作成します。 ID *6ba9a5d4-8456-4118-b521-9c5ca10cdf84* を持つ *Domain Controller Services* という名前の特定のアプリケーション ID が使用されます。 このアプリケーション ID は変更しないでください。

[New-AzureADServicePrincipal][New-AzureADServicePrincipal] コマンドレットを使用して Azure AD サービス プリンシパルを作成します。

```powershell
New-AzureADServicePrincipal -AppId "6ba9a5d4-8456-4118-b521-9c5ca10cdf84"
```

## <a name="create-a-managed-domain-resource-forest"></a>マネージド ドメイン リソース フォレストを作成する

マネージド ドメイン リソース フォレストを作成するには、`New-AzureAaddsForest` スクリプトを使用します。 このスクリプトは、一方向にバインドされたフォレストの作成 (次のセクション参照) など、マネージド ドメイン リソース フォレストの作成と管理をサポートする広範なコマンド セットの一部です。 これらのスクリプトは [PowerShell ギャラリー](https://www.powershellgallery.com/)から入手でき、Azure AD エンジニアリング チームによってデジタル署名されています。

1. まず、[New-AzResourceGroup][New-AzResourceGroup] コマンドレットを使用してリソース グループを作成します。 次の例では、リソース グループは *myResourceGroup* という名前が付けられ、*westus* リージョンに作成されます。 独自の名前と希望するリージョンを使用します。

    ```azurepowershell
    New-AzResourceGroup `
      -Name "myResourceGroup" `
      -Location "WestUS"
    ```

1. [Install-Script][Install-Script] コマンドレットを使用して、[PowerShell ギャラリー][powershell-gallery]から `New-AaddsResourceForestTrust` スクリプトをインストールします。

    ```powershell
    Install-Script -Name New-AaddsResourceForestTrust
    ```

1. `New-AzureAaddsForest` スクリプトに必要な次のパラメーターを確認します。 前提条件となっている **Azure PowerShell** モジュールと **Azure AD PowerShell** モジュールがあることも確認します。 アプリケーションとオンプレミスの接続を提供する仮想ネットワーク要件について計画済みであることも確認します。

    | 名前                         | スクリプト パラメーター          | 説明 |
    |:-----------------------------|---------------------------|:------------|
    | サブスクリプション                 | *-azureSubscriptionId*    | Azure AD DS の請求に使用されるサブスクリプション ID。 サブスクリプションの一覧は、[Get-AzureRMSubscription][Get-AzureRMSubscription] コマンドレットを使用して取得できます。 |
    | リソース グループ               | *-aaddsResourceGroupName* | マネージド ドメインおよび関連付けられているリソースのリソース グループの名前。 |
    | 場所                     | *-aaddsLocation*          | 使用しているマネージド ドメインがホストされる Azure リージョン。 利用可能なリージョンについては、[Azure AD DS でサポートされているリージョン](https://azure.microsoft.com/global-infrastructure/services/?products=active-directory-ds&regions=all)を参照してください。 |
    | Azure AD DS 管理者    | *-aaddsAdminUser*         | 最初のマネージド ドメイン管理者のユーザー プリンシパル名。 このアカウントは、Azure Active Directory の既存のクラウド ユーザー アカウントである必要があります。 このユーザー、およびスクリプトを実行しているユーザーは、*AAD DC 管理者* グループに追加されます。 |
    | Azure AD DS ドメイン名      | *-aaddsDomainName*        | フォレスト名の選択方法に関する前のガイダンスに基づく、マネージド ドメインの FQDN。 |

    Azure 仮想ネットワークと Azure AD DS サブネットがまだ存在しない場合は、`New-AzureAaddsForest` スクリプトを実行することにより、これらのリソースを作成できます。 このスクリプトでは、オプションで次を指定することにより、ワークロード サブネットも作成できます。

    | 名前                              | スクリプト パラメーター                  | 説明 |
    |:----------------------------------|:----------------------------------|:------------|
    | 仮想ネットワーク名              | *-aaddsVnetName*                  | マネージド ドメインの仮想ネットワークの名前。|
    | アドレス空間                     | *-aaddsVnetCIDRAddressSpace*      | CIDR 表記の仮想ネットワークのアドレス範囲 (仮想ネットワークを作成する場合)。|
    | Azure AD DS サブネット名           | *-aaddsSubnetName*                | マネージド ドメインをホストしている *aaddsVnetName* 仮想ネットワークのサブネットの名前。 このサブネットには、独自の VM やワークロードをデプロイしないでください。 |
    | Azure AD DS のアドレス範囲         | *-aaddsSubnetCIDRAddressRange*    | AAD DS インスタンスの、CIDR 表記のサブネット アドレス範囲 (*192.168.1.0/24* など)。 アドレス範囲は、仮想ネットワークのアドレス範囲に含まれており、他のサブネットとは異なる範囲である必要があります。 |
    | ワークロード サブネット名 (オプション)   | *-workloadSubnetName*             | 独自のアプリケーション ワークロード用に作成する *aaddsVnetName* 仮想ネットワークのサブネットの省略可能な名前。 VM とアプリケーションは、ピアリングされた Azure 仮想ネットワークに代わりに接続することもできます。 |
    | ワークロードのアドレス範囲 (オプション) | *-workloadSubnetCIDRAddressRange* | アプリケーション ワークロードの、CIDR 表記の省略可能なサブネット アドレス範囲 (*192.168.2.0/24* など)。 アドレス範囲は、仮想ネットワークのアドレス範囲に含まれており、他のサブネットとは異なる範囲である必要があります。|

1. 次に、`New-AzureAaaddsForest` スクリプトを使用して、マネージド ドメイン リソース フォレストを作成します。 次の例では、*addscontoso.com* という名前のフォレストを作成し、ワークロード サブネットを作成します。 独自のパラメーター名と IP アドレス範囲、または既存の仮想ネットワークを指定します。

    ```azurepowershell
    New-AzureAaddsForest `
        -azureSubscriptionId <subscriptionId> `
        -aaddsResourceGroupName "myResourceGroup" `
        -aaddsLocation "WestUS" `
        -aaddsAdminUser "contosoadmin@contoso.com" `
        -aaddsDomainName "aaddscontoso.com" `
        -aaddsVnetName "myVnet" `
        -aaddsVnetCIDRAddressSpace "192.168.0.0/16" `
        -aaddsSubnetName "AzureADDS" `
        -aaddsSubnetCIDRAddressRange "192.168.1.0/24" `
        -workloadSubnetName "myWorkloads" `
        -workloadSubnetCIDRAddressRange "192.168.2.0/24"
    ```

    マネージド ドメイン リソース フォレストとサポート リソースの作成には、かなりの時間がかかります。 スクリプトの完了をお待ちください。 Azure AD リソース フォレストのプロビジョニングがバックグラウンドで実行されている間に、次のセクションに進み、オンプレミスのネットワーク接続を構成します。

## <a name="configure-and-validate-network-settings"></a>ネットワーク設定を構成し、検証する

マネージド ドメインのデプロイが続行している間に、オンプレミスのデータセンターへのハイブリッド ネットワーク接続を構成して検証します。 また、定期的なメンテナンスのためにマネージド ドメインと共に使用する管理 VM も必要です。 ハイブリッド接続の一部は環境に既に存在している可能性があります。また、接続を構成するには、チーム内の他のメンバーと協力することが必要になる場合もあります。

開始する前に、[ネットワークに関する考慮事項と推奨事項](tutorial-create-forest-trust.md#networking-considerations)について理解しておく必要があります。

1. Azure VPN または Azure ExpressRoute 接続を使用して、Azure へのオンプレミス ネットワークに対するハイブリッド接続を作成します。 ハイブリッド ネットワーク構成についてはこのドキュメントでは説明しませんが、現在の環境内に既に存在している可能性があります。 特定のシナリオの詳細については、次の記事を参照してください。

    * [Azure サイト間 VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) に関する記事。
    * [Azure ExpressRoute の概要](../expressroute/expressroute-introduction.md)に関する記事。

    > [!IMPORTANT]
    > マネージド ドメインの仮想ネットワークに直接接続を作成する場合は、別のゲートウェイ サブネットを使用します。 マネージド ドメインのサブネットにはゲートウェイを作成しないでください。

1. マネージド ドメインを管理するため、管理 VM を作成してマネージド ドメインに参加させ、必要な AD DS 管理ツールをインストールします。

    マネージド ドメイン リソース フォレストがデプロイされている間に、[Windows Server VM を作成](./join-windows-vm.md)し、必要な管理ツールをインストールするため、[コア AD DS 管理ツールをインストール](./tutorial-create-management-vm.md)します。 ドメインが正常にデプロイされた後、次の手順のいずれかを実行するまで待って、管理 VM をマネージド ドメインに参加させます。

1. オンプレミスのネットワークと Azure 仮想ネットワークの間のネットワーク接続を検証します。

    * `ping` やリモート デスクトップなどを使用して、オンプレミスのドメイン コントローラーがマネージド VM に接続できることを確認します。
    * もう一度 `ping` などのユーティリティを使用して、管理 VM がオンプレミスのドメイン コントローラーに接続できることを確認します。

1. Azure portal で、**Azure AD Domain Services** を検索して選択します。 使用するマネージド ドメイン (*aaddscontoso.com* など) を選択し、状態のレポートが **[Running]\(実行中\)** になるまで待ちます。

    実行中になったら、[Azure 仮想ネットワークの DNS 設定を更新](tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network)し、[Azure AD DS のユーザー アカウントを有効化](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)して、マネージド ドメイン リソース フォレストの構成を完了します。

1. 概要ページに表示される DNS アドレスをメモしておきます。 これらのアドレスは、次のセクションで信頼関係のオンプレミス Active Directory 側を構成するときに必要になります。
1. 管理 VM を再起動して新しい DNS 設定を受信できるようにし、[その VM をマネージド ドメインに参加](join-windows-vm.md#join-the-vm-to-the-managed-domain)させます。
1. 管理 VM がマネージド ドメインに参加したら、リモート デスクトップを使用してもう一度接続します。

    コマンド プロンプトから、`nslookup` とマネージド ドメイン リソース フォレストの名前を使用して、リソース フォレストの名前解決を検証します。

    ```console
    nslookup aaddscontoso.com
    ```

    コマンドは、リソース フォレストの 2 つの IP アドレスを返すはずです。

## <a name="create-the-forest-trust"></a>フォレストの信頼を作成する

フォレストの信頼は、2 つの部分から成っています。マネージド ドメイン リソース フォレストでの一方向の送信フォレストの信頼と、オンプレミスの AD DS フォレストでの一方向の受信フォレストの信頼です。 この信頼関係の両側を手動で作成します。 両方の側が作成されると、ユーザーとリソースはそのフォレストの信頼を使用して正常に認証できるようになります。 マネージド ドメイン リソース フォレストでは、オンプレミスのフォレストに対する一方向の送信フォレストの信頼が最大 5 つサポートされます。

### <a name="create-the-managed-domain-side-of-the-trust-relationship"></a>信頼関係のマネージド ドメイン側を作成する

`Add-AaddsResourceForestTrust` スクリプトを使用して、信頼関係のマネージド ドメイン側を作成します。 まず、[Install-Script][Install-Script] コマンドレットを使用して、[PowerShell ギャラリー][powershell-gallery]から `Add-AaddsResourceForestTrust` スクリプトをインストールします。

```powershell
Install-Script -Name Add-AaddsResourceForestTrust
```

次に、スクリプトに次の情報を指定します。

| 名前                               | スクリプト パラメーター     | 説明 |
|:-----------------------------------|:---------------------|:------------|
| Azure AD DS ドメイン名            | *-ManagedDomainFqdn* | マネージド ドメインの FQDN (*aaddscontoso.com* など) |
| オンプレミスの AD DS ドメイン名      | *-TrustFqdn*         | 信頼済みフォレストの FQDN (*onprem.contoso.com* など) |
| 信頼フレンドリ名                | *-TrustFriendlyName* | 信頼関係のフレンドリ名。 |
| オンプレミス AD DS DNS の IP アドレス | *-TrustDnsIPs*       | 一覧表示されている信頼される側のドメインの DNS サーバー IPv4 アドレスの、コンマ区切りの一覧。 |
| 信頼パスワード                     | *-TrustPassword*     | 信頼関係の複雑なパスワード。 このパスワードは、オンプレミスの AD DS で一方向の受信の信頼を作成するときにも入力されます。 |
| 資格情報                        | *-Credentials*       | Azure に対する認証に使用される資格情報。 ユーザーは、*AAD DC 管理者グループ* に含まれている必要があります。 指定されていない場合は、スクリプトにより認証が要求されます。 |

次の例では、*myAzureADDSTrust* という名前の信頼関係を *onprem.contoso.com* に対して作成します。 独自のパラメーター名とパスワードを使用してください。

```azurepowershell
Add-AaddsResourceForestTrust `
    -ManagedDomainFqdn "aaddscontoso.com" `
    -TrustFqdn "onprem.contoso.com" `
    -TrustFriendlyName "myAzureADDSTrust" `
    -TrustDnsIPs "10.0.1.10,10.0.1.11" `
    -TrustPassword <complexPassword>
```

> [!IMPORTANT]
> 信頼パスワードを忘れないようにしてください。 信頼のオンプレミス側を作成するときに、同じパスワードを使用する必要があります。

## <a name="configure-dns-in-the-on-premises-domain"></a>オンプレミスのドメイン内で DNS を構成する

オンプレミスの環境からマネージド ドメインを正しく解決するには、既存の DNS サーバーにフォワーダーを追加することが必要になる場合があります。 マネージド ドメインと通信するようにオンプレミスの環境が構成されていない場合は、オンプレミスの AD DS ドメインに対して管理ワークステーションから次の手順を行います。

1. **[スタート] | [管理ツール] | [DNS]** の順に選択します。
1. *myAD01* などの DNS サーバーを右クリックし、 **[プロパティ]** を選択します。
1. **[フォワーダー]** 、 **[編集]** の順に選択して、他のフォワーダーを追加します。
1. マネージド ドメインの IP アドレス (*10.0.1.4* と *10.0.1.5* など) を追加します。
1. ローカルのコマンド プロンプトで、マネージド ドメイン リソース フォレスト ドメイン名の **nslookup** を使用して、名前解決を検証します。 たとえば、`Nslookup aaddscontoso.com` を実行すると、マネージド ドメイン リソース フォレストの 2 つの IP アドレスが返されるはずです。

## <a name="create-inbound-forest-trust-in-the-on-premises-domain"></a>オンプレミス ドメインに受信フォレストの信頼を作成する

オンプレミスの AD DS ドメインには、マネージド ドメインに対する受信フォレストの信頼が必要です。 この信頼は、オンプレミスの AD DS ドメインで手動で作成する必要があり、Azure portal から作成することはできません。

オンプレミスの AD DS ドメイン上で受信の信頼を構成するには、オンプレミスの AD DS ドメインに対して管理ワークステーションから次の手順を行います。

1. **[スタート] | [管理ツール] | [Active Directory Active Directory ドメインと信頼関係]** の順に選択します。
1. *onprem.contoso.com* などのドメインを右クリックし、 **[プロパティ]** を選択します。
1. **[信頼]** タブ、 **[新しい信頼]** の順に選択します。
1. マネージド ドメインの名前 (*aaddscontoso.com* など) を入力し、 **[次へ]** を選択します。
1. **フォレストの信頼** を作成するオプションを選択して、**一方向: 受信** の信頼を作成します。
1. **[This domain only]\(このドメインのみ\)** に信頼を作成することを選択します。 次の手順では、Azure portal でマネージド ドメインに対する信頼を作成します。
1. **フォレスト全体の認証** を使用することを選択してから、信頼パスワードを入力して確認します。 これと同じパスワードを、次のセクションの Azure portal にも入力します。
1. 既定のオプションを使用して次のいくつかのウィンドウをステップ実行し、オプションの **[確認しない]** を選択します。 そのマネージド ドメイン リソース フォレストに対する委任された管理者アカウントには、必要なアクセス許可が付与されていないため、この信頼関係は検証できません。 この動作は仕様です。
1. **[完了]** を選択します。

## <a name="validate-resource-authentication"></a>リソース認証を検証する

次の一般的なシナリオにより、ユーザーとリソースへのアクセスとがフォレストの信頼によって正しく認証されることを検証できます。

* [Azure AD DS リソース フォレストからのオンプレミスのユーザー認証](#on-premises-user-authentication-from-the-azure-ad-ds-resource-forest)
* [オンプレミスのユーザーを使用して Azure AD DS リソース フォレスト内のリソースにアクセスする](#access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user)
    * [ファイルとプリンターの共有を有効にする](#enable-file-and-printer-sharing)
    * [セキュリティ グループを作成してメンバーを追加する](#create-a-security-group-and-add-members)
    * [フォレスト間のアクセス用にファイル共有を作成する](#create-a-file-share-for-cross-forest-access)
    * [リソースに対するフォレスト間認証を検証する](#validate-cross-forest-authentication-to-a-resource)

### <a name="on-premises-user-authentication-from-the-azure-ad-ds-resource-forest"></a>Azure AD DS リソース フォレストからのオンプレミスのユーザー認証

Windows Server 仮想マシンをマネージド ドメインのリソース ドメインに参加させる必要があります。 この仮想マシンを使用して、オンプレミスのユーザーが仮想マシン上で認証されることをテストします。

1. リモート デスクトップとマネージド ドメイン管理者の資格情報を使用して、マネージド ドメイン リソース フォレストに参加している Windows Server VM に接続します。 ネットワーク レベル認証 (NLA) エラーが発生した場合は、使用したユーザー アカウントがドメイン ユーザー アカウントではないことを確認します。

    > [!TIP]
    > Azure AD Domain Services に参加しているご利用の VM に安全に接続するには、サポートされている Azure リージョンで [Azure Bastion ホスト サービス](../bastion/bastion-overview.md)を使用できます。

1. コマンド プロンプトを開き、`whoami` コマンドを使用して、現在認証されているユーザーの識別名を表示します。

    ```console
    whoami /fqdn
    ```

1. `runas` コマンドを使用して、オンプレミスのドメインからのユーザーとして認証を受けます。 次のコマンドで、`userUpn@trusteddomain.com` を、信頼されているオンプレミス ドメインからのユーザーの UPN に置き換えます。 このコマンドでは、ユーザーのパスワードの入力を求められます。

    ```console
    Runas /u:userUpn@trusteddomain.com cmd.exe
    ```

1. 認証が成功した場合は、新しいコマンド プロンプトが開きます。 新しいコマンド プロンプトのタイトルには、`running as userUpn@trusteddomain.com` が含まれています。
1. 新しいコマンド プロンプトで `whoami /fqdn` を使用して、オンプレミスの Active Directory から認証されたユーザーの識別名を表示します。

### <a name="access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user"></a>オンプレミスのユーザーを使用して Azure AD DS リソース フォレスト内のリソースにアクセスする

マネージド ドメイン リソース フォレストに参加している Windows Server VM を使用すると、オンプレミス ドメインのコンピューターからオンプレミス ドメインのユーザーを認証するときに、ユーザーがリソース フォレストでホストされているリソースにアクセスできるシナリオのテストを行えます。 次の例では、さまざまな一般的なシナリオを作成およびテストする方法を示しています。

#### <a name="enable-file-and-printer-sharing"></a>ファイルとプリンターの共有を有効にする

1. リモート デスクトップとマネージド ドメイン管理者の資格情報を使用して、マネージド ドメイン リソース フォレストに参加している Windows Server VM に接続します。 ネットワーク レベル認証 (NLA) エラーが発生した場合は、使用したユーザー アカウントがドメイン ユーザー アカウントではないことを確認します。

    > [!TIP]
    > Azure AD Domain Services に参加しているご利用の VM に安全に接続するには、サポートされている Azure リージョンで [Azure Bastion ホスト サービス](../bastion/bastion-overview.md)を使用できます。

1. **[Windows の設定]** を開き、 **[ネットワークと共有センター]** を検索して選択します。
1. **[共有の詳細設定の変更]** のオプションを選択します。
1. **[ドメイン プロファイル]** で、 **[ファイルとプリンターの共有を有効にする]** 、 **[変更の保存]** の順に選択します。
1. **[ネットワークと共有センター]** を閉じます。

#### <a name="create-a-security-group-and-add-members"></a>セキュリティ グループを作成してメンバーを追加する

1. **[Active Directory ユーザーとコンピューター]** を開きます。
1. ドメイン名を右クリックし、 **[新規]** を選択してから、 **[組織単位]** を選択します。
1. [名前] ボックスで、「*LocalObjects*」と入力して、 **[OK]** を選択します。
1. ナビゲーション ウィンドウで右クリックして、 **[LocalObjects]** を選択します。 **[新規]** を選択してから、 **[グループ]** を選択します。
1. **[グループ名]** ボックスに「*FileServerAccess*」と入力します。 **[グループのスコープ]** の場合は、 **[ドメイン ローカル]** を選択してから、 **[OK]** を選択します。
1. コンテンツ ウィンドウで、 **[FileServerAccess]** をダブルクリックします。 **[メンバー]** 、 **[追加]** 、 **[場所]** の順に選択します。
1. **[場所]** ビューからご利用のオンプレミスの Active Directory を選択して、 **[OK]** を選択します。
1. **[選択するオブジェクト名を入力してください]** ボックスに「*Domain Users*」と入力します。 **[名前の確認]** を選択してから、オンプレミスの Active Directory の資格情報を入力して、 **[OK]** を選択します。

    > [!NOTE]
    > 信頼関係は一方向のみであるため、資格情報を指定する必要があります。 つまり、マネージド ドメインからのユーザーは、リソースにアクセスすることも、信頼された (オンプレミスの) ドメイン内のユーザーやグループを検索することもできません。

1. ご利用のオンプレミスの Active Directory からの **[ドメイン ユーザー]** グループは、 **[FileServerAccess]** グループのメンバーである必要があります。 **[OK]** を選択してグループを保存し、ウィンドウを閉じます。

#### <a name="create-a-file-share-for-cross-forest-access"></a>フォレスト間のアクセス用にファイル共有を作成する

1. マネージド ドメイン リソース フォレストに参加している Windows Server VM 上に、フォルダーを作成し、「*CrossForestShare*」などの名前を入力します。
1. フォルダーを右クリックし、 **[プロパティ]** を選択します。
1. **[セキュリティ]** タブを選択してから、 **[編集]** を選択します。
1. *[CrossForestShare のアクセス許可]* ダイアログ ボックスで **[追加]** を選択します。
1. **[選択するオブジェクト名を入力してください]** に、「*FileServerAccess*」と入力し、 **[OK]** を選択します。
1. **[グループまたはユーザー名]** 一覧から *[FileServerAccess]* を選択します。 **[FileServerAccess のアクセス許可]** 一覧で、 **[変更]** アクセス許可と **[書き込み]** アクセス許可に対して *[許可]* を選択し、 **[OK]** を選択します。
1. **[共有]** タブを選択してから、 **[詳細な共有]** を選択します。
1. **[このフォルダーを共有する]** を選択し、次に *CrossForestShare* など、ファイル共有の覚えやすい名前を **[共有名]** に入力します。
1. **[アクセス許可]** を選択します。 **[Permissions for Everyone]\(全員のアクセス許可\)** 一覧で、 **[変更]** アクセス許可に対して **[許可]** を選択します。
1. **[OK]** を 2 回選択して、 **[閉じる]** を選択します。

#### <a name="validate-cross-forest-authentication-to-a-resource"></a>リソースに対するフォレスト間認証を検証する

1. ご利用のオンプレミスの Active Directory からのユーザー アカウントを使用して、ご利用のオンプレミスの Active Directory に参加している Windows コンピューターにサインインします。
1. **Windows Explorer** 使用して、完全修飾ホスト名と共有 (`\\fs1.aaddscontoso.com\CrossforestShare` など) を使用して作成した共有に接続します。
1. 書き込みアクセス許可を検証するには、フォルダー内で右クリックし、 **[新規]** を選択してから、 **[テキスト ドキュメント]** を選択します。 既定の名前 **[新しいテキスト ドキュメント]** を使用します。

    書き込みアクセス許可が適切に設定されている場合は、新しいテキスト ドキュメントが作成されます。 次の手順では、必要に応じてファイルを開き、編集したり削除したりします。
1. 読み取りアクセス許可を検証するには、 **[新しいテキスト ドキュメント]** を開きます。
1. 変更アクセス許可を検証するには、ファイルにテキストを追加し、 **[メモ帳]** を閉じます。 変更を保存するように促すメッセージが表示されたら、 **[保存]** を選択します。
1. 削除アクセス許可を検証するには、 **[新しいテキスト ドキュメント]** を右クリックし、 **[削除]** を選択します。 ファイルの削除を確定するには **[はい]** を選択します。

## <a name="update-or-remove-outbound-forest-trust"></a>送信フォレストの信頼を更新または削除する

マネージド ドメインからの既存の一方向の送信フォレストを更新する必要がある場合は、`Get-AaddsResourceForestTrusts` および `Set-AaddsResourceForestTrust` スクリプトを使用できます。 これらのスクリプトは、フォレストの信頼のフレンドリ名や信頼パスワードを更新する場合に役立ちます。 マネージド ドメインから一方向の送信の信頼を削除する場合は、`Remove-AaddsResourceForestTrust` スクリプトを使用できます。 関連付けられているオンプレミス AD DS フォレスト内の一方向の受信フォレストの信頼を手動で削除する必要があります。

### <a name="update-a-forest-trust"></a>フォレストの信頼を更新する

通常の運用では、マネージド ドメイン フォレストとオンプレミスのフォレストは、両者間の定期的なパスワード更新プロセスをネゴシエートします。 これは、通常の AD DS 信頼関係のセキュリティ プロセスの一部です。 信頼関係で問題が発生し、既知のパスワードに手動でリセットする必要がある場合を除き、信頼パスワードを手動でローテーションする必要はありません。 詳細については、[信頼されたドメイン オブジェクトのパスワード変更](concepts-forest-trust.md#tdo-password-changes)を参照してください。

次の例で、送信の信頼パスワードを手動でリセットする必要がある場合に、既存の信頼関係を更新する方法の手順を示します。

1. [Install-Script][Install-Script] コマンドレットを使用して、[PowerShell ギャラリー][powershell-gallery]から `Get-AaddsResourceForestTrusts` および `Set-AaddsResourceForestTrust` スクリプトをインストールします。

    ```powershell
    Install-Script -Name Get-AaddsResourceForestTrusts,Set-AaddsResourceForestTrust
    ```

1. 既存の信頼を更新する前に、`Get-AaddsResourceForestTrusts` スクリプトを使用して信頼リソースを取得する必要があります。 次の例では、既存の信頼が *existingTrust* という名前のオブジェクトに割り当てられています。 更新する独自のマネージド ドメイン フォレスト名とオンプレミスのフォレスト名を指定します。

    ```powershell
    $existingTrust = Get-AaddsResourceForestTrust `
        -ManagedDomainFqdn "aaddscontoso.com" `
        -TrustFqdn "onprem.contoso.com" `
        -TrustFriendlyName "myAzureADDSTrust"
    ```

1. 既存の信頼パスワードを更新するために、`Set-AaddsResourceForestTrust` スクリプトを使用します。 前の手順で作成した既存の信頼オブジェクトを指定してから、新しい信頼関係のパスワードを指定します。 PowerShell ではパスワードの複雑さが適用されないため、セキュリティで保護されたパスワードを環境に対して生成して使用するようにしてください。

    ```powershell
    Set-AaddsResourceForestTrust `
        -Trust $existingTrust `
        -TrustPassword <newComplexPassword>
    ```

### <a name="delete-a-forest-trust"></a>フォレストの信頼を削除する

マネージド ドメインからオンプレミスの AD DS フォレストへの一方向の送信フォレストの信頼が不要になった場合は、削除することができます。 信頼を削除する前に、そのオンプレミスの AD DS フォレストに対する認証が必要なアプリケーションやサービスがないことを確認してください。 オンプレミスの AD DS フォレストで一方向の受信の信頼を手動で削除する必要もあります。

1. [Install-Script][Install-Script] コマンドレットを使用して、[PowerShell ギャラリー][powershell-gallery]から `Remove-AaddsResourceForestTrust` スクリプトをインストールします。

    ```powershell
    Install-Script -Name Remove-AaddsResourceForestTrust
    ```

1. 次に、`Remove-AaddsResourceForestTrust` スクリプトを使用して、フォレストの信頼を削除します。 次の例では、*aaddscontoso.com* という名前のマネージド ドメイン フォレストと *onprem.contoso.com* という名前のオンプレミスのフォレストとの間の、*myAzureADDSTrust* という名前の信頼が削除されます。 削除する独自のマネージド ドメイン フォレスト名とオンプレミスのフォレスト名を指定します。

    ```powershell
    Remove-AaddsResourceForestTrust `
        -ManagedDomainFqdn "aaddscontoso.com" `
        -TrustFqdn "onprem.contoso.com" `
        -TrustFriendlyName "myAzureADDSTrust"
    ```

オンプレミスの AD DS フォレストから一方向の受信の信頼を削除するには、オンプレミスの AD DS フォレストへのアクセス権を持つ管理コンピューターに接続し、次の手順を実行します。

1. **[スタート] | [管理ツール] | [Active Directory Active Directory ドメインと信頼関係]** の順に選択します。
1. *onprem.contoso.com* などのドメインを右クリックし、 **[プロパティ]** を選択します。
1. **[信頼]** タブを選択し、マネージド ドメイン フォレストからの既存の受信の信頼を選択します。
1. **[削除]** を選択し、その受信の信頼を削除することを確認します。

## <a name="next-steps"></a>次のステップ

この記事で学習した内容は次のとおりです。

> [!div class="checklist"]
> * Azure PowerShell を使用してマネージド ドメイン リソース フォレストを作成する
> * Azure PowerShell を使用して、マネージド ドメインで一方向の送信フォレストの信頼を作成する
> * マネージド ドメイン接続をサポートするように、オンプレミスの AD DS 環境で DNS を構成する
> * オンプレミスの AD DS 環境で一方向の受信フォレストの信頼を作成する
> * 認証とリソース アクセスのために信頼関係をテストおよび検証する

Azure AD DS 内のフォレストの種類に関する概念的な詳細については、[リソース フォレストとは何か][concepts-forest]および[フォレストの信頼が Azure AD DS で機能する方法][concepts-trust]に関するページを参照してください。

<!-- INTERNAL LINKS -->
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance-advanced]: tutorial-create-instance-advanced.md
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
[New-AzResourceGroup]: /powershell/module/Az.Resources/New-AzResourceGroup
[network-peering]: ../virtual-network/virtual-network-peering-overview.md
[New-AzureADServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
[Get-AzureRMSubscription]: /powershell/module/AzureRM.Profile/Get-AzureRmSubscription
[Install-Script]: /powershell/module/powershellget/install-script

<!-- EXTERNAL LINKS -->
[powershell-gallery]: https://www.powershellgallery.com/