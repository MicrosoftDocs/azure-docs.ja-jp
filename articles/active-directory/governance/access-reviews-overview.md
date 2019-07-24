---
title: アクセス レビューとは - Azure Active Directory | Microsoft Docs
description: Azure Active Directory アクセス レビューを使用すると、組織のガバナンス、リスク管理、コンプライアンスの取り組みを満たすように、グループ メンバーシップとアプリケーションのアクセスを制御することができます。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 01/18/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1563a023f397999deb5c6abd40843d6a376b0492
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2019
ms.locfileid: "58576124"
---
# <a name="what-are-azure-ad-access-reviews"></a>Azure AD アクセス レビューとは

組織で Azure Active Directory (Azure AD) アクセス レビューを使うことにより、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、ロールの割り当てを効率的に管理できます。 ユーザーのアクセスを定期的にレビューし、適切なユーザーのみが継続的なアクセス権を持っていることを確認できます。

次のビデオでは、アクセス レビューの簡単な概要を説明します。

>[!VIDEO https://www.youtube.com/embed/kDRjQQ22Wkk]

## <a name="why-are-access-reviews-important"></a>アクセス レビューが重要である理由

Azure AD を使用すると、組織内での共同作業、およびパートナーなどの外部組織のユーザーとの共同作業が可能です。 ユーザーは、グループへの参加、ゲストの招待、クラウド アプリへの接続、作業用や個人用のデバイスからのリモート作業を行うことができます。 セルフ サービスの機能を利用する便利さゆえ、よりよいアクセス管理機能が必要になっています。

- 新しい従業員が参加するとき、生産性のための適切なアクセス権をどのようにして確実に付与しますか。
- ユーザーがチームを移動したり会社を辞めたりするとき (特にゲストが関係している場合)、どのようにして古いアクセス権を確実に削除しますか。
- 過剰なアクセス権は、アクセス権の制御の欠如を示しているため、監査所見やセキュリティ侵害につながる可能性があります。
- リソースの所有者と事前に連絡を取り、リソースへのアクセス権を持つユーザーを定期的にレビューしていることを確認する必要があります。

## <a name="when-to-use-access-reviews"></a>アクセス レビューを使用すべきとき

- **特権ロールのユーザーが多すぎるとき:** 管理者アクセス権を持っているユーザーの数、その内で全体管理者の数、管理タスクに割り当てられた後で削除されていない招待ゲストまたはパートナーがいるかどうかを確認するのはよいことです。 [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) エクスペリエンスでは、全体管理者などの [Azure AD ロール](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)またはユーザー アクセス管理者などの [Azure リソース ロール](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)でロール割り当てユーザーを認定できます。
- **自動化が実行不可能なとき:** セキュリティ グループまたは Office 365 のグループで動的メンバーシップに対するルールを作成できますが、HR データが Azure AD 内にない場合や、ユーザーがグループを離れた後で後任のトレーニングのためにアクセスする必要がある場合はどうしますか。 そのようなときは、そのグループに対してレビューを作成し、まだアクセスを必要とするユーザーが継続的アクセス権を持っていることを確認することができます。
- **グループが新しい目的に使用されるとき:** Azure AD に同期されるグループがある場合、または営業チーム グループの全員に対して Salesforce アプリケーションを有効にする場合は、異なるリスク コンテンツでグループを使用する前にグループ メンバーシップを確認するようグループ所有者に依頼するとよいことがあります。
- **ビジネス クリティカルなデータ アクセス:** 特定のリソースでは、監査のため、IT 組織の外部のユーザーに対し、定期的にサインオフして、アクセスが必要な正当な理由を示すよう依頼することが必要な場合があります。
- **ポリシーの例外リストを維持するため:** 理想的な世界では、すべてのユーザーが、組織のリソースへのアクセスをセキュリティで保護するアクセス ポリシーに従っているでしょう。 ただし、ビジネス ケースには、例外を認める必要がある場合もあります。 IT 管理者は、このタスクを管理し、ポリシー例外の見落としを防ぎ、これらの例外が定期的にレビューされている証明を監査者に提供することができます。
- **グループのゲストがまだ必要であることを確認するようグループの所有者に依頼するため:** 一部のオンプレミス IAM では従業員のアクセスは自動化される場合がありますが、招待されたゲストではされません。 グループでビジネスの重要なコンテンツへのアクセス権をゲストに付与する場合、ゲストにアクセスに対する正当なビジネス ニーズがまだあることを確認するのは、グループ所有者の責任です。
- **定期的にレビューを繰り返す場合:** 毎週、毎月、毎四半期、毎年などの設定された周期でユーザーのアクセス レビューを繰り返すよう設定することができ、レビュー担当者は各レビューの開始時に通知されます。 レビュー担当者は、使いやすいインターフェイスとスマートな推奨事項の助けを借りてアクセスを承認または拒否することができます。

## <a name="where-do-you-create-reviews"></a>レビューを作成する場所

何をレビューするかにより、Azure AD アクセス レビュー、Azure AD エンタープライズ アプリ (プレビュー)、または Azure AD PIM でアクセス レビューを作成します。

| ユーザーのアクセス権 | レビュー担当者になれるユーザー | レビューが作成される場所 | レビュー担当者のエクスペリエンス |
| --- | --- | --- | --- |
| セキュリティ グループ メンバー</br>Office グループ メンバー | 指定されたレビュー担当者</br>グループ所有者</br>自己レビュー | Azure AD アクセス レビュー</br>Azure AD グループ | アクセス パネル |
| 接続されたアプリに割り当て | 指定されたレビュー担当者</br>自己レビュー | Azure AD アクセス レビュー</br>azure AD エンタープライズ アプリ (プレビュー) | アクセス パネル |
| Azure AD ロール | 指定されたレビュー担当者</br>自己レビュー | Azure AD PIM | Azure ポータル |
| Azure リソース ロール | 指定されたレビュー担当者</br>自己レビュー | Azure AD PIM | Azure ポータル |

## <a name="prerequisites"></a>前提条件

アクセス レビューを使用するには、次のいずれかのライセンスが必要です。

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5 ライセンス

詳細については、「[方法:Azure Active Directory Premium へのサインアップ](../fundamentals/active-directory-get-started-premium.md)」または「[Enterprise Mobility + Security E5 Trial](https://aka.ms/emse5trial)」をご覧ください。

## <a name="get-started-with-access-reviews"></a>アクセス レビューでの作業の開始

アクセス レビューの作成と実行の詳細については、この短いデモをご覧ください。

>[!VIDEO https://www.youtube.com/embed/6KB3TZ8Wi40]

組織にアクセス レビューを展開する準備ができたら、ビデオの手順に従って、配布を準備し、管理者をトレーニングして、最初のアクセス レビューを作成してください。

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

## <a name="enable-access-reviews"></a>アクセス レビューを有効にする

アクセス レビューを有効にするには、次の手順のようにします。

1. グローバル管理者またはユーザー管理者として、アクセス レビューを使用する [Azure portal](https://portal.azure.com) にサインインします。

1. **[すべてのサービス]** をクリックして、アクセス レビュー サービスを見つけます。

1. **[アクセス レビュー]** をクリックします。

    ![[すべてのサービス] - [アクセス レビュー]](./media/access-reviews-overview/all-services-access-reviews.png)

1. ナビゲーション リストで、**[配布準備をする]** をクリックして **[アクセス レビューの配布準備]** ページを開きます。

    ![アクセス レビューの配布準備](./media/access-reviews-overview/onboard-button.png)

1. **[作成]** をクリックして、現在のディレクトリでアクセス レビューを有効にします。

    ![アクセス レビューの配布準備](./media/access-reviews-overview/onboard-access-reviews.png)

    次にアクセス レビューを開始すると、アクセス レビュー オプションが有効になります。

    ![有効になったアクセス レビュー](./media/access-reviews-overview/access-reviews-enabled.png)

## <a name="next-steps"></a>次の手順

- [グループまたはアプリケーションのアクセス レビューを作成する](create-access-review.md)
- [Azure AD 管理者ロールに含まれるユーザーのアクセス レビューを作成する](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)
- [グループまたはアプリケーションに対するアクセスのレビュー](perform-access-review.md)
- [グループまたはアプリケーションに対するアクセス レビューを完了する](complete-access-review.md)
