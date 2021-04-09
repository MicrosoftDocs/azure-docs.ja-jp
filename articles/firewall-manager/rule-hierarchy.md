---
title: Azure Firewall ポリシーを使用して規則の階層を定義する
description: Azure Firewall ポリシーを使用して規則の階層を定義し、コンプライアンスを適用する方法について説明します。
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: how-to
ms.date: 08/26/2020
ms.author: victorh
ms.openlocfilehash: 1ba683e3d616f52854f1055dab9b9fe2d389116a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92331738"
---
# <a name="use-azure-firewall-policy-to-define-a-rule-hierarchy"></a>Azure Firewall ポリシーを使用して規則の階層を定義する

セキュリティ管理者は、ファイアウォールを管理し、オンプレミスとクラウドのデプロイ全体でコンプライアンスを確保する必要があります。 重要なコンポーネントは、ファイアウォール規則を自動的に作成するように CI/CD パイプラインをアプリケーション チームが柔軟に実装できるようにする機能です。

Azure Firewall ポリシーを使用すると、規則の階層を定義し、コンプライアンスを適用することができます。

- 子アプリケーション チーム ポリシーの上に中央の基本ポリシーをオーバーレイする階層構造を指定します。 基本ポリシーは優先順位が高く、子ポリシーの前に実行されます。
- Azure のカスタムの役割定義を使用して、基本ポリシーを誤って削除することを防止し、サブスクリプションまたはリソース グループ内の規則コレクション グループへの選択的なアクセスを付与します。 

## <a name="solution-overview"></a>ソリューションの概要

この例の大まかな手順は次のとおりです。

1. Security チーム リソース グループにベース ファイアウォール ポリシーを作成します。 
3. 基本ポリシーで IT セキュリティ固有の規則を定義します。 これにより、トラフィックを許可または拒否する規則の共通セットが追加されます。
4. 基本ポリシーを継承するアプリケーション チーム ポリシーを作成します。 
5. ポリシーでアプリケーション チーム固有の規則を定義します。 また、既存のファイアウォールから規則を移行することもできます。
6. Azure Active Directory カスタム ロールを作成して、規則コレクション グループへのきめの細かいアクセスを用意し、ファイアウォール ポリシー スコープでロールを追加します。 次の例では、Sales チームのメンバーは、Sales チームのファイアウォール ポリシーの規則コレクション グループを編集できます。 Database チームと Engineering チームにも同じことが当てはまります。
7. ポリシーを対応するファイアウォールに関連付けます。 Azure ファイアウォールには、割り当てられたポリシーを 1 つだけ割り当てることができます。 このためには、各アプリケーション チームが独自のファイアウォールを所有している必要があります。



:::image type="content" source="media/rule-hierarchy/contoso-teams.png" alt-text="チームと要件" border="false":::

### <a name="create-the-firewall-policies"></a>ファイアウォール ポリシーを作成する

- 基本のファイアウォール ポリシー。

アプリケーション チームごとにポリシーを作成します。

- Sales ファイアウォール ポリシー。 Sales ファイアウォール ポリシーは、基本のファイアウォール ポリシーを継承します。
- Database ファイアウォール ポリシー。 Database ファイアウォール ポリシーは、基本ファイアウォール ポリシーを継承します。
- Engineering ファイアウォール ポリシー。 Engineering ファイアウォール ポリシーも、基本のファイアウォール ポリシーを継承します。

:::image type="content" source="media/rule-hierarchy/policy-hierarchy.png" alt-text="ポリシー階層" border="false":::

### <a name="create-custom-roles-to-access-the-rule-collection-groups"></a>規則コレクション グループにアクセスするカスタム ロールを作成する 

各アプリケーション チームに対してカスタム ロールを定義します。 ロールには操作とスコープを定義します。 アプリケーション チームは、それぞれのアプリケーションの規則コレクション グループを編集できます。

おおまかに説明すると、次の手順に従ってカスタム ロールを定義します。

1. サブスクリプションを取得します。

   `Select-AzSubscription -SubscriptionId xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxx`
2. 次のコマンドを実行します。

   `Get-AzProviderOperation "Microsoft.Support/*" | FT Operation, Description -AutoSize`
3. Get-AzRoleDefinition コマンドを使用して、閲覧者ロールを JSON 形式で出力します。 

   `Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole.json`
4. エディターで ReaderSupportRole.json ファイルを開きます。

   この JSON 出力を次に示します。 各種のプロパティについては、 [Azure カスタム ロール](../role-based-access-control/custom-roles.md)に関するページを参照してください。

```json
   { 
     "Name": "Reader", 
     "Id": "acdd72a7-3385-48ef-bd42-f606fba81ae7", 
     "IsCustom": false, 
     "Description": "Lets you view everything, but not make any changes.", 
     "Actions": [ 
      "*/read" 
     ], 
     "NotActions": [], 
     "DataActions": [], 
     "NotDataActions": [], 
     "AssignableScopes": [ 
       "/" 
     ] 
   } 
```
5. JSON ファイルを編集して、 

   `*/read", "Microsoft.Network/*/read", "Microsoft.Network/firewallPolicies/ruleCollectionGroups/write` 

   操作を  **Actions**  プロパティに追加します。 read 操作の後に必ずコンマを追加してください。 この操作により、ユーザーは規則コレクション グループを作成および更新できるようになります。
6.  **AssignableScopes** で、サブスクリプション ID を次の形式で追加します。 

   `/subscriptions/xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxx`

   明示的にサブスクリプション ID を追加してください。それ以外の場合、サブスクリプションにロールをインポートできなくなります。
7.  **Id**  プロパティ行を削除し、 **IsCustom**  プロパティを true に変更します。
8.  **Name**  および  **Description**  の各プロパティを「*AZFM Rule Collection Group Author*」(AZFM 規則コレクション グループ作成者) と「*Users in this role can edit Firewall Policy rule collection groups*」(このロールのユーザーはファイアウォール ポリシー規則コレクション グループを編集できます) に変更します

JSON ファイルは次の例のようになります。

```
{ 

    "Name":  "AZFM Rule Collection Group Author", 
    "IsCustom":  true, 
    "Description":  "Users in this role can edit Firewall Policy rule collection groups", 
    "Actions":  [ 
                    "*/read", 
                    "Microsoft.Network/*/read", 
                     "Microsoft.Network/firewallPolicies/ruleCollectionGroups/write" 
                ], 
    "NotActions":  [ 
                   ], 
    "DataActions":  [ 
                    ], 
    "NotDataActions":  [ 
                       ], 
    "AssignableScopes":  [ 
                             "/subscriptions/xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxx"] 
} 
```
9. 新しいカスタム ロールを作成するには、New-AzRoleDefinition コマンドを使用して、JSON ロール定義ファイルを指定します。 

   `New-AzRoleDefinition -InputFile "C:\CustomRoles\RuleCollectionGroupRole.json`

### <a name="list-custom-roles"></a>カスタム ロールの一覧表示

すべてのカスタム ロールを一覧表示するには、Get-AzRoleDefinition コマンドを使用します。

   `Get-AzRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom`

Azure portal でカスタム ロールを確認することもできます。 サブスクリプションにアクセスし、 **[アクセス制御 (IAM)]** 、 **[ロール]** の順に選択します。

:::image type="content" source="media/rule-hierarchy/sales-app-policy.png" alt-text="SalesAppPolicy":::

:::image type="content" source="media/rule-hierarchy/sales-app-policy-read.png" alt-text="SalesAppPolicy の読み取りアクセス許可":::

詳細については、[Azure PowerShell を使用して Azure カスタム ロールを作成する](../role-based-access-control/tutorial-custom-role-powershell.md)」をご覧ください。

### <a name="add-users-to-the-custom-role"></a>カスタム ロールにユーザーを追加する

ポータルで、AZFM 規則コレクション グループの作成者ロールにユーザーを追加し、ファイアウォール ポリシーへのアクセスを付与することができます。

1. ポータルから、アプリケーション チームのファイアウォール ポリシーを選択します (SalesAppPolicy など)。
2. **[アクセス制御]** を選択します。
3. **[ロールの割り当ての追加]** を選択します。
4. ロールにユーザーまたはユーザー グループ (たとえば、Sales チーム) を追加します。

他のファイアウォール ポリシーについても、この手順を繰り返します。

### <a name="summary"></a>まとめ

カスタム役割を使用したファイアウォール ポリシーを使用して、ファイアウォール ポリシー規則コレクション グループへの選択的アクセスを付与できるようになりました。

ユーザーには以下を行うアクセス許可がありません。
- Azure Firewall またはファイアウォール ポリシーを削除する。
- ファイアウォール ポリシー階層、DNS 設定、または脅威インテリジェンスを更新する。
- AZFM 規則コレクション グループ作成者グループのメンバーではないファイアウォール ポリシーを更新する。

セキュリティ管理者は、基本ポリシーを使用してガードレールを適用し、企業の必要に応じて特定の種類のトラフィック (ICMP など) をブロックすることができます。 

## <a name="next-steps"></a>次のステップ

詳細については、[Azure Firewall ポリシー](policy-overview.md)に関するページを参照してください。

