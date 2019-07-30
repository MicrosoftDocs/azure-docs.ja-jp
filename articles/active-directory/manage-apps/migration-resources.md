---
title: アプリを Azure Active Directory に移行するためのリソース | Microsoft Docs
description: アプリケーションのアクセスと認証を Azure Active Directory (Azure AD) に移行するために役立つリソース。
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 09/19/2018
ms.author: mimart
ms.reviewer: baselden
ms.collection: M365-identity-device-management
ms.openlocfilehash: b39704b25c26998e16ca8080c4e44dd1adf6965c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67108213"
---
# <a name="resources-for-migrating-applications-to-azure-active-directory"></a>アプリケーションを Azure Active Directory に移行するためのリソース

アプリケーションのアクセスと認証を Azure Active Directory (Azure AD) に移行するために役立つリソース。 この簡単なアンケート (https://aka.ms/AppsMigrationFeedback) ) を行って、アプリを Azure AD に移行した体験についてフィードバック (移行の妨げ、ツール/ガイダンスの必要性、オンプレミスの IDP を保持する理由など) を集めます。 

| Resource  | 説明  |
|:-----------|:-------------|
|[Migrating your apps to Azure AD](https://aka.ms/migrateapps/whitepaper) (アプリの Azure AD への移行) | このホワイト ペーパーは、移行のメリットを示し、4 つのおおまかなフェーズ (検出、分類、移行、および継続的な管理) を経て移行するための計画を立案する方法について説明します。 プロセスの考え方とプロジェクトを実行しやすいピースに分割する方法について案内します。 ドキュメント全体に、作業中に役立つ重要なリソースへのリンクがあります。 |
|[Solution guide: Migrating apps from Active Directory Federation Services (AD FS) to Azure AD](https://aka.ms/migrateapps/adfssolutionguide) (ソリューション ガイド: Active Directory フェデレーション サービス (AD FS) から Azure AD へのアプリの移行) | このソリューション ガイドでは、移行ホワイト ペーパーで概要が説明されているアプリケーション移行プロジェクトの 4 つのフェーズの計画と実行について説明します。 このガイドでは、アプリケーションを Azure Directory Federated Services (AD FS) から Azure AD に移行するための具体的な目標に対してこれらのフェーズを適用する方法について説明します。|
| [Tool: Active Directory Federation Services Migration Readiness Script](https://aka.ms/migrateapps/adfstools) (ツール: Active Directory フェデレーション サービス移行準備スクリプト) | これは、アプリが Azure AD に移行する準備が整っているかどうかを判別するために、オンプレミスの Active Directory フェデレーション サービス (AD FS) サーバーで実行できるスクリプトです。|
| [Deployment plan: Migrating from AD FS to password hash sync](https://aka.ms/ADFSTOPHSDPDownload) (デプロイ計画: AD FS からパスワード ハッシュの同期への移行) | パスワード ハッシュの同期では、ユーザー パスワードのハッシュがオンプレミスの Active Directory から Azure AD に同期されます。 これにより、Azure AD は、オンプレミスの Active Directory との対話なしでユーザーを認証できます。| 
| [Deployment plan: Migrating from AD FS to pass-through authentication](https://aka.ms/ADFSTOPTADPDownload) (デプロイ計画: AD FS からパススルー認証への移行)|Azure AD パススルー認証を使用すると、ユーザーは同じパスワードを使用して、オンプレミスのアプリケーションとクラウド ベースのアプリケーションの両方にサインインできます。 この機能は、ユーザーが記憶するパスワードが 1 つ減るため、エクスペリエンスが向上します。 さらに、覚えておく必要があるパスワードが 1 つだけであれば、ユーザーがサインイン方法を忘れる可能性が低くなるため、IT ヘルプデスクのコストが削減されます。 この機能により、ユーザーが Azure AD を使用してサインインするとき、ユーザーのパスワードがオンプレミスの Active Directory に対して直接検証されます。|
| [Deployment plan: Enabling Single Sign-on to a SaaS app with Azure AD](https://aka.ms/SSODPDownload) (デプロイ計画: Azure AD による SaaS アプリに対するシングル サインオンの有効化) | シングル サインオン (SSO) は、1 つのユーザー アカウントを使って 1 回サインインするだけで作業に必要なすべてのアプリとリソースにアクセスできる機能です。 たとえば、サインインした後、ユーザーは、2 回目の認証 (パスワードの入力など) なしで Microsoft Office から SalesForce や Box に移動できます。 
| [Deployment plan: Extending apps to Azure AD with Application Proxy](https://aka.ms/AppProxyDPDownload) (デプロイ計画: アプリケーション プロキシによる Azure AD へのアプリの拡張)| 従業員のノート PC やその他のデバイスからオンプレミスのアプリケーションにアクセスするには、従来は仮想プライベート ネットワーク (VPN) または非武装地帯 (DMZ) が必要でした。 これらのソリューションは、複雑でセキュリティ保護が困難であるだけでなく、設定と管理にコストがかかります。 Azure AD アプリケーション プロキシは、オンプレミス アプリケーションに簡単にアクセスできるようにします。 |
| [デプロイ計画](../fundamentals/active-directory-deployment-plans.md) | 多要素認証、条件付きアクセス、ユーザーのプロビジョニング、シームレス SSO、セルフサービス パスワード リセットなどの機能をデプロイするためのデプロイ計画を見つけてください。 |


