---
title: Office 365 グループの名前付けポリシーの追加に関するクイック スタート - Azure Active Directory | Microsoft Docs
description: Azure Active Directory で新しいユーザーを追加する方法または既存のユーザーを削除する方法について説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: quickstart
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4105fa17041c7cefd1387d1ee50c177b8c55fc9
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58651288"
---
# <a name="quickstart-naming-policy-for-groups-in-azure-active-directory"></a>クイック スタート:Azure Active Directory におけるグループの名前付けポリシー

このクイック スタートでは、Azure Active Directory (Azure AD) テナントに存在するグループの並べ替えと検索がしやすいよう、ユーザーによって作成される Office 365 グループの名前付けポリシーをテナントに設定します。 名前付けポリシーの使用例を次に示します。

* グループ、メンバーシップ、地理的地域の機能やグループ作成者の職務を伝える。
* アドレス帳でグループを分類しやすいようにする。
* グループ名やグループ エイリアスで特定の単語の使用を禁止する。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="install-powershell-cmdlets"></a>PowerShell コマンドレットのインストール

PowerShell コマンドを実行する前に、古いバージョンの Windows PowerShell 用 Azure Active Directory PowerShell for Graph モジュールをアンインストールし、[Azure Active Directory PowerShell for Graph - パブリック プレビュー リリース 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137) をインストールする必要があります。 

1. 管理者として Windows PowerShell アプリを開きます。
2. 以前のバージョンの AzureADPreview をアンインストールします。
  

   ```powershell
   Uninstall-Module AzureADPreview
   ```

3. 最新バージョンの AzureADPreview をインストールします。
  

   ```powershell
   Install-Module AzureADPreview
   ```

   信頼されていないリポジトリへのアクセスに関するメッセージが表示されたら、「**Y**」と入力します。新しいモジュールのインストールには数分かかる場合があります。

## <a name="set-up-naming-policy"></a>名前付けポリシーの設定

### <a name="step-1-sign-in-using-powershell-cmdlets"></a>手順 1: PowerShell のコマンドレットを使用してサインインする

1. Windows PowerShell アプリを開きます。 昇格された特権は必要ありません。

2. 次のコマンドを実行して、コマンドレットを実行する準備をします。
  

   ```powershell
   Import-Module AzureADPreview
   Connect-AzureAD
   ```

   表示された **[アカウントにサインイン]** 画面で、管理者アカウントとパスワードを入力してサービスに接続し、**[サインイン]** を選択します。

3. 「[グループの設定を構成するための Azure Active Directory コマンドレット](groups-settings-cmdlets.md)」の手順に従って、このテナントのグループ設定を作成します。

### <a name="step-2-view-the-current-settings"></a>手順 2: 現在の設定を表示する

1. 現在の名前付けポリシーの設定を表示します。
  

   ```powershell
   $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | Where-Object -Property DisplayName -Value "Group.Unified" -EQ).id
   ```

  
2. 現在のグループ設定を表示します。
  

   ```powershell
   $Setting.Values
   ```

  

### <a name="step-3-set-the-naming-policy-and-any-custom-blocked-words"></a>手順 3: 名前付けポリシーとカスタム禁止単語を設定する

1. Azure AD PowerShell でグループ名のプレフィックスとサフィックスを設定します。 機能を適切に動作させるには、設定に [GroupName] を含める必要があります。
  

   ```powershell
   $Setting["PrefixSuffixNamingRequirement"] =“GRP_[GroupName]_[Department]"
   ```

  
2. 制限するカスタム禁止単語を設定します。 次の例は、独自のカスタム単語を追加する方法を示しています。
  

   ```powershell
   $Setting["CustomBlockedWordsList"]=“Payroll,CEO,HR"
   ```

  
3. 次の例のように、新しいポリシーの設定を保存して有効にします。
  

   ```powershell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | Where-Object -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```

  
これで終了です。 名前付けポリシーが設定され、カスタム禁止単語が追加されました。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

1. Azure AD PowerShell でグループ名のプレフィックスとサフィックスを空にします。
  

   ```powershell
   $Setting["PrefixSuffixNamingRequirement"] =""
   ```

  
2. カスタム禁止単語を空にします。
  

   ```powershell
   $Setting["CustomBlockedWordsList"]=""
   ```

  
3. 設定を保存します。
  

   ```powershell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | Where-Object -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```

## <a name="next-steps"></a>次の手順

このクイック スタートでは、PowerShell のコマンドレットを使って、Azure AD テナントの名前付けポリシーを設定する方法について説明しました。

技術的な制約やカスタム禁止単語の追加、Office 365 の各アプリ間のエンド ユーザー エクスペリエンスについて詳しくは、次の記事に進んで名前付けポリシーの詳細をご覧ください。
> [!div class="nextstepaction"]
> [名前付けポリシーの詳細](groups-naming-policy.md)