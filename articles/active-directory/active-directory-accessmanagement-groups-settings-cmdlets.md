---
title: "Azure Active Directory コマンドレットを使用したグループ設定の構成 | Microsoft Docs"
description: "Azure Active Directory コマンドレットを使用して、グループの設定を管理する方法です"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 9f2090e6-3af4-4f07-bbb2-1d18dae89b73
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro;
ms.translationtype: HT
ms.sourcegitcommit: 760543dc3880cb0dbe14070055b528b94cffd36b
ms.openlocfilehash: 2ee71536257d4349fdf8d80bdcb1899a1d244293
ms.contentlocale: ja-jp
ms.lasthandoff: 08/10/2017

---
# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>グループの設定を構成するための Azure Active Directory コマンドレット

> [!IMPORTANT]
> このコンテンツは、Office 365 グループにのみ適用されます。 ユーザーにセキュリティ グループの作成を許可するには、「[Set-MSOLCompanySettings](https://docs.microsoft.com/en-us/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)」に記載されているように `Set-MSOLCompanySettings -UsersPermissionToCreateGroupsEnabled $True` を設定します。 

Office365 グループの設定は、Settings オブジェクトおよび SettingsTemplate オブジェクトを使用して構成します。 最初は、ディレクトリには Settings オブジェクトが表示されません。これは、ディレクトリが既定の設定で構成されているためです。 既定の設定を変更するには、Settings テンプレートを使用して新しい Settings オブジェクトを作成する必要があります。 Settings テンプレートは、Microsoft によって定義されます。 複数の Settings テンプレートがサポートされています。 ディレクトリの Office 365 グループ設定を構成するには、"Group.Unified" という名前のテンプレートを使用します。 1 つのグループの Office 365 グループ設定を構成するには、"Group.Unified.Guest" という名前のテンプレートを使用します。 このテンプレートは、Office 365 グループへのゲストのアクセスを管理するために使用します。 

コマンドレットは、Azure Active Directory PowerShell V2 モジュールの一部です。 モジュールをダウンロードしてお使いのコンピューターにインストールする手順については、「[Azure Active Directory PowerShell Version 2](https://docs.microsoft.com/powershell/azuread/)」を参照してください。 公開版のバージョン 2 モジュールは、[PowerShell ギャラリー](https://www.powershellgallery.com/packages/AzureAD/)からインストールできます。

## <a name="retrieve-a-specific-settings-value"></a>特定の設定値の取得
取得する設定の名前がわかっている場合、以下のコマンドレットを使って、現在の設定値を取得することができます。 この例では、"UsageGuidelinesUrl" という名前の設定の値を取得しています。 ディレクトリの設定と名前の詳細については、この記事の後の方で説明します。

```powershell
(Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value UsageGuidelinesUrl -EQ
```

## <a name="create-settings-at-the-directory-level"></a>ディレクトリ レベルでの設定の作成
次の手順では、ディレクトリ内のすべての Office 365 グループ (統合グループ) に適用される設定をディレクトリ レベルで作成します。

1. DirectorySettings コマンドレットでは、使用する SettingsTemplate の ID を指定する必要があります。 使用する ID を把握していない場合、このコマンドレットでは、すべての Settings テンプレートの一覧が返されます。
  
  ```
  PS C:> Get-AzureADDirectorySettingTemplate
  ```
  このコマンドレットを呼び出すと、使用可能なすべてのテンプレートが返されます。
  
  ```
  Id                                   DisplayName         Description
  --                                   -----------         -----------
  62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified       ...
  08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest Settings for a specific Unified Group
  16933506-8a8d-4f0d-ad58-e1db05a5b929 Company.BuiltIn     Setting templates define the different settings that can be used for the associ...
  4bc7f740-180e-4586-adb6-38b2e9024e6b Application...
  898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy       Settings ...
  5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule       Settings ...
  ```
2. 使用ガイドラインの URL を追加するには、まず使用ガイドラインの URL 値を定義する SettingsTemplate オブジェクト、つまり、Group.Unified テンプレートを取得する必要があります。
  
  ```
  $Template = Get-AzureADDirectorySettingTemplate -Id 62375ab9-6b52-47ed-826b-58e47e0e304b
  ```
3. 次に、そのテンプレートに基づいて新しい Settings オブジェクトを作成します。
  
  ```
  $Setting = $template.CreateDirectorySetting()
  ```  
4. その後、使用ガイドラインの値を更新します。
  
  ```
  $setting["UsageGuidelinesUrl"] = "<https://guideline.com>"
  ```  
5. 最後に、設定を適用します。
  
  ```
  New-AzureADDirectorySetting -DirectorySetting $setting
  ```

このコマンドレットが正常に完了すると、新しい Settings オブジェクトの ID が返されます。
  ```
  Id                                   DisplayName TemplateId                           Values
  --                                   ----------- ----------                           ------
  c391b57d-5783-4c53-9236-cefb5c6ef323             62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
  ```
Group.Unified SettingsTemplate で定義される設定は次のとおりです。

| **設定** | **説明** |
| --- | --- |
|  <ul><li>EnableGroupCreation<li>型: ブール<li>既定: True |ディレクトリで統合グループの作成を許可するかどうかを示すフラグ。 |
|  <ul><li>GroupCreationAllowedGroupId<li>型: 文字列<li>既定値: “” |EnableGroupCreation == false の場合でも統合グループの作成がメンバーに許可されているセキュリティ グループの GUID。 |
|  <ul><li>UsageGuidelinesUrl<li>型: 文字列<li>既定値: “” |グループ使用ガイドラインへのリンク。 |
|  <ul><li>ClassificationDescriptions<li>型: 文字列<li>既定値: “” | 分類に関する説明のコンマ区切りリスト。 |
|  <ul><li>DefaultClassification<li>型: 文字列<li>既定値: “” | 何も指定されていない場合にグループの既定の分類として使用される分類。|
|  <ul><li>PrefixSuffixNamingRequirement<li>型: 文字列<li>既定値: “” |まだ実装されていません
|  <ul><li>AllowGuestsToBeGroupOwner<li>型: ブール<li>既定: False | ゲスト ユーザーがグループの所有者になれるかどうかを示すブール値。 |
|  <ul><li>AllowGuestsToAccessGroups<li>型: ブール<li>既定: True | ゲスト ユーザーが統合グループのコンテンツにアクセスできるかどうかを示すブール値。 |
|  <ul><li>GuestUsageGuidelinesUrl<li>型: 文字列<li>既定値: “” | ゲストの使用ガイドラインへのリンクの URL。 |
|  <ul><li>AllowToAddGuests<li>型: ブール<li>既定: True | このディレクトリにゲストを追加することが許可されているかどうかを示すブール値。|
|  <ul><li>ClassificationList<li>型: 文字列<li>既定値: “” |統合グループに適用できる有効な分類の値のコンマ区切りの一覧。 |
|  <ul><li>EnableGroupCreation<li>型: ブール<li>既定: True | 管理者以外のユーザーが新しい統合グループを作成できるかどうかを示すブール値。 |


## <a name="read-settings-at-the-directory-level"></a>ディレクトリ レベルでの設定の読み取り
次の手順では、ディレクトリ内のすべての Office グループに適用される設定をディレクトリ レベルで読み取ります。

1. 既存のディレクトリ設定をすべて読み取ります。
  ```
  Get-AzureADDirectorySetting -All $True
  ```
  このコマンドレットでは、すべてのディレクトリ設定の一覧が返されます。
  ```
  Id                                   DisplayName   TemplateId                           Values
  --                                   -----------   ----------                           ------
  c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
  ```

2. 特定のグループの設定をすべて読み取ります。
  ```
  Get-AzureADObjectSetting -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -TargetType Groups
  ```

3. Settings Id GUID を使用して、特定のディレクトリの Settings オブジェクトのすべてのディレクトリ設定値を読み取ります。
  ```
  (Get-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323).values
  ```
  このコマンドレットでは、この特定のグループのこの Settings オブジェクトの名前と値が返されます。
  ```
  Name                          Value
  ----                          -----
  ClassificationDescriptions
  DefaultClassification
  PrefixSuffixNamingRequirement
  AllowGuestsToBeGroupOwner     False 
  AllowGuestsToAccessGroups     True
  GuestUsageGuidelinesUrl
  GroupCreationAllowedGroupId
  AllowToAddGuests              True
  UsageGuidelinesUrl            <https://guideline.com>
  ClassificationList
  EnableGroupCreation           True
  ```

## <a name="update-settings-for-a-specific-group"></a>特定のグループの設定の更新

1. "Groups.Unified.Guest" という名前の Settings テンプレートを検索します。
  ```
  Get-AzureADDirectorySettingTemplate
  
  Id                                   DisplayName            Description
  --                                   -----------            -----------
  62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified          ...
  08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest    Settings for a specific Unified Group
  4bc7f740-180e-4586-adb6-38b2e9024e6b Application            ...
  898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy Settings ...
  5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule Settings ...
  ```
2. Groups.Unified.Guest テンプレートのテンプレート オブジェクトを取得します。
  ```
  $Template = Get-AzureADDirectorySettingTemplate -Id 08d542b9-071f-4e16-94b0-74abb372e3d9
  ```
3. テンプレートから新しい Settings オブジェクトを作成します。
  ```
  $Setting = $Template.CreateDirectorySetting()
  ```

4. この設定を必要な値に設定します。
  ```
  $Setting["AllowToAddGuests"]=$False
  ```
5. ディレクトリ内に、必要なグループ用の新しい設定を作成します。
  ```
  New-AzureADObjectSetting -TargetType Groups -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -DirectorySetting $Setting
  
  Id                                   DisplayName TemplateId                           Values
  --                                   ----------- ----------                           ------
  25651479-a26e-4181-afce-ce24111b2cb5             08d542b9-071f-4e16-94b0-74abb372e3d9 {class SettingValue {...
  ```

## <a name="update-settings-at-the-directory-level"></a>ディレクトリ レベルでの設定の更新

次の手順では、ディレクトリ内のすべての統合グループに適用される設定をディレクトリ レベルで更新します。 以下の例では、ディレクトリ内に Settings オブジェクトが既に存在することを前提としています。

1. 既存の Settings オブジェクトを検索します。
  ```
  Get-AzureADDirectorySetting | Where-object -Property Displayname -Value "Group.Unified" -EQ
  
  Id                                   DisplayName   TemplateId                           Values
  --                                   -----------   ----------                           ------
  c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
  
  $setting = Get-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323
  ```
2. 値を更新します。
  
  ```
  $Setting["AllowToAddGuests"] = "false"
  ```
3. 設定を更新します。
  
  ```
  Set-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323 -DirectorySetting $Setting
  ```

## <a name="remove-settings-at-the-directory-level"></a>ディレクトリ レベルでの設定の削除
次の手順では、ディレクトリ内のすべての Office グループに適用される設定をディレクトリ レベルで削除します。
  ```
  Remove-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323c
  ```

## <a name="cmdlet-syntax-reference"></a>コマンドレット構文リファレンス
Azure Active Directory PowerShell のその他のドキュメントについては、 [Azure Active Directory コマンドレット](/powershell/azure/install-adv2?view=azureadps-2.0)を参照してください。

## <a name="additional-reading"></a>その他の情報

* [Azure Active Directory グループによるリソースへのアクセス管理](active-directory-manage-groups.md)
* [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)

