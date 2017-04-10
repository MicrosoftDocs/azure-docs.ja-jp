---
title: "Azure Key Vault の概要 | Microsoft Docs"
description: "このチュートリアルを使用すると、Azure Key Vault で、強化されたコンテナーを Azure に作成し、暗号化キーやシークレットを Azure に格納して管理できるようになります。"
services: key-vault
documentationcenter: 
author: cabailey
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 36721e1d-38b8-4a15-ba6f-14ed5be4de79
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 10/24/2016
ms.author: cabailey
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 115a718977906b3e647b628017f7ab46a588d7f2


---
# <a name="get-started-with-azure-key-vault"></a>Azure Key Vault の概要
Azure Key Vault は、ほとんどのリージョンで使用できます。 詳細については、 [Key Vault の価格のページ](https://azure.microsoft.com/pricing/details/key-vault/)を参照してください。

## <a name="introduction"></a>はじめに
このチュートリアルを使用すると、Azure Key Vault で、強化されたコンテナー (資格情報コンテナー) を Azure に作成し、暗号化キーやシークレットを Azure に格納して管理できるようになります。 ここでは、Azure PowerShell を使用して、Azure アプリケーションで使用できるキーまたはパスワードを含む資格情報コンテナーを作成するプロセスについて説明します。 アプリケーションがそのキーやパスワードを使用する方法についても説明します。

**推定所要時間:** 20 分

> [!NOTE]
> このチュートリアルでは、Key Vault でキーやシークレットを使用するようにアプリケーションを承認する手順の中で、Azure アプリケーションを作成する方法については説明していません。
> 
> このチュートリアルでは、Azure PowerShell を使用します。 クロスプラットフォーム コマンド ライン インターフェイスの手順については、 [対応するチュートリアル](key-vault-manage-with-cli.md)を参照してください。
> 
> 

Azure Key Vault の概要については、「 [Azure Key Vault とは](key-vault-whatis.md)

## <a name="prerequisites"></a>前提条件
このチュートリアルを完了するには、以下が必要です。

* Microsoft Azure サブスクリプション。 サブスクリプションがない場合でも、 [無料アカウント](https://azure.microsoft.com/pricing/free-trial/)にサインアップできます。
* Azure PowerShell **1.1.0 以降のバージョン**。 Azure PowerShell をインストールして、Azure サブスクリプションに関連付けるには、「 [Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs)」を参照してください。 Azure PowerShell をインストール済みで、バージョンがわからない場合は、Azure PowerShell コンソールで「 `(Get-Module azure -ListAvailable).Version`」と入力します。 Azure PowerShell バージョン 0.9.1 ～ 0.9.8 がインストールされている場合は、少し変更を加えるだけで、引き続きこのチュートリアルを利用できます。 たとえば、 `Switch-AzureMode AzureResourceManager` コマンドを使用する必要があったり、Azure Key Vault のコマンドの一部が変更されていたりします。 バージョン 0.9.1 ～ 0.9.8 の Key Vault コマンドレットの一覧については、「[Azure Key Vault Cmdlets (Azure Key Vault コマンドレット)](https://msdn.microsoft.com/library/azure/dn868052\(v=azure.98\).aspx)」を参照してください。 
* このチュートリアルで作成したキーやパスワードを使用して構成されるアプリケーション。 サンプル アプリケーションは、[Microsoft ダウンロード センター](http://www.microsoft.com/en-us/download/details.aspx?id=45343)から入手できます。 手順については、付属の Readme ファイルをご覧ください。

このチュートリアルは、Azure PowerShell の初心者向けに設計されていますが、モジュール、コマンドレット、セッションなどの基本概念を理解していることを前提としています。 詳細については、 [Windows PowerShell の概要](https://technet.microsoft.com/library/hh857337.aspx)に関するページを参照してください。

このチュートリアルに表示されているコマンドレットの詳しいヘルプを確認には、 **Get-Help** コマンドレットを使用します。

    Get-Help <cmdlet-name> -Detailed

たとえば、 **Login-AzureRmAccount** コマンドレットのヘルプを確認するには、次のように入力します。

    Get-Help Login-AzureRmAccount -Detailed

また、次のチュートリアルをお読みになり、Azure PowerShell での Azure リソース マネージャーについて詳しく理解してください。

* [Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs)
* [リソース マネージャーでの Azure PowerShell の使用](../powershell-azure-resource-manager.md)

## <a name="a-idconnectaconnect-to-your-subscriptions"></a><a id="connect"></a>サブスクリプションへの接続
Azure PowerShell セッションを開始し、次のコマンドで Azure アカウントにサインインします。  

    Login-AzureRmAccount 

Azure の特定のインスタンス (たとえば Azure Government) を使用している場合は、このコマンドで -Environment パラメーターを使用してください。 次に例を示します。`Login-AzureRmAccount –Environment (Get-AzureRmEnvironment –Name AzureUSGovernment)`

ポップアップ ブラウザー ウィンドウで、Azure アカウントのユーザー名とパスワードを入力します。 Azure PowerShell は、このアカウントに関連付けられているすべてのサブスクリプションを取得し、既定で最初のサブスクリプションを使用します。

複数のサブスクリプションがあり、特定の 1 つのサブスクリプションを指定して Azure Key Vault を使用する場合は、次のように入力して自分のアカウントのサブスクリプションを表示します。

    Get-AzureRmSubscription

使用するサブスクリプションを指定するには、次のように入力します。

    Set-AzureRmContext -SubscriptionId <subscription ID>

Azure PowerShell の詳細については、「 [How to install and configure Azure PowerShell (Azure PowerShell のインストールと構成の方法)](/powershell/azureps-cmdlets-docs)」をご覧ください。

## <a name="a-idresourceacreate-a-new-resource-group"></a><a id="resource"></a>新しいリソース グループを作成する
Azure リソース マネージャーを使用すると、すべての関連するリソースが 1 つのリソース グループ内に作成されます。 このチュートリアルでは、 **ContosoResourceGroup** という名前の新しいリソース グループを作成します。

    New-AzureRmResourceGroup –Name 'ContosoResourceGroup' –Location 'East Asia'


## <a name="a-idvaultacreate-a-key-vault"></a><a id="vault"></a>Key Vault を作成する
[New-AzureRmKeyVault](https://msdn.microsoft.com/library/azure/mt603736\(v=azure.300\).aspx) コマンドレットを使用して、キー コンテナーを作成します。 このコマンドレットには、3 つの必須パラメーター (**リソース グループ名**、**キー コンテナー名**、**地理的な場所**) が含まれています。

たとえば、コンテナー名に **ContosoKeyVault**、リソース グループ名に **ContosoResourceGroup**、場所に**東アジア**を使用する場合は、次のように入力します。

    New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'

このコマンドレットの出力は、作成されたばかりの Key Vault のプロパティを示します。 最も重要な 2 つのプロパティは、次のとおりです。

* **Vault Name**: この例では、これは **ContosoKeyVault** です。 この名前を他の Key Vault コマンドレットに使用できます。
* **Vault URI**: この例では、これは https://contosokeyvault.vault.azure.net/ です。 その REST API から資格情報コンテナーを使用するアプリケーションは、この URI を使用する必要があります。

Azure アカウントは、この Key Vault ですべての操作の実行が許可されるようになりました。 まだ、どのユーザーも許可されていません。

> [!NOTE]
> 新しいキー コンテナーを作成するときに "**サブスクリプションが名前空間 'Microsoft.KeyVault' を使用するように登録されていません**" というエラーが表示された場合は、`Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.KeyVault"` を実行した後に、New-AzureRmKeyVault コマンドを再実行します。 詳細については、「[Register-AzureRmResourceProvider](https://msdn.microsoft.com/library/azure/mt759831\(v=azure.300\).aspx)」を参照してください。
> 
> 

## <a name="a-idaddaadd-a-key-or-secret-to-the-key-vault"></a><a id="add"></a>キーやシークレットを Key Vault に追加する
Azure Key Vault でソフトウェアで保護されたキーを作成する場合は、[Add-AzureKeyVaultKey](https://msdn.microsoft.com/library/azure/dn868048\(v=azure.300\).aspx) コマンドレットを使用して次のように入力します。

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey' -Destination 'Software'

ただし、既存のソフトウェアで保護されたキーが C:\ ドライブの PFX ファイルに保存されていて、softkey.pfx という名前で Azure Key Vault にアップロードする場合は、次のように入力して、変数 **securepfxpwd** を .PFX ファイルのパスワード **123** に設定します。

    $securepfxpwd = ConvertTo-SecureString –String '123' –AsPlainText –Force

次のように入力して、.PFX ファイルからキーをインポートします。このキーは、Key Vault サービスのソフトウェアで保護されます。

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd


作成したキーや、Azure Key Vault にアップロードしたキーは、その URI を使用すると参照できます。 **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** を使用すると、常に最新のバージョンを取得できます。また、**https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** を使用すると、その特定のバージョンを取得できます。  

このキーの URI を表示するには、次のように入力します。

    $Key.key.kid

資格情報コンテナーにシークレットを追加する (SQLPassword という名前で値が Pa$$w0rd のパスワードを Azure Key Vault に設定する) には、次のように入力して、まず Pa$$w0rd 値をセキュリティで保護された文字列に変換します。

    $secretvalue = ConvertTo-SecureString 'Pa$$w0rd' -AsPlainText -Force

その後、次のように入力します。

    $secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword' -SecretValue $secretvalue

Azure Key Vault に追加したパスワードは、その URI を使用すると参照できます。 **https://ContosoVault.vault.azure.net/secrets/SQLPassword** を使用すると、常に最新のバージョンを取得できます。また、**https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** を使用すると、その特定のバージョンを取得できます。

このシークレットの URI を表示するには、次のように入力します。

    $secret.Id

作成したキーやシークレットを表示してみましょう。

* キーを表示するには、次のように入力します。`Get-AzureKeyVaultKey –VaultName 'ContosoKeyVault'`
* シークレットを表示するには、次のように入力します。`Get-AzureKeyVaultSecret –VaultName 'ContosoKeyVault'`

Key Vault とキーやシークレットは、アプリケーションを使用できる状態になりました。 これらを使用するには、アプリケーションを承認する必要があります。  

## <a name="a-idregisteraregister-an-application-with-azure-active-directory"></a><a id="register"></a>Azure Active Directory にアプリケーションを登録する
この手順は通常、開発者が別のコンピューター上で行います。 これは Azure Key Vault に固有のものではありませんが、完全を期すために説明します。

> [!IMPORTANT]
> チュートリアルを完了するには、この手順で登録するアカウント、資格情報コンテナー、アプリケーションがすべて同じ Azure ディレクトリに格納されている必要があります。
> 
> 

Key Vault を使用するアプリケーションは、Azure Active Directory から取得したトークンを使用して認証する必要があります。 これを行うには、アプリケーションの所有者は、まず Azure Active Directory でアプリケーションを登録する必要があります。 登録の最後に、アプリケーションの所有者は次の値を取得します。

* **アプリケーション ID** (クライアント ID とも呼ばれます) と**認証キー** (共有シークレットとも呼ばれます)。 アプリケーションは、トークンを取得するために、この 2 つの値を Azure Active Directory に示す必要があります。 これを行うようにアプリケーションを構成する方法は、アプリケーションによって異なります。 Key Vault のサンプル アプリケーション用に、アプリケーション所有者は app.config ファイルでこれらの値を設定します。

Azure Active Directory にアプリケーションを登録するには:

1. Azure クラシック ポータルにサインインします。
2. 左側で、 **[Active Directory]**をクリックし、アプリケーションを登録するディレクトリを選択します。 <br> <br> **注:** キー コンテナーを作成した Azure サブスクリプションが含まれている、同じディレクトリを選択する必要があります。 ディレクトリが不明な場合は、 **[設定]**をクリックし、キー コンテナーを作成したサブスクリプションを見つけて、最後の列に表示されているディレクトリ名をご確認ください。
3. **[アプリケーション]**をクリックします。 アプリがディレクトリに追加されていない場合は、このページには **[アプリケーションの追加]** リンクのみが表示されます。 リンクをクリックするか、コマンド バーの **[追加]** をクリックします。
4. **アプリケーションの追加**ウィザードの **[実行する作業を選択してください。]** ページで、**[組織で開発中のアプリケーションを追加]** をクリックします。
5. **[アプリケーション情報の指定]** ページで、アプリケーションの名前を指定し、**[Web アプリケーションや Web API]** (既定値) を選択します。 **次へ** アイコンをクリックします。
6. **[アプリのプロパティ]** ページで、**[サインオン URL]** と **[アプリケーション ID/URI]** を Web アプリケーションに指定します。 アプリケーションにこれらの値を設定していない場合は、この手順のための値を作成します (両方のボックスに http://test1.contoso.com を指定するなど)。 これらのサイトが存在するかどうかは関係ありません。 各アプリケーションのアプリケーション ID URI がディレクトリ内のすべてのアプリケーションで異なっていることが重要です。 ディレクトリは、この文字列を使用してアプリを識別します。
7. **完了** アイコンをクリックし、ウィザードで変更内容を保存します。
8. **[クイック スタート]** ページで、**[構成]** をクリックします。
9. **[キー]** セクションまでスクロールし、期間を選択して **[保存]** をクリックします。 ページが更新され、キーの値が表示されます。 このキーと**クライアント ID** の値を使用してアプリケーションを構成する必要があります  (この構成の手順はアプリケーション固有です)。
10. このページからクライアント ID 値をコピーします。この値は、資格情報コンテナーに権限を設定するために次の手順で使用します

## <a name="a-idauthorizeaauthorize-the-application-to-use-the-key-or-secret"></a><a id="authorize"></a>キーまたはシークレットを使用してアプリケーションを承認する
コンテナーのキーまたはシークレットへのアクセスをアプリケーションに承認するには、 [Set-AzureRmKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/mt603625\(v=azure.300\).aspx) コマンドレットを使用します。

たとえば、資格情報コンテナー名が **ContosoKeyVault** で、承認するアプリケーションのクライアント ID が 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed の場合、アプリケーションの暗号化を解除し、資格情報コンテナー内のキーで署名することを承認するには、次のように実行します。

    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign

その同じアプリケーションを認証し、資格情報コンテナーのシークレットの読み取りを許可する場合、次を実行します。

    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToSecrets Get

## <a name="a-idhsmaif-you-want-to-use-a-hardware-security-module-hsm"></a><a id="HSM"></a>ハードウェア セキュリティ モジュール (HSM) を使用する場合
さらに安心感を高めたい場合には、ハードウェア セキュリティ モジュール (HSM) でキーのインポートや生成を行うことができ、キーは HSM の境界内から出ることはありません。 HSM は、FIPS 140-2 レベル 2 で検証済みです。 この要件が自分に当てはまらない場合は、このセクションをスキップし、 [Key Vault と関連するキーとシークレットを削除する](#delete)に進んでください。

これらの HSM で保護されたキーを作成するには、[HSM で保護されたキーがサポートされている Azure Key Vault Premium サービス レベル](https://azure.microsoft.com/pricing/free-trial/)を使用する必要があります。 また、この機能は Azure China では使用できないことに注意してください。

キー コンテナーを作成するときに、**-SKU** パラメーターを追加します。

    New-AzureRmKeyVault -VaultName 'ContosoKeyVaultHSM' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -SKU 'Premium'



このキー コンテナーには、ソフトウェアで保護されたキー (前述のとおり) と HSM で保護されたキーを追加できます。 HSM で保護されたキーを作成するには、 **-Destination** パラメーターを 'HSM' に設定します。

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -Destination 'HSM'

次のコマンドを使用して、自分のコンピューターの PFX ファイルからキーをインポートできます。 このコマンドでは、Key Vault サービスでキーを HSM にインポートします。

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd -Destination 'HSM'


次のコマンドは、“bring your own key" (BYOK) パッケージをインポートします。 このシナリオを使用すると、ローカルの HSM でキーを生成し、これを Key Vault サービスで HSM に転送でき、キーは HSM の境界内から出ることはありません。

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\ITByok.byok' -Destination 'HSM'

この BYOK パッケージを生成する方法の詳細な手順については、「 [Azure Key Vault の HSM 保護キーを生成し、転送する方法](key-vault-hsm-protected-keys.md)」を参照してください。

## <a name="a-iddeleteadelete-the-key-vault-and-associated-keys-and-secrets"></a><a id="delete"></a>Key Vault と関連付けられているキーやシークレットを削除する
キー コンテナーと、それに含まれるキーまたはシークレットが不要になった場合は、[Remove-AzureRmKeyVault](https://msdn.microsoft.com/library/azure/mt619485\(v=azure.300\).aspx) コマンドレットを使用してキー コンテナーを削除できます。

    Remove-AzureRmKeyVault -VaultName 'ContosoKeyVault'

または、Key Vault やそのグループに含まれる他のすべてのリソースを含む、Azure リソース グループ全体を削除できます。

    Remove-AzureRmResourceGroup -ResourceGroupName 'ContosoResourceGroup'


## <a name="a-idotheraother-azure-powershell-cmdlets"></a><a id="other"></a>その他の Azure PowerShell コマンドレット
Azure Key Vault の管理に役立つその他のコマンドは次のとおりです。

* `$Keys = Get-AzureKeyVaultKey -VaultName 'ContosoKeyVault'`: このコマンドは、すべてのキーと選択したプロパティを表形式で取得します。
* `$Keys[0]`: このコマンドは、指定されたキーのプロパティの完全な一覧を表示します。
* `Get-AzureKeyVaultSecret`: このコマンドは、すべてのシークレットの名前と選択したプロパティを表形式で一覧します。
* `Remove-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey'`: 特定のキーを削除する方法の例です。
* `Remove-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword'`: 特定のシークレットを削除する方法の例です。

## <a name="a-idnextanext-steps"></a><a id="next"></a>次のステップ
Web アプリケーションでの Azure Key Vault の使用方法に関するフォローアップ チュートリアルについては、「 [Web アプリケーションからの Azure Key Vault の使用](key-vault-use-from-web-application.md)」をご覧ください。

Key Vault の使用方法については、「 [Azure Key Vault のログ記録](key-vault-logging.md)」を参照してください。

Azure Key Vault の Azure PowerShell コマンドレットの最新の一覧については、「[Azure Key Vault Cmdlets (Azure Key Vault コマンドレット)](https://msdn.microsoft.com/library/azure/dn868052\(v=azure.300\).aspx)」を参照してください。 

プログラミング リファレンスについては、「 [Azure Key Vault 開発者ガイド](key-vault-developers-guide.md)」を参照してください。




<!--HONumber=Dec16_HO2-->


