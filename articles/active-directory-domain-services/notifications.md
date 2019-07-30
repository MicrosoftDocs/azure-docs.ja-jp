---
title: Azure Active Directory Domain Services:通知設定 | Microsoft Docs
description: Azure AD Domain Services の通知設定
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: b9af1792-0b7f-4f3e-827a-9426cdb33ba6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: a89b13d40f4eea08ecdb0f1eb8d68d1a146aca2b
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2019
ms.locfileid: "67472792"
---
# <a name="notification-settings-in-azure-ad-domain-services"></a>Azure AD Domain Services の通知設定

Azure AD Domain Services の通知を使用することで、ご利用のマネージド ドメインで正常性アラートが検出されたときにすぐに情報を把握することができます。  

この機能を利用できるのは、クラシック仮想ネットワーク上にないマネージド ドメインでのみです。


## <a name="how-to-check-your-azure-ad-domain-services-email-notification-settings"></a>Azure AD Domain Services のメール通知設定を確認する方法

1. Azure portal で、[Azure AD Domain Services ページ](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices)に移動します。
2. ご利用のマネージド ドメインをテーブルから選択します。
3. 左側のナビゲーションで、 **[通知設定]** を選択します。

このページには、Azure AD Domain Services のメール通知の対象となる受信者がすべて表示されます。

## <a name="what-does-an-email-notification-look-like"></a>メール通知の外観

次の図は、メール通知の例です。

![メール通知の例](./media/active-directory-domain-services-alerts/email-alert.png)

このメールには、アラートが存在するマネージド ドメインのほか、検出時刻、Azure portal における Azure AD Domain Services の正常性ページへのリンクが記載されています。

> [!WARNING]
> メール内のリンクは、Microsoft であることが確認された差出人から送信されたメールであることを必ず確かめたうえでクリックしてください。 このメールは常に azure-noreply@microsoft.com から送信されます。


## <a name="why-would-i-receive-email-notifications"></a>メール通知が送信される理由

Azure AD Domain Services は、ユーザーのドメインに関して重要な最新情報を提供する目的でメール通知を送信します。  これらの通知の用途は、サービスへの影響が差し迫っている、すぐに対処すべき緊急の問題に限られます。 それぞれのメール通知は、マネージド ドメイン上のアラートによってトリガーされます。 これらのアラートは Azure portal にも表示され、[Azure AD Domain Services の正常性ページ](check-health.md)で確認できます。

広告、更新プログラム、営業目的のメールが Azure AD Domain Services からこのリストに送信されることはありません。

## <a name="when-will-i-receive-email-notifications"></a>メール通知が送信されるタイミング

通知は、ご利用のマネージド ドメインで[新しいアラート](troubleshoot-alerts.md)が検出されると直ちに送信されます。 アラートが解決されない場合、メール通知が 4 日に 1 回リマインダーとして送信されます。

## <a name="who-should-receive-the-email-notifications"></a>メール通知の宛先


 Azure AD Domain Services のメール受信者のリストは、マネージド ドメインを管理し、かつ変更を加えることのできるユーザーで構成することをお勧めします。 このメール リストは、見つかった問題に対する "最初のレスポンダー" と考えてください。 別途追加したいメール アドレスが 5 件を超える場合は、配布リストを作成して通知リストに追加することをお勧めします。

Azure AD Domain Services に関する通知には、さらに最大 5 件のメール アドレスを追加することができます。 また、対象ディレクトリのすべての全体管理者と "AAD DC Administrators" グループの個々のメンバーに Azure AD Domain Services のメール通知を送信することもできます。 Azure AD Domain Services から送信できる通知の宛先メール アドレスは最大 100 件 (全体管理者と AAD DC Administrators のリストを含む) です。


## <a name="how-to-add-an-additional-email-recipient"></a>メールの受信者を追加する方法

> [!WARNING]
> この通知設定を変更すると、自分の通知設定だけでなく、マネージド ドメイン全体の通知設定が変更されます。

1. Azure portal で、[Azure AD Domain Services ページ](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices)に移動します。
2. ご利用のマネージド ドメインをクリックします。
3. 左側のナビゲーションで、 **[通知設定]** をクリックします。
4. メールを追加するには、[その他の受信者] テーブルにメール アドレスを入力します。
5. 最上部のナビゲーションで [保存] をクリックします。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

#### <a name="i-received-an-email-notification-for-an-alert-but-when-i-logged-on-to-the-azure-portal-there-was-no-alert-what-happened"></a>アラートのメール通知を受信しましたが、Azure portal にログオンしてもアラートが見当たりません。 なぜでしょうか?

解決したアラートは Azure portal から消えます。 最も可能性が高い理由として、マネージド ドメインのアラートが、メール通知を受け取った他の受信者によって解決されたか、Azure AD Domain Services によって自動的に解決されたことが考えられます。


#### <a name="why-can-i-not-edit-the-notification-settings"></a>通知設定を編集できないのはなぜですか?

Azure portal の通知設定ページにアクセスできない場合、Azure AD Domain Services を編集するアクセス許可はありません。 全体管理者に連絡して Azure AD Domain Services のリソースを編集するためのアクセス許可を取得するか、受信者リストから削除してもらう必要があります。

#### <a name="i-dont-seem-to-be-receiving-email-notifications-even-though-i-provided-my-email-address-why"></a>メール アドレスを指定したにもかかわらず、メール通知が届かないようです。 なぜですか?

迷惑メール フォルダーに通知が入っていないかを確認し、送信者 (azure-noreply@microsoft.com) をホワイトリストに登録するようにしてください。

## <a name="next-steps"></a>次の手順
- [マネージド ドメインのアラートを解決する](troubleshoot-alerts.md)
- [Azure AD Domain Services の詳細を確認する](overview.md)
- [製品チームに連絡する](contact-us.md)

## <a name="contact-us"></a>お問い合わせ
[フィードバックの共有およびサポートについては](contact-us.md)、Azure Active Directory Domain Services 製品チームにお問い合わせください。
