---
title: Azure AD のセキュリティで保護されたハイブリッド アクセス | Microsoft Docs
description: この記事では、オンプレミス、パブリッククラウド、またはプライベート クラウドにあるレガシ アプリケーションを Azure AD と統合するためのパートナー ソリューションについて説明します。 アプリ デリバリー コントローラーまたはネットワークを Azure AD に接続することで、レガシ アプリをセキュリティで保護します。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 12/18/2019
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 295891afbb0136e0b05bcd49f4045e0e8bcff6e5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763042"
---
# <a name="secure-hybrid-access-secure-legacy-apps-with-app-delivery-controllers-and-networks"></a>安全なハイブリッド アクセス:アプリ デリバリー コントローラーとネットワークを使用してレガシ アプリをセキュリティで保護する

オンプレミスやクラウドのレガシ認証アプリケーションを、既存のアプリケーション デリバリー コントローラーまたはネットワークによって Azure AD に接続することで、それらの認証アプリケーションを保護できるようになりました。 これにより、 Azure AD 条件付きアクセスや Azure AD Identity Protection Azure AD のような Azure AD 機能を使用して、すべてのアプリケーションにわたり、ギャップを埋めてセキュリティ体制を強化することができます。

ビジネス プロセスにとってまだ非常に重要であるのに、Azure AD を使用する前は保護できなかったレガシ アプリケーションを、既存のネットワークとデリバリー コントローラーを使用して容易に保護できます。 お客様はおそらく、これらのアプリケーションの保護を開始するために必要なものはすべて既に所有しています。

![安全なハイブリッド アクセスを示しているイメージ](media/secure-hybrid-access/secure-hybrid-access.png)

以下のベンダーが、Azure AD との統合のために事前に構築されたソリューションと詳細なガイダンスを提供しています。

* [Akamai Enterprise Application Access (EAA)](../saas-apps/akamai-tutorial.md)
* [Citrix アプリケーション デリバリー コントローラー (ADC)](../saas-apps/citrix-netscaler-tutorial.md)
* [F5 Big-IP APM](https://aka.ms/f5-hybridaccessguide)
* [Zscaler Private Access (ZPA)](https://aka.ms/zscaler-hybridaccessguide)
