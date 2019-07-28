---
title: Azure Active Directory の条件付きアクセスとは | Microsoft Docs
description: Azure Active Directory の条件付きアクセスを利用すると、リソースに対してだれがアクセスを試みているかだけでなく、どのようにアクセスしようとしているかという点も加味して、アクセスを許可するかどうかを自動的に判断する機構を実装することができます。その具体的な方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 02/14/2019
ms.author: joflore
author: microsoftguyjflo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 370771d44e2fbbf0fca7af453f7425ebc1b1bdcd
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612611"
---
# <a name="what-is-conditional-access"></a>条件付きアクセスとは

セキュリティは、クラウドを使用する組織の最大の懸念事項です。 クラウド セキュリティの重要な側面は、クラウド リソースを管理する際の ID とアクセスです。 モバイルを重視したクラウド中心の世界では、ユーザーはさまざまなデバイスやアプリを使用してどこからでも組織のリソースにアクセスできます。 このため、だれがリソースにアクセスできるかに重点を置くだけでは十分ではなくなっています。 セキュリティと生産性のバランスをうまく取るためには、リソースへのアクセス方法も考慮してアクセスの制御を決定する必要があります。 Azure Active Directory (Azure AD) の条件付きアクセスを使用すると、この要件に対処することができます。 条件付きアクセスは、Azure Active Directory の機能です。 条件付きアクセスを使用すると、クラウド アプリへのアクセスを許可するかどうかの判断を各種の条件に基づいて自動的に行うアクセスの制御を実装できます。

条件付きアクセス ポリシーは、第 1 段階認証が完了した後で適用されます。 つまり、条件付きアクセスは、サービス拒否 (DoS) 攻撃などのシナリオの防御の最前線を意図したものではありませんが、これらのイベントのシグナル (サインインのリスク レベル、要求の場所など) を利用してアクセスを判別できます。  

![コントロール](./media/overview/81.png)

この記事では、Azure AD の条件付きアクセスの概念的な概要について説明します。

## <a name="common-scenarios"></a>一般的なシナリオ

モバイル ファースト、クラウド ファーストの世界で、Azure Active Directory を使用してデバイス、アプリ、およびサービスにどこからでもサインオンできます。 デバイス (BYOD を含みます) や企業ネットワーク外での作業、サードパーティが提供する SaaS アプリの急増によって、皆さんは次の 2 つの対立する目標を達成することを迫られています。

- 場所や時間を問わず、常にユーザーの生産性を高められるようにすること
- 企業の資産を常に保護すること

条件付きアクセス ポリシーを使用すると、必要な条件下で適切なアクセスの制御を適用できます。 Azure AD の条件付きアクセスでは、必要なときにセキュリティを強化し、必要でない場合にはユーザーに干渉しないようにすることができます。

条件付きアクセスが役立ついくつかの一般的なアクセスの問題を次に示します。

- **[サインイン リスク](conditions.md#sign-in-risk)** :Azure AD Identity Protection では、サインイン リスクを検出します。 検出されたサインイン リスクが悪意のあるユーザーを示している場合、どのようにアクセスを制限しますか。 正当なユーザーによってサインインが実行されたことを示す、より強力な証拠が必要になったとしたら、どうすればよいのでしょうか。 特定のユーザーをアプリにアクセスできないようブロックして差し支えないほどのきわめて不審な点がある場合は、どうしますか。  
- **[ネットワークの場所](location-condition.md)** :Azure AD はどこからでもアクセスできます。 IT 部門の管理下にないネットワークの場所からアクセスが試行された場合はどうしますか。 ユーザー名とパスワードの組み合わせが、企業ネットワークからのアクセスを試行するうえでの十分な身元証明になることがあります。 他の予期しない国または地域から開始されたアクセス試行に対してより強力な身元証明が必要な場合はどうしますか。 特定の場所からのアクセス試行をブロックする必要がある場合はどうしますか。  
- **[デバイスの管理](conditions.md#device-platforms)** :Azure AD では、ユーザーはモバイル デバイスと個人デバイスを含むさまざまなデバイスからクラウド アプリにアクセスできます。 IT 部門によって管理されているデバイスによってのみアクセスを試行できるようにする必要がある場合はどうしますか。 環境内のクラウド アプリで特定の種類のデバイスによるアクセスをブロックする必要がある場合はどうしますか。
- **[クライアント アプリケーション](conditions.md#client-apps)** :現在、Web ベースのアプリ、モバイル アプリ、デスクトップ アプリなどのさまざまな種類のアプリを使用して多くのクラウド アプリにアクセスできます。 既知の問題の原因となるクライアント アプリの種類を使用してアクセス試行が実行された場合はどうしますか。 特定の種類のアプリについて IT 部門が管理するデバイスが必要な場合はどうしますか。

これらの質問と関連する回答は、Azure AD の条件付きアクセスの一般的なアクセス シナリオを表します。
条件付きアクセスは、ポリシー ベースのアプローチを使用してアクセス シナリオを処理できるようにする Azure Active Directory の機能です。

> [!VIDEO https://www.youtube.com/embed/eLAYBwjCGoA]

## <a name="conditional-access-policies"></a>条件付きアクセス ポリシー

条件付きアクセス ポリシーは、次のパターンを使用したアクセス シナリオの定義です。

![コントロール](./media/overview/10.png)


**When this happens** (これが発生した場合は) では、ポリシーをトリガーする理由を定義します。 この理由は、満たされている条件のグループによって特徴付けられます。 Azure AD の条件付きアクセスでは、2 つの割り当て条件が特別な役割を果たします。

- **[ユーザー](conditions.md#users-and-groups)** :アクセスを試行するユーザー (**Who (だれが)** )。
- **[クラウド アプリ](conditions.md#cloud-apps-and-actions)** :アクセス試行のターゲット (**What (何を)** )。

この 2 つの条件は、条件付きアクセス ポリシーでは必須です。 2 つの必須条件に加えて、アクセス試行の実行方法を説明する追加の条件を含めることもできます。 一般的な例として、モバイル デバイスの使用や、企業ネットワーク外の場所があります。 詳しくは、「[Azure Active Directory 条件付きアクセスの条件の概要](conditions.md)」をご覧ください。

条件とアクセスの制御の組み合わせによって、条件付きアクセス ポリシーを表現します。

![コントロール](./media/overview/51.png)

Azure AD の条件付きアクセスを使うと、承認されたユーザーがクラウド アプリにどのようにアクセスするかを制御できます。 条件付きアクセス ポリシーの目的は、クラウド アプリへのアクセス試行の実行方法に基づいて、アクセス試行に対して追加のアクセスの制御を適用することです。

ポリシーベースのアプローチを使用してクラウド アプリへのアクセスを保護すると、技術的な実装について心配することなく、この記事で概説されている構造を使用して環境のポリシー要件の下書き作成を開始できます。

## <a name="azure-ad-conditional-access-and-federated-authentication"></a>Azure AD の条件付きアクセスとフェデレーション認証

条件付きアクセス ポリシーは、[フェデレーション認証](../../security/azure-ad-choose-authn.md#federated-authentication)とシームレスに連動します。 このサポートには、サポートされているすべての条件と制御の他に、[Azure AD レポート](../reports-monitoring/concept-sign-ins.md)を使用した、アクティブなユーザー サインインへのポリシーの適用状況の可視化が含まれます。

"*Azure AD を使用したフェデレーション認証*" とは、信頼された認証サービスによって Azure AD に対するユーザー認証が処理されることを意味します。 信頼された認証サービスとは、たとえば Active Directory フェデレーション サービス (AD FS) などのフェデレーション サービスです。 この構成では、プライマリ ユーザー認証がサービスで実行された後、個々のアプリケーションへのサインインに Azure AD が使用されます。 ユーザーがアクセスしようとしているアプリケーションへのアクセスが許可される前に、Azure AD の条件付きアクセスが適用されます。 

構成された条件付きアクセス ポリシーで多要素認証が要求されている場合、Azure AD では既定で Azure MFA が使用されます。 フェデレーション サービスを MFA に使用する場合は、[PowerShell](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainfederationsettings) で `-SupportsMFA` を `$true` に設定することで、MFA が必要なときにフェデレーション サービスにリダイレクトされるよう Azure AD を構成できます。 この設定は、`wauth= http://schemas.microsoft.com/claims/multipleauthn` を使用して Azure AD によって発行された MFA チャレンジ要求をサポートするフェデレーション認証サービスで機能します。

ユーザーがフェデレーション認証サービスにサインインした後、他のポリシー要件 (デバイス コンプライアンスや承認されたアプリケーションなど) が Azure AD によって処理されます。

## <a name="license-requirements"></a>ライセンスの要件

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

[Microsoft 365 Business ライセンス](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)をお持ちのお客様も、条件付きアクセス機能にアクセスできます。 

## <a name="next-steps"></a>次の手順

お使いの環境に条件付きアクセスを実装する方法については、「[Azure Active Directory の条件付きアクセスの展開を計画する](plan-conditional-access.md)」をご覧ください。
