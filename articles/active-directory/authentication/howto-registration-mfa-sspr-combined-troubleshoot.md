---
title: 統合された登録の使用のトラブルシューティング - Azure Active Directory
description: Azure AD Multi-Factor Authentication とパスワード リセットのセルフサービスの結合された登録のトラブルシューティングを行う (プレビュー)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: ab7c38d23cb1f05e07488810640aeb791ded3d4a
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74847390"
---
# <a name="troubleshooting-combined-security-information-registration-preview"></a>結合されたセキュリティ情報の登録のトラブルシューティング (プレビュー)

この記事の情報は、統合された登録エクスペリエンスのユーザーによって報告された問題をトラブルシューティングしている管理者を支援するように考慮されています。

|     |
| --- |
| Azure Multi-Factor Authentication と Azure Active Directory (Azure AD) セルフサービスのパスワード リセットのための統合されたセキュリティ情報の登録は、Azure AD のパブリック プレビュー機能です。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。|
|     |

## <a name="audit-logs"></a>監査ログ

統合された登録に関して記録されたイベントは、Azure AD 監査ログの [認証方法] カテゴリに分類されます。

![登録イベントを示している Azure AD の監査ログインターフェイス](media/howto-registration-mfa-sspr-combined-troubleshoot/combined-security-info-audit-log.png)

次の表は、統合された登録によって生成されたすべての監査イベントの一覧を示しています。

| アクティビティ | Status | 理由 | 説明 |
| --- | --- | --- | --- |
| ユーザーが必要なすべてのセキュリティ情報を登録した | Success | ユーティリティが必要なすべてのセキュリティ情報を登録しました。 | このイベントは、ユーザーが登録を正常に完了したときに発生します。|
| ユーザーが必要なすべてのセキュリティ情報を登録した | 失敗 | ユーザーがセキュリティ情報の登録を取り消しました。 | このイベントは、ユーザーが中断モードから登録を取り消したときに発生します。|
| ユーザーがセキュリティ情報を登録した | Success | ユーザーが*方法*を登録しました。 | このイベントは、ユーザーが個々の方法を登録したときに発生します。 *方法*は、認証アプリ、電話、電子メール、セキュリティの質問、アプリ パスワード、代替の電話などにすることができます。| 
| ユーザーがセキュリティ情報を確認した | Success | ユーザーが正常にセキュリティ情報を確認しました。 | このイベントは、ユーザーがセキュリティ情報の確認ページで **[良い]** を選択したときに発生します。|
| ユーザーがセキュリティ情報を確認した | 失敗 | ユーザーがセキュリティ情報を確認できませんでした。 | このイベントは、ユーザーがセキュリティ情報の確認ページで **[良い]** を選択したが、バックエンドで何かが失敗したときに発生します。|
| ユーザーがセキュリティ問題を削除した | Success | ユーザーが*方法*を削除しました。 | このイベントは、ユーザーが個々の方法を削除したときに発生します。 *方法*は、認証アプリ、電話、電子メール、セキュリティの質問、アプリ パスワード、代替の電話などにすることができます。|
| ユーザーがセキュリティ問題を削除した | 失敗 | ユーザーが*方法*を削除できませんでした。 | このイベントは、ユーザーが方法を削除しようとしたが、その試みが何らかの理由で失敗したときに発生します。 *方法*は、認証アプリ、電話、電子メール、セキュリティの質問、アプリ パスワード、代替の電話などにすることができます。|
| ユーザーが既定のセキュリティ情報を変更した | Success | ユーザーが*方法*の既定のセキュリティ情報を変更しました。 | このイベントは、ユーザーが既定の方法を変更したときに発生します。 *方法*は、認証アプリの通知、認証アプリまたはトークンからのコード、+X XXXXXXXXXX の呼び出し、+X XXXXXXXXX へのコードのテキスト送信などにすることができます。|
| ユーザーが既定のセキュリティ情報を変更した | 失敗 | ユーザーが*方法*の既定のセキュリティ情報を変更できませんでした。 | このイベントは、ユーザーが既定の方法を変更しようとしたが、その試みが何らかの理由で失敗したときに発生します。 *方法*は、認証アプリの通知、認証アプリまたはトークンからのコード、+X XXXXXXXXXX の呼び出し、+X XXXXXXXXX へのコードのテキスト送信などにすることができます。|

## <a name="troubleshooting-interrupt-mode"></a>割り込みモードのトラブルシューティング

| 症状 | トラブルシューティングの手順 |
| --- | --- |
| 予期していた方法が表示されません。 | 1.ユーザーに Azure AD 管理者ロールがあるかどうかを確認します。 ある場合は、SSPR 管理者ポリシーの違いを確認します。 <br> 2.ユーザーが中断される理由が、Multi-Factor Authentication 登録の適用か SSPR 登録の適用であるかを確認します。 どの方法が表示される必要があるかを判断するために、「統合された登録のモード」で[フローチャート](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md#combined-registration-modes)を確認します。 <br> 3.Multi-Factor Authentication または SSPR ポリシーがどのくらい最近に変更されたかを判定します。 最近、変更が加えられた場合、更新されたポリシーが反映されるまでしばらく時間がかかることがあります。|

## <a name="troubleshooting-manage-mode"></a>管理モードのトラブルシューティング

| 症状 | トラブルシューティングの手順 |
| --- | --- |
| 特定の方法を追加する選択肢がありません。 | 1.その方法が Multi-Factor Authentication または SSPR に対して有効になっているかどうかを判定します。 <br> 2.その方法が有効になっている場合は、ポリシーを再度保存し、1 ～ 2 時間待ってから再テストします。 <br> 3.方法が有効になっている場合は、ユーザーが設定を許可されている、その最大数の方法を既に設定していないことを確認します。|

## <a name="disable-combined-registration"></a>結合された登録を無効にする

ユーザーが新しい統合されたエクスペリエンスで電話番号またはモバイル アプリ、あるいはその両方を登録すると、サービスによってそのユーザーに対するこれらの方法に一連のフラグ (StrongAuthenticationMethods) が設定されます。 この機能により、ユーザーは、Multi-Factor Authentication が必要な場合は常に、これらの方法で Multi-Factor Authentication を実行できます。

管理者がプレビューを有効にした場合、ユーザーは新しいエクスペリエンスを使って登録し、その後、管理者がプレビューを無効にすると、ユーザーは知らないうちに Multi-Factor Authentication にも登録される場合があります。

統合された登録を完了したユーザーがセルフサービスのパスワード リセット (SSPR) の現在の登録ページ ([https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)) に移動した場合、そのページにアクセスする前に Multi-Factor Authentication を実行するよう求められます。 この手順は技術的な観点から予測されますが、以前に SSPR にのみ登録されたユーザーにとっては新しい手順です。 この余分な手順により、ユーザーのセキュリティ体制はセキュリティ レベルが追加されることで向上しますが、管理者としてはユーザーが Multi-Factor Authentication を実行できないようにユーザーをロールバックしたい場合があります。  

### <a name="how-to-roll-back-users"></a>ユーザーをロールバックする方法

管理者は、ユーザーの Multi-Factor Authentication の設定をリセットしたい場合、次のセクションで提供されている PowerShell スクリプトを使用できます。 このスクリプトは、ユーザーのモバイル アプリまたは電話番号、あるいはその両方の StrongAuthenticationMethods プロパティをクリアします。 ユーザーに対してこのスクリプトを実行した場合、そのユーザーは、必要に応じて Multi-Factor Authentication に再登録する必要があります。 影響を受けるすべてのユーザーをロールバックする前に、1 人または 2 人のユーザーでロールバックをテストすることをお勧めします。

以下の手順では、ユーザーまたはユーザーのグループをロールバックします。

#### <a name="prerequisites"></a>前提条件

1. 適切な Azure AD PowerShell モジュールをインストールします。 PowerShell ウィンドウで、次のコマンドを実行してモジュールをインストールします。

   ```powershell
   Install-Module -Name MSOnline
   Import-Module MSOnline
   ```

1. 影響を受けるユーザー オブジェクト ID のリストを 1 行ごとに 1 つの ID を含むテキスト ファイルとしてコンピューターに保存します。 ファイルの場所を書き留めておきます。
1. 次のスクリプトをコンピューターに保存し、スクリプトの場所をメモします。

   ```powershell
   <# 
   //********************************************************
   //*                                                      *
   //*   Copyright (C) Microsoft. All rights reserved.      *
   //*                                                      *
   //********************************************************
   #>

   param($path)

   # Define Remediation Fn
   function RemediateUser {

       param  
       (
           $ObjectId
       )

       $user = Get-MsolUser -ObjectId $ObjectId

       Write-Host "Checking if user is eligible for rollback: UPN: "  $user.UserPrincipalName  " ObjectId: "  $user.ObjectId -ForegroundColor Yellow

       $hasMfaRelyingParty = $false
       foreach($p in $user.StrongAuthenticationRequirements)
       {
           if ($p.RelyingParty -eq "*")
           {
               $hasMfaRelyingParty = $true
               Write-Host "User was enabled for per-user MFA." -ForegroundColor Yellow
           }
       }

       if ($user.StrongAuthenticationMethods.Count -gt 0 -and -not $hasMfaRelyingParty)
       {
           Write-Host $user.UserPrincipalName " is eligible for rollback" -ForegroundColor Yellow
           Write-Host "Rolling back user ..." -ForegroundColor Yellow
           Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName $user.UserPrincipalName
           Write-Host "Successfully rolled back user " $user.UserPrincipalName -ForegroundColor Green
       }
       else
       {
           Write-Host $user.UserPrincipalName " is not eligible for rollback. No action required."
       }

       Write-Host ""
       Start-Sleep -Milliseconds 750
   }

   # Connect
   Import-Module MSOnline
   Connect-MsolService

   foreach($line in Get-Content $path)
   {
       RemediateUser -ObjectId $line
   }
   ```

#### <a name="rollback"></a>ロールバック

PowerShell ウィンドウで、次のコマンドを実行し、スクリプトとユーザー ファイルの場所を指定します。 求められたら、グローバル管理者の資格情報を入力します。 スクリプトでは、各ユーザーの更新操作の結果が出力されます。

`<script location> -path <user file location>`

### <a name="disable-the-preview-experience"></a>プレビュー エクスペリエンスを無効にする

ユーザーのプレビュー エクスペリエンスを無効にするには、次の手順を実行します。

1. ユーザー管理者として Azure Portal にサインインします。
2. **[Azure Active Directory]**  >  **[ユーザー設定]**  >  **[アクセス パネル プレビュー機能の設定を管理]** の順に移動します。
3. **[ユーザーはセキュリティ情報の登録と管理のためにプレビュー機能を使用できます]** で、セレクターを **[なし]** に設定して **[保存]** を選択します。

ユーザーは、プレビュー エクスペリエンスを使った登録を求められなくなります。

## <a name="next-steps"></a>次の手順

* [パスワード リセットのセルフサービスと Azure Multi-Factor Authentication の結合された登録のパブリック プレビューについてさらに学習する](concept-registration-mfa-sspr-combined.md)
