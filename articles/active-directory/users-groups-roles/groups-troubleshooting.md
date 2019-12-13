---
title: 動的グループ メンバーシップで問題を修正する - Azure AD | Microsoft Docs
description: Azure Active Directory での動的グループ メンバーシップのトラブルシューティングのヒント
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f685ac63e3b4a8cf466be4eb4561472fb084d49
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/13/2019
ms.locfileid: "74026538"
---
# <a name="troubleshoot-and-resolve-groups-issues"></a>グループに関する問題のトラブルシューティングと解決

## <a name="troubleshooting-group-creation-issues"></a>グループの作成に関する問題のトラブルシューティング

**Azure portal でセキュリティ グループの作成を無効にしましたが、Powershell を使用してまだグループを作成できます** Azure portal の **[User can create security groups in Azure portals]\(ユーザーは Azure portal でセキュリティ グループを作成できる\)** 設定は、管理者以外のユーザーがアクセス パネルまたは Azure portal でセキュリティ グループを作成できるかどうかを制御します。 Powershell を使用したセキュリティ グループの作成については制御されません。

Powershell で管理者以外のユーザーによるグループの作成を無効にするには、次のようにします。
1. 管理者以外のユーザーにグループの作成が許可されていることを確認します。
   

   ```powershell
   Get-MsolCompanyInformation | Format-List UsersPermissionToCreateGroupsEnabled
   ```

  
2. `UsersPermissionToCreateGroupsEnabled : True` が返された場合は、管理者以外のユーザーはグループを作成できます。 この機能を無効にするには、次のコマンドを実行します。
  

   ``` 
   Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
   ```

<br/>**Powershell で動的グループを作成しようとしたときに、最大許容グループ数のエラーが表示されました**<br/>
Powershell で _動的グループ ポリシーの最大許容グループ数に達した_ ことを示すメッセージが表示される場合、テナント内の動的グループの数が上限に達したことを意味します。 テナントごとの動的グループの最大数は 5,000 です。

新しい動的グループを作成するには、まず既存の動的グループをいくつか削除する必要があります。 上限を増やす方法はありません。

## <a name="troubleshooting-dynamic-memberships-for-groups"></a>グループの動的メンバーシップのトラブルシューティング

**グループに対するルールを構成しましたが、グループのメンバーシップが更新されません**<br/>
1. ルール内のユーザー属性またはデバイス属性の値を確認します。 ルールを満たすユーザーが存在することを確認してください。 デバイスの場合は、デバイスのプロパティを調べて、同期された属性に予期される値が含まれていることを確認してください。<br/>
2. メンバーシップの処理の状態を調べて、処理が完了しているかどうかを確認してください。 グループの **[概要]** ページで、[メンバーシップの処理状態](groups-create-rule.md#check-processing-status-for-a-rule)と最終更新日を確認できます。

何も問題がなさそうな場合、グループが設定されるまでしばらく待ってください。 グループを初めて設定する場合、またはルールの変更後に設定する場合、テナントのサイズによっては最大 24 時間かかる場合があります。

**ルールの設定を変更したのですが、そのルールの既存のメンバーが削除されてしまいました**<br/>これは正しい動作です。 ルールを有効にしたり変更を加えたりするとグループの既存のメンバーは削除されます。 ルールの評価から返されたユーザーは、グループのメンバーとして追加されます。

**ルールを追加または変更してもすぐにはメンバーシップの変更を確認できません。なぜでしょうか。**<br/>メンバーシップの評価に特化した機能が、非同期のバックグラウンド プロセスで定期的に実行されます。 プロセスにかかる時間は、ディレクトリ内のユーザー数と、ルールの結果として作成されるグループのサイズによって変わります。 ユーザー数の少ないディレクトリであれば通常、グループ メンバーシップの変更が数分以内に反映されます。 ディレクトリのユーザー数が多いと、変更が反映されるまでに 30 分以上かかる場合があります。

**グループが今すぐ処理されるように強制するにはどうすればよいですか。**<br/>
現時点では、グループの処理をオンデマンドで自動的にトリガーする方法はありません。 ただし、メンバーシップ ルールを更新して末尾に空白文字を追加すると、手動で再処理をトリガーできます。  

**ルール処理エラーが発生しました**<br/>次の表は、一般的な動的メンバーシップ ルール エラーとその修正方法をまとめたものです。

| ルール パーサー エラー | 間違った使用法 | 正しい使用法 |
| --- | --- | --- |
| エラー:属性がサポートされていません。 |(user.invalidProperty -eq "Value") |(user.department -eq "value")<br/><br/>該当する属性が、[サポートされているプロパティ一覧](groups-dynamic-membership.md#supported-properties)に記載されていることを確認してください。 |
| エラー:属性で演算子がサポートされていません。 |(user.accountEnabled -contains true) |(user.accountEnabled -eq true)<br/><br/>プロパティの型に対してサポートされていない演算子が使用されています (この例では、-contains をブール型で使用することはできません)。 プロパティの型に合った適切な演算子を使用してください。 |
| エラー:クエリ コンパイル エラー。 | 1. (user.department -eq "Sales") (user.department -eq "Marketing")<br>2.  (user.userPrincipalName -match "*@domain.ext") | 1.演算子が不足しています。 結合述語を 2 つ使用してください (-and または -or)。<br>(user.department -eq "Sales") -or (user.department -eq "Marketing")<br>2.-match に使用されている正規表現に誤りがあります。<br>(user.userPrincipalName -match ".*@domain.ext")<br>あるいは: (user.userPrincipalName -match "@domain.ext$") |

## <a name="next-steps"></a>次の手順

次の記事は、Azure Active Directory に関する追加情報を示します。

* [Azure Active Directory グループによるリソースへのアクセス管理](../fundamentals/active-directory-manage-groups.md)
* [Azure Active Directory のアプリケーション管理](../manage-apps/what-is-application-management.md)
* [Azure Active Directory とは](../fundamentals/active-directory-whatis.md)
* [オンプレミス ID と Azure Active Directory の統合](../hybrid/whatis-hybrid-identity.md)