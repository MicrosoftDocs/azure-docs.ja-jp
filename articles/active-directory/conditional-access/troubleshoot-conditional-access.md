---
title: 条件付きアクセスでのサインインに関する問題のトラブルシューティング - Azure Active Directory
description: この記事では、条件付きアクセス ポリシーによって予期しない結果が発生した場合の対処方法について説明します
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 08/07/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6778b556795f4e079100f1a7bcbb8b9465e9e315
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2020
ms.locfileid: "88032970"
---
# <a name="troubleshooting-sign-in-problems-with-conditional-access"></a>条件付きアクセスでのサインインに関する問題のトラブルシューティング

この記事の情報は、エラー メッセージと Azure AD のサインイン ログを使用して、条件付きアクセスに関連する予期しないサインイン結果をトラブルシューティングするために使用できます。

## <a name="conditional-access-sign-in-interrupt"></a>条件付きアクセスのサインインの割り込み

最初の方法は、表示されるエラー メッセージを確認することです。 Web ブラウザーを使用してサインインする際の問題については、エラー ページ自体に詳細な情報が含まれています。 この情報だけで、問題の内容の説明と、解決策の提案が得られる場合があります。

![サインイン エラー - 準拠しているデバイスが必要です](./media/troubleshoot-conditional-access/image1.png)

上記のエラーでは、会社のモバイル デバイス管理ポリシーを満たすデバイスまたはクライアント アプリケーションからのみ、アプリケーションにアクセスできることを示すメッセージが表示されます。 この場合は、アプリケーションとデバイスはそのポリシーを満たしていません。

## <a name="azure-ad-sign-in-events"></a>Azure AD サインイン イベント

サインインの中断に関する詳細情報を取得する 2 番目の方法は、Azure AD サインイン イベントを確認して、適用された条件付きアクセス ポリシーとその理由を調べることです。

問題に関する詳細は、最初のエラー ページで **[詳細]** をクリックすると表示できます。 **[詳細]** をクリックすると、トラブルシューティング情報が表示されます。この情報は、Azure AD サインイン イベントでユーザーに表示された特定のエラー イベントを検索する場合や、Microsoft でサポート インシデントを開く場合に役立ちます。

![Web ブラウザーのサインインが中断された条件付きアクセスの詳細。](./media/troubleshoot-conditional-access/image2.png)

適用された条件付きアクセス ポリシーとその理由を確認するには、次の手順を実行します。

1. グローバル管理者、セキュリティ管理者、またはグローバル閲覧者として、**Azure portal** にサインインします。
1. **[Azure Active Directory]**  >  **[サインイン]** の順に移動します。
1. 確認するサインインのイベントを見つけます。 フィルターと列を追加または削除して、不要な情報を除外します。
   1. フィルターを追加して、範囲を絞り込みます。
      1. **関連付け ID**: 調査対象の特定のイベントがある場合。
      1. **条件付きアクセス**: ポリシーの失敗/成功を表示します。 フィルターの範囲を設定して、失敗のみを表示するように結果を制限します。
      1. **ユーザー名**: 特定のユーザーに関連する情報を表示します。
      1. **日付**: 対象の期間に範囲を設定します。

   ![サインイン ログで条件付きアクセス フィルターを選択します](./media/troubleshoot-conditional-access/image3.png)

1. ユーザーのサインイン エラーに対応するサインイン イベントが見つかったら、 **[条件付きアクセス]** タブを選択します。[条件付きアクセス] タブには、サインインが中断される原因となった特定のポリシーが表示されます。
   1. **[トラブルシューティングおよびサポート]** タブの情報により、コンプライアンス要件を満たしていなかったデバイスなど、サインインが失敗した理由が明確に示される場合があります。
   1. さらに調査するには、 **[ポリシー名]** をクリックして、ポリシーの構成にドリルダウンします。 **[ポリシー名]** をクリックすると、選択したポリシーのポリシー構成ユーザー インターフェイスが表示され、確認および編集できます。
   1. 条件付きアクセス ポリシーの評価に使用された **クライアント ユーザー**と**デバイスの詳細**は、サインイン イベントの **[基本情報]** 、 **[場所]** 、 **[デバイス情報]** 、 **[認証の詳細]** 、 **[追加情報]** タブでも参照できます。

### <a name="policy-details"></a>ポリシーの詳細

サインイン イベントでポリシーの右側にある省略記号を選択すると、ポリシーの詳細が表示されます。 これにより、管理者はポリシーが正常に適用された、またはされていない理由に関して、追加情報を得ることができます。

   ![サインイン イベントの [条件付きアクセス] タブ](./media/troubleshoot-conditional-access/image5.png)

   ![ポリシーの詳細 (プレビュー)](./media/troubleshoot-conditional-access/policy-details.png)

左側にはサインイン時に収集された詳細情報が表示され、右側には適用された条件付きアクセス ポリシーの要件をそれらの詳細が満たすかどうかが示されます。 条件付きアクセス ポリシーは、すべての条件が満たされているか、構成されていない場合にのみ適用されます。

イベントの情報が、サインイン結果を理解したり、必要な結果を得るためにポリシーを調整したりするのに十分でない場合は、サポート インシデントを開くことができます。 対象のサインイン イベントの **[トラブルシューティングおよびサポート]** タブに移動して、 **[新しいサポート リクエストを作成する]** を選択します。

![サインイン イベントの [トラブルシューティングおよびサポート] タブ](./media/troubleshoot-conditional-access/image6.png)

インシデントを送信するときに、サインイン イベントの要求 ID および時刻と日付をインシデント送信の詳細に入力してください。 Microsoft サポートはこの情報を使用して、対象のイベントを見つけることができます。

### <a name="conditional-access-error-codes"></a>条件付きアクセスのエラー コード

| サインイン エラー コード | エラー文字列 |
| --- | --- |
| 53000 | DeviceNotCompliant |
| 53001 | DeviceNotDomainJoined |
| 53002 | ApplicationUsedIsNotAnApprovedApp |
| 53003 | BlockedByConditionalAccess |
| 53004 | ProofUpBlockedDueToRisk |

## <a name="next-steps"></a>次のステップ

- [Azure Active Directory ポータルのサインイン アクティビティ レポート](../reports-monitoring/concept-sign-ins.md)
- [What If ツールを使用した条件付きアクセスのトラブルシューティング](troubleshoot-conditional-access-what-if.md)
- [Azure Active Directory の条件付きアクセス](best-practices.md)のベスト プラクティス
