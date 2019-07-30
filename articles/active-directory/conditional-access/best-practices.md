---
title: Azure Active Directory の条件付きアクセスのベスト プラクティス | Microsoft Docs
description: 条件付きアクセス ポリシーを構成するときに知っておくべきことと避けるべきことについて説明します。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 01/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 79a27fb5e243d2590e3fae85c6c820c4a43af0d5
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509421"
---
# <a name="best-practices-for-conditional-access-in-azure-active-directory"></a>Azure Active Directory の条件付きアクセスのベスト プラクティス

[Azure Active Directory (Azure AD) の条件付きアクセス](../active-directory-conditional-access-azure-portal.md)を使用すると、許可されているユーザーがクラウド アプリにアクセスする方法を制御できます。 この記事では、次の項目に関する情報を提供します。

- 知っておくべきこと 
- 条件付きアクセス ポリシーを構成するときに避けるべきこと 

この記事は、「[Azure Active Directory の条件付きアクセスとは](../active-directory-conditional-access-azure-portal.md)」で説明する概念と用語について理解していることを前提としています。

## <a name="whats-required-to-make-a-policy-work"></a>ポリシーを機能させるために必要なこと

新しいポリシーを作成するとき、ユーザー、グループ、アプリケーション、またはアクセスの制御は選択されていません。

![クラウド アプリ](./media/best-practices/02.png)

ポリシーを機能させるには、以下を構成する必要があります。

| 対象           | 方法                                  | 理由 |
| :--            | :--                                  | :-- |
| **クラウド アプリ** |1 つまたは複数のアプリを選択します。  | 条件付きアクセス ポリシーの目的は、許可されているユーザーがクラウド アプリにアクセスする方法を制御できるようにすることです。|
| **ユーザーとグループ** | 選択したクラウド アプリにアクセスする権限が付与されたユーザーまたはグループを、少なくとも 1 つ選択します。 | ユーザーとグループが割り当てられていない条件付きアクセス ポリシーがトリガーされることはありません。 |
| **アクセス制御** | アクセスの制御を少なくとも 1 つ選択します。 | 条件が満たされた場合のポリシー プロセッサの対応を決める必要があります。 |

## <a name="what-you-should-know"></a>知っておくべきこと

### <a name="how-are-conditional-access-policies-applied"></a>条件付きアクセス ポリシーはどのように適用されますか。

クラウド アプリにアクセスするときに、複数の条件付きアクセス ポリシーが適用される場合があります。 この場合、適用されるすべてのポリシーを満たす必要があります。 たとえば、あるポリシーで MFA が要求されていて、別のポリシーで準拠するデバイスが要求されている場合は、MFA を経由し、準拠しているデバイスを使用する必要があります。 

すべてのポリシーは 2 つのフェーズで適用されます。

- **第 1** のフェーズでは、すべてのポリシーが評価され、満たされていないすべてのアクセス制御が収集されます。 
- **第 2** のフェーズでは、満たしていない要件を満たすように求められます。 いずれかのポリシーによってアクセスがブロックされる場合、ユーザーはブロックされ、他のポリシー制御を満たすよう求めるメッセージは表示されません。 いずれのポリシーによってもブロックされない場合は、次の順序で他のポリシー制御を満たすように求められます。

   ![順序](./media/best-practices/06.png)
    
   外部の MFA プロバイダー、次に使用条件が来ます。

### <a name="how-are-assignments-evaluated"></a>割り当てはどのように評価されますか。

すべての割り当ては、論理的に **AND** 処理されます。 複数の割り当てを構成した場合、ポリシーをトリガーするには、すべての割り当てが満たされている必要があります。  

組織のネットワークの外部から行われたすべての接続に適用される場所の条件を構成する必要がある場合は、次を行います。

- **すべての場所**を含める
- **すべての信頼できる IP** を除外する

### <a name="what-to-do-if-you-are-locked-out-of-the-azure-ad-admin-portal"></a>Azure AD 管理ポータルからロックアウトされた場合はどのように対処すればよいですか。

条件付きアクセス ポリシーが正しく設定されていないことが原因で Azure AD ポータルからロックアウトされた場合は、次の操作を行います。

- まだブロックされていない管理者が組織にいるかどうかが確認されます。 Azure Portal へのアクセス権を持つ管理者であれば、ご自身のサインインに影響するポリシーを無効にできます。 
- ポリシーを更新できる管理者が組織にいない場合は、サポート要求を送信する必要があります。 Microsoft サポートが、アクセスの妨げとなっている条件付きアクセス ポリシーを確認し、更新できます。

### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>Azure クラシック ポータルと Azure Portal の両方にポリシーが構成されている場合はどうなりますか。  

Azure Active Directory によって両方のポリシーが適用されます。ユーザーは、すべての要件が満たされた場合のみ、アクセスできます。

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>Intune Silverlight ポータルと Azure Portal の両方にポリシーが構成されている場合はどうなりますか。

Azure Active Directory によって両方のポリシーが適用されます。ユーザーは、すべての要件が満たされた場合のみ、アクセスできます。

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>同じユーザーに対して複数のポリシーが構成されている場合はどうなりますか。  

毎回のサインインで、すべてのポリシーが Azure Active Directory によって評価され、すべての要件が満たされていることを確認したうえで、ユーザーのアクセスが許可されます。 アクセスのブロックは、他のすべての構成設定よりも優先されます。 

### <a name="does-conditional-access-work-with-exchange-activesync"></a>条件付きアクセスは、Exchange ActiveSync と連携しますか。

はい。いくつかの[制限](block-legacy-authentication.md)がありますが、条件付きアクセス ポリシーで Exchange ActiveSync を使用できます。 

### <a name="how-should-you-configure-conditional-access-with-office-365-apps"></a>Office 365 アプリでは条件付きアクセスをどのように構成すればよいですか。

Office 365 のアプリは相互接続されているため、ポリシーを作成する際は、よく使うアプリを一緒に割り当てることをお勧めします。

相互接続される一般的なアプリケーションとしては、Microsoft Flow、Microsoft Planner、Microsoft Teams、Office 365 Exchange Online、Office 365 SharePoint Online、Office 365 Yammer などがあります。

このことは、多要素認証など、ユーザーとのやり取りを必要とするポリシーで、セッションまたはタスクの開始時にアクセスを制御する際に重要となります。 そのようにしなかった場合、ユーザーがアプリ内で一部のタスクを実行できなくなります。 たとえば、アンマネージド デバイスで SharePoint にアクセスする際にのみ多要素認証を要求し、メールにアクセスする際には要求しなかった場合、メールで作業しているユーザーが、SharePoint ファイルをメッセージに添付できなくなります。 詳細については、「[Azure Active Directory 条件付きアクセスのサービス依存関係の概要](service-dependencies.md)」をご覧ください。

## <a name="what-you-should-avoid-doing"></a>避けるべきこと

条件付きアクセス フレームワークは、優れた構成柔軟性を提供します。 ただし、柔軟性が高いということは、リリースの前に各構成ポリシーを慎重に見直して、望ましくない結果を避ける必要があることも意味します。 この状況では、**すべてのユーザー/グループ/クラウド アプリ**などの完全なセットに影響する割り当てには特に注意を払う必要があります。

お客様の環境では、次の構成を避ける必要があります。

**すべてのユーザー、すべてのクラウド アプリに対して:**

- **アクセスのブロック** - この構成では組織全体がブロックされるため、明らかによい方法ではありません。
- **準拠しているデバイスが必要です** - 登録されたデバイスをまだ持っていないユーザーに対して、このポリシーは Intune ポータルへのアクセスを含むすべてのアクセスをブロックします。 このポリシーにより、登録済みのデバイスを持たない管理者は、Azure Portal に戻ってポリシーを変更することができなくなります。
- **ドメインへの参加が必要** - このポリシーでは、ドメイン参加済みデバイスをまだ持っていない場合に、組織内のすべてのユーザーのアクセスがブロックされる可能性もあります。
- **アプリの保護ポリシーが必要** - このポリシーでは、Intune ポリシーがない場合に、組織内のすべてのユーザーのアクセスがブロックされる可能性もあります。 Intune のアプリの保護ポリシーがあるクライアント アプリケーションを使用していない管理者は、Intune や Azure などのポータルに戻ることが、このポリシーによってブロックされます。

**すべてのユーザー、すべてのクラウド アプリ、すべてのデバイス プラットフォームに対して:**

- **アクセスのブロック** - この構成では組織全体がブロックされるため、明らかによい方法ではありません。

## <a name="how-should-you-deploy-a-new-policy"></a>新しいポリシーはどのように展開する必要がありますか。

最初に、[what if ツール](what-if-tool.md)を使用してポリシーを評価します。

新しいポリシーを環境で使用する準備が整ったら、段階的に展開します。

1. 少数のユーザーにポリシーを適用し、想定どおりに動作することを確認します。 
1. ポリシーを拡張して、含めるユーザーを増やすとき。 続けてすべての管理者をポリシーから除外し、管理者がまだアクセスできて、変更が必要な場合にポリシーを更新できることを確認します。
1. 必要な場合にのみ、すべてのユーザーにポリシーを適用します。 

ベスト プラクティスとして、次のようなユーザー アカウントを作成します。

- ポリシー管理専用である 
- すべてのポリシーから除外されている

## <a name="policy-migration"></a>ポリシーの移行

次の理由から、Azure Portal で作成していないポリシーの移行を検討します。

- 以前は処理できなかったシナリオに対処できるようになった可能性があります。
- ポリシーを統合することで、管理する必要のあるポリシーの数を減らすことができます。   
- 一元的な場所ですべての条件付きアクセス ポリシーを管理できます。
- Azure クラシック ポータルは廃止されました。   

詳しくは、「[Azure Portal でクラシック ポリシーを移行する](policy-migration.md)」をご覧ください。

## <a name="next-steps"></a>次の手順

次のことが知りたい場合:

- 条件付きアクセス ポリシーの構成方法については、[Azure Active Directory の条件付きアクセスを使用した特定のアプリに対する MFA の要求](app-based-mfa.md)に関する記事をご覧ください。
- 条件付きアクセス ポリシーの計画方法については、「[方法: Azure Active Directory の条件付きアクセスの展開を計画する](plan-conditional-access.md)」をご覧ください。
