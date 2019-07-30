---
title: オンプレミスのアプリを Cloud App Security と統合する - Azure Active Directory | Microsoft Docs
description: オンプレミス アプリケーションを Microsoft Cloud App Security (MCAS) と連携するように Azure Active Directory で構成します。 MCAS のアプリの条件付きアクセス制御を使用して、条件付きアクセス ポリシーに基づいてセッションをリアルタイムで監視および制御します。 これらのポリシーは、Azure Active Directory (Azure AD) でアプリケーション プロキシが使用されているオンプレミス アプリケーションに適用できます。
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: a7b9b27a3b8329f906b1adfd48d28892a7edc4d1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67108443"
---
# <a name="configure-real-time-application-access-monitoring-with-microsoft-cloud-app-security-and-azure-active-directory"></a>Microsoft Cloud App Security と Azure Active Directory を使用してリアルタイムでのアプリケーション アクセスの監視を構成する
Microsoft Cloud App Security (MCAS) を使用してリアルタイムで監視を行うように、オンプレミス アプリケーションを Azure Active Directory (Azure AD) で構成します。 MCAS では、条件付きアクセス ポリシーに基づいたリアルタイムでのセッションの監視と制御に、アプリの条件付きアクセス制御が使用されます。 これらのポリシーは、Azure Active Directory (Azure AD) でアプリケーション プロキシが使用されているオンプレミス アプリケーションに適用できます。

MCAS を使用して作成できるポリシーの種類の例をいくつか次に示します。

- アンマネージド デバイスの機密ドキュメントのダウンロードをブロックまたは保護する。
- 高リスク ユーザーがアプリケーションにサインオンするタイミングを監視し、セッション内からそのユーザーのアクションを記録する。 この情報を使用して、ユーザーの動作を分析することで、セッション ポリシーを適用する方法を判断します。
- クライアント証明書またはデバイスのコンプライアンスを使用して、アンマネージド デバイスからの特定のアプリケーションへのアクセスをブロックする。
- 企業ネットワーク外からのユーザー セッションを制限する。 ご自身の企業ネットワークの外からアプリケーションにアクセスするユーザーには、制限付きアクセスを付与できます。 たとえば、この制限付きアクセスによって、ユーザーによる機密ドキュメントのダウンロードをブロックできます。

詳細については、[Microsoft Cloud App Security のアプリの条件付きアクセス制御によるアプリの保護](/cloud-app-security/proxy-intro-aad)に関するページをご覧ください。

## <a name="requirements"></a>必要条件

ライセンス:

- EMS E5 ライセンス、または 
- Azure Active Directory Premium P1 および MCAS スタンドアロン。

オンプレミスのアプリケーション:

- オンプレミスのアプリケーションでは、Kerberos 制約付き委任 (KCD) を使用する必要があります

アプリケーション プロキシの構成:

- ご自身の環境の準備、アプリケーション プロキシ コネクタのインストールなど、アプリケーション プロキシを使用するように Azure AD を構成します。 チュートリアルについては、[Azure AD のアプリケーション プロキシを介してリモート アクセスするためのオンプレミス アプリケーションの追加](application-proxy-add-on-premises-application.md)に関するページをご覧ください。 

## <a name="add-on-premises-application-to-azure-ad"></a>オンプレミスのアプリケーションを Azure AD に追加する

オンプレミスのアプリケーションを Azure AD に追加します。 クイック スタートについては、「[オンプレミス アプリを Azure AD に追加する](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)」を参照してください。 アプリケーションを追加するときは、 **[Add your on-premises application]\(オンプレミス アプリケーションを追加\)** ブレードで必ず次の 2 つを設定してください。

- **事前認証**: **Azure Active Directory** を入力します。
- **アプリケーション本文の URL を変換する**: **[はい]** を選択します。

これらの 2 つの設定は、アプリケーションで MCAS を使用するために必要です。

## <a name="test-the-on-premises-application"></a>オンプレミスのアプリケーションをテストする

ご自身のアプリケーションを Azure AD に追加したら、「[アプリケーションをテストする](application-proxy-add-on-premises-application.md#test-the-application)」の手順を使用してテストするユーザーを追加し、サインオンをテストします。 

## <a name="deploy-conditional-access-app-control"></a>アプリの条件付きアクセス制御をデプロイする

条件付きアクセスのアプリケーション制御を使用してご自身のアプリケーションを構成するには、「[Azure AD アプリの Conditional Access App Control のデプロイ](/cloud-app-security/proxy-deployment-aad)」の手順に従ってください。


## <a name="test-conditional-access-app-control"></a>アプリの条件付きアクセス制御をテストする

アプリの条件付きアクセス制御での Azure AD アプリケーションのデプロイをテストするには、[Azure AD アプリのデプロイのテスト](/cloud-app-security/proxy-deployment-aad)に関するページの手順に従ってください。





