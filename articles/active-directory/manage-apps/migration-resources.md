---
title: アプリを Azure Active Directory に移行するためのリソース | Microsoft Docs
description: アプリケーションのアクセスと認証を Azure Active Directory (Azure AD) に移行するために役立つリソース。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 02/29/2020
ms.author: kenwith
ms.reviewer: baselden
ms.openlocfilehash: ac7e273ebcc4cf541bd94bf7e7fad05802db8d0b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99258305"
---
# <a name="resources-for-migrating-applications-to-azure-active-directory"></a>アプリケーションを Azure Active Directory に移行するためのリソース

アプリケーションのアクセスと認証を Azure Active Directory (Azure AD) に移行するために役立つリソース。

| リソース  | 説明  |
|:-----------|:-------------|
|[Migrating your apps to Azure AD](https://aka.ms/migrateapps/whitepaper) (アプリの Azure AD への移行) | このホワイト ペーパーは、移行のメリットを示し、4 つのおおまかなフェーズ (検出、分類、移行、および継続的な管理) を経て移行するための計画を立案する方法について説明します。 プロセスの考え方とプロジェクトを実行しやすいピースに分割する方法について案内します。 ドキュメント全体に、作業中に役立つ重要なリソースへのリンクがあります。 |
|[Solution guide: Migrating apps from Active Directory Federation Services (AD FS) to Azure AD](./migrate-adfs-apps-to-azure.md) (ソリューション ガイド: Active Directory フェデレーション サービス (AD FS) から Azure AD へのアプリの移行) | このソリューション ガイドでは、移行ホワイト ペーパーで概要が説明されているアプリケーション移行プロジェクトの 4 つのフェーズの計画と実行について説明します。 このガイドでは、アプリケーションを Azure Directory Federated Services (AD FS) から Azure AD に移行するための具体的な目標に対してこれらのフェーズを適用する方法について説明します。|
|[開発者向けチュートリアル: 開発者向けの AD FS から Azure AD へのアプリケーション移行プレイブック](https://aka.ms/adfsplaybook) | この ASP.NET コード サンプルのセットと付随するチュートリアルは、Active Directory Federation Services (AD FS) と統合されているアプリケーションを Azure Active Directory (Azure AD) に安全に移行する方法を学習するのに役立ちます。 このチュートリアルは、AD FS と Azure AD の両方でのアプリの構成について学習する必要があるだけでなく、このプロセスでコード ベースによって必要とされる変更についても認識し、確信を持つ開発者を対象にしています。|
| [Tool: Active Directory Federation Services Migration Readiness Script](https://aka.ms/migrateapps/adfstools) (ツール: Active Directory フェデレーション サービス移行準備スクリプト) | これは、アプリが Azure AD に移行する準備が整っているかどうかを判別するために、オンプレミスの Active Directory フェデレーション サービス (AD FS) サーバーで実行できるスクリプトです。|
| [Deployment plan: Migrating from AD FS to password hash sync](https://aka.ms/ADFSTOPHSDPDownload) (デプロイ計画: AD FS からパスワード ハッシュの同期への移行) | パスワード ハッシュの同期では、ユーザー パスワードのハッシュがオンプレミスの Active Directory から Azure AD に同期されます。 これにより、Azure AD は、オンプレミスの Active Directory との対話なしでユーザーを認証できます。| 
| [Deployment plan: Migrating from AD FS to pass-through authentication](https://aka.ms/ADFSTOPTADPDownload) (デプロイ計画: AD FS からパススルー認証への移行)|Azure AD パススルー認証を使用すると、ユーザーは同じパスワードを使用して、オンプレミスのアプリケーションとクラウド ベースのアプリケーションの両方にサインインできます。 この機能は、ユーザーが記憶するパスワードが 1 つ減るため、エクスペリエンスが向上します。 さらに、覚えておく必要があるパスワードが 1 つだけであれば、ユーザーがサインイン方法を忘れる可能性が低くなるため、IT ヘルプデスクのコストが削減されます。 この機能により、ユーザーが Azure AD を使用してサインインするとき、ユーザーのパスワードがオンプレミスの Active Directory に対して直接検証されます。|
| [Deployment plan: Enabling Single Sign-on to a SaaS app with Azure AD](https://aka.ms/SSODPDownload) (デプロイ計画: Azure AD による SaaS アプリに対するシングル サインオンの有効化) | シングル サインオン (SSO) は、1 つのユーザー アカウントを使って 1 回サインインするだけで作業に必要なすべてのアプリとリソースにアクセスできる機能です。 たとえば、サインインした後、ユーザーは、2 回目の認証 (パスワードの入力など) なしで Microsoft Office から SalesForce や Box に移動できます。 
| [Deployment plan: Extending apps to Azure AD with Application Proxy](https://aka.ms/AppProxyDPDownload) (デプロイ計画: アプリケーション プロキシによる Azure AD へのアプリの拡張)| 従業員のノート PC やその他のデバイスからオンプレミスのアプリケーションにアクセスするには、従来は仮想プライベート ネットワーク (VPN) または非武装地帯 (DMZ) が必要でした。 これらのソリューションは、複雑でセキュリティ保護が困難であるだけでなく、設定と管理にコストがかかります。 Azure AD アプリケーション プロキシは、オンプレミス アプリケーションに簡単にアクセスできるようにします。 |
| [デプロイ計画](../fundamentals/active-directory-deployment-plans.md) | 多要素認証、条件付きアクセス、ユーザーのプロビジョニング、シームレス SSO、セルフサービス パスワード リセットなどの機能をデプロイするためのデプロイ計画を見つけてください。 |