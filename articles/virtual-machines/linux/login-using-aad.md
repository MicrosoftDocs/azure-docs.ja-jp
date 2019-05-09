---
title: Linux VM に Azure Active Directory の資格情報を使用してログインする | Microsoft Docs
description: この記事では、ユーザー ログインに Azure Active Directory 認証を使用する Linux VM を作成および構成する方法について説明します。
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/17/2018
ms.author: cynthn
ms.openlocfilehash: a1743e677e1005e5b4479c1d431b6b8bdbe77c8f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57848696"
---
# <a name="log-in-to-a-linux-virtual-machine-in-azure-using-azure-active-directory-authentication-preview"></a>Azure Active Directory 認証 (プレビュー) を使用して Azure の Linux 仮想マシンにログインする

Azure の Linux 仮想マシン (VM) のセキュリティを強化するために、Azure Active Directory (AD) の認証と統合できます。 Linux VM に Azure AD の認証を使用することで、VM へのアクセスを許可/拒否するポリシーを一元管理および施行します。 この記事では、Azure AD 認証を使用して Linux VM を作成し、構成する方法について説明します。

> [!NOTE]
> この機能はプレビュー段階にあり、運用環境の仮想マシンまたはワークロードでの使用はお勧めしません。 テスト後に破棄する予定のテスト用仮想マシンでこの機能を使用してください。

Azure AD の認証を使用して、Azure Linux VM にログインすると、次のような多くのメリットがあります。

- **セキュリティの強化:**
  - お客様の会社の AD 資格情報を使用して、Azure Linux VM にログインできます。 ローカル管理者アカウントを作成したり、資格情報の有効期間を管理する必要がなくなります。
  - ローカル管理者アカウントへの依存度を低減することで、資格情報の損失/漏洩や、セキュリティ性の弱い資格情報をユーザーが設定することを憂慮する必要がなくなります。
  - Azure AD ディレクトリ用に設定されたパスワードの複雑性と、パスワードの有効期間ポリシーを使用して、Linux VM もセキュリティ保護できます。
  - Azure 仮想マシンへのログイン セキュリティをさらに強化するために、多要素認証を設定することができます。
  - Azure Active Directory を使用して Linux VM にログインする機能は、[フェデレーション サービス](../../active-directory/hybrid/how-to-connect-fed-whatis.md)を使用するお客様も使用できます。

- **シームレスな コラボレーション:** ロールベースのアクセス制御 (RBAC) を使用することで、どのユーザーが、正規のユーザーまたは管理者権限を持つユーザーとして特定の VM にサインインできるかを指定できます。 ユーザーがチームに参加またはチームから脱退する場合は、適切なアクセス権が付与されるよう VM の RBAC ポリシーを更新できます。 この操作は、不要な SSH 公開キーを削除して VM をスクラブするよりも簡単です。 従業員が退職し、そのユーザー アカウントが無効化または Azure AD から削除されると、リソースにアクセスできなくなります。

## <a name="supported-azure-regions-and-linux-distributions"></a>サポートされている Azure リージョンと Linux ディストリビューション

この機能のプレビュー期間中は、次の Linux ディストリビューションがサポートされます。

| ディストリビューション | Version |
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

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、Azure CLI バージョン 2.0.31 以降を実行していることがこのチュートリアルの要件になります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

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

Azure AD 資格情報を使用して Linux VM にログインするには、VM 拡張機能に Azure Active Directory ログインをインストールします。 VM 拡張機能は、Azure 仮想マシンでのデプロイ後の構成と自動タスクを提供する小さなアプリケーションです。 [az vm extension set](/cli/azure/vm/extension#az-vm-extension-set) を使用して、*myResourceGroup* リソース グループの *myVM* という名前の VM に *AADLoginForLinux* 拡張機能をインストールします。

```azurecli-interactive
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory.LinuxSSH \
    --name AADLoginForLinux \
    --resource-group myResourceGroup \
    --vm-name myVM
```

VM に拡張機能がインストールされると、*provisioningState* に *Succeeded* が表示されます。

## <a name="configure-role-assignments-for-the-vm"></a>仮想マシン ロールの割り当てを構成する

Azure のロールベース アクセス制御 (RBAC) ポリシーは、VM にログオンできるユーザーを規定します。 VM へのログインを承認するには、次の 2 つの RBAC ロールが使用されます。

- **仮想マシンの管理者ログイン**:このロールが割り当てられたユーザーは Windows 管理者または Linux のルート ユーザーの権限を持つユーザーとして Azure 仮想マシンにログインできます。
- **仮想マシンのユーザー ログイン**:このロールが割り当てられたユーザーは正規ユーザーの権限を持つユーザーとして Azure 仮想マシンにログインできます。

> [!NOTE]
> SSH 経由でユーザーが VM にログインできるようにするには、*仮想マシンの管理者ログイン*または*仮想マシンのユーザー ログイン* ロールのいずれかを割り当てる必要があります。 VM の*所有者*または*共同作成者*ロールが割り当てられた Azure ユーザーに、SSH 経由で VM にログインする権限は自動的には付与されません。

次の例では、[az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) を使用して、VM に対する*仮想マシンの管理者ログイン* ロールを現在の Azure ユーザーに割り当てます。 [az account show](/cli/azure/account#az-account-show) を使用して、アクティブな Azure アカウントのユーザー名を取得し、[az vm show](/cli/azure/vm#az-vm-show) を使用して前の手順で作成された VM に*スコープ*が設定されています。 スコープは、リソース グループまたはサブスクリプション レベルで割り当てることもでき、通常の RBAC 継承のアクセス許可が適用されます。 詳細については、「[Azure のロールベースのアクセス制御](../../role-based-access-control/overview.md)」を参照してください。

```azurecli-interactive
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> AAD ドメインとログオン ユーザー名ドメインが一致しない場合は、*--assignee* のユーザー名だけでなく、*--assignee-object-id* を使用してユーザー アカウントのオブジェクト ID を指定する必要があります。 ユーザー アカウントのオブジェクト ID は、[az ad user list](/cli/azure/ad/user#az-ad-user-list)を使用して取得できます。

RBAC を使用して、Azure サブスクリプション リソースへのアクセスを管理する方法の詳細については、[Azure CLI](../../role-based-access-control/role-assignments-cli.md)、[Azure portal](../../role-based-access-control/role-assignments-portal.md)、または [Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md) の使用に関する記事を参照してください。

Linux 仮想マシンにサインインする特定のユーザーに対して多要素認証を要求するように Azure AD を構成することもできます。 詳細については、「[クラウドでの Azure Multi-Factor Authentication の概要](../../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md)」を参照してください。

## <a name="log-in-to-the-linux-virtual-machine"></a>Linux 仮想マシンにログインする

まず、[az vm show](/cli/azure/vm#az-vm-show) を使用して VM のパブリック IP アドレスを取得します。

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Azure AD 資格情報を使用して Azure Linux 仮想マシンにログインします。 `-l` パラメーターを使用して、独自の Azure AD アカウントのアドレスを指定できます。 アカウントのアドレスは、すべて小文字で入力する必要があります。 前述のコマンドからお使いの VM のパブリック IP アドレスを使用します。

```azurecli-interactive
ssh -l azureuser@contoso.onmicrosoft.com publicIps
```

[https://microsoft.com/devicelogin](https://microsoft.com/devicelogin) でワンタイム ユーザー コードを使用して Azure AD にサインインするように求められます。 次の例で示すように、ワンタイム ユーザー コードをコピーし、デバイスのログイン ページに貼り付けます。

```bash
~$ ssh -l azureuser@contoso.onmicrosoft.com 13.65.237.247
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FJS3K6X4D to authenticate. Press ENTER when ready.
```

メッセージが表示されたら、ログイン ページに Azure AD ログイン資格情報を入力します。 認証が完了すると、Web ブラウザーに次のメッセージが表示されます。

    You have signed in to the Microsoft Azure Linux Virtual Machine Sign-In application on your device.

ブラウザー ウィンドウを閉じ、SSH プロンプト ウィンドウに戻り、**Enter** キーを押します。 これで、*VM ユーザー*または *VM 管理者*などの、割り当てられたロール権限を持つユーザーとして、Azure Linux 仮想マシンにサインインしました。 ユーザー アカウントに*仮想マシンの管理者ログイン*ロールが割り当てられている場合は `sudo` を使用してルート権限を必要とするコマンドを実行できます。

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

Azure AD の資格情報を使用して SSH 経由でログインしようとしたときによく発生する問題は、RBAC ロールが割り当てられていないために、サインインを求める画面が繰り返し表示されることです。 次のセクションを使用してこの問題を解決してください。

### <a name="access-denied-rbac-role-not-assigned"></a>アクセスが拒否されました:RBAC の役割が割り当てられていません

SSH プロンプトで次のエラーが表示された場合は、*仮想マシンの管理者ログイン*または*仮想マシンのユーザー ログイン* ロールのいずれかをユーザーに付与する RBAC ポリシーが VM に設定されていることを確認してください。

```bash
login as: azureuser@contoso.onmicrosoft.com
Using keyboard-interactive authentication.
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FJX327AXD to authenticate. Press ENTER when ready.
Using keyboard-interactive authentication.
Access denied:  to sign-in you be assigned a role with action 'Microsoft.Compute/virtualMachines/login/action', for example 'Virtual Machine User Login'
Access denied
```

### <a name="continued-ssh-sign-in-prompts"></a>SSH サインイン画面が繰り返し表示される

Web ブラウザーで認証手続きを完了した直後に、新しいコードを使ってもう一度サインインすることを求められる場合があります。 このエラーは、通常、SSH プロンプトに入力したサインイン名と Azure AD へのサインインに使用するアカウントの間の不整合により発生します。 この問題を解決するには、次の手順を実行してください。

- SSH プロンプトに入力したサインイン名が正しいことを確認します。 サインイン名の入力ミスにより、SSH プロンプトに指定したサインイン名と Azure AD へのサインインに使用するアカウントの間で不整合が発生している場合があります。 たとえば、「*azureuser\@contoso.onmicrosoft.com*」ではなく、「*azuresuer\@contoso.onmicrosoft.com*」と入力しました。
- 複数のユーザー アカウントを使用している場合は、Azure AD にサインインするときに、ブラウザーのウィンドウに別のユーザー アカウントを入力していないことを確認します。
- Linux は、大文字と小文字を区別するオペレーティング システムです。 'Azureuser@contoso.onmicrosoft.com' と 'azureuser@contoso.onmicrosoft.com' の差により、不一致が発生します。 SSH プロンプトに大文字と小文字を正しく入力して UPN を指定してください。

## <a name="preview-feedback"></a>プレビューのフィードバック

[Azure AD フィードバック フォーラム](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)で、このプレビュー機能に関するフィードバックを共有するか、その使用に関する問題を報告してください

## <a name="next-steps"></a>次の手順

Azure Active Directory の詳細については、「[Azure Active Directory とは](../../active-directory/fundamentals/active-directory-whatis.md)」を参照してください
