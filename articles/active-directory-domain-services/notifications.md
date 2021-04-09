---
title: Azure AD Domain Services のメール通知 | Microsoft Docs
description: Azure Active Directory Domain Services のマネージド ドメインでの問題について警告するメール通知を構成する方法について説明します
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: b9af1792-0b7f-4f3e-827a-9426cdb33ba6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 4242092af86f8bc06ef9c9eba068ce07725de01f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619166"
---
# <a name="configure-email-notifications-for-issues-in-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services での問題に関するメール通知を構成する

Azure Active Directory Domain Services (Azure AD DS) のマネージド ドメインの正常性は、Azure プラットフォームによって監視されます。 Azure portal の正常性状態ページには、マネージド ドメインのすべてのアラートが表示されます。 適切なタイミングで問題に応答するために、Azure AD DS マネージド ドメインで問題が検出されたらすぐに正常性アラートでレポートするようにメール通知を構成できます。

この記事では、マネージド ドメイン用にメール通知受信者を構成する方法について説明します。

## <a name="email-notification-overview"></a>メール通知の概要

マネージド ドメインでの問題を警告するために、メール通知を構成できます。 これらのメール通知には、アラートが存在するマネージド ドメインのほか、検出時刻や Azure portal の正常性ページへのリンクが記載されます。 提供されているトラブルシューティングのアドバイスに従って、問題を解決できます。

次のメール通知の例は、マネージド ドメインで重大な警告またはアラートが生成されたことを示しています。

![メール通知の例](./media/active-directory-domain-services-alerts/email-alert.png)

> [!WARNING]
> メッセージ内のリンクをクリックする前に、メールが確認済みの Microsoft 送信者から送られてきたものであることを必ず確認してください。 メール通知は、常に `azure-noreply@microsoft.com` アドレスから送信されます。

### <a name="why-would-i-receive-email-notifications"></a>メール通知が送信される理由

Azure AD DS は、マネージド ドメインに関する重要な更新情報についてメール通知を送信します。 これらの通知は、サービスに影響があり、すぐに対処する必要がある、緊急の問題に限られます。 それぞれのメール通知は、マネージド ドメイン上のアラートによってトリガーされます。 アラートは Azure portal にも表示され、[Azure AD DS の正常性ページ][check-health]で確認できます。

Azure AD DS から、広告、更新プログラム、または営業目的のメールが送信されることはありません。

### <a name="when-will-i-receive-email-notifications"></a>メール通知が送信されるタイミング

通知は、マネージド ドメインで[新しいアラート][troubleshoot-alerts]が検出されると、直ちに送信されます。 アラートが解決されない場合は、さらにメール通知が 4 日に 1 回、リマインダーとして送信されます。

### <a name="who-should-receive-the-email-notifications"></a>メール通知の宛先

Azure AD DS のメール受信者のリストは、マネージド ドメインを管理および変更できるユーザーで構成する必要があります。 このメール リストは、すべてのアラートや問題に対する "最初のレスポンダー" であると考えてください。

メール通知には、メール受信者を最大 5 人まで追加できます。 メール通知の受信者を 6 人以上にしたい場合は、配布リストを作成し、代わりにそれを通知リストに追加します。

また、Azure AD ディレクトリのすべての "*グローバル管理者*" と *AAD DC Administrators* グループのすべてのメンバーがメール通知を受信するようにすることもできます。 Azure AD DS から通知を送信できる宛先メール アドレスは最大 100 件です (グローバル管理者と AAD DC Administrators のリストを含む)。

## <a name="configure-email-notifications"></a>電子メール通知の構成

メール通知の既存の受信者を確認したり、他の受信者を追加したりするには、次の手順を実行します。

1. Azure portal で、**Azure AD Domain Services** を検索して選択します。
1. 目的のマネージド ドメインを選択します (例: *aaddscontoso.com*)。
1. Azure AD DS リソース ウィンドウの左側で、 **[通知設定]** を選択します。 メール通知の既存の受信者が表示されます。
1. メールの受信者を追加するには、[その他の受信者] テーブルにメール アドレスを入力します。
1. 終了したら、最上部のナビゲーションで **[保存]** を選択します。

> [!WARNING]
> 通知設定を変更すると、自分の通知設定だけでなく、マネージド ドメイン全体の通知設定が更新されます。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="i-received-an-email-notification-for-an-alert-but-when-i-logged-on-to-the-azure-portal-there-was-no-alert-what-happened"></a>アラートのメール通知を受信しましたが、Azure portal にログオンしてもアラートが見当たりません。 なぜでしょうか?

アラートは、解決されると、Azure portal から消去されます。 最も可能性が高い理由として、マネージド ドメインのアラートがメール通知を受け取った他の受信者によって解決されたか、Azure プラットフォームによって自動的に解決されたことが考えられます。

### <a name="why-can-i-not-edit-the-notification-settings"></a>通知設定を編集できないのはなぜですか?

Azure portal の通知設定ページにアクセスできない場合は、マネージド ドメインを編集するアクセス許可がありません。 グローバル管理者に連絡して Azure AD DS のリソースを編集するためのアクセス許可を取得するか、受信者リストから削除してもらう必要があります。

### <a name="i-dont-seem-to-be-receiving-email-notifications-even-though-i-provided-my-email-address-why"></a>メール アドレスを指定したにもかかわらず、メール通知が届かないようです。 なぜですか?

通知が迷惑メール フォルダーに入っていないことを確認し、`azure-noreply@microsoft.com` を送信者として許可してください。

## <a name="next-steps"></a>次のステップ

レポートされる可能性があるいくつかの問題のトラブルシューティングの詳細については、[マネージド ドメインでのアラートの解決][troubleshoot-alerts]に関するページを参照してください。

<!-- INTERNAL LINKS -->
[check-health]: check-health.md
[troubleshoot-alerts]: troubleshoot-alerts.md
