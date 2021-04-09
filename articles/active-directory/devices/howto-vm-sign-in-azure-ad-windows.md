---
title: Azure Active Directory (プレビュー) を使用して Azure 内の Windows 仮想マシンにサインインする
description: Windows を実行している Azure VM への Azure AD サインイン
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.custom: references_regions, devx-track-azurecli
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee392666a6c6807497eeac2a2291dac915c4e136
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101644308"
---
# <a name="sign-in-to-windows-virtual-machine-in-azure-using-azure-active-directory-authentication-preview"></a>Azure Active Directory 認証 (プレビュー) を使用して Azure 内の Windows 仮想マシンにサインインする

組織は、**Windows Server 2019 Datacenter エディション** または **Windows 10 1809** 以降を実行している Azure 仮想マシン (VM) に Azure Active Directory (AD) 認証を利用できるようになりました。 Azure AD を使用して VM を認証することにより、ポリシーを一元的に管理し、適用することができます。 Azure のロール ベースのアクセス制御 (Azure RBAC) や Azure AD 条件付きアクセスなどのツールを使用すると、VM にアクセスできるユーザーを制御することができます。 この記事では、Azure AD 認証を使用できるように Windows Server 2019 VM を作成して構成する方法について説明します。

> [!NOTE]
> Azure Windows VM への Azure AD サインインは、Azure Active Directory のパブリック プレビュー機能です。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

Azure AD 認証を使用して、Azure 内の Windows VM にログインすると、次のような数多くのメリットがあります。

- 通常と同様、統合または管理された Azure AD 資格情報を使用できます。
- ローカル管理者アカウントを管理する必要がなくなります。
- Azure RBAC を使用すると、VM への適切なアクセス権を必要に応じて付与し、不要になったら削除することができます。
- VM へのアクセスを許可する前に、Azure AD 条件付きアクセスにより、次のような追加要件を適用できます。 
   - 多要素認証
   - サインイン リスク チェック
- VDI のデプロイの一部である Azure Windows VM の Azure AD 参加の自動化とスケーリングを行います。

> [!NOTE]
> この機能を有効にすると、Azure の Windows VM が Azure AD に参加することになります。 オンプレミス AD や Azure AD DS などの他のドメインに参加させることはできません。 この操作が必要な場合は、拡張機能をアンインストールして、Azure AD テナントから VM を切断する必要があります。

## <a name="requirements"></a>必要条件

### <a name="supported-azure-regions-and-windows-distributions"></a>サポートされる Azure リージョンと Windows ディストリビューション

この機能のプレビュー期間中は、次の Windows ディストリビューションがサポートされます。

- Windows Server 2019 Datacenter
- Windows 10 1809 以降

> [!IMPORTANT]
> Azure AD 参加済みの VM にリモート接続できるのは、VM として **同じ** ディレクトリに対して Azure AD 登録済み (Windows 10 20H1 より)、Azure AD 参加済み、またはハイブリッド Azure AD 参加済みの Windows 10 PC からのみです。 

この機能のプレビュー期間中は、次の Azure リージョンがサポートされます。

- すべての Azure グローバル リージョン

> [!IMPORTANT]
> このプレビュー機能を使用するには、サポートされる Windows ディストリビューションおよびサポートされる Azure リージョンのみにデプロイしてください。 この機能は、Azure Government やソブリン クラウドではサポートされていません。

### <a name="network-requirements"></a>ネットワークの要件

Azure 内の Windows VM に対して Azure AD 認証を有効にするには、VM のネットワーク構成で、TCP ポート 443 を経由した次のエンドポイントへの発信アクセスが確実に許可されているようにする必要があります。

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- `https://pas.windows.net`

## <a name="enabling-azure-ad-login-in-for-windows-vm-in-azure"></a>Azure 内の Windows VM に対して Azure AD ログインを有効にする

Azure 内の Windows VM に Azure AD ログインを使用するには、最初に Windows VM に対して Azure AD ログイン オプションを有効にする必要があります。その後、VM にログインする権限を持つユーザーの Azure ロールの割り当てを構成する必要があります。
Windows VM に対して Azure AD ログインを有効にするには、次のような複数の方法があります。

- Windows VM の作成時に Azure portal のエクスペリエンスを使用する
- Windows VM の作成時に、**または既存の Windows VM に対して**、Azure Cloud Shell エクスペリエンスを使用する

### <a name="using-azure-portal-create-vm-experience-to-enable-azure-ad-login"></a>Azure portal の VM の作成エクスペリエンスを使用して Azure AD ログインを有効にする

Windows Server 2019 Datacenter または Windows 10 1809 以降の VM イメージに対して Azure AD ログインを有効にすることができます。 

Azure AD ログオンを使用して、Azure で Windows Server 2019 Datacenter VM を作成するには、次の手順を行います。 

1. VM を作成するためのアクセス権を持つアカウントを使用して [Azure portal](https://portal.azure.com) にサインし、 **[+ リソースの作成]** を選択します。
1. [マーケットプレースを検索] 検索バーに「**Windows Server**」と入力します。
   1. **[Windows Server]** をクリックして、[ソフトウェア プランの選択] ドロップダウンから **[Windows Server 2019 Datacenter]** を選択します。
   1. **[作成]** をクリックします。
1. [管理] タブの [Azure Active Directory] セクションで **[AAD 資格情報を使用してログインする (プレビュー)]** オプションを [オフ] から **[オン]** に変更して有効にします。
1. [ID] セクションの **[システム割り当てマネージド ID]** が **[オン]** に設定されていることを確認します。 [AAD 資格情報を使用してログインする (プレビュー)] を有効にすると、この操作は自動的に行われます。
1. 仮想マシンの作成エクスペリエンスの残りの部分に移動します。 このプレビューでは、VM の管理者ユーザー名とパスワードを作成する必要があります。

![[仮想マシンの作成] の [AAD 資格情報を使用してログインする (プレビュー)]](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-login-with-azure-ad.png)

> [!NOTE]
> Azure AD 資格情報を使用して VM にログインするには、最初に、以下のセクションのいずれかの説明に従って VM のロールの割り当てを構成する必要があります。

### <a name="using-the-azure-cloud-shell-experience-to-enable-azure-ad-login"></a>Azure Cloud Shell のエクスペリエンスを使用して Azure AD ログインを有効にする

Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 Cloud Shell には一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 [コピー] ボタンを選択してコードをコピーし、Cloud Shell に貼り付けて Enter キーを押すだけで、コードを実行することができます。 Cloud Shell は、次のようにいくつかの方法で開くことができます。

- コード ブロックの右上隅にある **[使ってみる]** を選択します。
- ブラウザーで Cloud Shell を開きます。
- [Azure portal](https://portal.azure.com) の右上隅にあるメニューの [Cloud Shell] ボタンを選択します。

CLI をローカルにインストールして使用する場合、この記事では、Azure CLI バージョン 2.0.31 以降を実行していることが要件となります。 バージョンを確認するには、az --version を実行します。 インストールまたはアップグレードする必要がある場合は、記事「[Azure CLI のインストール](/cli/azure/install-azure-cli)」を参照してください。

1. [az group create](/cli/azure/group#az-group-create) を使用して、リソース グループを作成します。 
1. サポートされるリージョンのサポートされるディストリビューションを使用して、[az vm create](/cli/azure/vm#az-vm-create)で VM を作成します。 
1. Azure AD ログイン VM 拡張機能をインストールします。 

次の例では、southcentralus リージョンの myResourceGroup という名前のリソース グループで Win2019Datacenter を使用する myVM という名前の VM をデプロイします。 次の例では、必要に応じて、任意のリソース グループと VM 名を指定できます。

```AzureCLI
az group create --name myResourceGroup --location southcentralus

az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Win2019Datacenter \
    --assign-identity \
    --admin-username azureuser \
    --admin-password yourpassword
```

> [!NOTE]
> Azure AD ログイン VM 拡張機能をインストールするには、仮想マシンのシステム割り当てマネージド ID を有効にする必要があります。

VM とサポートするリソースを作成するには数分かかります。

最後に、Azure AD ログイン VM 拡張機能をインストールし、Windows VM に対して Azure AD ログインを有効にします。 VM 拡張機能は、Azure 仮想マシンでのデプロイ後の構成と自動タスクを提供する小さなアプリケーションです。 [az vm extension](/cli/azure/vm/extension#az-vm-extension-set) セットを使用して、`myResourceGroup` リソース グループの `myVM` という名前の VM に AADLoginForWindows 拡張機能をインストールします。

> [!NOTE]
> 既存の Windows Server 2019 または Windows 10 1809 以降の VM に AADLoginForWindows 拡張機能をインストールし、これを Azure AD 認証用に有効にすることができます。 AZ CLI の例を次に示します。

```AzureCLI
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory \
    --name AADLoginForWindows \
    --resource-group myResourceGroup \
    --vm-name myVM
```

この拡張機能が VM にインストールされると、`Succeeded` の `provisioningState` が表示されます。

## <a name="configure-role-assignments-for-the-vm"></a>仮想マシン ロールの割り当てを構成する

VM を作成したので、VM にログインできるユーザーを決定する Azure RBAC ポリシーを構成する必要があります。 VM へのログインを承認するには、次の 2 つの Azure ロールが使用されます。

- **仮想マシンの管理者ログイン**:このロールを割り当てられたユーザーは、管理者特権を持つユーザーとして Azure 仮想マシンにログインできます。
- **仮想マシンのユーザー ログイン**:このロールが割り当てられたユーザーは正規ユーザーの権限を持つユーザーとして Azure 仮想マシンにログインできます。

> [!NOTE]
> ユーザーが RDP 経由で VM にログインできるようにするには、仮想マシンの管理者ログインまたは仮想マシンのユーザー ログインのいずれかのロールを割り当てる必要があります。 VM の所有者ロールまたは共同作成者ロールが割り当てられた Azure ユーザーに対して、RDP 経由で VM にログインする権限は自動的には付与されません。 これは、仮想マシンを管理するユーザーと仮想マシンにアクセスできるユーザーを監査上分離するためです。

VM のロールの割り当てを構成するには、次のような複数の方法があります。

- Azure AD ポータルのエクスペリエンスを使用する
- Azure Cloud Shell のエクスペリエンスを使用する

> [!NOTE]
> 仮想マシンの管理者ログインと仮想マシンのユーザー ログインのロールは、dataActions を使用しているため、管理グループのスコープで割り当てることはできません。 現時点では、これらのロールは、サブスクリプション、リソース グループまたはリソース スコープでのみ割り当てることができます。

### <a name="using-azure-ad-portal-experience"></a>Azure AD ポータルのエクスペリエンスを使用する

Azure AD を有効にした Windows Server 2019 Datacenter VM のロールの割り当てを構成するには、次の操作を行います。

1. 特定の仮想マシンの [概要] ページに移動します。
1. メニュー オプションから **[アクセス制御 (IAM)]** を選択します。
1. **[追加]** 、 **[ロールの割り当ての追加]** の順に選択して、[ロールの割り当ての追加] ペインを開きます。
1. **[ロール]** ドロップダウン リストで、 **[仮想マシンの管理者ログイン]** や **[仮想マシンのユーザー ログイン]** などのロールを選択します。
1. **[選択]** フィールドで、ユーザー、グループ、サービス プリンシパル、またはマネージド ID を選択します。 [選択] 一覧で、ユーザー、グループ、サービス プリンシパル、またはマネージド ID を選択します。 一覧にセキュリティ プリンシパルが表示されない場合には、 **[選択]**  ボックスに表示名、メール アドレス、オブジェクト識別子を入力してディレクトリを検索します。
1. **[保存]** を選択して、ロールを割り当てます。

しばらくすると、セキュリティ プリンシパルに選択されたスコープのロールが割り当てられます。

![VM にアクセスするユーザーにロールを割り当てる](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-access-control-assign-role.png)

### <a name="using-the-azure-cloud-shell-experience"></a>Azure Cloud Shell のエクスペリエンスを使用する

次の例では、[az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) を使用し、現在の Azure ユーザーに対して、VM に対する仮想マシンの管理者ログイン ロールを割り当てます。 アクティブな Azure アカウントのユーザー名は [az account show](/cli/azure/account#az-account-show) で取得され、スコープは、[az vm show](/cli/azure/vm#az-vm-show) により、前の手順で作成された VM に設定されます。 スコープは、リソース グループまたはサブスクリプション レベルで割り当てることもでき、Azure RBAC における通常の継承アクセス許可が適用されます。 詳細については、[Azure Active Directory 認証を使用した Azure の Linux 仮想マシンへのログイ](../../virtual-machines/linux/login-using-aad.md)に関するページを参照してください。

```   AzureCLI
$username=$(az account show --query user.name --output tsv)
$vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> AAD ドメインとログオン ユーザー名ドメインが一致しない場合は、`--assignee` のユーザー名だけでなく、`--assignee-object-id` を使用してユーザー アカウントのオブジェクト ID を指定する必要があります。 ユーザー アカウントのオブジェクト ID は、[az ad user list](/cli/azure/ad/user#az-ad-user-list)を使用して取得できます。

Azure RBAC を使用して、Azure サブスクリプション リソースへのアクセスを管理する方法の詳細については、次の記事を参照してください。

- [Azure CLI を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-cli.md)
- [Azure portal を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-portal.md)
- [Azure PowerShell を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-powershell.md)。

## <a name="using-conditional-access"></a>条件付きアクセスの使用

Azure AD サインインで有効になる Azure 上の Windows VM へのアクセスを承認する前に、多要素認証やユーザー サインイン リスク チェックなどの条件付きアクセス ポリシーを適用できます。 条件付きアクセス ポリシーを適用するには、クラウド アプリまたはアクションの割り当てオプションから "Azure Windows VM サインイン" アプリを選択し、条件としてサインイン リスクを使用するか、アクセス制御付与として多要素認証を要求する、あるいはその両方を実行します。 

> [!NOTE]
> "Azure Windows VM サインイン" アプリへのアクセス要求に対して、"Azure Windows VM サインイン" をアクセス制御付与として使用する場合は、Azure上のターゲット Windows VM に対して RDP セッションを開始するクライアントの一部として、多要素認証要求を提供する必要があります。 Windows 10 クライアントでこれを実現する唯一の方法は、RDP クライアントで Windows Hello for Business の PIN または生体認証を使用することです。 生体認証のサポートは、Windows 10 バージョン 1809 で RDP クライアントに追加されています。 Windows Hello for Business 認証を使用するリモート デスクトップは、証明書信頼モデルを使用するデプロイでのみ利用でき、現時点ではキー信頼モデルでは利用できません。

> [!WARNING]
> ユーザー単位で有効化または適用された Azure AD Multi-Factor Authentication は、VM のサインインではサポートされていません。

## <a name="log-in-using-azure-ad-credentials-to-a-windows-vm"></a>Azure AD 資格情報を使用して Windows VM にログインする

> [!IMPORTANT]
> Azure AD 参加済みの VM にリモート接続できるのは、VM として **同じ** ディレクトリに対して Azure AD 登録済み (最低限必要なビルドは 20H1)、Azure AD 参加済み、またはハイブリッド Azure AD 参加済みの Windows 10 PC からのみです。 さらに、Azure AD 資格情報を使用して RDP 接続するには、ユーザーは 2 つの Azure ロールのいずれか (仮想マシンの管理者ログイン、または仮想マシンのユーザー ログイン) に属している必要があります。 Azure AD 登録済みの Windows 10 PC を使用している場合は、資格情報を `AzureAD\UPN` の形式で入力する必要があります (例: `AzureAD\john@contoso.com`)。 現在、AADLoginForWindows 拡張機能で Azure Active Directory 認証を使用してログインするために Azure Bastion を使用することはできません。直接 RDP のみサポートされています。

Azure AD を使用して Windows Server 2019 仮想マシンにログインするには、次の操作を行います。 

1. Azure AD ログオンが有効になっている仮想マシンの [概要] ページに移動します。
1. **[接続]** を選択して、[仮想マシンに接続する] ブレードを開きます。
1. **[RDP ファイルのダウンロード]** を選択します。
1. **[開く]** を選択して、リモート デスクトップ接続クライアントを起動します。
1. **[接続]** を選択して、Windows ログオン ダイアログを起動します。
1. Azure AD 資格情報を使用してログオンします。

以上で、割り当てられたロール (VM ユーザーまたは VM 管理者など) のアクセス許可を持つユーザーとして、Windows Server 2019 Azure 仮想マシンにサインインしました。 

> [!NOTE]
> .RDP ファイルを自分のコンピューターにローカルで保存できます。これにより、後でリモート デスクトップ接続を起動する際に、Azure portal で仮想マシンの [概要] ページに移動して、接続オプションを使用する必要がなくなります。

## <a name="troubleshoot"></a>トラブルシューティング

### <a name="troubleshoot-deployment-issues"></a>デプロイに関する問題のトラブルシューティング

VM が Azure AD 参加プロセスを完了するには、AADLoginForWindows 拡張機能が正常にインストールされている必要があります。 VM 拡張機能を正常にインストールできない場合は、次の手順を行います。

1. ローカル管理者アカウントを使用して VM に RDP 接続し、以下にある `CommandExecution.log` ファイルを調べます。
   
   `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.ActiveDirectory.AADLoginForWindows\0.3.1.0.`

   > [!NOTE]
   > 最初の失敗後に拡張機能を再起動すると、デプロイ エラーを含むログが、`CommandExecution_YYYYMMDDHHMMSSSSS.log` として保存されます。 "
1. VM で PowerShell コマンド プロンプトを開き、Azure ホストで実行されている Instance Metadata Service (IMDS) エンドポイントに対して次のクエリを実行し、返される結果を確認します。

   | 実行するコマンド | 想定される出力 |
   | --- | --- |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/instance?api-version=2017-08-01"` | VM に関する正しい情報 |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/info?api-version=2018-02-01"` | Azure サブスクリプションに関連付けられている有効なテナント ID |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/oauth2/token?resource=urn:ms-drs:enterpriseregistration.windows.net&api-version=2018-02-01"` | Azure Active Directory によって発行された、この VM に割り当てられているマネージド ID の有効なアクセス トークン |

   > [!NOTE]
   > アクセス トークンは、[calebb.net](http://calebb.net/) などのツールを使用してデコードできます。 アクセス トークンの `appid` と VM に割り当てられたマネージド ID が一致していることを確認します。

1. 次のコマンド ラインを使用して、必要なエンドポイントに VM から確実にアクセスできるようにします。
   
   - `curl https://login.microsoftonline.com/ -D -`
   - `curl https://login.microsoftonline.com/<TenantID>/ -D -`

   > [!NOTE]
   > `<TenantID>` を、Azure サブスクリプションに関連付けられている Azure AD テナント ID に置き換えます。

   - `curl https://enterpriseregistration.windows.net/ -D -`
   - `curl https://device.login.microsoftonline.com/ -D -`
   - `curl https://pas.windows.net/ -D -`

1. Device State (デバイスの状態) を表示するには、`dsregcmd /status` を実行します。 目標は、Device State (デバイスの状態) で `AzureAdJoined : YES` と表示されることです。

   > [!NOTE]
   > Azure AD 参加アクティビティは、イベント ビューアーの `User Device Registration\Admin` ログの下にキャプチャされます。

AADLoginForWindows 拡張機能が特定のエラー コードで失敗した場合、次の手順を行うことができます。

#### <a name="issue-1-aadloginforwindows-extension-fails-to-install-with-terminal-error-code-1007-and-exit-code--2145648574"></a>問題 1: AADLoginForWindows 拡張機能のインストールが、ターミナル エラー コード '1007' と終了コード -2145648574 で失敗しました。

拡張機能により Azure AD テナント情報が照会されないため、この終了コードは `DSREG_E_MSI_TENANTID_UNAVAILABLE` に変換されます。

1. Azure VM により Instance Metadata Service から TenantID を取得できることを確認します。

   - ローカル管理者として VM に RDP 接続し、VM で管理者特権でのコマンド ラインから次のコマンドを実行して、エンドポイントから有効なテナント ID が返されることを確認します。
      
      - `curl -H Metadata:true http://169.254.169.254/metadata/identity/info?api-version=2018-02-01`

1. VM 管理者は AADLoginForWindows 拡張機能のインストールを試みましたが、システム割り当てマネージド ID があらかじめ有効にされていません。 VM の [ID] ブレードに移動します。 [システム割り当て済み] タブで、[状態] が [オン] に切り替えられていることを確認します。

#### <a name="issue-2-aadloginforwindows-extension-fails-to-install-with-exit-code--2145648607"></a>問題 2:AADLoginForWindows 拡張機能のインストールは、終了コード -2145648607 で失敗しました。

拡張機能は `https://enterpriseregistration.windows.net` エンドポイントに到達できないため、この終了コードは `DSREG_AUTOJOIN_DISC_FAILED` に変換されます。

1. 次のコマンド ラインを使用して、必要なエンドポイントに VM からアクセスできることを確認します。

   - `curl https://login.microsoftonline.com/ -D -`
   - `curl https://login.microsoftonline.com/<TenantID>/ -D -`
   
   > [!NOTE]
   > `<TenantID>` を、Azure サブスクリプションに関連付けられている Azure AD テナント ID に置き換えます。 テナント ID を検索する必要がある場合、アカウント名にマウス カーソルを合わせてディレクトリとテナント ID を取得するか、Azure portal で **[Azure Active Directory]、[プロパティ]、[ディレクトリ ID]** の順に選択します。

   - `curl https://enterpriseregistration.windows.net/ -D -`
   - `curl https://device.login.microsoftonline.com/ -D -`
   - `curl https://pas.windows.net/ -D -`

1. コマンドが "ホスト名 `<URL>` を解決できません" で失敗した場合、このコマンドを実行して、VM で使用されている DNS サーバーを特定してみてください。
   
   `nslookup <URL>`

   > [!NOTE] 
   > `<URL>` を、エンドポイントで使用される完全修飾ドメイン名 (`login.microsoftonline.com` など) に置き換えます。

1. 次に、パブリック DNS サーバーを指定してコマンドを正常に実行できるかどうかを確認します。

   `nslookup <URL> 208.67.222.222`

1. 必要に応じて、Azure VM が属するネットワーク セキュリティ グループに割り当てられている DNS サーバーを変更します。

#### <a name="issue-3-aadloginforwindows-extension-fails-to-install-with-exit-code-51"></a>問題 3:AADLoginForWindows 拡張機能のインストールは、次により失敗しました。終了コード:51

終了コード 51 は、"VM のオペレーティング システムでは、この拡張機能はサポートされていません" に変換されます。

パブリック プレビューでは、AADLoginForWindows 拡張機能は、Windows Server 2019 または Windows 10 (ビルド 1809 以降) にのみインストールものです。 Windows が確実にサポートされているバージョンであるようにします。 Windows のビルドがサポートされていない場合は、VM 拡張機能をアンインストールします。

### <a name="troubleshoot-sign-in-issues"></a>サインアップに関する問題のトラブルシューティング

Azure AD 資格情報を使用して RDP 接続しようとしたときに発生する可能性のある一般的なエラーとしては、Azure ロールが割り当てられていない、クライアントが承認されていない、サインイン方法として 2FA (2 要素認証) が必要、などがあります。 次の情報を使用して、これらの問題を修正してください。

Device State (デバイスの状態) と SSO State (SSO 状態) を表示するには、`dsregcmd /status`を実行します。 目標は、Device State (デバイスの状態) で `AzureAdJoined : YES` と表示され、`SSO State`で `AzureAdPrt : YES`と表示されることです。

また、Azure AD アカウントを使用した RDP サインインは、イベント ビューアーの `AAD\Operational` イベント ログ下にキャプチャされます。

#### <a name="azure-role-not-assigned"></a>Azure ロールが割り当てられていない

VM へのリモート デスクトップ接続を開始したときに次のエラー メッセージが表示された場合、 

- このデバイスを使用できないようにアカウントが構成されています。 詳細については、システム管理者にお問い合わせください。

![このデバイスを使用できないようにアカウントが構成されています。](./media/howto-vm-sign-in-azure-ad-windows/rbac-role-not-assigned.png)

VM に対して、仮想マシンの管理者ログインまたは仮想マシンのユーザー ログインのどちらかのロールをユーザーに付与する [Azure RBAC ポリシーが構成されている](../../virtual-machines/linux/login-using-aad.md)ことを確認してください。

> [!NOTE]
> Azure のロールの割り当てに関する問題が発生した場合は、「[Azure RBAC のトラブルシューティング](../../role-based-access-control/troubleshooting.md#azure-role-assignments-limit)」を参照してください。
 
#### <a name="unauthorized-client"></a>承認されていないクライアント

VM へのリモート デスクトップ接続を開始したときに次のエラー メッセージが表示された場合、 

- 資格情報が正しくありません。

![お使いの資格情報は機能しませんでした](./media/howto-vm-sign-in-azure-ad-windows/your-credentials-did-not-work.png)

リモート デスクトップ接続を開始するために使用している Windows 10 PC が、VM が参加している Azure AD ディレクトリと同じディレクトリに Azure AD 参加済みまたはハイブリッド Azure AD 参加済みのいずれかであることを確認します。 デバイスID の詳細については、「[デバイス ID とは](./overview.md)」の記事を参照してください。

> [!NOTE]
> Windows 10 ビルド 20H1 では、VM への RDP 接続を開始するために、Azure AD 登録済み PC のサポートが追加されました。 Azure AD 登録済み (Azure AD 参加済みまたはハイブリッド Azure AD 参加済みではない) PC を RDP クライアントとして使用して VM への接続を開始する場合は、`AzureAD\UPN` の形式 (例: `AzureAD\john@contoso.com`) で資格情報を入力する必要があります。

Azure AD への参加が完了した後に AADLoginForWindows 拡張機能がアンインストールされていないことを確認します。

また、サーバーとクライアントの **両方** で、セキュリティ ポリシー "Network security: Allow PKU2U authentication requests to this computer to use online identities" (ネットワーク セキュリティ: このコンピューターに対する PKU2U 認証要求でオンライン ID を使用することを許可する) が有効になっていることを確認します。
 
#### <a name="mfa-sign-in-method-required"></a>MFA サインイン方法が必要

VM へのリモート デスクトップ接続を開始したときに次のエラー メッセージが表示された場合、 

- The sign-in method you're trying to use isn't allowed. (使用しようとしているサインイン方法は許可されていません。) Try a different sign-in method or contact your system administrator. (別のサインイン方法を使用するか、システム管理者に問い合わせてください。)

![The sign-in method you're trying to use isn't allowed. (使用しようとしているサインイン方法は許可されていません。)](./media/howto-vm-sign-in-azure-ad-windows/mfa-sign-in-method-required.png)

リソースにアクセスする前に多要素認証 (MFA) を要求する条件付きアクセス ポリシーを構成している場合は、VM へのリモート デスクトップ接続を開始する Windows 10 PC で、Windows Hello などの強力な認証方法を使用したサインインが行われるようにする必要があります。 リモート デスクトップ接続で強力な認証方法が使用されていない場合は、前述のエラーが表示されます。

Windows Hello for Business のデプロイがなく、それが当面は選択肢にならない場合は、MFA を要求するクラウド アプリの一覧から "Azure Windows VM サインイン" アプリを除外する条件付きアクセス ポリシーを構成することで、MFA 要件を除外できます。 Windows Hello for Business の詳細については、[Windows Hello for Business の概要](/windows/security/identity-protection/hello-for-business/hello-identity-verification)に関するページを参照してください。

> [!NOTE]
> RDP での Windows Hello for Business PIN 認証は Windows 10 のいくつかのバージョンでサポートされています。ただし、RDP での生体認証のサポートは Windows 10 バージョン 1809 で追加されています。 RDP での Windows Hello for Business 認証の使用は、証明書信頼モデルを使用するデプロイでのみ利用できます。現時点ではキー信頼モデルでは利用できません。
 
## <a name="preview-feedback"></a>プレビューのフィードバック

[Azure AD フィードバック フォーラム](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)で、このプレビュー機能に関するフィードバックを共有するか、プレビュー機能の使用に関する問題を報告してください。

## <a name="next-steps"></a>次のステップ

Azure Active Directory の詳細については、「[Azure Active Directory とは](../fundamentals/active-directory-whatis.md)」を参照してください。
