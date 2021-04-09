---
title: Azure Active Directory の資格情報を使用して Linux VM にログインする
description: Azure Active Directory 認証を使用してサインインするために、Linux VM を作成して構成する方法について説明します。
author: SanDeo-MSFT
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure
ms.date: 11/17/2020
ms.author: sandeo
ms.openlocfilehash: 44dfd07a5b749d88552bb1dcac2ee4b4e5ce65e4
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102565242"
---
# <a name="preview-log-in-to-a-linux-virtual-machine-in-azure-using-azure-active-directory-authentication"></a>プレビュー:Azure Active Directory 認証を使用して Azure の Linux 仮想マシンにログインする

Azure の Linux 仮想マシン (VM) のセキュリティを強化するために、Azure Active Directory (AD) の認証と統合できます。 Linux VM に Azure AD の認証を使用することで、VM へのアクセスを許可/拒否するポリシーを一元管理および施行します。 この記事では、Azure AD 認証を使用して Linux VM を作成し、構成する方法について説明します。


> [!IMPORTANT]
> Azure Active Directory 認証は、現在、パブリック プレビューの段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。
> テスト後に破棄する予定のテスト用仮想マシンでこの機能を使用してください。
>


Azure AD の認証を使用して、Azure Linux VM にログインすると、次のような多くのメリットがあります。

- **セキュリティの強化:**
  - お客様の会社の AD 資格情報を使用して、Azure Linux VM にログインできます。 ローカル管理者アカウントを作成したり、資格情報の有効期間を管理する必要がなくなります。
  - ローカル管理者アカウントへの依存度を低減することで、資格情報の損失/漏洩や、セキュリティ性の弱い資格情報をユーザーが設定することを憂慮する必要がなくなります。
  - Azure AD ディレクトリ用に設定されたパスワードの複雑性と、パスワードの有効期間ポリシーを使用して、Linux VM もセキュリティ保護できます。
  - Azure 仮想マシンへのログイン セキュリティをさらに強化するために、多要素認証を設定することができます。
  - Azure Active Directory を使用して Linux VM にログインする機能は、[フェデレーション サービス](../../active-directory/hybrid/how-to-connect-fed-whatis.md)を使用するお客様も使用できます。

- **シームレスな コラボレーション:** Azure ロールベースのアクセス制御 (Azure RBAC) を使用することで、どのユーザーが、正規のユーザーまたは管理者権限を持つユーザーとして特定の VM にサインインできるかを指定できます。 ユーザーがチームに参加またはチームから脱退する場合は、適切なアクセス権が付与されるよう VM の Azure RBAC ポリシーを更新できます。 この操作は、不要な SSH 公開キーを削除して VM をスクラブするよりも簡単です。 従業員が退職し、そのユーザー アカウントが無効化または Azure AD から削除されると、リソースにアクセスできなくなります。

## <a name="supported-azure-regions-and-linux-distributions"></a>サポートされている Azure リージョンと Linux ディストリビューション

この機能のプレビュー期間中は、次の Linux ディストリビューションがサポートされます。

| Distribution | Version |
| --- | --- |
| CentOS | CentOS 6、CentOS 7 |
| Debian | Debian 9 |
| openSUSE | openSUSE Leap 42.3 |
| RedHat Enterprise Linux | RHEL 6、RHEL 7 | 
| SUSE Linux Enterprise Server | SLES 12 |
| Ubuntu Server | Ubuntu 14.04 LTS、Ubuntu Server 16.04、Ubuntu Server 18.04 |


この機能のプレビュー期間中は、次の Azure リージョンがサポートされます。

- すべてのグローバル Azure リージョン

>[!IMPORTANT]
> このプレビュー機能を使用するには、サポートされている Linux ディストリビューションおよびサポートされている Azure リージョンのみに展開してください。 この機能は、Azure Government やソブリン クラウドではサポートされていません。
>
> Azure Kubernetes Service (AKS) クラスターでは、この拡張機能の使用はサポートされていません。 詳細については、[AKS のポリシーのサポート](../../aks/support-policies.md)に関するページを参照してください。


CLI をローカルにインストールして使用する場合、Azure CLI バージョン 2.0.31 以降を実行していることがこのチュートリアルの要件になります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="network-requirements"></a>ネットワークの要件

Azure 内の Linux VM に対して Azure AD 認証を有効にするには、VM のネットワーク構成で、TCP ポート 443 を経由した次のエンドポイントへの発信アクセスが確実に許可されているようにする必要があります。

* https:\//login.microsoftonline.com
* https:\//login.windows.net
* https:\//device.login.microsoftonline.com
* https:\//pas.windows.net
* https:\//management.azure.com
* https:\//packages.microsoft.com

> [!NOTE]
> 現時点では、Azure AD 認証が有効になっている VM に対して Azure ネットワーク セキュリティ グループを構成することはできません。

## <a name="create-a-linux-virtual-machine"></a>Linux 仮想マシンの作成

[az group create](/cli/azure/group#az-group-create) を使用してリソース グループを作成し、サポートされているディストリビューションとサポートされているリージョンで [az vm create](/cli/azure/vm#az-vm-create) を使用して VM を作成します。 次の例では、*southcentralus* リージョンの *myResourceGroup* リソース グループで *Ubuntu 16.04 LTS* を使用する *myVM* という名前の VM を展開します。 次の例では、必要に応じて、任意のリソース グループと VM 名を指定できます。

```azurecli-interactive
az group create --name myResourceGroup --location southcentralus

az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

VM とサポートするリソースを作成するには数分かかります。

## <a name="install-the-azure-ad-login-vm-extension"></a>Azure AD ログイン VM 拡張機能をインストールする

> [!NOTE]
> この拡張機能を、以前に作成した VM にデプロイする場合は、マシンに少なくとも 1 GB のメモリが割り当てられていることを確認してください。そうでない場合、拡張機能のインストールは失敗します

Azure AD 資格情報を使用して Linux VM にログインするには、VM 拡張機能に Azure Active Directory ログインをインストールします。 VM 拡張機能は、Azure 仮想マシンでのデプロイ後の構成と自動タスクを提供する小さなアプリケーションです。 [az vm extension set](/cli/azure/vm/extension#az-vm-extension-set) を使用して、*myResourceGroup* リソース グループの *myVM* という名前の VM に *AADLoginForLinux* 拡張機能をインストールします。

```azurecli-interactive
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory.LinuxSSH \
    --name AADLoginForLinux \
    --resource-group myResourceGroup \
    --vm-name myVM
```

VM に拡張機能が正常にインストールされると、*provisioningState* に *Succeeded* が表示されます。 拡張機能をインストールするには、VM には実行中の VM エージェントが必要です。 詳細については、[VM エージェントの概要](../extensions/agent-windows.md)に関する記事をご覧ください。

## <a name="configure-role-assignments-for-the-vm"></a>仮想マシン ロールの割り当てを構成する

Azure のロールベース アクセス制御 (Azure RBAC) ポリシーは、VM にログオンできるユーザーを規定します。 VM へのログインを承認するには、次の 2 つの Azure ロールが使用されます。

- **仮想マシンの管理者ログイン**:このロールが割り当てられたユーザーは Windows 管理者または Linux のルート ユーザーの権限を持つユーザーとして Azure 仮想マシンにログインできます。
- **仮想マシンのユーザー ログイン**:このロールが割り当てられたユーザーは正規ユーザーの権限を持つユーザーとして Azure 仮想マシンにログインできます。

> [!NOTE]
> SSH 経由でユーザーが VM にログインできるようにするには、*仮想マシンの管理者ログイン* または *仮想マシンのユーザー ログイン* ロールのいずれかを割り当てる必要があります。 仮想マシンの管理者ログインと仮想マシンのユーザー ログインのロールは、dataActions を使用しているため、管理グループのスコープで割り当てることはできません。 現時点では、これらのロールは、サブスクリプション、リソース グループまたはリソース スコープでのみ割り当てることができます。 VM の *所有者* または *共同作成者* ロールが割り当てられた Azure ユーザーに、SSH 経由で VM にログインする権限は自動的には付与されません。 

次の例では、[az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) を使用して、VM に対する *仮想マシンの管理者ログイン* ロールを現在の Azure ユーザーに割り当てます。 [az account show](/cli/azure/account#az-account-show) を使用して、アクティブな Azure アカウントのユーザー名を取得し、[az vm show](/cli/azure/vm#az-vm-show) を使用して前の手順で作成された VM に *スコープ* が設定されています。 スコープは、リソース グループまたはサブスクリプション レベルで割り当てることもでき、Azure RBAC における通常の継承アクセス許可が適用されます。 詳細については、[Azure RBAC](../../role-based-access-control/overview.md) に関するページを参照してください

```azurecli-interactive
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> AAD ドメインとログオン ユーザー名ドメインが一致しない場合は、 *--assignee* のユーザー名だけでなく、 *--assignee-object-id* を使用してユーザー アカウントのオブジェクト ID を指定する必要があります。 ユーザー アカウントのオブジェクト ID は、[az ad user list](/cli/azure/ad/user#az-ad-user-list)を使用して取得できます。

Azure RBAC を使用して、Azure サブスクリプション リソースへのアクセスを管理する方法の詳細については、[Azure CLI](../../role-based-access-control/role-assignments-cli.md)、[Azure portal](../../role-based-access-control/role-assignments-portal.md)、または [Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md) の使用に関するページを参照してください。

## <a name="using-conditional-access"></a>条件付きアクセスの使用

Azure AD サインインで有効になる Azure 上の Linux VM へのアクセスを承認する前に、多要素認証やユーザー サインイン リスク チェックなどの条件付きアクセス ポリシーを適用できます。 条件付きアクセス ポリシーを適用するには、クラウド アプリまたはアクションの割り当てオプションから "Azure Linux VM サインイン" アプリを選択し、条件としてサインイン リスクを使用するか、アクセス制御付与として多要素認証を要求する、あるいはその両方を実行します。 

> [!WARNING]
> ユーザー単位で有効化または適用された Azure AD Multi-Factor Authentication は、VM のサインインではサポートされていません。

## <a name="log-in-to-the-linux-virtual-machine"></a>Linux 仮想マシンにログインする

まず、[az vm show](/cli/azure/vm#az-vm-show) を使用して VM のパブリック IP アドレスを取得します。

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Azure AD 資格情報を使用して Azure Linux 仮想マシンにログインします。 `-l` パラメーターを使用して、独自の Azure AD アカウントのアドレスを指定できます。 例のアカウントを自分のものに置き換えてください。 アカウントのアドレスは、すべて小文字で入力する必要があります。 サンプルの IP アドレスを、前のコマンドの、ご自分の VM のパブリック IP アドレスに置き換えます。

```console
ssh -l azureuser@contoso.onmicrosoft.com 10.11.123.456
```

[https://microsoft.com/devicelogin](https://microsoft.com/devicelogin) でワンタイム ユーザー コードを使用して Azure AD にサインインするように求められます。 ワンタイム ユーザー コードをコピーし、デバイスのログイン ページに貼り付けます。

メッセージが表示されたら、ログイン ページに Azure AD ログイン資格情報を入力します。 

認証が完了すると、Web ブラウザーに `You have signed in to the Microsoft Azure Linux Virtual Machine Sign-In application on your device.` のメッセージが表示されます。

ブラウザー ウィンドウを閉じ、SSH プロンプト ウィンドウに戻り、**Enter** キーを押します。 

これで、*VM ユーザー* または *VM 管理者* などの、割り当てられたロール権限を持つユーザーとして、Azure Linux 仮想マシンにサインインしました。 ご自分のユーザー アカウントに *仮想マシンの管理者ログイン* ロールが割り当てられている場合は、`sudo` を使用してルート権限を必要とするコマンドを実行できます。

## <a name="sudo-and-aad-login"></a>sudo と AAD ログイン

初めて sudo を実行するときに、2 回目の認証をするように求められます。 sudo を実行するのに再認証を必要としない場合は、sudoers ファイル `/etc/sudoers.d/aad_admins` を編集して、次の行を、

```bash
%aad_admins ALL=(ALL) ALL
```

次の行に置き換えることができます。

```bash
%aad_admins ALL=(ALL) NOPASSWD:ALL
```


## <a name="troubleshoot-sign-in-issues"></a>サインアップに関する問題のトラブルシューティング

Azure AD の資格情報を使用して SSH 経由でログインしようとしたときによく発生する問題は、Azure ロールが割り当てられていないために、サインインを求める画面が繰り返し表示されることです。 次のセクションを使用してこの問題を解決してください。

### <a name="access-denied-azure-role-not-assigned"></a>アクセスが拒否されました:Azure ロールが割り当てられていません

SSH プロンプトで次のエラーが表示された場合は、対象の VM に対して、*仮想マシンの管理者ログイン* または *仮想マシンのユーザー ログイン* ロールのいずれかをユーザーに付与する Azure RBAC ポリシーが構成済みであることを確認してください。

```output
login as: azureuser@contoso.onmicrosoft.com
Using keyboard-interactive authentication.
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FJX327AXD to authenticate. Press ENTER when ready.
Using keyboard-interactive authentication.
Access denied:  to sign-in you be assigned a role with action 'Microsoft.Compute/virtualMachines/login/action', for example 'Virtual Machine User Login'
Access denied
```
> [!NOTE]
> Azure のロールの割り当てに関する問題が発生した場合は、「[Azure RBAC のトラブルシューティング](../../role-based-access-control/troubleshooting.md#azure-role-assignments-limit)」を参照してください。

### <a name="continued-ssh-sign-in-prompts"></a>SSH サインイン画面が繰り返し表示される

Web ブラウザーで認証手続きを完了した直後に、新しいコードを使ってもう一度サインインすることを求められる場合があります。 このエラーは、通常、SSH プロンプトに入力したサインイン名と Azure AD へのサインインに使用するアカウントの間の不整合により発生します。 この問題を解決するには、次の手順を実行してください。

- SSH プロンプトに入力したサインイン名が正しいことを確認します。 サインイン名の入力ミスにより、SSH プロンプトに指定したサインイン名と Azure AD へのサインインに使用するアカウントの間で不整合が発生している場合があります。 たとえば、「*azureuser\@contoso.onmicrosoft.com*」ではなく、「*azuresuer\@contoso.onmicrosoft.com*」と入力しました。
- 複数のユーザー アカウントを使用している場合は、Azure AD にサインインするときに、ブラウザーのウィンドウに別のユーザー アカウントを入力していないことを確認します。
- Linux は、大文字と小文字を区別するオペレーティング システムです。 'Azureuser@contoso.onmicrosoft.com' と 'azureuser@contoso.onmicrosoft.com' の差により、不一致が発生します。 SSH プロンプトに大文字と小文字を正しく入力して UPN を指定してください。

### <a name="other-limitations"></a>その他の制限事項

入れ子になったグループまたはロールの割り当てによってアクセス権を継承したユーザーは、現在サポートされていません。 ユーザーまたはグループには、[必要なロールの割り当て](#configure-role-assignments-for-the-vm)が直接割り当てられている必要があります。 たとえば、管理グループまたは入れ子になったグループのロールの割り当てを使用しても、ユーザーのサインインを許可する正しいアクセス許可は付与されません。

## <a name="preview-feedback"></a>プレビューのフィードバック

[Azure AD フィードバック フォーラム](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)で、このプレビュー機能に関するフィードバックを共有するか、その使用に関する問題を報告してください

## <a name="next-steps"></a>次のステップ

Azure Active Directory の詳細については、「[Azure Active Directory とは](../../active-directory/fundamentals/active-directory-whatis.md)」を参照してください