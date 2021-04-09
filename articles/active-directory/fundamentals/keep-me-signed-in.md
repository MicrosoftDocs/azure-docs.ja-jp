---
title: Azure Active Directory アカウントに "サインインの状態を維持しますか?" のプロンプトを構成する
description: "\"サインインの状態を維持しますか?\" プロンプトを表示する \"サインインしたままにする (KMSI)\" 機能、Azure Active Directory ポータルでこの機能を構成する方法、およびサインインの問題のトラブルシューティングを行う方法について説明します。"
services: active-directory
author: CelesteDG
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: fundamentals
ms.date: 06/05/2020
ms.author: celested
ms.reviewer: asteen, jlu, hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: bed6bc43dfc15abf2bdf9f38a5de2240d348d6fb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89320258"
---
# <a name="configure-the-stay-signed-in-prompt-for-azure-ad-accounts"></a>Azure AD アカウントに "サインインの状態を維持しますか?" のプロンプトを構成する

"サインインしたままにする (KMSI)" 機能は、ユーザーが正常にサインインした後、"**サインインの状態を維持しますか?** " というプロンプトを表示します。 ユーザーがこのプロンプトに **[はい]** と応答した場合、"サインインしたままにする" サービスにより永続的な [更新トークン](../develop/developer-glossary.md#refresh-token)が付与されます。 フェデレーション テナントの場合、このプロンプトは、ユーザーがフェデレーション ID サービスで正常に認証された後に表示されます。

次の図は、マネージド テナントとフェデレーション テナントのユーザー サインイン フローと、新しい "サインインしたままにする" プロンプトを示しています。 このフローにはスマート ロジックが含まれていて、機械学習システムによってリスクの高いサインインまたは共有デバイスからのサインインが検出されると、 **[サインインの状態を維持しますか?]** オプションは表示されなくなります。

:::image type="content" source="./media/keep-me-signed-in/kmsi-workflow.png" alt-text="マネージド テナントとフェデレーション テナントのユーザー サインイン フローを示す図":::

> [!NOTE]
> "サインインしたままにする" オプションを構成するには、Azure Active Directory (Azure AD) Premium 1 エディション、Premium 2 エディション、または Basic エディションを使用しているか、Microsoft 365 ライセンスを持っている必要があります。 ライセンスとエディションの詳細については、「[Azure AD Premium へのサインアップ](active-directory-get-started-premium.md)」を参照してください。<br><br>Azure AD のワールドワイド インスタンスを使用している中国のお客様は、Azure AD Premium および Basic エディションを使用できます。 中国の 21Vianet が運営する Azure サービスでは、Azure AD Premium エディションと Basic エディションは現在サポートされていません。 詳細については、[Azure AD フォーラム](https://feedback.azure.com/forums/169401-azure-active-directory/)を利用してお問い合わせください。

## <a name="configure-kmsi"></a>KMSI の構成

1. ディレクトリのグローバル管理者アカウントを使用して [Azure portal](https://portal.azure.com/) にサインインします。
1. **Azure Active Directory** を選択し、 **[会社のブランド]** を選択し、 **[構成]** を選択します。
1. **[詳細設定]** セクションで、 **[サインインしたままにするオプションを表示する]** 設定を見つけます。

   この設定では、ユーザーが明示的にサインアウトするまで Azure AD にサインインしたままにするかどうかを選択できます。
   * **[いいえ]** を選択した場合、 **[サインインの状態を維持しますか?]** オプションはユーザーが正常にサインインした後に非表示になります。ユーザーは、ブラウザーを閉じて再度開くたびにサインインする必要があります。
   * **[はい]** を選択した場合は、 **[サインインの状態を維持しますか?]** オプションがユーザーに表示されます。

    :::image type="content" source="./media/keep-me-signed-in/kmsi-company-branding-advanced-settings-kmsi-1.png" alt-text="[サインインしたままにするオプションを表示する] 設定を示すスクリーンショット":::

## <a name="troubleshoot-sign-in-issues"></a>サインアップに関する問題のトラブルシューティング

ユーザーが **[サインインの状態を維持しますか?]** プロンプトに対する操作をしないままサインイン試行を中止した場合、次の図に示すように、中断を示すサインイン ログ エントリが表示されます。

:::image type="content" source="./media/keep-me-signed-in/kmsi-stay-signed-in-prompt.png" alt-text="サインインの状態を維持しますか? プロンプトを表示する":::

サインイン エラーの詳細は次のとおりです。この例では強調表示されています。

* **サインイン エラー コード**: 50140
* **エラーの理由**: ユーザーがサインインしたときの "サインインしたままにする" 割り込みによりエラーが発生しました。

:::image type="content" source="./media/keep-me-signed-in/kmsi-sign-ins-log-entry.png" alt-text="サインインしたままにする 割り込みが記録されたサインイン ログ エントリの例":::

ユーザーに割り込みが表示されないようにするには、ブランドの詳細設定で **[サインインしたままにするオプションを表示する]** 設定を **[いいえ]** に設定します。 これにより、Azure AD ディレクトリ内のすべてのユーザーに対して KMSI プロンプトが無効になります。

また、条件付きアクセスで永続的なブラウザー セッション制御を使用して、ユーザーに KMSI プロンプトが表示されないようにすることもできます。 このオプションを使用すると、選択したユーザー グループ (全体管理者など) に対して KMSI プロンプトを無効にすることができます。ディレクトリ内の残りのユーザーのサインイン動作は影響を受けません。 詳細については、[ユーザー サインインの頻度](../conditional-access/howto-conditional-access-session-lifetime.md)に関するページを参照してください。 

ユーザーにベネフィットがある場合にのみ KMSI プロンプトが表示されるようにするには、次のような場合に意図的に KMSI プロンプトが表示されないようにします。

* ユーザーがシームレス SSO と統合 Windows 認証 (IWA) を使用してサインインしている
* ユーザーが Active Directory フェデレーション サービス (AD FS) と IWA を使用してサインインしている
* ユーザーがテナントのゲストである
* ユーザーのリスク スコアが高い
* ユーザーまたは管理者の同意フロー中にサインインが発生する
* 永続的なブラウザー セッション制御が条件付きアクセス ポリシーで構成されている

## <a name="next-steps"></a>次のステップ

サインイン セッションのタイムアウトに影響するその他の設定について学習する。

* Microsoft 365 - [アイドル セッション タイムアウト](/sharepoint/sign-out-inactive-users)
* Azure AD 条件付きアクセス - [ユーザー サインインの頻度](../conditional-access/howto-conditional-access-session-lifetime.md)
* Azure portal - [ディレクトリレベルの非アクティブ タイムアウト](../../azure-portal/set-preferences.md#change-the-directory-timeout-setting-admin)