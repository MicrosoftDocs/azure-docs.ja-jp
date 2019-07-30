---
title: 特定組織からの招待を許可またはブロックする - Azure Active Directory | Microsoft Docs
description: 管理者が Azure Portal または PowerShell を使用して、B2B ユーザーの特定ドメインへのアクセスを許可またはブロックするアクセス リストまたは拒否リストを設定する方法を示します。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/19/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: sasubram
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa975446c19db3176fdb89ccfb1a987b1fda049d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67113217"
---
# <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>B2B ユーザーに対する特定組織からの招待を許可またはブロックする

B2B ユーザーに対する特定組織からの招待を許可またはブロックする、許可リストまたは拒否リストを使用できます。 たとえば、個人の電子メール アドレス ドメインをブロックする場合は、gmail.com や Outlook.com などのドメインを含む拒否リストを設定できます。 または、企業が Contoso.com、Fabrikam.com、Litware.com などの他の企業と協力していて、招待をこれらの組織のみに制限する場合は、許可リストに Contoso.com、Fabrikam.com、および Litware.com を追加することができます。
  
## <a name="important-considerations"></a>重要な考慮事項

- 許可リストまたは拒否リストのいずれかを作成できます。 両方の種類のリストを設定することはできません。 既定では、許可リストに含まれないドメインはすべて拒否リストに含まれます。また、その逆も言えます。 
- 各組織に作成できるポリシーは 1 つだけです。 ポリシーを更新してより多くのドメインを含めることも、ポリシーを削除して新規に作成することもできます。 
- このリストは、OneDrive for Business や SharePoint Online の許可/ブロック リストとは無関係に機能します。 SharePoint Online で個々のファイルの共有を制限する場合は、OneDrive for Business および SharePoint Online の許可リストまたは拒否リストを設定する必要があります。 詳細については、「[SharePoint Online と OneDrive for Business の制限付きドメイン共有](https://support.office.com/article/restricted-domains-sharing-in-sharepoint-online-and-onedrive-for-business-5d7589cd-0997-4a00-a2ba-2320ec49c4e9)」を参照してください。
- このリストは、招待を既に使用した外部ユーザーには適用されません。 リストは、リストの設定後に適用されます。 ユーザーの招待が保留中の状態にあり、ドメインをブロックするポリシーを設定した場合、ユーザーが招待の使用を試みると失敗します。

## <a name="set-the-allow-or-deny-list-policy-in-the-portal"></a>ポータルで許可リストまたは拒否リストのポリシーを設定する

既定では、 **[Allow invitations to be sent to any domain (most inclusive) (どのドメインに送信される招待も許可する (最も包括的)]** の設定が有効になっています。 この場合、任意の組織から B2B ユーザーを招待できます。

### <a name="add-a-deny-list"></a>拒否リストを追加する

これは、ある組織が、ほぼすべての組織と協力するが、特定ドメインのユーザーが B2B ユーザーとして招待されないようにする最も一般的なシナリオです。

拒否リストを追加するには:

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. **[Azure Active Directory]**  >  **[ユーザー]**  >  **[ユーザー設定]** の順に選択します。
3. **[外部ユーザー]** で、 **[Manage external collaboration settings (外部コラボレーション設定の管理)]** を選択します。
4. **[Collaboration restrictions (コラボレーション制限)]** で、 **[Deny invitations to the specified domains (指定したドメインへの招待を拒否)]** を選択します。
5. **[TARGET DOMAINS (ターゲット ドメイン)]** で、ブロックするドメインの 1 つの名前を入力します。 複数ドメインの場合は、それぞれのドメインを新しい行に入力します。 例:

   ![追加したドメインと共に拒否オプションを表示する](./media/allow-deny-list/DenyListSettings.png)
 
6. 終了したら **[保存]** をクリックします。

ポリシーの設定後、ブロックしたドメインからユーザーを招待しようとすると、ユーザーのドメインは現在、招待ポリシーによってブロックされていることを示すメッセージが表示されます。
 
### <a name="add-an-allow-list"></a>許可リストを追加する

これは、より制限の厳しい構成で、許可リストに特定のドメインを設定し、その他すべての組織や記載されていないドメインに対する招待を制限することができます。 

許可リストを使用する場合は、どのようなビジネス ニーズであるかを十分に評価するために時間をかけるようにしてください。 このポリシーを厳しくしすぎると、ユーザーはドキュメントを電子メールで送信したり、IT による認可を伴わないコラボレーションの方法を別に見つけたりするようになります。


許可リストを追加するには、次の手順を実行します。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. **[Azure Active Directory]**  >  **[ユーザー]**  >  **[ユーザー設定]** の順に選択します。
3. **[外部ユーザー]** で、 **[Manage external collaboration settings (外部コラボレーション設定の管理)]** を選択します。
4. **[Collaboration restrictions]\(コラボレーション制限\)** の **[Allow invitations only to the specified domains (most restrictive)]\(指定したドメインへの招待を許可 (制限が最も厳しい)\)** を選択します。
5. **[TARGET DOMAINS]\(ターゲット ドメイン\)** で、許可するドメインの 1 つの名前を入力します。 複数ドメインの場合は、それぞれのドメインを新しい行に入力します。 例:

   ![追加したドメインと共に許可オプションを表示する](./media/allow-deny-list/AllowListSettings.png)
 
6. 終了したら **[保存]** をクリックします。

ポリシーの設定後、許可リストにないドメインからユーザーを招待しようとすると、ユーザーのドメインは現在、招待ポリシーによってブロックされていることを示すメッセージが表示されます。

### <a name="switch-from-allow-to-deny-list-and-vice-versa"></a>許可リストと拒否リストの切り替え 

あるポリシーから別のポリシーに切り替えると、既存のポリシー構成は破棄されます。 切り替えを実行する前に、構成の詳細情報をバックアップしてください。 

## <a name="set-the-allow-or-deny-list-policy-using-powershell"></a>PowerShell を使用して許可リストまたは拒否リストのポリシーを設定する

### <a name="prerequisite"></a>前提条件

PowerShell を使用して許可リストまたは拒否リストを設定するには、Windows PowerShell 用 Azure Active Directory モジュールのプレビュー バージョンをインストールする必要があります。 具体的には、AzureADPreview モジュール バージョン 2.0.0.98 またはそれ以降をインストールします。

モジュールのバージョン (およびモジュールがインストールされているかどうか) を確認するには:
 
1. 管理者特権で Windows PowerShell を開きます (管理者として実行)。 
2. 次のコマンドを実行し、コンピューターに任意のバージョンの Windows PowerShell 用 Azure Active Directory モジュールがインストールされているかどうかを確認します。

   ```powershell  
   Get-Module -ListAvailable AzureAD*
   ```

モジュールがインストールされていない場合や、必要なバージョンがない場合は、次のいずれかを行います。

- 結果が返されない場合は、次のコマンドを実行し、AzureADPreview モジュールの最新バージョンをインストールします。
  
   ```powershell  
   Install-Module AzureADPreview
   ```
- 結果に AzureAD モジュールだけが表示される場合は、次のコマンドを実行し、AzureADPreview モジュールをインストールします。 

   ```powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ```
- 結果に AzureADPreview モジュールだけが表示され、バージョンが 2.0.0.98 より前の場合は、次のコマンドを実行してモジュールを更新します。 

   ```powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
   ```

- 結果に AzureAD モジュールと AzureADPreview モジュールの両方が表示され、AzureADPreview モジュールのバージョンが 2.0.0.98 より前の場合は、次のコマンドを実行してモジュールを更新します。 

   ```powershell 
   Uninstall-Module AzureAD 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
    ```

### <a name="use-the-azureadpolicy-cmdlets-to-configure-the-policy"></a>AzureADPolicy コマンドレットを使用してポリシーを構成する

許可リストまたは拒否リストを作成するには、[New-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview) コマンドレットを使用します。 次の例は、"live.com" ドメインをブロックする拒否リストの設定方法を示しています。

```powershell 
$policyValue = @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}")

New-AzureADPolicy -Definition $policyValue -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
```

次は同じ例を示していますが、ポリシーの定義がインラインになっています。

```powershell  
New-AzureADPolicy -Definition @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}") -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
```

許可リストまたは拒否リストのポリシーを設定するには、[Set-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/set-azureadpolicy?view=azureadps-2.0-preview) コマンドレットを使用します。 例:

```powershell   
Set-AzureADPolicy -Definition $policyValue -Id $currentpolicy.Id 
```

ポリシーを取得するには、[Get-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview) コマンドレットを使用します。 例:

```powershell
$currentpolicy = Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy'} | select -First 1 
```

ポリシーを削除するには、[Remove-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/remove-azureadpolicy?view=azureadps-2.0-preview) コマンドレットを使用します。 例:

```powershell
Remove-AzureADPolicy -Id $currentpolicy.Id 
```

## <a name="next-steps"></a>次の手順

- Azure AD B2B の概要については、「[Azure AD B2B コラボレーションとは](what-is-b2b.md)」を参照してください。
- 条件付きアクセスと B2B コラボレーションについては、「[B2B コラボレーション ユーザーの条件付きアクセス](conditional-access.md)」を参照してください。



