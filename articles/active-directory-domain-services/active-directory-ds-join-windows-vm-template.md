---
title: Windows Server VM の Azure Active Directory Domain Services への参加 | Microsoft Docs
description: Azure Resource Manager テンプレートを使用して Windows Server 仮想マシンをマネージド ドメインに参加させます。
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 4eabfd8e-5509-4acd-86b5-1318147fddb5
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: ergreenl
ms.openlocfilehash: 5b70a16d1da8f939a23bf7a96a03098463cfc529
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55203336"
---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain-using-a-resource-manager-template"></a>Resource Manager テンプレートを使用して Windows Server 仮想マシンをマネージド ドメインに参加させます。
この記事では、Resource Manager テンプレートを使用して Windows Server 仮想マシンを Azure AD Domain Services のマネージド ドメインに参加させる方法を示します。

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>開始する前に
この記事に記載されているタスクを実行するには、次が必要です。
1. 有効な **Azure サブスクリプション**。
2. オンプレミス ディレクトリまたはクラウド専用ディレクトリのいずれかと同期されている **Azure AD ディレクトリ** 。
3. **Azure AD ドメイン サービス** が Azure AD ディレクトリに対して有効である必要があります。 有効になっていない場合は、 [作業の開始に関するガイド](active-directory-ds-getting-started.md)に記載されているすべてのタスクを実行してください。
4. マネージド ドメインの IP アドレスを、必ず仮想ネットワークの DNS サーバーとして構成します。 詳しくは、[Azure 仮想ネットワークの DNS 設定を更新する方法](active-directory-ds-getting-started-dns.md)に関するページをご覧ください。
5. [Azure AD Domain Services のマネージド ドメインとのパスワードの同期](active-directory-ds-getting-started-password-sync.md)に必要な手順をすべて実行します。


## <a name="install-and-configure-required-tools"></a>必要なツールをインストールして構成する
このドキュメントで説明している手順を実行するには、次のオプションのいずれかを使用できます。
* **Azure PowerShell**:[インストールと構成](https://azure.microsoft.com/documentation/articles/powershell-install-configure/)
* **Azure CLI**:[インストールと構成](https://azure.microsoft.com/documentation/articles/xplat-cli-install/)


## <a name="option-1-provision-a-new-windows-server-vm-and-join-it-to-a-managed-domain"></a>オプション 1:新しい Windows Server VM をプロビジョニングしてマネージド ドメインに参加させる
**クイック スタート テンプレート名**:[201-vm-domain-join](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)

Windows Server 仮想マシンをデプロイしてマネージド ドメインに参加させるには、次の手順を実行します。
1. [クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)に移動します。
2. **[Azure へのデプロイ]** をクリックします。
3. **[カスタム デプロイ]** ページで、仮想マシンのプロビジョニングに必要な情報を入力します。
4. 仮想マシンをプロビジョニングする **Azure サブスクリプション**を選択します。 Azure AD Domain Services を有効にしたのと同じ Azure サブスクリプションを選択してください。
5. 既存の**リソース グループ**を選択するか、新しいリソース グループを作成します。
6. 新しい仮想マシンをデプロイする**場所**を選択します。
7. **[Existing VNET Name]\(既存の VNET 名\)** で、Azure AD Domain Services のマネージド ドメインをデプロイした仮想ネットワークを指定します。
8. **[Existing Subnet Name]\(既存のサブネット名\)** で、この仮想マシンをデプロイする仮想ネットワーク内のサブネットを指定します。 仮想ネットワーク内のゲートウェイ サブネットは選択しないでください。 また、マネージド ドメインがデプロイされている専用サブネットも選択しないでください。
9. **[DNS Label Prefix]\(DNS ラベルのプレフィックス\)** で、プロビジョニングする仮想マシンのホスト名を指定します。 たとえば、"contoso-win" を指定します。
10. 仮想マシンに対して適切な **[VM サイズ]** を選択します。
11. **[参加するドメイン]** で、マネージド ドメインの DNS ドメイン名を指定します。
12. **[ドメイン ユーザー名]** で、VM をマネージド ドメインに参加させるために使用する、マネージド ドメイン上のユーザー アカウント名を指定します。
13. **[ドメイン パスワード]** で、"domainUsername" パラメーターで参照されるドメイン ユーザー アカウントのパスワードを指定します。
14. 省略可能:仮想マシンを追加するカスタム OU への **OU パス**を指定できます。 このパラメーターの値を指定しない場合、仮想マシンはマネージド ドメイン上の既定の **[AAD DC Computers]\(AAD DC コンピューター\)** OU に追加されます。
15. **[VM Admin Username]\(VM 管理ユーザー名\)** フィールドで、仮想マシンのローカル管理者のアカウント名を指定します。
16. **[VM Admin Password]\(VM 管理パスワード\)** フィールドで、仮想マシンのローカル管理者のパスワードを指定します。 仮想マシンをパスワードのブルート フォース攻撃から保護するために、強力なローカル管理者パスワードを指定してください。
17. **[上記の使用条件に同意する]** をクリックします。
18. **[購入]** をクリックして仮想マシンをプロビジョニングします。

> [!WARNING]
> **パスワードの取り扱いには注意してください。**
> テンプレート パラメーター ファイルには、仮想マシンのローカル管理者パスワードだけでなく、ドメイン アカウントのパスワードも含まれます。 このファイルをファイル共有や他の共有場所に置いたままにしないでください。 仮想マシンのデプロイが完了したら、このファイルを破棄することをお勧めします。
>

デプロイが正常に完了すると、新しくプロビジョニングした Windows 仮想マシンがマネージド ドメインに参加します。


## <a name="option-2-join-an-existing-windows-server-vm-to-a-managed-domain"></a>オプション 2:既存の Windows Server VM をマネージド ドメインに参加させる
**クイック スタート テンプレート**:[201-vm-domain-join-existing](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)

既存の Windows Server 仮想マシンをマネージド ドメインに参加させるには、次の手順を実行します。
1. [クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)に移動します。
2. **[Azure へのデプロイ]** をクリックします。
3. **[カスタム デプロイ]** ページで、仮想マシンのプロビジョニングに必要な情報を入力します。
4. 仮想マシンをプロビジョニングする **Azure サブスクリプション**を選択します。 Azure AD Domain Services を有効にしたのと同じ Azure サブスクリプションを選択してください。
5. 既存の**リソース グループ**を選択するか、新しいリソース グループを作成します。
6. 新しい仮想マシンをデプロイする**場所**を選択します。
7. **[VM List]\(VM 一覧\)** フィールドで、マネージド ドメインに参加させる既存の仮想マシンの名前を指定します。 コンマを使用して、個々の VM の名前を区切ります。 たとえば、**contoso web, contoso api** のようにします。
8. **[Domain Join User Name]\(ドメイン参加ユーザー名\)** で、VM をマネージド ドメインに参加させるために使用する、マネージド ドメイン上のユーザー アカウント名を指定します。
9. **[Domain Join User Password]\(ドメイン参加ユーザー パスワード\)** で、"domainUsername" パラメーターで参照されるドメイン ユーザー アカウントのパスワードを指定します。
10. **[ドメイン FQDN]** で、マネージド ドメインの DNS ドメイン名を指定します。
11. 省略可能:仮想マシンを追加するカスタム OU への **OU パス**を指定できます。 このパラメーターの値を指定しない場合、仮想マシンはマネージド ドメイン上の既定の **[AAD DC Computers]\(AAD DC コンピューター\)** OU に追加されます。
12. **[上記の使用条件に同意する]** をクリックします。
13. **[購入]** をクリックして仮想マシンをプロビジョニングします。

> [!WARNING]
> **パスワードの取り扱いには注意してください。**
> テンプレート パラメーター ファイルには、仮想マシンのローカル管理者パスワードだけでなく、ドメイン アカウントのパスワードも含まれます。 このファイルをファイル共有や他の共有場所に置いたままにしないでください。 仮想マシンのデプロイが完了したら、このファイルを破棄することをお勧めします。
>

デプロイが正常に完了すると、指定した Windows 仮想マシンがマネージド ドメインに参加します。


## <a name="related-content"></a>関連コンテンツ
* [Azure PowerShell の概要](/powershell/azure/overview)
* [Azure クイック スタート テンプレート - 新しい VM をドメインに参加させる](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)
* [Azure クイック スタート テンプレート - 既存の VM をドメインに参加させる](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)
* [Resource Manager テンプレートと Azure PowerShell を使用したリソースのデプロイ](../azure-resource-manager/resource-group-template-deploy.md)
