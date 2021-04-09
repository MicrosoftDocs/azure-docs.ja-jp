---
title: Just-In-Time アクセスを承認する
description: Azure Managed Applications のお客様が、マネージド アプリケーションへのジャストインタイム アクセス要求を承認する方法について説明します。
author: MSEvanhi
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: 4a3604a3256fb2d3f4253891bbc28c7685748cd2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "75649371"
---
# <a name="configure-and-approve-just-in-time-access-for-azure-managed-applications"></a>Azure Managed Applications でジャストインタイム アクセスの構成と承認を行う

マネージド アプリケーションのコンシューマーとして、マネージド リソース グループへの永続的なアクセス権を発行元に与えることに不安を覚えることがあります。 マネージド リソースへのアクセス権の付与をもっと制御するために、Azure Managed Applications には、ジャストインタイム (JIT) アクセスと呼ばれる機能が提供されています (この機能は、現在プレビュー段階です)。 それを使用して、発行元がどの時点でどのくらいの期間、リソース グループにアクセスできるかを承認できます。 発行元は、その期間中に必要な更新を実行できますが、その期間が終了すると、発行元のアクセス権は期限切れになります。

アクセス権を付与するためのワークフローは次のとおりです。

1. 発行元が、マーケットプレースにマネージド アプリケーションを追加し、JIT アクセスを使用できることを指定します。

1. デプロイ時に、お客様がマネージド アプリケーションのインスタンスに対する JIT アクセスを有効にします。

1. デプロイ後に、JIT アクセスの設定を変更できます。

1. 発行元がアクセス要求を送信します。

1. お客様が要求を承認します。

この記事では、JIT アクセスを有効にし、要求を承認するためにコンシューマーが行う操作について説明します。 JIT アクセス権付きのマネージド アプリケーションの発行方法については、[Azure Managed Applications でのジャストインタイム アクセスの要求](request-just-in-time-access.md)に関する記事を参照してください。

> [!NOTE]
> ジャストインタイム アクセスを使用するには、[Azure Active Directory P2 ライセンス](../../active-directory/privileged-identity-management/subscription-requirements.md)が必要です。

## <a name="enable-during-deployment"></a>デプロイ時に有効にする

1. [Azure portal](https://portal.azure.com) にサインインします。

1. マーケットプレースのエントリで、JIT が有効になっているマネージド アプリケーションを見つけます。 **［作成］** を選択します

1. 新しいマネージド アプリケーションの値を指定するのと同時に、**JIT 構成** 手順で、マネージド アプリケーションに対する JIT アクセスを有効または無効にすることができます。 **[JIT アクセスの有効化]** で **[はい]** を選択します。 マーケットプレースで JIT の有効化が定義されているマネージド アプリケーションでは、このオプションは既定で選択されています。

   ![アクセスを構成する](./media/approve-just-in-time-access/configure-jit-access.png)

   JIT アクセスの有効化は、デプロイ時にのみ実行できます。 **[いいえ]** を選択した場合、発行元は、マネージド リソース グループに対する永続的なアクセス権を取得します。 JIT アクセスは、後で有効にすることはできません。

1. 既定の承認設定を変更するには、**[JIT 構成のカスタマイズ]** を選択します。

   ![アクセスのカスタマイズ](./media/approve-just-in-time-access/customize-jit-access.png)

   既定では、JIT が有効になっているマネージド アプリケーションの設定は次のようになっています。

   * 承認モード - 自動
   * 最大アクセス期間 - 8 時間
   * 承認者 - なし

   [承認モード] を **[自動]** に設定した場合、要求ごとに承認者に通知が送信されますが、要求は自動的に承認されます。 **[手動]** に設定した場合、要求ごとに承認者に通知が送信され、承認者のいずれかが要求を承認する必要があります。

   [アクティブ化の最大期間] には、発行元がマネージド リソース グループへのアクセスを要求できる最大期間を指定します。

   承認者の一覧は、JIT アクセス要求を承認できる Azure Active Directory ユーザーです。 承認者を追加するには、**[承認者の追加]** を選択し、ユーザーを検索します。

   設定を更新した後、**[保存]** を選択します。

## <a name="update-after-deployment"></a>デプロイ後に更新する

要求の承認方法の値を変更することができます。 ただし、デプロイ時に JIT アクセスを有効にしていない場合、後でそれを有効にすることはできません。

デプロイされたマネージド アプリケーションの設定を変更するには:

1. ポータルで、マネージド アプリケーションを選択します。

1. **[JIT 構成]** を選択し、必要に応じて設定を変更します。

   ![アクセス設定を変更する](./media/approve-just-in-time-access/change-settings.png)

1. 操作が完了したら、**[保存]** をクリックします。

## <a name="approve-requests"></a>要求の承認

発行元がアクセスを要求すると、要求の通知が送信されます。 JIT アクセス要求は、マネージド アプリケーションから直接承認するか、Azure AD Privileged Identity Management サービスを介してすべてのマネージド アプリケーションで承認することができます。 ジャストインタイム アクセスを使用するには、[Azure Active Directory P2 ライセンス](../../active-directory/privileged-identity-management/subscription-requirements.md)が必要です。

マネージド アプリケーションを介して要求を承認するには:

1. マネージド アプリケーションで **[JIT アクセス]** を選択し、**[要求の承認]** を選択します。

   ![要求の承認](./media/approve-just-in-time-access/approve-requests.png)
 
1. 承認する要求を選択します。

   ![要求を選択する](./media/approve-just-in-time-access/select-request.png)

1. フォームで、承認の理由を指定し、**[承認]** を選択します。

Azure AD Privileged Identity Management を介して要求を承認するには:

1. **[すべてのサービス]** を選択し、**Azure AD Privileged Identity Management** の検索を開始します。 使用可能なオプションから、それを選択します。

   ![サービスを検索する](./media/approve-just-in-time-access/search.png)

1. **[要求の承認]** を選択します。

   ![[要求の承認] を選択する](./media/approve-just-in-time-access/select-approve-requests.png)

1. **[Azure Managed Applications]** を選択し、承認する要求を選択します。

   ![要求を選択する](./media/approve-just-in-time-access/view-requests.png)

## <a name="next-steps"></a>次のステップ

JIT アクセス権付きのマネージド アプリケーションの発行方法については、[Azure Managed Applications でのジャストインタイム アクセスの要求](request-just-in-time-access.md)に関する記事を参照してください。
