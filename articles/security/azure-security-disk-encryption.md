---
title: "Windows および Linux IaaS VM の Azure Disk Encryption | Microsoft Docs"
description: "この記事では、Windows および Linux IaaS VM の Microsoft Azure Disk Encryption の概要を示します。"
services: security
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: TomSh
ms.assetid: d3fac8bb-4829-405e-8701-fa7229fb1725
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2017
ms.author: kakhan
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: f52b9064d4771c714b829a409037ef6f03c54161
ms.lasthandoff: 03/21/2017


---
# <a name="azure-disk-encryption-for-windows-and-linux-iaas-vms"></a>Windows および Linux IaaS VM の Azure ディスク暗号化
Microsoft Azure では、データのプライバシーおよびデータ主権の確保に積極的に取り組んでいます。 お客様は、暗号化キーを暗号化し、制御し、管理すると共に、データ アクセスを制御および監査するための幅広い先進テクノロジを通じて、Azure でホストされているデータを制御できます。 この制御機能により、お客様はビジネス ニーズに最適なソリューションを柔軟に選択することができます。

この記事では、データを保護するための新しいテクノロジ ソリューションである、"Windows および Linux IaaS VM の Azure Disk Encryption" について紹介します。 このソリューションは、セキュリティとコンプライアンスに関する組織のコミットメント履行に役立つよう設計されています。 この記事では、サポートされているシナリオやユーザー エクスペリエンスを含む、Azure Disk Encryption 機能の使用方法に関する詳細なガイダンスを提供します。

> [!NOTE]
> 特定の推奨事項により、データ、ネットワーク、またはコンピューティング リソースの使用量が増え、その結果、ライセンスまたはサブスクリプション コストの追加が必要になる可能性があります。

## <a name="overview"></a>概要
Azure Disk Encryption は、Windows および Linux IaaS 仮想マシン ディスクを暗号化するのに役立つ新機能です。 この機能では、OS およびデータ ディスクのボリュームを暗号化するために、Windows の業界標準である [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) 機能と Linux の [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) 機能が使用されます。 このソリューションは [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) と統合されており、ディスクの暗号化キーとシークレットは Key Vault サブスクリプションで制御および管理できます。 またこのソリューションでは、仮想マシン ディスク上のすべてのデータが、Azure Storage での保存時に暗号化されます。

現在、Windows および Linux IaaS VM の Azure Disk Encryption は、Azure のすべてのパブリック リージョンで、Standard Storage アカウントを使用した VM と Premium Storage アカウントを使用した VM を対象に一般提供されています。

### <a name="encryption-scenarios"></a>暗号化のシナリオ
Azure Disk Encryption ソリューションでは、次の顧客シナリオがサポートされています。

* 事前に暗号化した仮想ハード ディスク (VHD) と暗号化キーから作成された新しい IaaS VM での暗号化を有効にする
* Azure Marketplace イメージから作成された新しい IaaS VM での暗号化を有効にする
* Azure で実行されている既存の IaaS VM での暗号化を有効にする
* Windows IaaS VM での暗号化を無効にする
* Linux IaaS VM でのデータ ドライブの暗号化を無効にする

このソリューションでは、Microsoft Azure で有効になっている場合、IaaS VM の以下のシナリオがサポートされます。

* Azure Key Vault との統合
* Standard レベルの VM: [A、D、DS、G、GS などの IaaS VM シリーズ](https://azure.microsoft.com/pricing/details/virtual-machines/)
* Windows および Linux IaaS VM の暗号化を有効にする
* Windows IaaS VM の OS およびデータ ドライブの暗号化を無効にする
* Linux IaaS VM のデータ ドライブの暗号化を無効にする
* Windows クライアント OS を実行している IaaS VM の暗号化を有効にする
* ボリュームのマウント パスの暗号化を有効にする
* mdadm を使用してディスク ストライピング (RAID) が構成されている Linux VM の暗号化を有効にする
* データ ディスクの LVM を使用して Linux VM の暗号化を有効にする
* ストレージ スペースを使用して構成されている Windows VM の暗号化を有効にする
* Azure のすべてのパブリック リージョンがサポートされる

このソリューションの現在のリリースでは、以下のシナリオ、機能、およびテクノロジはサポートされていません。

* Basic レベルの IaaS VM
* Linux IaaS VM の OS ドライブの暗号化を無効にする
* 従来の VM の作成方法を使用して作成された IaaS VM
* オンプレミス キー管理サービスとの統合
* Azure Files (共有ファイル システム)、ネットワーク ファイル システム (NFS)、ダイナミック ボリューム、およびソフトウェアベースの RAID システムで構成されている Windows VM

### <a name="encryption-features"></a>暗号化機能
Azure IaaS VM の Azure Disk Encryption を有効にしてデプロイすると、指定された構成に応じて、以下の機能が有効になります。

* ストレージに保存中のブート ボリュームを保護するために OS ボリュームを暗号化する
* ストレージに保存中のデータ ボリュームを保護するためにデータ ボリュームを暗号化する
* Windows IaaS VM での OS およびデータ ドライブの暗号化を無効にする
* Linux IaaS VM でのデータ ドライブの暗号化を無効にする
* Key Vault サブスクリプションの暗号化キーとシークレットを保護する
* 暗号化された IaaS VM の暗号化状態をレポートする
* IaaS 仮想マシンからディスク暗号化構成設定を削除する
* 暗号化された VM を Azure Backup サービスでバックアップおよび復元する

> [!NOTE]
> 暗号化された VM のバックアップと復元は、KEK 構成を使用して暗号化された VM についてのみサポートされています。 KEK を使用せずに暗号化された VM については、サポートされていません。 KEK は、VM の暗号化を有効にするための省略可能なパラメーターです。

Windows および Linux IaaS VM の Azure Disk Encryption ソリューションには、以下のものが含まれています。

* Windows のディスク暗号化拡張機能。
* Linux のディスク暗号化拡張機能。
* ディスク暗号化 PowerShell コマンドレット。
* ディスク暗号化コマンド ライン インターフェイス (CLI) コマンドレット。
* ディスク暗号化 Azure Resource Manager テンプレート。

Azure Disk Encryption ソリューションは、Windows または Linux OS を実行している IaaS VM でサポートされます。 サポートされているオペレーティング システムについて詳しくは、後述の「前提条件」セクションをご覧ください。

> [!NOTE]
> Azure Disk Encryption による VM ディスクの暗号化による追加料金は発生しません。

### <a name="value-proposition"></a>価値の提案
Azure Disk Encryption の管理ソリューションを使用すると、次のビジネス ニーズに対応できます。

* 業界標準の暗号化テクノロジを使用して、組織のセキュリティおよびコンプライアンス要件に対処できます。そのため、保存中の IaaS VM はセキュリティで保護されます。
* IaaS VM はお客様が管理するキーとポリシーに従ってブートします。またお客様は、Key Vault での IaaS VM の使用量を監査することができます。

### <a name="encryption-workflow"></a>暗号化のワークフロー
Windows および Linux VM のディスク暗号化を有効にするには、次の操作を行います。

1. 上記の暗号化シナリオの中から、実行する暗号化シナリオを選択します。
2. Azure Disk Encryption Resource Manager テンプレート、PowerShell コマンドレット、または CLI コマンドを使用してディスク暗号化を有効にすることを選択し、暗号化構成を指定します。

   * お客様が暗号化した VHD の場合は、暗号化した VHD をストレージ アカウントにアップロードし、暗号化キー マテリアルを Key Vault にアップロードします。 その後、暗号化構成を指定して新しい IaaS VM での暗号化を有効にします。
   * Marketplace から作成された新しい VM と、Azure で既に実行されている既存の VM については、IaaS VM での暗号化を有効にするための暗号化構成を指定します。

3. 暗号化キー マテリアル (Windows システムの場合は BitLocker 暗号化キー、Linux の場合はパスフレーズ) を Key Vault から読み取るためのアクセス権を Azure プラットフォームに付与し、IaaS VM での暗号化を有効にします。

4. Key Vault に暗号化キー マテリアルを書き込むための Azure Active Directory (Azure AD) アプリケーション ID を指定します。 これにより、手順 2 で述べたシナリオについて、IaaS VM での暗号化が有効になります。

5. Azure は、暗号化と Key Vault の構成を使用して VM サービス モデルを更新し、暗号化された VM をセットアップします。

 ![Azure での Microsoft マルウェア対策](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

### <a name="decryption-workflow"></a>暗号化解除のワークフロー
IaaS VM のディスク暗号化を無効にするには、以下の手順に従います。

1. Azure Disk Encryption Resource Manager テンプレートまたは PowerShell コマンドレットを使用して暗号化解除の構成を指定することで、Azure で実行中の IaaS VM での暗号化を無効に (暗号化解除) します。

 この手順によって、Windows IaaS VM で実行されている OS またはデータ ボリューム、もしくはその両方の暗号化が無効になります。 ただし、先のセクションで述べたように、Linux で OS ディスクの暗号化を無効にすることはサポートされていません。 暗号化解除の手順は、Linux VM のデータ ドライブでのみ許可されます。
2. Azure によって VM サービス モデルが更新され、IaaS VM は暗号化解除済みとしてマークされます。 VM の内容は保存時に暗号化されなくなります。

 暗号化を無効にしても、Key Vault や暗号化キー マテリアル (Windows システムの場合は BitLocker 暗号化キー、Linux の場合はパスフレーズ) は削除されません。

## <a name="prerequisites"></a>前提条件
概要セクションに記載されているサポート対象シナリオの Azure IaaS VM で Azure Disk Encryption を有効にする前に、以下の前提条件を確認してください。

* サポートされているリージョンにおいて Azure でリソースを作成するための有効なアクティブ Azure サブスクリプションが必要です。
* Azure Disk Encryption は、次の Windows サーバー バージョンでサポートされています: Windows Server 2008 R2、Windows Server 2012、Windows Server 2012 R2、および Windows Server 2016。
* Azure Disk Encryption は、次の Windows クライアント バージョンでサポートされています: Windows 8 クライアントおよび Windows 10 クライアント。

> [!NOTE]
> Windows Server 2008 R2 の場合、Azure で暗号化を有効にする前に、.Net Framework 4.5 をインストールする必要があります。 オプションの更新プログラムである Windows Server 2008 R2 x64 ベース システム用の Microsoft .NET Framework 4.5.2 ([KB2901983](https://support.microsoft.com/kb/2901983)) をインストールし、Windows 更新プログラムからこれをインストールすることができます。
>
> Azure Disk Encryption は、次の Linux サーバー バージョンでサポートされています: Ubuntu、CentOS、SUSE、SUSE Linux Enterprise Server (SLES) および Red Hat Enterprise Linux。

> [!NOTE]
> 現時点では、Linux OS ディスク暗号化は、RHEL 7.2、RHEL 7.3、CentOS 7.2n、Ubuntu 16.04 の各 Linux ディストリビューションでサポートされています。
>
> すべてのリソース (たとえば、Key Vault、ストレージ アカウント、VM など) が同じ Azure リージョンとサブスクリプションに属している必要があります。

> [!NOTE]
> Azure Disk Encryption では、Key Vault と VM が同じ Azure リージョンに属している必要があります。 それぞれ別のリージョンで構成すると、Azure Disk Encryption 機能を有効にする場合にエラーが発生します。

* Azure Key Vault を Azure Disk Encryption 用にセットアップして構成する場合は、この付録の「Key Vault を Azure Disk Encryption 用にセットアップして構成する」をご覧ください。
* Azure Active Directory 内の Azure AD アプリケーションを Azure Disk Encryption 用にセットアップして構成する場合は、「前提条件」セクションの「Azure Active Directory 内の Azure AD アプリケーションをセットアップする」をご覧ください。
* Azure AD アプリケーションの Key Vault アクセス ポリシーをセットアップして構成する場合は、「前提条件」セクションの「Azure AD アプリケーションの Key Vault アクセス ポリシーをセットアップする」をご覧ください。
* 事前に暗号化された Windows VHD を準備する場合は、付録の「事前に暗号化された Windows VHD を準備する」セクションをご覧ください。
* 事前に暗号化された Linux VHD を準備する場合は、付録の「事前に暗号化された Linux VHD を準備する」セクションをご覧ください。
* Azure プラットフォームには、Key Vault 内の暗号化キーまたはシークレットへのアクセス権を付与する必要があります。これにより、Azure プラットフォームが仮想マシンの OS ボリュームをブートして暗号化する際に、それらの情報を仮想マシンに提供できるようになります。 Azure プラットフォームにアクセス許可を付与するには、Key Vault に **EnabledForDiskEncryption** プロパティを設定します。 詳しくは、この付録の「Key Vault を Azure Disk Encryption 用にセットアップして構成する」をご覧ください。
* Key Vault シークレットおよび KEK URL のバージョン管理が必要です。 Azure では、このバージョン管理制限が適用されます。 有効なシークレットと KEK URI については、次の例をご覧ください。

  * 有効なシークレット URL の例:   *https://contosovault.vault.azure.net/secrets/BitLockerEncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * 有効な KEK URL の例:   *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Azure Disk Encryption では、ポート番号を Key Vault シークレットおよび KEK URL の一部として指定することはサポートされません。 サポートされない Key Vault URL とサポートされる Key Vault URL の例については、以下をご覧ください。

  * 受け入れられない Key Vault URL:  *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * 受け入れられる Key Vault URL:   *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Azure Disk Encryption 機能を有効にするには、IaaS VM が次のネットワーク エンドポイントの構成要件を満たす必要があります。

  * Key Vault に接続するためのトークンを取得するには、IaaS VM が Azure Active Directory エンドポイント \[Login.windows.net\] に接続できる必要があります。
  * 暗号化キーを Key Vault に書き込むには、IaaS VM が Key Vault エンドポイントに接続できる必要があります。
  * IaaS VM は、Azure 拡張リポジトリをホストする Azure ストレージ エンドポイントと、VHD ファイルをホストする Azure ストレージ アカウントに接続できる必要があります。

  > [!NOTE]
  > セキュリティ ポリシーで Azure VM からインターネットへのアクセスが制限されている場合は、上記の URI を解決し、IP への送信接続を許可するための特定のルールを構成することができます。
  >
  > Azure Disk Encryption を構成するには、[Azure PowerShell](https://github.com/Azure/azure-powershell/releases) の最新バージョンをダウンロードします。

 > [!NOTE]
  > Azure Disk Encryption は、[Azure PowerShell SDK Version 1.1.0](https://github.com/Azure/azure-powershell/releases/tag/v1.1.0-January2016) ではサポートされていません。 Azure PowerShell 1.1.0 の使用に関連するエラーが発生する場合は、「[Azure Disk Encryption Error Related to Azure PowerShell 1.1.0 (Azure PowerShell 1.1.0 に関連する Azure Disk Encryption エラー)](http://blogs.msdn.com/b/azuresecurity/archive/2016/02/10/azure-disk-encryption-error-related-to-azure-powershell-1-1-0.aspx)」をご覧ください。

* Azure CLI コマンドのいずれかを実行して、Azure サブスクリプションに関連付けるには、まず、以下のように Azure CLI をインストールする必要があります。
  * Azure CLI をインストールして、Azure サブスクリプションに関連付ける場合は、[Azure CLI のインストールおよび構成方法](../cli-install-nodejs.md)に関するページを参照してください。
  * Mac、Linux、および Windows 用の Azure CLI を Azure Resource Manager で使用する場合は、「[Resource Manager モードでの Azure CLI コマンド](../virtual-machines/azure-cli-arm-commands.md)」をご覧ください。
* Azure Disk Encryption ソリューションでは、Windows IaaS VM に対して BitLocker 外部キー保護機能を使用します。 VM がドメインに参加している場合は、TPM 保護機能を適用するグループ ポリシーをプッシュしないでください。 "互換性のある TPM が装備されていない BitLocker を許可する" のグループ ポリシーについては、「[BitLocker Group Policy Reference](https://technet.microsoft.com/library/ee706521)」(BitLocker グループ ポリシー リファレンス) をご覧ください。
* Azure AD アプリケーションを作成するか、Key Vault を作成するか、または既存の Key Vault をセットアップして暗号化を有効にする場合は、「[Azure Disk Encryption prerequisite PowerShell script](https://github.com/Azure/azure-powershell/blob/dev/src/ResourceManager/Compute/Commands.Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1)」(Azure Disk Encryption の前提条件となる PowerShell スクリプト) をご覧ください。
* Azure CLI を使用してディスク暗号化の前提条件を構成する場合は、[こちらの Bash スクリプト](https://github.com/ejarvi/ade-cli-getting-started)をご覧ください。
* Azure Disk Encryption で暗号化が有効になっている場合、暗号化された VM を Azure バックアップ サービスでバックアップおよび復元するには、Azure Disk Encryption のキー構成を使用して VM を暗号化する必要があります。 バックアップ サービスでは、KEK 構成を使用して暗号化された VM のみがサポートされます。 このサービスでは、KEK を使用して暗号化された VM のみがサポートされます。 KEK オプションを使用して VM の暗号化を有効にする場合は、下記のディスク暗号化デプロイメント シナリオをご覧ください。

#### <a name="set-up-the-azure-ad-application-in-azure-active-directory"></a>Azure Active Directory 内の Azure AD アプリケーションをセットアップする
Azure で実行中の VM に対して暗号化を有効にする必要がある場合、Azure Disk Encryption により、暗号化キーが生成され、Key Vault に書き込まれます。 Key Vault の暗号化キーを管理するには、Azure AD 認証が必要です。

そのため、Azure AD アプリケーションを作成してください。 アプリケーションを登録するための詳しい手順については、ブログ投稿「[Azure Key Vault - Step by Step](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx)」(Azure Key Vault - 手順) の「Get an Identity for the Application」(アプリケーションの ID の取得) セクションをご覧ください。 この投稿には、Key Vault のセットアップと構成に関する役立つ例も多数含まれています。 認証には、クライアント シークレット ベースの認証か、クライアント証明書ベースの Azure AD 認証を使用できます。

#### <a name="client-secret-based-authentication-for-azure-ad"></a>Azure AD のクライアント シークレット ベースの認証
以降のセクションは、Azure AD のクライアント シークレット ベースの認証を構成するのに役立ちます。

##### <a name="create-an-azure-ad-application-by-using-azure-powershell"></a>Azure PowerShell を使用して Azure AD アプリケーションを作成する
次の PowerShell コマンドレットを使用して Azure AD アプリケーションを作成します。

    $aadClientSecret = “yourSecret”
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -Password $aadClientSecret
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

> [!NOTE]
> $azureAdApplication.ApplicationId は Azure AD ClientID、$aadClientSecret はクライアント シークレットです。これらは、Azure Disk Encryption を有効にするために後で使用します。 Azure AD のクライアント シークレットは適切に保護してください。

##### <a name="setting-up-the-azure-ad-client-id-and-secret-from-the-azure-classic-portal"></a>Azure クラシック ポータルから Azure AD のクライアント ID とシークレットをセットアップする
Azure AD のクライアント ID とシークレットは、[Azure クラシック ポータル]( https://manage.windowsazure.com)を使用してセットアップすることもできます。 このタスクを実行するには、次の操作を行います。

1. **[Active Directory]** タブをクリックします。

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig3.png)

2. **[アプリケーションの追加]** をクリックし、アプリケーション名を入力します。

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig4.png)

3. 矢印ボタンをクリックし、アプリケーションのプロパティを構成します。

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig5.png)

4. 左下隅にあるチェック マークをクリックして終了します。 アプリケーション構成ページが表示され、Azure AD のクライアント ID がページの下部に表示されます。

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig6.png)

5. **[保存]** をクリックして、Azure AD のクライアント シークレットを保存します。 キーのテキスト ボックスに Azure AD のクライアント シークレットが表示されます。 この情報は適切に保護してください。

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig7.png)

 > [!NOTE]
 > 上記のフローは Azure クラシック ポータルではサポートされていません。

##### <a name="use-an-existing-application"></a>既存のアプリケーションを使用する
次のコマンドを実行するには、[Azure AD PowerShell モジュール](https://technet.microsoft.com/library/jj151815.aspx)を取得して使用します。

> [!NOTE]
> 以下のコマンドは、新規の PowerShell ウィンドウから実行する必要があります。 これらのコマンドを実行する場合は、Azure PowerShell や Azure Resource Manager のウィンドウを使用しないでください。 このアプローチを推奨するのは、これらのコマンドレットが MSOnline モジュールまたは Azure AD PowerShell にあるためです。

    $clientSecret = ‘<yourAadClientSecret>’
    $aadClientID = '<Client ID of your Azure AD application>'
    connect-msolservice
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type password -Value $clientSecret

#### <a name="certificate-based-authentication-for-azure-ad"></a>Azure AD の証明書ベースの認証
> [!NOTE]
> Azure AD の証明書ベースの認証は、現時点では Linux VM ではサポートされていません。

以降のセクションでは、Azure AD の証明書ベースの認証を構成する方法について説明します。

##### <a name="create-an-azure-ad-application"></a>Azure AD アプリケーションの作成
Azure AD アプリケーションを作成するには、次の PowerShell コマンドレットを実行します。

> [!NOTE]
> 次の `yourpassword` 文字列をセキュリティで保護されたパスワードに置き換え、パスワードを保護してください。

    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate("C:\certificates\examplecert.pfx", "yourpassword")
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -KeyValue $keyValue -KeyType AsymmetricX509Cert
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

この手順が完了したら、PFX ファイルを Key Vault にアップロードし、VM にその証明書をデプロイするために必要なアクセス ポリシーを有効にします。

##### <a name="use-an-existing-azure-ad-application"></a>既存の Azure AD アプリケーションを使用する
既存のアプリケーションの証明書ベースの認証を構成する場合は、次の PowerShell コマンドレットを使用します。 必ず、新しい PowerShell ウィンドウから実行してください。

    $certLocalPath = 'C:\certs\myaadapp.cer'
    $aadClientID = '<Client ID of your Azure AD application>'
    connect-msolservice
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate
    $cer.Import($certLocalPath)
    $binCert = $cer.GetRawCertData()
    $credValue = [System.Convert]::ToBase64String($binCert);
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type asymmetric -Value $credValue -Usage verify

この手順が完了したら、PFX ファイルを Key Vault にアップロードし、VM に証明書をデプロイするために必要なアクセス ポリシーを有効にします。

##### <a name="upload-a-pfx-file-to-your-key-vault"></a>PFX ファイルを Key Vault にアップロードする
このプロセスの詳細については、「[The Official Azure Key Vault Team Blog](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx)」(Azure Key Vault チームのオフィシャル ブログ) をご覧ください。 ただし、タスクに必要なものは以下の PowerShell コマンドレットだけです。 必ず、Azure PowerShell コンソールから実行してください。

> [!NOTE]
> 次の `yourpassword` 文字列をセキュリティで保護されたパスワードに置き換え、パスワードを保護してください。

    $certLocalPath = 'C:\certs\myaadapp.pfx'
    $certPassword = "yourpassword"
    $resourceGroupName = ‘yourResourceGroup’
    $keyVaultName = ‘yourKeyVaultName’
    $keyVaultSecretName = ‘yourAadCertSecretName’

    $fileContentBytes = get-content $certLocalPath -Encoding Byte
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

    $jsonObject = @"
    {
    "data": "$filecontentencoded",
    "dataType" :"pfx",
    "password": "$certPassword"
    }
    "@

    $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
    $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

    Switch-AzureMode -Name AzureResourceManager
    $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
    Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName -SecretValue $secret
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ResourceGroupName $resourceGroupName –EnabledForDeployment

##### <a name="deploy-a-certificate-in-your-key-vault-to-an-existing-vm"></a>既存の VM に Key Vault の証明書をデプロイする
PFX のアップロードが完了したら、次のコマンドレットを使用して、Key Vault 内の証明書を既存の VM にデプロイします。
 ```
    $resourceGroupName = ‘yourResourceGroup’
    $keyVaultName = ‘yourKeyVaultName’
    $keyVaultSecretName = ‘yourAadCertSecretName’
    $vmName = ‘yourVMName’
    $certUrl = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName).Id
    $sourceVaultId = (Get-AzureRmKeyVault -VaultName $keyVaultName -ResourceGroupName $resourceGroupName).ResourceId
    $vm = Get-AzureRmVM -ResourceGroupName $resourceGroupName -Name $vmName
    $vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore "My" -CertificateUrl $certUrl
    Update-AzureRmVM -VM $vm  -ResourceGroupName $resourceGroupName
 ```

#### <a name="set-up-the-key-vault-access-policy-for-the-azure-ad-application"></a>Azure AD アプリケーションの Key Vault アクセス ポリシーをセットアップする
Azure AD アプリケーションには、Vault 内のキーまたはシークレットへのアクセス権が必要です。 [`Set-AzureKeyVaultAccessPolicy`](https://msdn.microsoft.com/library/azure/dn903607.aspx) コマンドレットで、_–ServicePrincipalName_ パラメーター値としてクライアント ID (アプリケーションの登録時に生成されたもの) を使用して、アプリケーションにアクセス許可を付与します。 詳しくは、ブログ投稿「[Azure Key Vault - Step by Step](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx)」(Azure Key Vault - 手順) をご覧ください。 次に示すのは、PowerShell を使用してこのタスクを実行する方法の例です。

    $keyVaultName = '<yourKeyVaultName>'
    $aadClientID = '<yourAadAppClientID>'
    $rgname = '<yourResourceGroup>'
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $rgname

> [!NOTE]
> Azure Disk Encryption では、Azure AD のクライアント アプリケーションに対して、アクセス ポリシー (_WrapKey_ および _Set_ アクセス許可) を構成する必要があります。

## <a name="terminology"></a>用語集
このテクノロジで使用される一般的な用語をいくつか理解するために、以下の用語表を使用してください。

| 用語集 | 定義 |
| --- | --- |
| Azure AD | Azure AD とは [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/) のことです。 Azure AD アカウントは、Key Vault のシークレットの認証、格納、および取得を行うための前提条件です。 |
| Azure Key Vault | Key Vault は、Federal Information Processing Standards (FIPS) に照らして検証されたハードウェア セキュリティ モジュールに基づく、暗号化キー管理サービスです。Key Vault は暗号化キーと機密性の高い情報を保護するために役立ちます。 詳しくは、「[Key Vault](https://azure.microsoft.com/services/key-vault/)」のドキュメントをご覧ください。 |
| ARM | Azure リソース マネージャー |
| BitLocker |[BitLocker](https://technet.microsoft.com/library/hh831713.aspx) は、Windows IaaS VM でディスク暗号化を有効にするために使用される業界公認の Windows ボリューム暗号化テクノロジです。 |
| BEK | BitLocker 暗号化キーは、OS ブート ボリュームとデータ ボリュームの暗号化に使用されます。 BitLocker キーは、Key Vault でシークレットとして保護されます。 |
| CLI | [Azure コマンド ライン インターフェイス](../cli-install-nodejs.md)に関する記事をご覧ください。 |
| DM-Crypt |[DM Crypt](https://en.wikipedia.org/wiki/Dm-crypt) は、Linux IaaS VM でディスク暗号化を有効にするために使用される Linux ベースの透過的なディスク暗号化サブシステムです。 |
| KEK | キー暗号化キーは、シークレットを保護またはラップするために使用できる非対称キー (RSA 2048) です。 ハードウェア セキュリティ モジュール (HSM) で保護されたキーまたはソフトウェアで保護されたキーを指定できます。 詳しくは、[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) のドキュメントをご覧ください。 |
| PS コマンドレット | [Azure PowerShell コマンドレット](/powershell/azureps-cmdlets-docs)に関する記事をご覧ください。 |

### <a name="set-up-and-configure-your-key-vault-for-azure-disk-encryption"></a>Key Vault を Azure Disk Encryption 用にセットアップして構成する
Azure Disk Encryption は、Key Vault のディスク暗号化キーとシークレットを保護するのに役立ちます。 Azure Disk Encryption の Key Vault をセットアップするには、以下のセクションの各手順を実行します。

#### <a name="create-a-key-vault"></a>Key Vault を作成します
Key Vault を作成するには、次のオプションのいずれかを使用します。

* ["101-Key-Vault-Create" Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)
* [Azure PowerShell の Key Vault コマンドレット](https://msdn.microsoft.com/library/dn868052.aspx)
* Azure リソース マネージャー

> [!NOTE]
> サブスクリプションの Key Vault を既にセットアップしている場合は、次のセクションに進んでください。

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig1.png)

#### <a name="set-up-a-key-encryption-key-optional"></a>キー暗号化キーをセットアップする (省略可能)
BitLocker 暗号化キーのセキュリティに対する追加レイヤーとして KEK を使用する場合は、Key Vault に KEK を追加します。 [`Add-AzureKeyVaultKey`](https://msdn.microsoft.com/library/dn868048.aspx) コマンドレットを使用して、Key Vault 内にキー暗号化キーを作成します。 オンプレミスのキー管理 HSM から KEK をインポートすることもできます。 詳しくは、「[Key Vault のドキュメント](https://azure.microsoft.com/documentation/services/key-vault/)」をご覧ください。

    Add-AzureKeyVaultKey [-VaultName] <string> [-Name] <string> -Destination <string> {HSM | Software}

KEK は、Azure Resource Manager に移動するか、または Key Vault インターフェイスを使用して追加できます。

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig2.png)

#### <a name="set-key-vault-permissions"></a>Key Vault のアクセス許可をセットアップする
Azure プラットフォームには、Key Vault 内の暗号化キーまたはシークレットへのアクセス権を付与する必要があります。これにより、ボリュームをブートして暗号化する際に、それらの情報を VM に提供できるようになります。 Azure プラットフォームにアクセス許可を付与するには、Key Vault の PowerShell コマンドレットを使用して、Key Vault に **EnabledForDiskEncryption** プロパティを設定します。

    Set-AzureRmKeyVaultAccessPolicy -VaultName <yourVaultName> -ResourceGroupName <yourResourceGroup> -EnabledForDiskEncryption

**EnabledForDiskEncryption** プロパティは、[Azure リソース エクスプローラー](https://resources.azure.com)で設定することもできます。

前述のとおり、Key Vault には **EnabledForDiskEncryption** プロパティを設定する必要があります。 17 以下の場合、デプロイは失敗します。

Azure AD アプリケーションのアクセス ポリシーは、次に示すように、Key Vault インターフェイスから設定できます。

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig3.png)

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig3b.png)

**[高度なアクセス ポリシー]** タブで、Key Vault が Azure Disk Encryption に対して有効になっていることを確認します。

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig4.png)

## <a name="disk-encryption-deployment-scenarios-and-user-experiences"></a>ディスク暗号化のデプロイメント シナリオとユーザー エクスペリエンス
有効にできるディスク暗号化シナリオは多数あり、手順はシナリオによって異なる場合があります。 以下のセクションでは、それらのシナリオについて詳しく説明します。

### <a name="enable-encryption-on-new-iaas-vms-that-are-created-from-the-marketplace"></a>Marketplace から作成された新しい IaaS VM での暗号化を有効にする
Azure の Marketplace から作成された新しい IaaS Windows VM でのディスク暗号化は、[Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image)を使用して有効化できます。

1. Azure クイック スタート テンプレートで、**[Azure にデプロイ]** をクリックし、**[パラメーター]** ブレードに暗号化構成を入力して、**[OK]** をクリックします。

2. サブスクリプション、リソース グループ、リソース グループの場所、法律条項および契約を選択し、**[作成]** をクリックして新しい IaaS VM での暗号化を有効にします。

> [!NOTE]
> このテンプレートでは、Windows Server 2012 ギャラリー イメージを使用する、暗号化された Windows VM が新規に作成されます。

この [Resource Manager テンプレート](https://aka.ms/fde-rhel)を使用して、200 GB の RAID-0 アレイを使用した新しい IaaS RedHat Linux 7.2 VM に対し、ディスク暗号化を有効にできます。 テンプレートをデプロイしたら、「[実行中の Linux VM での OS ドライブの暗号化](#encrypting-os-drive-on-a-running-linux-vm)」セクションで説明されている `Get-AzureRmVmDiskEncryptionStatus` コマンドレットを使用して、VM の暗号化の状態を確認します。 _VMRestartPending_ の状態が返された場合は、VM を再起動してください。

次の表は、Azure AD のクライアント ID を使用して Marketplace シナリオから作成された新しい VM に対する、Resource Manager テンプレートのパラメーターをまとめたものです。

| パラメーター | 説明 |
| --- | --- |
| adminUserName | 仮想マシンの管理者ユーザー名。 |
| adminPassword | 仮想マシンの管理者ユーザー パスワード。 |
| newStorageAccountName | OS とデータの VHD を格納するためのストレージ アカウントの名前。 |
| vmSize | VM のサイズ。 現時点では、Standard A、D、および G シリーズのみがサポートされています。 |
| virtualNetworkName | VM NIC が属している必要がある VNet の名前。 |
| subnetName | VM NIC が属している必要がある VNet のサブネットの名前。 |
| AADClientID | Key Vault にシークレットを書き込むためのアクセス許可を持つ Azure AD アプリケーションのクライアント ID。 |
| AADClientSecret | Key Vault にシークレットを書き込むためのアクセス許可を持つ Azure AD アプリケーションのクライアント シークレット。 |
| keyVaultURL | BitLocker キーのアップロード先となる Key Vault の URL。 これは、コマンドレット `(Get-AzureRmKeyVault -VaultName,-ResourceGroupName ).VaultURI` を使用して取得できます。 |
| keyEncryptionKeyURL | 生成された BitLocker キーの暗号化に使用されるキー暗号化キーの URL (省略可能)。 |
| keyVaultResourceGroup | Key Vault のリソース グループ。 |
| vmName | 暗号化操作が実行される VM の名前。 |

> [!NOTE]
> _KeyEncryptionKeyURL_ は省略可能なパラメーターです。 独自の KEK を使用して、Key Vault でデータ暗号化キー (パスフレーズ シークレット) の保護を強化することができます。

### <a name="enable-encryption-on-new-iaas-vms-that-are-created-from-customer-encrypted-vhd-and-encryption-keys"></a>事前に暗号化した VHD と暗号化キーから作成された新しい IaaS VM での暗号化を有効にする
このシナリオでは、Resource Manager テンプレート、PowerShell コマンドレット、または CLI コマンドを使用して、暗号化を有効にすることができます。 以下のセクションでは、Resource Manager テンプレートと CLI コマンドについて詳しく説明します。

次のいずれかのセクションの指示に従って、Azure で使用できる事前に暗号されたイメージを準備します。 イメージを作成したら、次のセクションの手順に従って、暗号化された Azure VM を作成できます。

* [事前に暗号化された Windows VHD を準備する](#preparing-a-pre-encrypted-windows-vhd)
* [事前に暗号化された Linux VHD を準備する](#preparing-a-pre-encrypted-linux-vhd)

#### <a name="using-the-resource-manager-template"></a>Resource Manager テンプレートの使用
暗号化された VHD でのディスク暗号化は、[Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-pre-encrypted-vm)を使用して有効化できます。

1. Azure クイック スタート テンプレートで、**[Azure にデプロイ]** をクリックし、**[パラメーター]** ブレードに暗号化構成を入力して、**[OK]** をクリックします。

2. サブスクリプション、リソース グループ、リソース グループの場所、法律条項および契約を選択し、**[作成]** をクリックして新しい IaaS VM での暗号化を有効にします。

次の表は、暗号化された VHD に対する Resource Manager テンプレートのパラメーターをまとめたものです。

| パラメーター | 説明 |
| --- | --- |
| newStorageAccountName | 暗号化された OS VHD を格納するためのストレージ アカウントの名前。 このストレージ アカウントは、VM と同じリソース グループおよび同じ場所に既に作成されている必要があります。 |
| osVhdUri | ストレージ アカウントからの OS VHD の URI。 |
| osType | OS 製品の種類 (Windows/Linux)。 |
| virtualNetworkName | VM NIC が属している必要がある VNet の名前。 この名前は、VM と同じリソース グループおよび同じ場所に既に作成されている必要があります。 |
| subnetName | VM NIC が属している必要がある VNet のサブネットの名前。 |
| vmSize | VM のサイズ。 現時点では、Standard A、D、および G シリーズのみがサポートされています。 |
| keyVaultResourceID | Azure Resource Manager 内で Key Vault リソースを識別するためのリソース ID。 これは、PowerShell コマンドレット `(Get-AzureRmKeyVault -VaultName &lt;yourKeyVaultName&gt; -ResourceGroupName &lt;yourResourceGroupName&gt;).ResourceId` を使用して取得できます。 |
| keyVaultSecretUrl | Key Vault 内にセットアップされたディスク暗号化キーの URL。 |
| keyVaultKekUrl | 生成されたディスク暗号化キーの暗号化に使用されるキー暗号化キーの URL。 |
| vmName | IaaS VM の名前。 |

#### <a name="using-powershell-cmdlets"></a>PowerShell コマンドレットの使用
暗号化された VHD でのディスク暗号化は、PowerShell コマンドレット [`Set-AzureRmVMOSDisk`](https://msdn.microsoft.com/library/azure/mt603746.aspx) を使用して有効化できます。  

#### <a name="using-cli-commands"></a>CLI コマンドの使用
このシナリオのディスク暗号化を CLI コマンドによって有効化するには、次の操作を行います。

1. Key Vault のアクセス ポリシーを設定します。

   * **EnabledForDiskEncryption** フラグを設定します。

    `azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
   * Key Vault にシークレットを書き込むためのアクセス許可を Azure AD アプリケーションに設定します。

    `azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys '["wrapKey"]' --perms-to-secrets '["set"]'`

2. 既存または実行中の VM に対して暗号化を有効にするには、次のように入力します。

 `azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId> --volume-type [All|OS|Data]`

3. 暗号化の状態を取得します。

 `azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json`

4. 暗号化された VHD から作成された新しい VM での暗号化を有効にするには、`azure vm create` コマンドで以下のパラメーターを使用します。

 ```
   * disk-encryption-key-vault-id <disk-encryption-key-vault-id>
   * disk-encryption-key-url <disk-encryption-key-url>
   * key-encryption-key-vault-id <key-encryption-key-vault-id>
   * key-encryption-key-url <key-encryption-key-url>
 ```

### <a name="enable-encryption-on-existing-or-running-iaas-windows-vm-in-azure"></a>Azure で既存または実行中の IaaS Windows VM での暗号化を有効にする
このシナリオでは、Resource Manager テンプレート、PowerShell コマンドレット、または CLI コマンドを使用して、暗号化を有効にすることができます。 以下のセクションでは、Resource Manager テンプレートと CLI コマンドを使用してこれを有効化する方法について詳しく説明します。

#### <a name="using-the-resource-manager-template"></a>Resource Manager テンプレートの使用
Azure 内にある既存または実行中の IaaS Windows VM でのディスク暗号化は、[Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)を使用して有効化できます。

1. Azure クイック スタート テンプレートで、**[Azure にデプロイ]** をクリックし、**[パラメーター]** ブレードに暗号化構成を入力して、**[OK]** をクリックします。

2. サブスクリプション、リソース グループ、リソース グループの場所、法律条項および契約を選択し、**[作成]** をクリックして、既存または実行中の IaaS VM での暗号化を有効にします。

次の表は、Azure AD のクライアント ID を使用する既存または実行中の VM に対する、Resource Manager テンプレートのパラメーターをまとめたものです。

| パラメーター | 説明 |
| --- | --- |
| AADClientID | Key Vault にシークレットを書き込むためのアクセス許可を持つ Azure AD アプリケーションのクライアント ID。 |
| AADClientSecret | Key Vault にシークレットを書き込むためのアクセス許可を持つ Azure AD アプリケーションのクライアント シークレット。 |
| KeyVaultName | BitLocker キーのアップロード先となる Key Vault の名前。 これは、コマンドレット `(Get-AzureRmKeyVault -ResourceGroupName <yourResourceGroupName>). Vaultname` を使用して取得できます。 |
|  keyEncryptionKeyURL | 生成された BitLocker キーの暗号化に使用されるキー暗号化キーの URL。 このパラメーターは、UseExistingKek ドロップダウン リストで **nokek** を選択した場合には省略可能です。 UseExistingKek ドロップダウン リストで **kek** を選択した場合は、_keyEncryptionKeyURL_ 値を入力する必要があります。 |
| volumeType | 暗号化操作が実行されるボリュームの種類。 有効な値は _OS_、_Data_、および _All_ です。 |
| sequenceVersion | BitLocker 操作のシーケンス バージョン。 ディスク暗号化操作が同じ VM で実行されるたびに、このバージョン番号をインクリメントします。 |
| vmName | 暗号化操作が実行される VM の名前。 |

> [!NOTE]
> _KeyEncryptionKeyURL_ は省略可能なパラメーターです。 独自の KEK を使用して、Key Vault でデータ暗号化キー (BitLocker 暗号化シークレット) の保護を強化することができます。

#### <a name="using-powershell-cmdlets"></a>PowerShell コマンドレットの使用
Azure Disk Encryption での暗号化を PowerShell コマンドレットによって有効化する方法については、ブログ投稿「[Explore Azure Disk Encryption with Azure PowerShell - Part 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx)」(Azure PowerShell を使用した Azure Disk Encryption の操作 - パート 1) および「[Explore Azure Disk Encryption with Azure PowerShell - Part 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx)」(Azure PowerShell を使用した Azure Disk Encryption の操作 - パート 2) をご覧ください。

#### <a name="using-cli-commands"></a>CLI コマンドの使用
Azure 内にある既存または実行中の IaaS Windows VM での暗号化を CLI コマンドによって有効化するには、次の操作を行います。

1. Key Vault のアクセス ポリシーを設定するには、次の操作を行います。
   * **EnabledForDiskEncryption** フラグを設定します。

    `azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
   * Key Vault にシークレットを書き込むためのアクセス許可を Azure AD アプリケーションに設定します。

    `azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys '["wrapKey"]' --perms-to-secrets '["set"]'`
2. 既存または実行中の VM に対して暗号化を有効にするには、次のコマンドを実行します。

 `azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId> --volume-type [All|OS|Data]`
3. 暗号化の状態を取得するには、次のコマンドを実行します。

 `azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json`
4. 暗号化された VHD から作成された新しい VM での暗号化を有効にするには、`azure vm create` コマンドで以下のパラメーターを使用します。

 ```
   * disk-encryption-key-vault-id <disk-encryption-key-vault-id>
   * disk-encryption-key-url <disk-encryption-key-url>
   * key-encryption-key-vault-id <key-encryption-key-vault-id>
   * key-encryption-key-url <key-encryption-key-url>
 ```

### <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm-in-azure"></a>Azure で既存または実行中の IaaS Linux VM での暗号化を有効にする
Azure 内にある既存または実行中の IaaS Linux VM でのディスク暗号化は、[Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)を使用して有効化できます。

1. Azure クイック スタート テンプレートで **[Azure にデプロイ]** をクリックし、**[パラメーター]** ブレードに暗号化構成を入力して、**[OK]** をクリックします。

2. サブスクリプション、リソース グループ、リソース グループの場所、法律条項および契約を選択し、**[作成]** をクリックして、既存または実行中の IaaS VM での暗号化を有効にします。

次の表は、Azure AD のクライアント ID を使用する既存または実行中の VM に対する、Resource Manager テンプレートのパラメーターをまとめたものです。

| パラメーター | 説明 |
| --- | --- |
| AADClientID | Key Vault にシークレットを書き込むためのアクセス許可を持つ Azure AD アプリケーションのクライアント ID。 |
| AADClientSecret | Key Vault にシークレットを書き込むためのアクセス許可を持つ Azure AD アプリケーションのクライアント シークレット。 |
| KeyVaultName | BitLocker キーのアップロード先となる Key Vault の名前。 これは、コマンドレット `(Get-AzureRmKeyVault -ResourceGroupName <yourResourceGroupName>). Vaultname` を使用して取得できます。 |
|  keyEncryptionKeyURL | 生成された BitLocker キーの暗号化に使用されるキー暗号化キーの URL。 このパラメーターは、UseExistingKek ドロップダウン リストで **nokek** を選択した場合には省略可能です。 UseExistingKek ドロップダウン リストで **kek** を選択した場合は、_keyEncryptionKeyURL_ 値を入力する必要があります。 |
| volumeType | 暗号化操作が実行されるボリュームの種類。 サポートされている有効な値は、_OS_ または _All_ (RHEL 7.2、CentOS 7.2、および Ubuntu 16.04) と _Data_ (それ以外のすべてのディストリビューション) です。 |
| sequenceVersion | BitLocker 操作のシーケンス バージョン。 ディスク暗号化操作が同じ VM で実行されるたびに、このバージョン番号をインクリメントします。 |
| vmName | 暗号化操作が実行される VM の名前。 |
| passPhrase | データ暗号化キーとして強力なパスフレーズを入力します。 |

> [!NOTE]
> _KeyEncryptionKeyURL_ は省略可能なパラメーターです。 独自の KEK を使用して、Key Vault でデータ暗号化キー (パスフレーズ シークレット) の保護を強化することができます。

#### <a name="cli-commands"></a>CLI コマンド
暗号化された VHD でのディスク暗号化は、[CLI コマンド](../cli-install-nodejs.md)をインストールして使用することで有効化できます。 Azure 内にある既存または実行中の IaaS Linux VM での暗号化を CLI コマンドによって有効化するには、次の操作を行います。

1. Key Vault のアクセス ポリシーを設定します。

 * **EnabledForDiskEncryption** フラグを設定します。

    `azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
 * Key Vault にシークレットを書き込むためのアクセス許可を Azure AD アプリケーションに設定します。

    `azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys '["wrapKey"]' --perms-to-secrets '["set"]'`

2. 既存または実行中の VM に対して暗号化を有効にするには、次のコマンドを実行します。

 `azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId> --volume-type [All|OS|Data]`

3. 暗号化の状態を取得します。

 `azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json`

4. 暗号化された VHD から作成された新しい VM での暗号化を有効にするには、`azure vm create` コマンドで以下のパラメーターを使用します。
 ```
   * disk-encryption-key-vault-id <disk-encryption-key-vault-id>
   * disk-encryption-key-url <disk-encryption-key-url>
   * key-encryption-key-vault-id <key-encryption-key-vault-id>
   * key-encryption-key-url <key-encryption-key-url>
 ```

### <a name="get-the-encryption-status-of-an-encrypted-iaas-vm"></a>暗号化された IaaS VM の暗号化状態を取得する
暗号化状態は、Azure Resource Manager、[PowerShell コマンドレット](https://msdn.microsoft.com/library/azure/mt622700.aspx)、または CLI コマンドを使用して取得できます。 以下のセクションでは、Azure クラシック ポータルと CLI コマンドを使用して暗号化状態を取得する方法について説明します。

#### <a name="get-the-encryption-status-of-an-encrypted-windows-vm-by-using-azure-resource-manager"></a>暗号化された Windows VM の暗号化状態を、Azure Resource Manager を使用して取得する
Azure Resource Manager から IaaS VM の暗号化状態を取得するには、次の操作を行います。

1. [Azure クラシック ポータル](https://portal.azure.com/)にサインインし、左側のウィンドウで **[仮想マシン]** をクリックして、サブスクリプション内の仮想マシンの概要ビューを表示します。 仮想マシン ビューは、**[サブスクリプション]** ドロップダウン リストでサブスクリプション名を選択することでフィルター処理できます。

2. **[仮想マシン]** ページの上部にある **[列]** をクリックします。

3. **[列を選択]** ブレードで、**[ディスク暗号化]** を選択し、**[更新]** をクリックします。 次の図に示すように、各 VM の _[有効]_ または _[無効]_ の暗号化状態を表示するディスク暗号化列が表示されます。

 ![Azure での Microsoft マルウェア対策](./media/azure-security-disk-encryption/disk-encryption-fig2.png)

#### <a name="get-the-encryption-status-of-an-encrypted-windowslinux-iaas-vm-by-using-the-disk-encryption-powershell-cmdlet"></a>暗号化された (Windows/Linux) IaaS VM の暗号化状態を、ディスク暗号化 PowerShell コマンドレットを使用して取得する
IaaS VM の暗号化状態は、ディスク暗号化 PowerShell コマンドレット `Get-AzureRmVMDiskEncryptionStatus` を使用して取得できます。 VM の暗号化設定を取得するには、次のように入力します。

    C:\> Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : NotEncrypted
    DataVolumesEncrypted       : Encrypted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a

_Get-AzureRmVMDiskEncryptionStatus_ の出力から、暗号化キーの URL を確認できます。

    C:\> $status = Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName
    e $VMName -ExtensionName $ExtensionName
    C:\> $status.OsVolumeEncryptionSettings

    DiskEncryptionKey                                                 KeyEncryptionKey                                               Enabled
    -----------------                                                 ----------------                                               -------
    Microsoft.Azure.Management.Compute.Models.KeyVaultSecretReference Microsoft.Azure.Management.Compute.Models.KeyVaultKeyReference    True


    C:\> $status.OsVolumeEncryptionSettings.DiskEncryptionKey.SecretUrl
    https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a
    C:\> $status.OsVolumeEncryptionSettings.DiskEncryptionKey

    SecretUrl                                                                                                               SourceVault
    ---------                                                                                                               -----------
    https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a Microsoft.Azure.Management....

OSVolumeEncrypted と DataVolumesEncrypted の設定値は _Encrypted_ に設定されています。これは、両方のボリュームが Azure Disk Encryption を使用して暗号化されることを示します。 Azure Disk Encryption での暗号化を PowerShell コマンドレットによって有効化する方法については、ブログ投稿「[Explore Azure Disk Encryption with Azure PowerShell - Part 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx)」(Azure PowerShell を使用した Azure Disk Encryption の操作 - パート 1) および「[Explore Azure Disk Encryption with Azure PowerShell - Part 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx)」(Azure PowerShell を使用した Azure Disk Encryption の操作 - パート 2) をご覧ください。

> [!NOTE]
> Linux VM では、`Get-AzureRmVMDiskEncryptionStatus` コマンドレットで暗号化状態が返されるまでに 3 ～ 4 分かかります。

#### <a name="get-the-encryption-status-of-the-iaas-vm-from-the-disk-encryption-cli-command"></a>ディスク暗号化 CLI コマンドから IaaS VM の暗号化状態を取得する
IaaS VM の暗号化状態は、ディスク暗号化 CLI コマンド `azure vm show-disk-encryption-status` を使用して取得できます。 VM の暗号化設定を取得するには、Azure CLI セッションで以下のように入力します。

    azure vm show-disk-encryption-status --resource-group <yourResourceGroupName> --name <yourVMName> --json  

#### <a name="disable-encryption-on-running-windows-iaas-vm"></a>実行中の Windows IaaS VM での暗号化を無効にする
実行中の Windows IaaS VM や Linux IaaS VM でのディスク暗号化を無効にし、暗号化解除の構成を指定するには、Azure Disk Encryption の Resource Manager テンプレートか、または PowerShell コマンドレットを使用できます。

##### <a name="windows-vm"></a>Windows VM
暗号化を無効にする手順を実行すると、Windows IaaS VM で実行されている OS、データ ボリューム、またはその両方の暗号化が無効になります。 OS ボリュームの暗号化を無効にして、データ ボリュームを暗号化されたままにすることはできません。 暗号化を無効にする手順を実行すると、Azure クラシック デプロイメント モデルによって VM サービス モデルが更新され、Windows IaaS VM は暗号化解除済みとしてマークされます。 VM の内容は保存時に暗号化されなくなります。 暗号化を解除しても、Key Vault や暗号化キー マテリアル (Windows の場合は BitLocker 暗号化キー、Linux の場合はパスフレーズ) は削除されません。

##### <a name="linux-vm"></a>Linux VM
暗号化を無効にする手順を実行すると、Linux IaaS VM で実行されているデータ ボリュームの暗号化が無効になります。

> [!NOTE]
> Linux VM では、OS ディスクの暗号化を無効にすることは許可されていません。

##### <a name="disable-encryption-on-an-existing-or-running-iaas-vm"></a>既存または実行中の VM での暗号化を無効にする
実行中の Windows IaaS VM でのディスク暗号化は、[Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm)を使用して無効化できます。

1. Azure クイック スタート テンプレートで、**[Azure にデプロイ]** をクリックし、**[パラメーター]** ブレードに暗号化解除構成を入力して、**[OK]** をクリックします。

2. サブスクリプション、リソース グループ、リソース グループの場所、法律条項および契約を選択し、**[作成]** をクリックして新しい IaaS VM での暗号化を有効にします。

Linux VM の場合は、[Disable encryption on a running Linux VM](https://aka.ms/decrypt-linuxvm) (実行中の Linux VM での暗号化を無効にする) テンプレートを使用して暗号化を無効にできます。

次の表は、実行中の IaaS VM での暗号化を無効にするための、Resource Manager テンプレートのパラメーターをまとめたものです。

| パラメーター | Description |
| --- | --- |
| vmName | 暗号化操作が実行される VM の名前。
| volumeType | 暗号化解除操作が実行されるボリュームの種類。 有効な値は _OS_、_Data_、および _All_ です。 _Data_ ボリュームの暗号化を無効にせずに、実行中の Windows IaaS VM の OS (ブート) ボリュームの暗号化を無効にすることはできません。 また、Linux VM では、OS ディスクの暗号化を無効にすることは許可されていません。 |
| sequenceVersion | BitLocker 操作のシーケンス バージョン。 ディスク暗号化解除操作が同じ VM で実行されるたびに、このバージョン番号をインクリメントします。 |

##### <a name="disable-encryption-on-an-existing-or-running-iaas-vm"></a>既存または実行中の VM での暗号化を無効にする
既存または実行中の IaaS VM での暗号化を PowerShell コマンドレットによって無効化する方法については、[`Disable-AzureRmVMDiskEncryption`](https://msdn.microsoft.com/library/azure/mt715776.aspx) をご覧ください。 このコマンドレットは、Windows VM と Linux VM の両方をサポートします。 このコマンドレットを実行すると、暗号化を無効にするための拡張機能が仮想マシンにインストールされます。 _Name_ パラメーターが指定されなかった場合、拡張機能は既定の名前 (_AzureDiskEncryption for Windows VMs_) で作成されます。

Linux VM では、AzureDiskEncryptionForLinux 拡張機能が使用されます。

> [!NOTE]
> このコマンドレットにより、仮想マシンが再起動されます。

## <a name="appendix"></a>付録
### <a name="connect-to-your-subscription"></a>サブスクリプションへの接続
続行する前に、この記事の「前提条件」セクションを確認してください。 すべての前提条件が満たされていることを確認したら、次の手順に従ってサブスクリプションに接続してください。

1. Azure PowerShell セッションを開始し、次のコマンドを使用して Azure アカウントにサインインします。

    `Login-AzureRmAccount`

2. 複数のサブスクリプションがあり、使用するサブスクリプションを指定する場合は、次のように入力して自分のアカウントのサブスクリプションを表示します。

    `Get-AzureRmSubscription`

3. 使用するサブスクリプションを指定するには、次のように入力します。

    `Select-AzureRmSubscription -SubscriptionName <Yoursubscriptionname>`

4. 構成されているサブスクリプションが正しいことを確認するには、次のように入力します。

    `Get-AzureRmSubscription`

5. Azure Disk Encryption コマンドレットがインストールされていることを確認するには、次のように入力します。

    `Get-command *diskencryption*`

6. 次のような出力が表示されるので、Azure Disk Encryption PowerShell のインストールを確認します。

```
    PS C:\Windows\System32\WindowsPowerShell\v1.0> get-command *diskencryption*
    CommandType  Name                                         Source                                                             
    Cmdlet       Get-AzureRmVMDiskEncryptionStatus            AzureRM.Compute                                                    
    Cmdlet       Disable-AzureRmVMDiskEncryption              AzureRM.Compute                                                    
    Cmdlet       Set-AzureRmVMDiskEncryptionExtension         AzureRM.Compute                                                     
```

### <a name="prepare-a-pre-encrypted-windows-vhd"></a>事前に暗号化された Windows VHD を準備する
以下のセクションに示すのは、事前に暗号化された Windows VHD を準備し、それを Azure IaaS 内の暗号化された VHD としてデプロイするために必要な情報です。 Azure Site Recovery や Azure 上に新しい Windows VM (VHD) を準備し、それらを起動する際には、これらの情報を使用してください。

#### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>グループ ポリシーを更新して非 TPM で OS を保護できるようにする
**[ローカル コンピューター ポリシー]** > **[コンピューターの構成]** > **[管理用テンプレート]** > **[Windows コンポーネント]** の下にある、**[BitLocker ドライブ暗号化]**という BitLocker グループ ポリシー設定を構成します。 以下の図に示すように、**[オペレーティング システムのドライブ]** > **[スタートアップ時に追加の認証を要求する]** > **[互換性のある TPM が装備されていない BitLocker を許可する]** の順に選択して、この設定を変更します。

![Azure での Microsoft マルウェア対策](./media/azure-security-disk-encryption/disk-encryption-fig8.png)

#### <a name="install-bitlocker-feature-components"></a>BitLocker 機能コンポーネントのインストール
Windows Server 2012 以降の場合は、次のコマンドを使用します。

    dism /online /Enable-Feature /all /FeatureName:BitLocker /quiet /norestart

Windows Server 2008 R2 の場合は、次のコマンドを使用します。

    ServerManagerCmd -install BitLockers

#### <a name="prepare-the-os-volume-for-bitlocker-by-using-bdehdcfg"></a>`bdehdcfg` を使用して BitLocker の OS ボリュームを準備する
OS のパーティションを圧縮して、BitLocker のコンピューターを準備するには、次のコマンドを実行します。

    bdehdcfg -target c: shrink -quiet

#### <a name="protect-the-os-volume-by-using-bitlocker"></a>BitLocker を使用して OS ボリュームを保護する
ブート ボリュームでの暗号化を、外部キーの保護機能を使用して有効化するには、[`manage-bde`](https://technet.microsoft.com/library/ff829849.aspx) コマンドを使用します。 また、外部ドライブまたは外部ボリューム上に外部キー (.bek ファイル) を配置します。 システム/ブート ボリュームでの暗号化は、次回のリブート後に有効になります。

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

> [!NOTE]
> BitLocker を使用して外部キーを取得する場合は、そのためのデータ/リソース VHD を別個に使用して VM を準備してください。

#### <a name="encrypting-an-os-drive-on-a-running-linux-vm"></a>実行中の Linux VM での OS ドライブの暗号化
実行中の Linux VM での OS のドライブの暗号化は、次のディストリビューションでサポートされます。

* RHEL 7.2
* CentOS 7.2
* Ubuntu 16.04

##### <a name="prerequisites-for-os-disk-encryption"></a>OS ディスクを暗号化するための前提条件

* VM は、Azure Resource Manager で Marketplace イメージから作成する必要があります。
* 少なくとも 4 GB の RAM を持つAzure VM (推奨するサイズは 7 GB)。
* (RHEL と CentOS については) SELinux を無効にします。 SELinux を無効にする方法については、 VM で「[SELinux User's and Administrator's Guide (SELinux ユーザーおよび管理者用ガイド)](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux)」の「4.4.2. Disabling SELinux (SELinux の無効化)」をご覧ください。
* SELinux を無効にしたら、少なくとも 1 回、VM を再起動してください。

##### <a name="steps"></a>手順
1. 上記のディストリビューションのいずれかを使用して、VM を作成します。

 CentOS 7.2 では、OS ディスクの暗号化は、特別なイメージを使用してサポートされます。 このイメージを使用するには、VM を作成するときに、SKU として "7.2n" を指定します。
 ```
    Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"
 ```
2. ニーズに応じて VM を構成します。 すべてのドライブ (OS およびデータ) を暗号化する場合は、/etc/fstab にデータ ドライブを指定してマウントできるようにする必要があります。

 > [!NOTE]
 > /etc/fstab にデータ ドライブを指定するには、ブロック デバイス名 (たとえば、/dev/sdb1 など) ではなく、UUID=... を使用してください。 暗号化中には、ドライブの順序が VM 上で変更されます。 VM がブロック デバイスの特定の順序に依存している場合、暗号化後のマウントは失敗します。

3. SSH セッションからサインアウトします。

4. OS を暗号化するには、[暗号化を有効にする](#enable-encryption-on-existing-or-running-iaas-linux-vm-in-azure)ときに、volumeType として **All** または **OS** を指定します。

 > [!NOTE]
 > `systemd` サービスとして実行されているすべてのユーザー スペースのプロセスは、`SIGKILL` によって強制終了されます。 VM を再起動してください。 実行中の VM での OS ディスクの暗号化を有効にする場合は、VM のダウンタイムを計画してください。

5. [次のセクション](#monitoring-os-encryption-progress)の指示に従って、暗号化の進行状況を定期的に監視します。

6. Get-AzureRmVmDiskEncryptionStatus で "VMRestartPending" が表示されたら、VM にサインインするか、ポータル、PowerShell、または CLI を使用して VM を再起動します。
    ```
    C:\> Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, reboot the VM
    ```
再起動する前に、VM の[ブート診断](https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/)を保存することをお勧めします。

#### <a name="monitoring-os-encryption-progress"></a>OS 暗号化の進行状況の監視
OS 暗号化の進行状況を監視するには、次の 3 つの方法があります。

* `Get-AzureRmVmDiskEncryptionStatus` コマンドレットを使用して、ProgressMessage フィールドを確認する。
    ```
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started
    ```
 VM が "OS disk encryption started (OS ディスクの暗号化が開始されました)" 状態に達するまで、Premium ストレージを使用する VM でおよそ 40 ～ 50 分かかります。

 WALinuxAgent での[問題 #388](https://github.com/Azure/WALinuxAgent/issues/388) により、一部のディストリビューションでは、`OsVolumeEncrypted` と `DataVolumesEncrypted` は `Unknown` と表示されます。 WALinuxAgent バージョン 2.1.5 以降では、この問題は自動的に修正されます。 出力に `Unknown` が含まれている場合は、Azure リソース エクスプローラーを使用して、ディスクの暗号化状態を確認できます。

 [Azure リソース エクスプローラー](https://resources.azure.com/)に移動し、左側の選択パネルで次の階層を展開します。

 ~~~~
 |-- subscriptions
     |-- [Your subscription]
          |-- resourceGroups
               |-- [Your resource group]
                    |-- providers
                         |-- Microsoft.Compute
                              |-- virtualMachines
                                   |-- [Your virtual machine]
                                        |-- InstanceView
~~~~                

 InstanceView で、画面を下にスクロールして、ドライブの暗号化の状態を表示します。

 ![VM インスタンス ビュー](./media/azure-security-disk-encryption/vm-instanceview.png)

* [ブート診断](https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/)を調べる。 ADE 拡張機能のメッセージには、頭に `[AzureDiskEncryption]` が付きます。

* SSH を使用して VM にサインインし、拡張機能ログを取得する。

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

 OS 暗号化の進行中は、VM にサインインしないでください。 ログのコピーは、他の 2 つの方法が失敗した場合にのみ行ってください。

#### <a name="prepare-a-pre-encrypted-linux-vhd"></a>事前に暗号化された Linux VHD を準備する
##### <a name="ubuntu-16"></a>Ubuntu 16
次の手順に従って、ディストリビューションのインストール時に暗号化を構成します。

1. ディスクをパーティション分割するときに **[Configure encrypted volumes]** を選択します。

 ![Ubuntu 16.04 のセットアップ](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2. 独立したブート ドライブを作成します。このドライブは暗号化しません。 ルート ドライブを暗号化します。

 ![Ubuntu 16.04 のセットアップ](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3. パスフレーズを指定します。 これは、KeyVault にアップロードするパスフレーズです。

 ![Ubuntu 16.04 のセットアップ](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4. パーティション分割を終了します。

 ![Ubuntu 16.04 のセットアップ](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. VM を起動し、パスフレーズの入力を求められたら、手順 3 で指定したパスフレーズを入力します。

 ![Ubuntu 16.04 のセットアップ](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6. [こちらの手順](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-create-upload-ubuntu/)に従って、VM を Azure にアップロードするための準備をします。 最後の手順 (VM のプロビジョニング解除) はまだ実行しないでください。

次の手順に従って、暗号化に関する Azure との連携を構成します。

1. 以下のスクリプトのコンテンツを使用して、/usr/local/sbin/azure_crypt_key.sh の下にファイルを作成します。 KeyFileName に注意してください。これは、Azure によって使用されるパスフレーズ ファイル名です。

    ```
    #!/bin/sh
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint
    modprobe vfat >/dev/null 2>&1
    modprobe ntfs >/dev/null 2>&1
    sleep 2
    OPENED=0
    cd /sys/block
    for DEV in sd*; do

        echo "> Trying device: $DEV ..." >&2
        mount -t vfat -r /dev/${DEV}1 $MountPoint >/dev/null||
        mount -t ntfs -r /dev/${DEV}1 $MountPoint >/dev/null
        if [ -f $MountPoint/$KeyFileName ]; then
                cat $MountPoint/$KeyFileName
                umount $MountPoint 2>/dev/null
                OPENED=1
                break
        fi
        umount $MountPoint 2>/dev/null
    done

      if [ $OPENED -eq 0 ]; then
        echo "FAILED to find suitable passphrase file ..." >&2
        echo -n "Try to enter your password: " >&2
        read -s -r A </dev/console
        echo -n "$A"
     else
        echo "Success loading keyfile!" >&2
    fi
```

2. */etc/crypttab* の crypt 構成を変更します。 次のようになります。
 ```
    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh
    ```

3. Windows で *azure_crypt_key.sh* を編集し、それを Linux にコピーした場合は、`dos2unix /usr/local/sbin/azure_crypt_key.sh` を実行します。

4. 実行可能アクセス許可をスクリプトに追加します。
 ```
    chmod +x /usr/local/sbin/azure_crypt_key.sh
 ```
5. */etc/initramfs-tools/modules* を編集し、次の行を追加します。
 ```
    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1
```
6. `update-initramfs -u -k all` を実行して initramfs を更新し、`keyscript` を有効にします。

7. これで、VM をプロビジョニング解除できるようになります。

 ![Ubuntu 16.04 のセットアップ](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig6.png)

8. 次の手順に進み、Azure に [VHD をアップロード](#upload-encrypted-vhd-to-an-azure-storage-account)します。

##### <a name="opensuse-132"></a>openSUSE 13.2
ディストリビューションのインストール時に暗号化を構成するには、次の操作を行います。
1. ディスクをパーティション分割するときに、**[ボリューム グループの暗号化]** を選択し、パスワードを入力します。 これは、Key Vault にアップロードするパスワードです。

 ![openSUSE 13.2 のセットアップ](./media/azure-security-disk-encryption/opensuse-encrypt-fig1.png)

2. パスワードを使用して VM を起動します。

 ![openSUSE 13.2 のセットアップ](./media/azure-security-disk-encryption/opensuse-encrypt-fig2.png)

3. 「[Azure 用の SLES または openSUSE 仮想マシンの準備](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-suse-create-upload-vhd/#prepare-opensuse-131)」の手順に従って、VM を Azure にアップロードするための準備をします。 最後の手順 (VM のプロビジョニング解除) はまだ実行しないでください。

暗号化に関する Azure との連携を構成するには、次の操作を行います。
1. /etc/dracut.conf を編集し、次の行を追加します。
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```
2. ファイル /usr/lib/dracut/modules.d/90crypt/module-setup.sh の末尾付近にある以下の行をコメント アウトします。
 ```
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
 ```

3. /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh ファイルの先頭に次の行を追加し、
 ```
    DRACUT_SYSTEMD=0
 ```
次の記述のすべての出現箇所を
 ```
    if [ -z "$DRACUT_SYSTEMD" ]; then
 ```
を次のように変更します。
```
    if [ 1 ]; then
```
4. /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh を編集し、それを "# Open LUKS device" に追加します。

    ```
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```
5. `/usr/sbin/dracut -f -v` を実行して initrd を更新します。

6. これで、VM をプロビジョニング解除し、Azure に [VHD をアップロード](#upload-encrypted-vhd-to-an-azure-storage-account)できるようになります。

##### <a name="centos-7"></a>CentOS 7
ディストリビューションのインストール時に暗号化を構成するには、次の操作を行います。
1. ディスクをパーティション分割するときに **[Encrypt my data]** を選択します。

 ![CentOS 7 のセットアップ](./media/azure-security-disk-encryption/centos-encrypt-fig1.png)

2. ルート パーティションで **[Encrypt]** が選択されていることを確認します。

 ![CentOS 7 のセットアップ](./media/azure-security-disk-encryption/centos-encrypt-fig2.png)

3. パスフレーズを指定します。 これは、Key Vault にアップロードするパスフレーズです。

 ![CentOS 7 のセットアップ](./media/azure-security-disk-encryption/centos-encrypt-fig3.png)

4. VM を起動し、パスフレーズの入力を求められたら、手順 3 で指定したパスフレーズを入力します。

 ![CentOS 7 のセットアップ](./media/azure-security-disk-encryption/centos-encrypt-fig4.png)

5. 「[Azure 用の CentOS ベースの仮想マシンの準備](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-create-upload-centos/#centos-70)」の「CentOS 7.0+」の手順に従って、VM を Azure にアップロードするための準備をします。 最後の手順 (VM のプロビジョニング解除) はまだ実行しないでください。

6. これで、VM をプロビジョニング解除し、Azure に [VHD をアップロード](#upload-encrypted-vhd-to-an-azure-storage-account)できるようになります。

暗号化に関する Azure との連携を構成するには、次の操作を行います。

1. /etc/dracut.conf を編集し、次の行を追加します。
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```

2. ファイル /usr/lib/dracut/modules.d/90crypt/module-setup.sh の末尾付近にある以下の行をコメント アウトします。
```
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
```

3. /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh ファイルの先頭に次の行を追加し、
```
    DRACUT_SYSTEMD=0
```
次の記述のすべての出現箇所を
```
    if [ -z "$DRACUT_SYSTEMD" ]; then
```
to
```
    if [ 1 ]; then
```
4. /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh を編集し、これを "# Open LUKS device" の後に追加します。
    ```
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```    
5. “/usr/sbin/dracut -f -v” を実行して initrd を更新します。

![CentOS 7 のセットアップ](./media/azure-security-disk-encryption/centos-encrypt-fig5.png)

### <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>暗号化された VHD を Azure ストレージ アカウントにアップロードする
BitLocker 暗号化または DM-Crypt 暗号化を有効にしたら、ローカルで暗号化された VHD をストレージ アカウントにアップロードする必要があります。

    Add-AzureRmVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]

### <a name="upload-the-disk-encryption-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>事前に暗号化された VM のディスク暗号化シークレットを Key Vault にアップロードする
前に取得したディスク暗号化シークレットを、Key Vault にシークレットとしてアップロードする必要があります。 Key Vault では、ディスク暗号化だけではなく、Azure AD クライアントに対するアクセス許可も有効になっている必要があります。

    $AadClientId = "YourAADClientId"
    $AadClientSecret = "YourAADClientSecret"

    $key vault = New-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

    Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
    Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption


#### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>KEK で暗号化されないディスク暗号化シークレット
Key Vault でシークレットをセットアップするには、[Set-AzureKeyVaultSecret](https://msdn.microsoft.com/library/dn868050.aspx) を使用します。 Windows 仮想マシンがある場合は、bek ファイルが base64 文字列としてエンコードされ、その後、`Set-AzureKeyVaultSecret` コマンドレットを使用して Key Vault にアップロードされます。 Linux の場合は、パスフレーズが base64 文字列としてエンコードされ、その後 Key Vault にアップロードされます。 また、Key Vault でシークレットを作成する際には、以下のタグが設定されます。

    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $secretName = [guid]::NewGuid().ToString()
    $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
    $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

    $secret = Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
    $secretUrl = $secret.Id

[KEK を使用せずに OS ディスクをアタッチする](#without-using-a-kek)場合は、次の手順で `$secretUrl` を使用します。

#### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>KEK で暗号化されるディスク暗号化シークレット
必要であれば、シークレットを Key Vault にアップロードする前に、キー暗号化キーを使用してシークレットを暗号化できます。 最初にキー暗号化キーを使用してシークレットを暗号化するには、ラップ [API](https://msdn.microsoft.com/library/azure/dn878066.aspx) を使用します。 このラップ操作の出力は、base64 URL エンコードされた文字列です。これは、[`Set-AzureKeyVaultSecret`](https://msdn.microsoft.com/library/dn868050.aspx) コマンドレットを使用してシークレットとしてアップロードできます。

    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzureKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzureKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id

[KEK を使用して OS ディスクをアタッチする](#using-a-kek)場合は、次の手順で `$KeyEncryptionKey` と `$secretUrl` を使用します。

### <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>OS ディスクをアタッチするときにシークレット URL を指定する
#### <a name="without-using-a-kek"></a>KEK を使用しない
OS ディスクをアタッチする際に、`$secretUrl` を渡す必要があります。 この URL は、「KEK で暗号化されないディスク暗号化シークレット」セクションで生成されたものです。

    Set-AzureRmVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl

#### <a name="using-a-kek"></a>KEK を使用する
OS ディスクをアタッチする際に、`$KeyEncryptionKey` と `$secretUrl` を渡します。 この URL は、「KEK で暗号化されないディスク暗号化シークレット」セクションで生成されたものです。

    Set-AzureRmVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id

## <a name="download-this-guide"></a>このガイドをダウンロードする
このガイドは [TechNet ギャラリー](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)からダウンロードできます。

## <a name="for-more-information"></a>BLOB の詳細
[Azure PowerShell を使用する Azure Disk Encryption の探索 - パート 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/16/explore-azure-disk-encryption-with-azure-powershell.aspx?wa=wsignin1.0)  
[Azure PowerShell を使用する Azure Disk Encryption の探索 - パート 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx)

