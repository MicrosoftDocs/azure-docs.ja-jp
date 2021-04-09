---
title: テンプレートを使用して Windows VM を Azure AD DS に参加させる | Microsoft Docs
description: Azure Resource Manager テンプレートを使用して、新規または既存の Windows Server VM を Azure Active Directory Domain Services のマネージド ドメインに参加させる方法について説明します。
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 4eabfd8e-5509-4acd-86b5-1318147fddb5
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: e7245e8e468ea051ee095d97cc250ad303aa80a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619438"
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
    * 必要であれば、1 つ目のチュートリアルで [Azure Active Directory Domain Services のマネージド ドメインを作成して構成][create-azure-ad-ds-instance]します。
* マネージド ドメインの一部であるユーザー アカウント。

## <a name="azure-resource-manager-template-overview"></a>Azure Resource Manager テンプレートの概要

Resource Manager テンプレートを使用すると、コード内で Azure インフラストラクチャを定義できます。 必要なリソース、ネットワーク接続、または VM の構成はすべて、テンプレート内に定義できます。 これらのテンプレートによって、一貫性のある再現可能なデプロイが毎回作成され、変更を行う際にバージョン管理することができます。 詳細については、[Azure Resource Manager のテンプレートの概要][template-overview]に関するページをご覧ください。

各リソースは、JavaScript Object Notation (JSON) を使用してテンプレートで定義されます。 次の JSON の例では、*Microsoft.Compute/virtualMachines/extensions* のリソースの種類を使用して、Active Directory ドメイン参加の拡張機能をインストールしています。 デプロイ時に指定されたパラメーターが、使用されます。 拡張機能がデプロイされると、指定したマネージド ドメインに VM が参加します。

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

Windows Server VM が必要な場合は、Resource Manager テンプレートを使用して、作成および構成することが可能です。 VM をデプロイすると、VM をマネージド ドメインに参加させるための拡張機能がインストールされます。 マネージド ドメインへの参加を検討している VM が既にある場合は、「[既存の Windows Server VM をマネージド ドメインに参加させる](#join-an-existing-windows-server-vm-to-a-managed-domain)」に進んでください。

Windows Server VM を作成して、それをマネージド ドメインに参加させるには、次の手順を完了します。

1. [クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)に移動します。 **[Azure に配置する]** を選択します。
1. **[カスタム デプロイ]** ページ上で、次の情報を入力して Windows Server VM を作成し、マネージド ドメインに参加させます。

    | 設定                   | 値 |
    |---------------------------|-------|
    | サブスクリプション              | Azure AD Domain Services を有効にしたのと同じ Azure サブスクリプションを選択してください。 |
    | Resource group            | お使いの VM 用のリソース グループを選択します。 |
    | 場所                  | お使いの VM 用の場所を選択します。 |
    | 既存の VNET の名前        | VM の接続先となる既存の仮想ネットワークの名前 (*myVnet* など)。 |
    | 既存のサブネットの名前      | 既存の仮想ネットワーク サブネットの名前 (*Workloads* など)。 |
    | DNS ラベル プレフィックス          | VM に使用する DNS 名を入力します (*myvm* など)。 |
    | VM サイズ                   | VM サイズを指定します (*Standard_DS2_v2* など)。 |
    | 参加するドメイン            | マネージド ドメインの DNS 名 (*aaddscontoso.com* など)。 |
    | ドメイン ユーザー名           | `contosoadmin@aaddscontoso.com` など、VM をマネージド ドメインに参加させるために使用する必要がある、マネージド ドメインでのユーザー アカウント。 このアカウントは、マネージド ドメインの一部である必要があります。 |
    | ドメイン パスワード           | 前の設定で指定したユーザー アカウントのパスワード。 |
    | オプションの OU パス          | VM を追加するカスタム OU。 このパラメーターに値を指定しない場合、VM は既定の *[AAD DC Computers]\(AAD DC コンピューター\)* の OU に追加されます。 |
    | VM 管理者のユーザー名         | VM 上に作成するためのローカル管理者アカウントを指定します。 |
    | VM 管理者のパスワード         | VM 用のローカル管理者のパスワードを指定します。 パスワードのブルート フォース攻撃から保護するために、強力なローカル管理者パスワードを作成してください。 |

1. 使用条件を確認して、 **[上記の使用条件に同意する]** チェック ボックスをオンにします。 準備ができたら、 **[購入]** を選択し、VM を作成してマネージド ドメインに参加させます。

> [!WARNING]
> **パスワードの取り扱いには注意してください。**
> テンプレート パラメーター ファイルでは、マネージド ドメインの一部であるユーザー アカウントのパスワードが要求されます。 このファイルに値を手動で入力して、ファイル共有やその他の共有の場所上でアクセス可能なままにしておくことはできません。

デプロイが正常に完了するまでには、数分かかります。 完了すると、Windows VM が作成されて、マネージド ドメインに参加します。 VM は、ドメイン アカウントを使用して、管理やサインインを行うことができます。

## <a name="join-an-existing-windows-server-vm-to-a-managed-domain"></a>既存の Windows Server VM をマネージド ドメインに参加させる

マネージド ドメインへの参加を検討している既存の VM または VM グループがある場合は、単に VM 拡張機能をデプロイするために、Resource Manager テンプレートを使用できます。

既存の Windows Server VM をマネージド ドメインに参加させるには、次の手順を完了します。

1. [クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)に移動します。 **[Azure に配置する]** を選択します。
1. **[カスタム デプロイ]** ページ上で、次の情報を入力して、VM をマネージド ドメインに参加させます。

    | 設定                   | 値 |
    |---------------------------|-------|
    | サブスクリプション              | Azure AD Domain Services を有効にしたのと同じ Azure サブスクリプションを選択してください。 |
    | Resource group            | 既存の VM に使用するリソース グループを選択します。 |
    | 場所                  | 既存の VM の場所を選択します。 |
    | VM リスト                   | マネージド ドメインに参加させるために、*MyVM1,myVM2* のように、既存の VM のコンマ区切りリストを入力します。 |
    | ドメイン参加ユーザー名     | `contosoadmin@aaddscontoso.com` など、VM をマネージド ドメインに参加させるために使用する必要がある、マネージド ドメインでのユーザー アカウント。 このアカウントは、マネージド ドメインの一部である必要があります。 |
    | ドメイン参加ユーザー パスワード | 前の設定で指定したユーザー アカウントのパスワード。 |
    | オプションの OU パス          | VM を追加するカスタム OU。 このパラメーターに値を指定しない場合、VM は既定の *[AAD DC Computers]\(AAD DC コンピューター\)* の OU に追加されます。 |

1. 使用条件を確認して、 **[上記の使用条件に同意する]** チェック ボックスをオンにします。 準備ができたら、 **[購入]** を選択し、VM をマネージド ドメインに参加させます。

> [!WARNING]
> **パスワードの取り扱いには注意してください。**
> テンプレート パラメーター ファイルでは、マネージド ドメインの一部であるユーザー アカウントのパスワードが要求されます。 このファイルに値を手動で入力して、ファイル共有やその他の共有の場所上でアクセス可能なままにしておくことはできません。

デプロイが正常に完了するまでには、しばらくかかります。 完了すると、指定した Windows VM がマネージド ドメインに参加し、ドメイン アカウントを使用して管理やサインインができるようになります。

## <a name="next-steps"></a>次のステップ

この記事では、Azure portal を使用して、テンプレートを利用したリソースの構成とデプロイを行いました。 [Azure PowerShell][deploy-powershell] または [Azure CLI][deploy-cli] を使用し、Resource Manager テンプレートを利用してリソースをデプロイすることも可能です。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[template-overview]: ../azure-resource-manager/templates/overview.md
[deploy-powershell]: ../azure-resource-manager/templates/deploy-powershell.md
[deploy-cli]: ../azure-resource-manager/templates/deploy-cli.md
