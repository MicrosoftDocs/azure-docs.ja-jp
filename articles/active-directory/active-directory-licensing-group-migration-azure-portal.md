---

title: "Azure Active Directory で個別にライセンスを付与されたユーザーをグループに移行する方法 | Microsoft Docs"
description: "Azure Active Directory を使用して個別のユーザー ライセンスをグループベースのライセンスに切り替える方法"
services: active-directory
keywords: "Azure AD のライセンス"
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/28/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: d7c5ba12738c4713517743ae8c44e236c5e1a210
ms.lasthandoff: 03/08/2017


---

# <a name="how-to-add-licensed-users-to-a-group-for-licensing-in-azure-active-directory"></a>Azure Active Directory でライセンスを付与するためにライセンスを持つユーザーをグループに追加する方法

既存のライセンスを組織のユーザーに "直接割り当て" ている、つまり、PowerShell スクリプトやその他のツールを使用して個別のユーザー ライセンスを割り当てている場合があります。 組織のライセンスを管理するためにグループベースのライセンスを使いたくなった場合、既存のソリューションをグループベースのライセンスにシームレスに切り替えるための移行計画が必要になります。

グループベースのライセンスへの移行の結果、現在ユーザーに割り当てられているライセンスが一時的に失われるような状況は避ける必要があります。これは、注意が必要な最も重要な点です。 ユーザーがサービスとそのデータにアクセスできなくなるリスクを排除するため、最終的にライセンスが削除される可能性のあるプロセスは回避する必要があります。

## <a name="recommended-migration-process"></a>推奨される移行プロセス

1. ユーザーに対するライセンスの割り当てと削除の管理に、(PowerShell などの) 既存の自動化を使用しています。 これはそのまま実行しておいてください。

2. 新しいライセンス グループを作成 (または使用する既存のグループを決定) して、必ず必要なユーザー全員をメンバーとして追加します。

3. 必要なライセンスをこれらのグループに割り当てます。既存の自動化 (PowerShell など) によってこれらのユーザーに適用されているのと同じライセンス状態を反映させることが目的です。

4. これらのグループの全ユーザーにライセンスが適用されたことを確認します。 これは、各グループの処理状態と監査ログを確認することで実行できます。

  - ライセンスの詳細を確認して、個別のユーザーをスポット チェックできます。 同一のライセンスが "直接" 割り当てられていると同時にグループから "継承" されていることがわかります。

  - PowerShell スクリプトを実行して、[ユーザーにライセンスを割り当てている方法](active-directory-licensing-group-advanced.md#use-powershell-to-see-who-has-inherited-and-direct-licenses)を確認できます。

  - 同一の製品ライセンスが "直接" とグループの両方でユーザーに割り当てられた場合、ユーザーによって使用されるライセンスは&1; つのみになります。 そのため、移行の実行に追加のライセンスは必要ありません。

5. 各グループにエラー状態のユーザーがいるかどうかをチェックして、ライセンス割り当ての失敗がないことを確認します。 詳細については、[グループのライセンスに関する問題の特定と解決](active-directory-licensing-group-problem-resolution-azure-portal.md)に関するページを参照してください。

6. 元の直接割り当ての削除を検討します。場合によっては、一部のユーザーの結果を先に監視するために "一定の間隔" でこれを段階的に実行する必要があります。

  元の直接割り当てをユーザーに残しておくこともできます。ただし、そのユーザーはライセンスが付与されたグループから抜けた場合に元のライセンスを保持したままになり、場合によっては望ましくありません。

## <a name="an-example"></a>例

ユーザーが 1,000 人の組織があるとします。 すべてのユーザーに Enterprise Mobility + Security (EMS) ライセンスが必要であり、 財務部門に所属する 200 人のユーザーには、Office 365 Enterprise E3 ライセンスが必要です。 現在、ユーザーが異動する際のライセンスの追加と削除には、オンプレミスで実行する PowerShell を使用しています。 ライセンスが Azure AD で自動的に管理されるよう、スクリプトからグループベースにライセンス付与を切り替えたいと考えています。

この場合、移行プロセスは次のようになります。

1. Azure Portal を使用して、Azure AD で EMS ライセンスを**すべてのユーザー** グループに割り当てます。 E3 ライセンスを、必要なすべてのユーザーが含まれた**財務部門**グループに割り当てます。

2. 各グループについて、すべてのユーザーのライセンス割り当てが完了したことを確認します。 各グループのブレードに移動して **[ライセンス]** を選択し、**[ライセンス]** ブレードの上部にある処理状態を確認します。

  - "Latest license changes have been applied to all users (最新のライセンス変更がすべてのユーザーに適用されました)" というメッセージを探して、処理が完了したことを確認します。

  - ライセンスの割り当てが正常に完了しなかった可能性があるユーザーがいることを示す通知が上部に表示されていないか探します。 一部のユーザーのライセンスが不足していなかったでしょうか。 または、一部のユーザーに競合するライセンス SKU があり、グループ ライセンスの継承を妨げていないでしょうか。

3. 一部のユーザーをスポット チェックして、"直接" とグループでライセンスが適用されていることを確認します。 ユーザーのブレードに移動して **[ライセンス]** を選択し、ライセンスの状態を確認します。

  - 移行中に想定されるユーザーの状態は、次のとおりです。

      ![想定されるユーザーの状態](media/active-directory-licensing-group-migration-azure-portal/expected-user-state.png)

  これで、直接付与されたライセンスと継承されたライセンスの両方がユーザーに備わっていることを確認できました。 **EMS** と **E3** の両方が割り当てられていることがわかります。

  - それぞれのライセンスを選択して、有効化されたサービスの詳細を表示します。 これを使用すると、直接付与されたライセンスとグループ ライセンスのまったく同じサービス プランがユーザーに対して有効になっているかどうかを確認できます。

      ![サービス プランのチェック](media/active-directory-licensing-group-migration-azure-portal/check-service-plans.png)

4. 直接付与されたライセンスとグループ ライセンスが同等であることを確認したら、直接付与されたライセンスをユーザーから削除できます。 ポータルで個々のユーザーのライセンスを削除し、問題ないことをテストしてから、自動化スクリプトを実行して一括で削除することができます。 次の例では、同じユーザーの直接付与されたライセンスがポータルを使用して削除されています。 ライセンス状態に変更はありませんが、直接割り当ての表示がなくなったことに注目してください。

  ![直接付与されたライセンスの削除](media/active-directory-licensing-group-migration-azure-portal/direct-licenses-removed.png)


## <a name="next-steps"></a>次のステップ

グループによるライセンス管理の他のシナリオについては、以下をご覧ください。

* [Azure Active Directory でのグループへのライセンス割り当て](active-directory-licensing-group-assignment-azure-portal.md)
* [Azure Active Directory のグループベースのライセンスとは](active-directory-licensing-whatis-azure-portal.md)
* [Azure Active Directory のグループのライセンスに関する問題の特定と解決](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Azure Active Directory のグループベースのライセンスが使用されるその他のシナリオ](active-directory-licensing-group-advanced.md)

