---
title: Azure Active Directory でのアプリケーション管理とは
description: クラウドとオンプレミス アプリケーションの Identity and Access Management (IAM) システムとして Azure Active Directory (AD) を使用する方法の概要です。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 01/22/2021
ms.author: kenwith
ms.reviewer: ''
ms.openlocfilehash: 247e824997fd95434246e49c78bf167f36e146c0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99258593"
---
# <a name="what-is-application-management"></a>アプリケーション管理とは

Azure AD は Identity and Access Management (IAM) システムです。 デジタル ID に関する情報を格納するための 1 つの場所を提供します。 ユーザー情報が格納される場所として Azure AD を使用するようにソフトウェア アプリケーションを構成できます。 

Azure AD は、アプリケーションと統合するように構成する必要があります。 つまり、どのアプリの ID 認証に使用されているかを把握している必要があります。 それらのアプリを Azure AD に認識させるプロセスと、Azure AD 側で行うべきアプリの処理方法は、アプリケーション管理と呼ばれます。

アプリケーションを管理するには、Azure Active Directory ポータルの [管理] セクションにある **[エンタープライズ アプリケーション]** ページを使用します。

![Azure AD ポータルの [管理] セクションの下にある [エンタープライズ アプリケーション] オプション。](media/what-is-application-management/enterprise-applications-in-nav.png)

## <a name="what-is-an-identity-and-access-management-iam-system"></a>Identity and Access Management (IAM) システムとは
アプリケーションとは、何らかの目的で使用されるソフトウェアのことです。 ほとんどのアプリでは、ユーザーにサインインが要求されます。

集中型 ID システムは、すべてのアプリケーションで使用できるユーザー情報を格納するための 1 つの場所を提供します。 このようなシステムが、Identity and Access Management (IAM) システムとして知られています。 Azure Active Directory は、Microsoft クラウドの IAM システムです。

>[!TIP]
>IAM システムは、ユーザー ID を追跡するための 1 つの場所を提供します。 Azure AD は、Microsoft クラウドの IAM システムです。

## <a name="why-manage-applications-with-a-cloud-solution"></a>クラウド ソリューションを使用してアプリケーションを管理する理由

組織には通常、ユーザーが業務を遂行するために使用している何百ものアプリケーションがあります。 ユーザーは、多数のデバイスおよび場所から、これらのアプリケーションにアクセスします。 日々、新しいアプリケーションが追加、開発され、また、廃止されます。 アプリとアクセス ポイントもそれだけ多くなるため、それらすべてと連携する ID ソリューションの使用が重要となります。

>[!TIP]
>Azure AD アプリケーション ギャラリーには、ID プロバイダーとして Azure AD で動作するようにあらかじめ構成されている多くの一般的なアプリケーションが含まれています。

## <a name="how-does-azure-ad-work-with-apps"></a>Azure AD がアプリでどのように動作するか

Azure AD は、クラウド アプリとオンプレミス アプリの中間に存在し、それらの ID 管理を担います。 

![Azure AD を通じてフェデレーションされているアプリを示す図](media/what-is-application-management/app-management-overview.png)

>[!TIP]
>会社の人事システムにユーザーを追加する際、それらのユーザーが自動的に Azure AD に追加されるよう、[ユーザー プロビジョニングを自動化](../app-provisioning/user-provisioning.md)することで管理コストを削減できます。 

## <a name="what-types-of-applications-can-i-integrate-with-azure-ad"></a>Azure AD と統合できるアプリケーションの種類

Azure AD は、ほぼすべてのアプリの ID システムとして使用することができます。 多くのアプリは、あらかじめ構成されているため、最小限の労力でセットアップすることができます。 こうした事前構成済みのアプリは、[Azure AD アプリ ギャラリー](/azure/active-directory/saas-apps/)で公開されています。 

まだギャラリーに存在しないアプリも、そのほとんどは、シングル サインオンを手動で構成することができます。 Azure AD には、いくつかの SSO オプションが用意されています。 最も一般的な方法としては、SAML ベースの SSO と OIDC ベースの SSO が挙げられます。 アプリを統合して SSO を実現する方法について詳しくは、「[シングル サインオンのオプション](sso-options.md)」を参照してください。 

オンプレミス アプリを使用している組織では、アプリ プロキシを使用してそれらを統合することができます。 アプリ プロキシを使用してそれらを統合できます。 詳細については、[Azure AD アプリケーション プロキシを介したオンプレミス アプリケーションへのリモート アクセスの提供](application-proxy.md)に関するページを参照してください。

>[!TIP]
>独自の基幹業務アプリケーションを構築するときに、それらを Azure AD と統合してシングル サインオンをサポートできます。 Azure AD 向けアプリ開発について詳しくは、[Microsoft ID プラットフォーム](..//develop/v2-overview.md)に関するページを参照してください。

## <a name="manage-risk-with-conditional-access-policies"></a>条件付きアクセス ポリシーによるリスクの管理

[条件付きアクセス](../conditional-access/concept-conditional-access-cloud-apps.md)と Azure AD シングル サインオン (SSO) を組み合わせることで、アプリケーションにアクセスするための高度なセキュリティが提供されます。 条件付きアクセス ポリシーは、設定された条件に基づいて粒度の細かい制御をアプリに提供します。 

## <a name="improve-productivity-with-single-sign-on"></a>シングル サインオンによる生産性の向上

シングル サインオン (SSO) によって、Microsoft 365 と皆さんが使用する他のすべてのアプリとの間で一元化されたユーザー エクスペリエンスが実現します。 しきりにユーザー名とパスワードを入力する習慣はもうやめましょう。

シングル サインオンの詳細については、[シングル サインオンの概要](what-is-single-sign-on.md)に関するページを参照してください。

## <a name="address-governance-and-compliance"></a>ガバナンスとコンプライアンスへの対応

セキュリティ インシデント ツールとイベント監視 (SIEM) ツールを使用するレポートを通じてアプリを監視します。 ポータルから、または API から、レポートにアクセスできます。 お使いのアプリケーションに誰がアクセスできるかをプログラムから監視して、アクセス レビュー経由で非アクティブなユーザーへのアクセスを削除します。

## <a name="manage-costs"></a>コストを管理する

Azure AD に移行することで、コストを節約し、オンプレミス インフラストラクチャの管理における負担を除去できます。 また、Azure AD はアプリケーションにセルフ サービス アクセスを提供し、管理者とユーザーの両者が時間を節約できます。 シングル サインオンにより、アプリケーション固有のパスワードはなくなります。 この 1 回限りのサインオンにより、アプリケーションのパスワード リセットと、パスワードの取得時の生産性低下に関連するコストを削減できます。

人事管理に重点を置いたアプリケーションや、多数のユーザーがいるアプリケーションについては、アプリのプロビジョニングを利用して効率化を図ることができます。 ユーザーを追加したり削除したりするプロセスが、アプリのプロビジョニングによって自動化されます。 詳細については、「[アプリケーションのプロビジョニングとは](../app-provisioning/user-provisioning.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [アプリケーション管理のクイックスタート シリーズ](view-applications-portal.md)
- [アプリケーション統合の概要](plan-an-application-integration.md)
- [プロビジョニングを自動化する方法](../app-provisioning/user-provisioning.md)