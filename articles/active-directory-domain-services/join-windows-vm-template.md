---
title: テンプレートを使用して Windows Server VM を Azure AD DS に参加させる |Microsoft Docs
description: Azure Resource Manager テンプレートを使用して、新規または既存の Windows Server VM を Azure Active Directory Domain Services のマネージド ドメインに参加させる方法について説明します。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 4eabfd8e-5509-4acd-86b5-1318147fddb5
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: iainfou
ms.openlocfilehash: d4e6beb376172e5ec5285d26b47fd23b396d5e38
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104108"
---
# <a name="join-a-windows-server-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain-using-a-resource-manager-template"></a>Resource Manager テンプレートを使用して Azure Active Directory Domain Services マネージド ドメインに Windows Server 仮想マシンを参加させる

Azure 仮想マシン (VM) のデプロイと構成を自動化するには、Resource Manager テンプレートを使用できます。 これらのテンプレートを使用すると、毎回、一貫性のあるデプロイを作成できます。 テンプレートに拡張機能を含めて、デプロイの一部として VM を自動的に構成することもできます。 1 つの便利な拡張機能では、Azure Active Directory Domain Services (Azure AD DS) のマネージド ドメインを使って利用できるドメインに、VM を参加させます。

この記事では、Resource Manager テンプレートを使用して Windows Server VM を作成し Azure AD DS マネージド ドメインに参加させる方法を示します。 また、既存の Windows Server VM を Azure AD DS ドメインに参加させる方法についても説明します。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下のリソースと特権が必要です。

* 有効な Azure サブスクリプション
    * Azure サブスクリプションをお持ちでない場合は、[アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。
* ご利用のサブスクリプションに関連付けられた Azure Active Directory テナント (オンプレミス ディレクトリまたはクラウド専用ディレクトリと同期されていること)。
    * 必要に応じて、[Azure Active Directory テナントを作成][create-azure-ad-tenant]するか、[ご利用のアカウントに Azure サブスクリプションを関連付け][associate-azure-ad-tenant]ます。
* Azure AD テナントで有効化され、構成された Azure Active Directory Domain Services のマネージド ドメイン。
    * 必要であれば、1 つ目のチュートリアルで [Azure Active Directory Domain Services インスタンスを作成して構成][create-azure-ad-ds-instance]します。
* Azure AD テナントの *Azure AD DC administrators* グループのメンバーであるユーザー アカウント。

## <a name="azure-resource-manager-template-overview"></a>Azure Resource Manager テンプレートの概要

Resource Manager テンプレートを使用すると、コード内で Azure インフラストラクチャを定義できます。 必要なリソース、ネットワーク接続、または VM の構成はすべて、テンプレート内に定義できます。 これらのテンプレートによって、一貫性のある再現可能なデプロイが毎回作成され、変更を行う際にバージョン管理することができます。 詳細については、[Azure Resource Manager のテンプレートの概要][template-overview]に関するページをご覧ください。

各リソースは、JSON を使用してテンプレート内に定義されます。 次の JSON の例では、*Microsoft.Compute/virtualMachines/extensions* のリソースの種類を使用して、Active Directory ドメイン参加の拡張機能をインストールしています。 デプロイ時に指定されたパラメーターが、使用されます。 拡張機能がデプロイされると、指定した Azure AD DS のマネージド ドメインに VM が参加します。

```json
 {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('dnsLabelPrefix'),'/joindomain')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', parameters('dnsLabelPrefix'))]"
      ],
      "properties": {
        "publisher": "Microsoft.Compute",
        "type": "JsonADDomainExtension",
        "typeHandlerVersion": "1.3",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "Name": "[parameters('domainToJoin')]",
          "OUPath": "[parameters('ouPath')]",
          "User": "[concat(parameters('domainToJoin'), '\\', parameters('domainUsername'))]",
          "Restart": "true",
          "Options": "[parameters('domainJoinOptions')]"
        },
        "protectedSettings": {
          "Password": "[parameters('domainPassword')]"
        }
      }
    }
```

同じテンプレート内で VM を作成しない場合でも、この VM 拡張機能をデプロイできます。 この記事の例では、次の両方の手法を示しています。

* [Windows Server VM を作成してマネージド ドメインに参加させる](#create-a-windows-server-vm-and-join-to-a-managed-domain)
* [既存の Windows Server VM をマネージド ドメインに参加させる](#join-an-existing-windows-server-vm-to-a-managed-domain)

## <a name="create-a-windows-server-vm-and-join-to-a-managed-domain"></a>Windows Server VM を作成してマネージド ドメインに参加させる

Windows Server VM が必要な場合は、Resource Manager テンプレートを使用して、作成および構成することが可能です。 VM をデプロイすると、VM を Azure AD DS マネージド ドメインに参加させるための拡張機能がインストールされます。 Azure AD DS マネージド ドメインへの参加を検討している VM が既にある場合は、「[既存の Windows Server VM をマネージド ドメインに参加させる](#join-an-existing-windows-server-vm-to-a-managed-domain)」に進んでください。

Windows Server VM を作成して、それを Azure AD DS マネージド ドメインに参加させるには、次の手順を完了します。

1. [クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)に移動します。 **[Azure に配置する]** を選択します。
1. **[カスタム デプロイ]** ページ上で、次の情報を入力してWindows Server VM を作成し、Azure AD DS マネージド ドメインに参加させます。

    | Setting                   | 値 |
    |---------------------------|-------|
    | Subscription              | Azure AD Domain Services を有効にしたのと同じ Azure サブスクリプションを選択してください。 |
    | Resource group            | お使いの VM 用のリソース グループを選択します。 |
    | Location                  | お使いの VM 用の場所を選択します。 |
    | 既存の VNET の名前        | VM の接続先となる既存の仮想ネットワークの名前 (*myVnet* など)。 |
    | 既存のサブネットの名前      | 既存の仮想ネットワーク サブネットの名前 (*Workloads* など)。 |
    | DNS ラベル プレフィックス          | VM に使用する DNS 名を入力します (*myvm* など)。 |
    | VM サイズ                   | VM サイズを指定します (*Standard_DS2_v2* など)。 |
    | 参加するドメイン            | Azure AD DS マネージド ドメインの DNS 名 (*contoso.com* など)。 |
    | ドメイン ユーザー名           | VM をマネージド ドメインに参加させるために使用する必要がある、Azure AD DS マネージド ドメインでのユーザー アカウント。 このアカウントは、 *[Azure AD DC administrators]\(Azure AD DC 管理者\)* グループのメンバーになっている必要があります。 |
    | ドメイン パスワード           | 前の設定で指定したユーザー アカウントのパスワード。 |
    | オプションの OU パス          | VM を追加するカスタム OU。 このパラメーターに値を指定しない場合、VM は既定の *[AAD DC Computers]\(AAD DC コンピューター\)* の OU に追加されます。 |
    | VM 管理者のユーザー名         | VM 上に作成するためのローカル管理者アカウントを指定します。 |
    | VM 管理者のパスワード         | VM 用のローカル管理者のパスワードを指定します。 パスワードのブルート フォース攻撃から保護するために、強力なローカル管理者パスワードを作成してください。 |

1. 使用条件を確認して、 **[上記の使用条件に同意する]** チェック ボックスをオンにします。 準備ができたら、 **[購入]** を選択し、VM を作成して Azure AD DS マネージド ドメインに参加させます。

> [!WARNING]
> **パスワードの取り扱いには注意してください。**
> テンプレート パラメーター ファイルでは、 *[Azure AD DC administrators]\(Azure AD DC 管理者\)* グループのメンバーになっているユーザー アカウントのパスワードが要求されます。 このファイルに値を手動で入力して、ファイル共有やその他の共有の場所上でアクセス可能なままにしておくことはできません。

デプロイが正常に完了するまでには、数分かかります。 完了すると、Windows VM が作成されて、Azure AD DS マネージド ドメインに参加します。 VM は、ドメイン アカウントを使用して、管理やサインインを行うことができます。

## <a name="join-an-existing-windows-server-vm-to-a-managed-domain"></a>既存の Windows Server VM をマネージド ドメインに参加させる

Azure AD DS マネージド ドメインへの参加を検討している既存の VM または VM グループがある場合は、単にVM 拡張機能をデプロイするために、Resource Manager テンプレートを使用できます。

既存の Windows Server VM を Azure AD DS マネージド ドメインに参加させるには、次の手順を完了します。

1. [クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)に移動します。 **[Azure に配置する]** を選択します。
1. **[カスタム デプロイ]** ページ上で、次の情報を入力して、VM を Azure AD DS マネージド ドメインに参加させます。

    | Setting                   | 値 |
    |---------------------------|-------|
    | Subscription              | Azure AD Domain Services を有効にしたのと同じ Azure サブスクリプションを選択してください。 |
    | Resource group            | 既存の VM に使用するリソース グループを選択します。 |
    | Location                  | 既存の VM の場所を選択します。 |
    | VM リスト                   | Azure AD DS マネージド ドメインに参加させるために、*MyVM1,myVM2* のように、既存の VM のコンマ区切りリストを入力します。 |
    | ドメイン参加ユーザー名     | VM をマネージド ドメインに参加させるために使用する必要がある、Azure AD DS マネージド ドメインでのユーザー アカウント。 このアカウントは、 *[Azure AD DC administrators]\(Azure AD DC 管理者\)* グループのメンバーになっている必要があります。 |
    | ドメイン参加ユーザー パスワード | 前の設定で指定したユーザー アカウントのパスワード。 |
    | オプションの OU パス          | VM を追加するカスタム OU。 このパラメーターに値を指定しない場合、VM は既定の *[AAD DC Computers]\(AAD DC コンピューター\)* の OU に追加されます。 |

1. 使用条件を確認して、 **[上記の使用条件に同意する]** チェック ボックスをオンにします。 準備ができたら、 **[購入]** を選択し、VM を Azure AD DS マネージド ドメインに参加させます。

> [!WARNING]
> **パスワードの取り扱いには注意してください。**
> テンプレート パラメーター ファイルでは、 *[Azure AD DC administrators]\(Azure AD DC 管理者\)* グループのメンバーになっているユーザー アカウントのパスワードが要求されます。 このファイルに値を手動で入力して、ファイル共有やその他の共有の場所上でアクセス可能なままにしておくことはできません。

デプロイが正常に完了するまでには、しばらくかかります。 完了すると、指定した Windows VM が Azure AD DS マネージド ドメインに参加し、ドメイン アカウントを使用して管理やサインインができるようになります。

## <a name="next-steps"></a>次の手順

この記事では、Azure portal を使用して、テンプレートを利用したリソースの構成とデプロイを行いました。 [Azure PowerShell][deploy-powershell] または [Azure CLI][deploy-cli] を使用し、Resource Manager テンプレートを利用してリソースをデプロイすることも可能です。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[template-overview]: ../azure-resource-manager/template-deployment-overview.md
[deploy-powershell]: ../azure-resource-manager/resource-group-template-deploy.md
[deploy-cli]: ../azure-resource-manager/resource-group-template-deploy-cli.md
