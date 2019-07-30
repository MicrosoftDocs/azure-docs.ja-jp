---
title: PowerShell を使用したグループ設定の構成 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory コマンドレットを使用して、グループの設定を管理する方法です
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 02/26/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5ccc4ef6c095eacd29590504d46756ead856574
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67058605"
---
# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>グループの設定を構成するための Azure Active Directory コマンドレット
この記事では、Azure Active Directory (Azure AD) PowerShell コマンドレットを使用して、グループを作成し、更新する手順を説明します。 このコンテンツは、Office 365 グループ (統合グループと呼ばれることもあります) にのみ適用されます。 

> [!IMPORTANT]
> 一部の設定には、Azure Active Directory Premium P1 ライセンスが必要です。 詳細については、「[テンプレート設定](#template-settings)」の表を参照してください。

管理者でないユーザーがセキュリティ グループを作成できないようにするには、「[Set-MSOLCompanySettings](https://docs.microsoft.com/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)」に記載されているように  `Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False` を設定します。 

Office365 グループの設定は、Settings オブジェクトおよび SettingsTemplate オブジェクトを使用して構成します。 最初は、ディレクトリには Settings オブジェクトが表示されません。これは、ディレクトリが既定の設定で構成されているためです。 既定の設定を変更するには、Settings テンプレートを使用して新しい Settings オブジェクトを作成する必要があります。 Settings テンプレートは、Microsoft によって定義されます。 複数の Settings テンプレートがサポートされています。 ディレクトリの Office 365 グループ設定を構成するには、"Group.Unified" という名前のテンプレートを使用します。 1 つのグループの Office 365 グループ設定を構成するには、"Group.Unified.Guest" という名前のテンプレートを使用します。 このテンプレートは、Office 365 グループへのゲストのアクセスを管理するために使用します。 

コマンドレットは、Azure Active Directory PowerShell V2 モジュールの一部です。 モジュールをダウンロードしてお使いのコンピューターにインストールする手順については、「[Azure Active Directory PowerShell Version 2](https://docs.microsoft.com/powershell/azuread/)」を参照してください。 公開版のバージョン 2 モジュールは、[PowerShell ギャラリー](https://www.powershellgallery.com/packages/AzureAD/)からインストールできます。



## <a name="create-settings-at-the-directory-level"></a>ディレクトリ レベルでの設定の作成
次の手順では、ディレクトリ内のすべての Office 365 グループに適用される設定をディレクトリ レベルで作成します。 Get-AzureADDirectorySettingTemplate コマンドレットは、[グラフ用の Azure AD PowerShell プレビュー モジュール](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137)でのみ使用できます。

1. DirectorySettings コマンドレットでは、使用する SettingsTemplate の ID を指定する必要があります。 使用する ID を把握していない場合、このコマンドレットでは、すべての Settings テンプレートの一覧が返されます。
  
   ```powershell
   Get-AzureADDirectorySettingTemplate
   ```
   このコマンドレットを呼び出すと、使用可能なすべてのテンプレートが返されます。
  
   ```powershell
   Id                                   DisplayName         Description
   --                                   -----------         -----------
   62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified       ...
   08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest Settings for a specific Office 365 group
   16933506-8a8d-4f0d-ad58-e1db05a5b929 Company.BuiltIn     Setting templates define the different settings that can be used for the associ...
   4bc7f740-180e-4586-adb6-38b2e9024e6b Application...
   898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy       Settings ...
   5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule       Settings ...
   ```
2. 使用ガイドラインの URL を追加するには、まず使用ガイドラインの URL 値を定義する SettingsTemplate オブジェクト、つまり、Group.Unified テンプレートを取得する必要があります。
  
   ```powershell
   $Template = Get-AzureADDirectorySettingTemplate -Id 62375ab9-6b52-47ed-826b-58e47e0e304b
   ```
3. 次に、そのテンプレートに基づいて新しい Settings オブジェクトを作成します。
  
   ```powershell
   $Setting = $template.CreateDirectorySetting()
   ```  
4. その後、使用ガイドラインの値を更新します。
  
   ```powershell
   $Setting["UsageGuidelinesUrl"] = "https://guideline.example.com"
   ```  
5. 次に設定を適用します。
  
   ```powershell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
6. 以下を使用して値を読み取ることができます。

   ```powershell
   $Setting.Values
   ```  
## <a name="update-settings-at-the-directory-level"></a>ディレクトリ レベルでの設定の更新
設定テンプレートの UsageGuideLinesUrl の値を更新するには、上記の手順 4 を使用して URL を編集してから、手順 5 を実行して新しい値を設定します。

UsageGuideLinesUrl の値を削除するには、上記の手順 4 を使用して URL が空の文字列になるように編集します。

   ```powershell
   $Setting["UsageGuidelinesUrl"] = ""
   ```  
次に、手順 5 を実行して新しい値を設定します。

## <a name="template-settings"></a>テンプレート設定
Group.Unified SettingsTemplate で定義される設定は次のとおりです。 特に記載のない限り、これらの機能には、Azure Active Directory Premium P1 ライセンスが必要です。 

| **設定** | **説明** |
| --- | --- |
|  <ul><li>EnableGroupCreation<li>型: Boolean<li>既定値はTrue |ディレクトリで管理者以外のユーザーによる Office 365 グループの作成を許可するかどうかを示すフラグ。 この設定には、Azure Active Directory Premium P1 ライセンスは必要ありません。|
|  <ul><li>GroupCreationAllowedGroupId<li>型: string<li>既定値: “” |EnableGroupCreation == false の場合でも Office 365 グループの作成がメンバーに許可されているセキュリティ グループの GUID。 |
|  <ul><li>UsageGuidelinesUrl<li>型: string<li>既定値: “” |グループ使用ガイドラインへのリンク。 |
|  <ul><li>ClassificationDescriptions<li>型: string<li>既定値: “” | 分類に関する説明のコンマ区切りリスト。 ClassificationDescriptions の値は、次の形式でのみ有効です。<br>$setting["ClassificationDescriptions"] ="Classification:Description,Classification:Description"<br>ここで、Classification は ClassificationList 内の文字列と一致します。|
|  <ul><li>DefaultClassification<li>型: string<li>既定値: “” | 何も指定されていない場合にグループの既定の分類として使用される分類。|
|  <ul><li>PrefixSuffixNamingRequirement<li>型: string<li>既定値: “” | Office 365 グループ用に構成された名前付け規則を定義する文字列。最大文字数は 64 文字です。 詳細については、[Office 365 グループへの名前付けポリシーの適用](groups-naming-policy.md)に関するページを参照してください。 |
| <ul><li>CustomBlockedWordsList<li>型: string<li>既定値: “” | ユーザーによるグループ名または別名での使用が許可されていないフレーズのコンマ区切りの文字列。 詳細については、[Office 365 グループへの名前付けポリシーの適用](groups-naming-policy.md)に関するページを参照してください。 |
| <ul><li>EnableMSStandardBlockedWords<li>型: Boolean<li>既定値は"False" | 使用しないでください
|  <ul><li>AllowGuestsToBeGroupOwner<li>型: Boolean<li>既定値はFalse | ゲスト ユーザーがグループの所有者になれるかどうかを示すブール値。 |
|  <ul><li>AllowGuestsToAccessGroups<li>型: Boolean<li>既定値はTrue | ゲスト ユーザーが Office 365 グループのコンテンツにアクセスできるかどうかを示すブール値。  この設定には、Azure Active Directory Premium P1 ライセンスは必要ありません。|
|  <ul><li>GuestUsageGuidelinesUrl<li>型: string<li>既定値: “” | ゲストの使用ガイドラインへのリンクの URL。 |
|  <ul><li>AllowToAddGuests<li>型: Boolean<li>既定値はTrue | このディレクトリにゲストを追加することが許可されているかどうかを示すブール値。|
|  <ul><li>ClassificationList<li>型: string<li>既定値: “” |Office 365 グループに適用できる有効な分類の値のコンマ区切りの一覧。 |

## <a name="example-configure-guest-policy-for-groups-at-the-directory-level"></a>例:ディレクトリ レベルでグループのゲスト ポリシーを構成する
1. すべての設定テンプレートを取得します。
   ```powershell
   Get-AzureADDirectorySettingTemplate
   ```
2. ディレクトリ レベルでグループのゲスト ポリシーを設定するには、Group.Unified テンプレートが必要です。
   ```powershell
   $Template = Get-AzureADDirectorySettingTemplate -Id 62375ab9-6b52-47ed-826b-58e47e0e304b
   ```
3. 次に、そのテンプレートに基づいて新しい Settings オブジェクトを作成します。
  
   ```powershell
   $Setting = $template.CreateDirectorySetting()
   ```  
4. 次に AllowToAddGuests 設定を更新します
   ```powershell
   $Setting["AllowToAddGuests"] = $False
   ```  
5. 次に設定を適用します。
  
   ```powershell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
6. 以下を使用して値を読み取ることができます。

   ```powershell
   $Setting.Values
   ```   

## <a name="read-settings-at-the-directory-level"></a>ディレクトリ レベルでの設定の読み取り

取得する設定の名前がわかっている場合、以下のコマンドレットを使って、現在の設定値を取得することができます。 この例では、"UsageGuidelinesUrl" という名前の設定の値を取得しています。 

   ```powershell
   (Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value UsageGuidelinesUrl -EQ
   ```
次の手順では、ディレクトリ内のすべての Office グループに適用される設定をディレクトリ レベルで読み取ります。

1. 既存のディレクトリ設定をすべて読み取ります。
   ```powershell
   Get-AzureADDirectorySetting -All $True
   ```
   このコマンドレットでは、すべてのディレクトリ設定の一覧が返されます。
   ```powershell
   Id                                   DisplayName   TemplateId                           Values
   --                                   -----------   ----------                           ------
   c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
   ```

2. 特定のグループの設定をすべて読み取ります。
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -TargetType Groups
   ```

3. Settings ID GUID を使用して、特定のディレクトリの Settings オブジェクトのすべてのディレクトリ設定値を読み取ります。
   ```powershell
   (Get-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323).values
   ```
   このコマンドレットでは、この特定のグループのこの Settings オブジェクトの名前と値が返されます。
   ```powershell
   Name                          Value
   ----                          -----
   ClassificationDescriptions
   DefaultClassification
   PrefixSuffixNamingRequirement
   CustomBlockedWordsList        
   AllowGuestsToBeGroupOwner     False 
   AllowGuestsToAccessGroups     True
   GuestUsageGuidelinesUrl
   GroupCreationAllowedGroupId
   AllowToAddGuests              True
   UsageGuidelinesUrl            https://guideline.example.com
   ClassificationList
   EnableGroupCreation           True
   ```

## <a name="remove-settings-at-the-directory-level"></a>ディレクトリ レベルでの設定の削除
次の手順では、ディレクトリ内のすべての Office グループに適用される設定をディレクトリ レベルで削除します。
   ```powershell
   Remove-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323c
   ```

## <a name="create-settings-for-a-specific-group"></a>特定のグループに対する設定を作成する

1. "Groups.Unified.Guest" という名前の Settings テンプレートを検索します。
   ```powershell
   Get-AzureADDirectorySettingTemplate
  
   Id                                   DisplayName            Description
   --                                   -----------            -----------
   62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified          ...
   08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest    Settings for a specific Office 365 group
   4bc7f740-180e-4586-adb6-38b2e9024e6b Application            ...
   898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy Settings ...
   5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule Settings ...
   ```
2. Groups.Unified.Guest テンプレートのテンプレート オブジェクトを取得します。
   ```powershell
   $Template1 = Get-AzureADDirectorySettingTemplate -Id 08d542b9-071f-4e16-94b0-74abb372e3d9
   ```
3. テンプレートから新しい Settings オブジェクトを作成します。
   ```powershell
   $SettingCopy = $Template1.CreateDirectorySetting()
   ```

4. この設定を必要な値に設定します。
   ```powershell
   $SettingCopy["AllowToAddGuests"]=$False
   ```
5. この設定を適用するグループの ID を取得します。
   ```powershell
   $groupID= (Get-AzureADGroup -SearchString "YourGroupName").ObjectId
   ```
6. ディレクトリ内に、必要なグループ用の新しい設定を作成します。
   ```powershell
   New-AzureADObjectSetting -TargetType Groups -TargetObjectId $groupID -DirectorySetting $SettingCopy
   ```
7. 設定を確認するには、次のコマンドを実行します。
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups | fl Values
   ```

## <a name="update-settings-for-a-specific-group"></a>特定のグループの設定の更新
1. 設定を更新するグループの ID を取得します。
   ```powershell
   $groupID= (Get-AzureADGroup -SearchString "YourGroupName").ObjectId
   ```
2. グループの設定を取得します。
   ```powershell
   $Setting = Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups
   ```
3. 必要に応じて、グループの設定を更新します。たとえば次のようにします。
   ```powershell
   $Setting["AllowToAddGuests"] = $True
   ```
4. 次に、この特定のグループに対する設定の ID を取得します。
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups
   ```
   次のような応答が取得されます。
   ```powershell
   Id                                   DisplayName            TemplateId                             Values
   --                                   -----------            -----------                            ----------
   2dbee4ca-c3b6-4f0d-9610-d15569639e1a Group.Unified.Guest    08d542b9-071f-4e16-94b0-74abb372e3d9   {class SettingValue {...
   ```
5. その後、この設定の新しい値を設定できます。
   ```powershell
   Set-AzureADObjectSetting -TargetType Groups -TargetObjectId $groupID -Id 2dbee4ca-c3b6-4f0d-9610-d15569639e1a -DirectorySetting $Setting
   ```
6. 設定の値を読み取って、それが正しく更新されているかどうかを確認できます。
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups | fl Values
   ```

## <a name="cmdlet-syntax-reference"></a>コマンドレット構文リファレンス
Azure Active Directory PowerShell のその他のドキュメントについては、 [Azure Active Directory コマンドレット](/powershell/azure/install-adv2?view=azureadps-2.0)を参照してください。

## <a name="additional-reading"></a>その他の情報

* [Azure Active Directory グループによるリソースへのアクセス管理](../fundamentals/active-directory-manage-groups.md)
* [オンプレミス ID と Azure Active Directory の統合](../hybrid/whatis-hybrid-identity.md)
