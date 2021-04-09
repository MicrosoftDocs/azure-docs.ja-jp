---
title: セキュリティ ポリシーの操作 | Microsoft Docs
description: この記事では、Azure Security Center でセキュリティ ポリシーを操作する方法について説明します。
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.date: 01/24/2021
ms.author: memildin
ms.openlocfilehash: 6ecedc20cf6924a82b6b4640d3caa75bc5958de0
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102101326"
---
# <a name="manage-security-policies"></a>セキュリティ ポリシーの管理

この記事では、セキュリティ ポリシーの構成方法と、それの Security Center での表示方法について説明します。 

## <a name="who-can-edit-security-policies"></a>セキュリティ ポリシーを編集できるユーザーは誰ですか。

REST API 経由または Windows PowerShell を使用して、Azure Policy ポータルからセキュリティ ポリシーを編集できます。

Security Center では Azure ロールベースのアクセス制御 (Azure RBAC) が使用されています。RBAC が提供する組み込みのロールは、Azure ユーザー、グループ、およびサービスに割り当てることができます。 ユーザーが Security Center を開くと、アクセスできるリソースに関する情報のみが表示されます。 これは、リソースのサブスクリプションに対して、"*所有者*"、"*共同作成者*"、または "*閲覧者*" のロールがユーザーに割り当てられていることを意味します。 次の 2 つの固有の Security Center ロールもあります。

- **セキュリティ閲覧者**: 推奨事項、アラート、ポリシー、正常性などの Security Center 項目を表示する権利を持っています。 変更は行えません。
- **セキュリティ管理者**:*セキュリティ閲覧者* と同じ表示権利を持っています。 セキュリティポリシーを更新し、アラートを無視することもできます。

## <a name="manage-your-security-policies"></a>セキュリティ ポリシーの管理

Security Center でセキュリティ ポリシーを表示するには:

1. **Security Center** ダッシュボードで **[セキュリティ ポリシー]** を選択します。

    :::image type="content" source="./media/security-center-policies/security-center-policy-mgt.png" alt-text="[ポリシー管理] ページ":::

   **[ポリシー管理]** 画面には、管理グループ、サブスクリプション、およびワークスペースの数、および管理グループの構造が表示されます。

1. ポリシーを参照するサブスクリプションまたは管理グループを選択します。

1. そのサブスクリプションまたは管理グループのセキュリティ ポリシー ページが表示されます。 利用可能な割り当て済みのポリシーが表示されます。

    :::image type="content" source="./media/tutorial-security-policy/security-policy-page.png" alt-text="Security Center のセキュリティ ポリシー ページ" lightbox="./media/tutorial-security-policy/security-policy-page.png":::

    > [!NOTE]
    > 既定のポリシーの横に "MG 継承済み" というラベルがある場合、そのポリシーが管理グループに割り当てられており、表示しているサブスクリプションに継承されていることを意味します。

1. このページで使用可能なオプションから選択します。

    1. 業界標準を使用するには、 **[標準をさらに追加]** を選択します。 詳細については、「[規制コンプライアンス ダッシュボードで標準セットをカスタイマイズする](update-regulatory-compliance-packages.md)」を参照してください。

    1. カスタム イニシアティブを割り当てて管理するには、 **[カスタム イニシアティブの追加]** を選択します。 詳細については、[カスタム セキュリティ イニシアチブおよびポリシーの使用](custom-security-policies.md)に関する記事をご覧ください。

    1. 既定のイニシアチブを表示して編集するには、 **[有効なポリシーの表示]** を選択し、下記の説明に従って操作を進めます。 

        :::image type="content" source="./media/security-center-policies/policy-screen.png" alt-text="[有効なポリシー] 画面":::

       この **[セキュリティ ポリシー]** 画面には、選択したサブスクリプションまたは管理グループに割り当てられているポリシーによって実行されたアクションが反映されます。
       
       * 上部のリンクを使用して、サブスクリプションまたは管理グループに適用されているポリシー **割り当て** を開きます。 これらのリンクを使用すると、割り当てにアクセスしたり、ポリシーを編集または無効化したりすることができます。 たとえば、特定のポリシー割り当てによってエンドポイントが実質的に保護されないようになっていることがわかった場合は、リンクを使用してそのポリシーを編集または無効化します。
       
       * ポリシーの一覧では、サブスクリプションまたは管理グループに対して実際に適用されているポリシーを確認できます。 スコープに適用されている各ポリシーの設定が考慮され、ポリシーによって実行されたアクションの累積的な結果が表示されます。 たとえば、ポリシーのある割り当てでは無効になっていても、別の割り当てでは AuditIfNotExist に設定されている場合、累積的な効果によって AuditIfNotExist が適用されます。 よりアクティブな効果が常に優先されます。
       
       * 設定できるポリシーの効果は、Append、Audit、AuditIfNotExists、Deny、DeployIfNotExists、Disabled です。 効果が適用されるしくみの詳細については、[Policy の効果](../governance/policy/concepts/effects.md)に関するページを参照してください。

       > [!NOTE]
       > 割り当てられているポリシーを確認すると、複数の割り当てが表示され、各割り当てのそれぞれの構成を参照できます。


## <a name="disable-security-policies-and-disable-recommendations"></a>セキュリティ ポリシーと推奨事項の無効化

環境に関係のない推奨事項がセキュリティ イニシアチブによってトリガーされる場合、その推奨事項が再び表示されるのを防ぐことができます。 推奨事項を無効にするには、推奨設定を生成する特定のポリシーを無効にします。

Security Center の規制コンプライアンス ツールで適用した規制標準のために必要な場合、無効にする推奨事項は引き続き表示されます。 組み込みのイニシアチブでポリシーを無効にした場合でも、コンプライアンスのために必要な場合は、規制標準のイニシアチブのポリシーによって推奨事項は引き続きトリガーされます。 規制標準イニシアチブのポリシーを無効にすることはできません。

推奨事項の詳細については、[セキュリティに関する推奨事項の管理](security-center-recommendations.md)についてのページを参照してください。

1. Security Center 内の **[Policy & Compliance]\(ポリシーとコンプライアンス\)** セクションで **[セキュリティ ポリシー]** を選択します。

    :::image type="content" source="./media/tutorial-security-policy/policy-management.png" alt-text="Azure Security Center でポリシー管理プロセスの開始":::

2. 推奨設定を無効にするサブスクリプションまたは管理グループを選択します。

   > [!NOTE]
   > 管理グループでは、そのポリシーがそのサブスクリプションに適用されることに注意してください。 そのため、サブスクリプションのポリシーを無効にしても、そのサブスクリプションが同じポリシーを使用する管理グループに属している場合は、そのポリシーの推奨事項は引き続き表示されます。 ポリシーは管理レベルから引き続き適用され、推奨事項は引き続き生成されます。

1. **[有効なポリシーの表示]** を選択します。

    :::image type="content" source="./media/tutorial-security-policy/view-effective-policy.png" alt-text="サブスクリプションに割り当てられた有効なポリシーを開く方法":::

1. 割り当てられたポリシーを選択します。

   ![ポリシーの選択](./media/tutorial-security-policy/security-policy.png)

1. **[パラメーター]** セクションで、推奨事項を呼び出すポリシーのうち、無効にするものを検索します。次に、ドロップダウン リストから **[無効]** を選択します。

   ![ポリシーの無効化](./media/tutorial-security-policy/disable-policy.png)

1. **[保存]** を選択します。

   > [!NOTE]
   > ポリシー無効化の変更が有効になるまでに、最大 12 時間かかる場合があります。

## <a name="next-steps"></a>次のステップ
このページでは、セキュリティ ポリシーについて説明しました。 関連情報については、次のページを参照してください。

- [PowerShell を使用してポリシーを設定する方法を確認する](../governance/policy/assign-policy-powershell.md)
- [Azure Policy でセキュリティ ポリシーを編集する方法を確認する](../governance/policy/tutorials/create-and-manage.md)
- [Azure Policy を使用して、サブスクリプションまたは管理グループにポリシーを設定する方法を確認する](../governance/policy/overview.md)
- [管理グループ内のすべてのサブスクリプションで Security Center を有効にする方法を確認する](onboard-management-group.md)