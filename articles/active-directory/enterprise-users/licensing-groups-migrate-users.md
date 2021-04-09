---
title: 直接ライセンスを持つユーザーをグループ ライセンスに追加する - Azure AD | Microsoft Docs
description: Azure Active Directory を使用して個別のユーザー ライセンスをグループベースのライセンスに移行する方法です
services: active-directory
keywords: Azure AD のライセンス
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: how-to
ms.workload: identity
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: seohack1;it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: adc80cf579ce3086abd4171b065f859acd0b9294
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96546507"
---
# <a name="how-to-migrate-users-with-individual-licenses-to-groups-for-licensing"></a>個別にライセンスを付与されたユーザーをライセンスのためにグループに移行する方法

既存のライセンスが直接割り当てによって組織のユーザーにデプロイされている、つまり、PowerShell スクリプトや他のツールを使用して個別のユーザー ライセンスが割り当てられている場合があります。 組織のライセンスを管理するためにグループベースのライセンスを使い始める前に、この移行プランを使用して、既存のソリューションをグループベースのライセンスにシームレスに置き換えることができます。

グループベースのライセンスへの移行の結果、現在ユーザーに割り当てられているライセンスが一時的に失われるような状況は避ける必要があります。これは、注意が必要な最も重要な点です。 ユーザーがサービスとそのデータにアクセスできなくなるリスクを排除するため、最終的にライセンスが削除される可能性のあるプロセスは回避する必要があります。

## <a name="recommended-migration-process"></a>推奨される移行プロセス

1. ユーザーに対するライセンスの割り当てと削除の管理に、(PowerShell などの) 既存の自動化を使用しています。 これはそのまま実行しておいてください。

1. 新しいライセンス グループを作成 (または使用する既存のグループを決定) して、必ず必要なユーザー全員をメンバーとして追加します。

1. 必要なライセンスをこれらのグループに割り当てます。既存の自動化 (PowerShell など) によってこれらのユーザーに適用されているのと同じライセンス状態を反映させることが目的です。

1. これらのグループの全ユーザーにライセンスが適用されたことを確認します。 このアプリケーションは、各グループの処理状態と監査ログを確認することで実行できます。

   - ライセンスの詳細を確認して、個別のユーザーをスポット チェックできます。 同一のライセンスが "直接" 割り当てられていると同時にグループから "継承" されていることがわかります。

   - PowerShell スクリプトを実行して、[ユーザーにライセンスを割り当てている方法](licensing-group-advanced.md#use-powershell-to-see-who-has-inherited-and-direct-licenses)を確認できます。

   - 同一の製品ライセンスが "直接" とグループの両方でユーザーに割り当てられた場合、ユーザーによって使用されるライセンスは 1 つのみになります。 そのため、移行の実行に追加のライセンスは必要ありません。

1. 各グループにエラー状態のユーザーがいるかどうかをチェックして、ライセンス割り当ての失敗がないことを確認します。 詳細については、[グループのライセンスに関する問題の特定と解決](licensing-groups-resolve-problems.md)に関するページを参照してください。

元の直接割り当てを削除することを検討してください。 これを段階的に実行し、最初にユーザーのサブセットの結果を監視することをお勧めします。 元の直接割り当てをユーザーに残すことができたが、ライセンスが付与されたグループからそのユーザーが抜けた場合、直接割り当てられたライセンスを保持したままになり、この状態は好ましくない場合もあります。

## <a name="an-example"></a>使用例

組織に 1,000 人のユーザーがいます。 すべてのユーザーに Office 365 Enterprise E3 ライセンスが必要です。 現在、組織は、ユーザーが異動する際のライセンスの追加と削除に、オンプレミスで実行する PowerShell を使用しています。 しかし、組織は、ライセンスが Azure AD で自動的に管理できるよう、スクリプトからグループベースにライセンス付与を切り替えたいと考えています。

この場合、移行プロセスは次のようになります。

1. Azure Portal を使用して、Azure AD で Office 365 E3 ライセンスを **すべてのユーザー** グループに割り当てます。

1. すべてのユーザーのライセンス割り当てが完了したことを確認します。 各グループの概要ページに移動して **[ライセンス]** を選択し、**[ライセンス]** ブレードの上部にある処理状態を確認します。

   - "Latest license changes have been applied to all users (最新のライセンス変更がすべてのユーザーに適用されました)" というメッセージを探して、処理が完了したことを確認します。

   - ライセンスの割り当てが正常に完了しなかった可能性があるユーザーがいることを示す通知が上部に表示されていないか探します。 一部のユーザーのライセンスが不足していなかったでしょうか。 または、一部のユーザーに競合するライセンス プランがあり、グループ ライセンスの継承を妨げていないでしょうか。

1. 一部のユーザーをスポット チェックして、"直接" とグループでライセンスが適用されていることを確認します。 ユーザーのプロファイル ページに移動して **[ライセンス]** を選択し、ライセンスの状態を確認します。

   - 移行中に想定されるユーザーの状態は、次のとおりです。

      ![移行中に想定されるユーザーの状態](./media/licensing-groups-migrate-users/expected-user-state.png)

     これで、直接付与されたライセンスと継承されたライセンスの両方がユーザーに備わっていることを確認できました。 Office 365 E3 が割り当てられていることが確認できます。

   - 各ライセンスを選択すると、有効になっているサービスが表示されます。 直接付与されたライセンスとグループ ライセンスによって、まったく同じサービスがユーザーに対して有効になっているかどうかを確認するには、**[割り当て]** を選択します。

1. 直接付与されたライセンスとグループ ライセンスが同等であることを確認したら、直接付与されたライセンスをユーザーから削除できます。 ポータルで個々のユーザーのライセンスを削除し、問題ないことをテストしてから、自動化スクリプトを実行して一括で削除することができます。 次の例では、同じユーザーの直接付与されたライセンスがポータルを使用して削除されています。 ライセンス状態に変更はありませんが、直接割り当ての表示がなくなったことに注目してください。

   ![直接付与されたライセンスが削除されていることを確認する](./media/licensing-groups-migrate-users/direct-licenses-removed.png)

## <a name="next-steps"></a>次のステップ

グループによるライセンス管理の他のシナリオについては、以下をご覧ください。

- [What is group-based licensing in Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md) (Azure Active Directory のグループベースのライセンスとは)
- [Assigning licenses to a group in Azure Active Directory](licensing-groups-assign.md) (Azure Active Directory でのグループへのライセンス割り当て)
- [Azure Active Directory のグループのライセンスに関する問題の特定と解決](licensing-groups-resolve-problems.md)
- [Azure Active Directory のグループベースのライセンスを使用して製品ライセンス間でユーザーを移行する方法](licensing-groups-change-licenses.md)
- [Azure Active Directory グループベース ライセンスのその他のシナリオ](licensing-group-advanced.md)
- [Azure Active Directory のグループベースのライセンスの PowerShell の例](licensing-ps-examples.md)
