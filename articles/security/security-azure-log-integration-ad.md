---
title: "Azure Active Directory 監査ログとの Azure ログ統合 | Microsoft Docs"
description: "Azure ログ統合サービスをインストールし、Azure 監査ログを統合する方法について説明します"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 08/08/2017
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 8a1295cc86057ed72940e774d0bd423d61142e31
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="integrate-azure-active-directory-audit-logs"></a>Azure Active Directory 監査ログの統合

Azure Active Directory (Azure AD) 監査イベントを利用すると、Azure Active Directory で発生した特権アクションを識別できます。 [Azure Active Directory 監査レポートのイベント](/active-directory/active-directory-reporting-audit-events#list-of-audit-report-events.md)に関する記事を確認することで、追跡できるイベントのタイプを確認できます。

> [!NOTE]
> [作業の開始](security-azure-log-integration-get-started.md)に関する記事を読み、手順を完了してから、この記事の手順を試してください。

## <a name="steps-to-integrate-azure-active-directory-audit-logs"></a>Azure Active Directory 監査ログを統合する手順

1. コマンド プロンプトを開き、このコマンドを実行します。

   ``cd c:\Program Files\Microsoft Azure Log Integration``

2. 次のコマンドを実行します。 
 
   ``azlog createazureid``

   このコマンドは Azure ログインを要求します。 このコマンドは、管理者、共同管理者、または所有者としてログインし、Azure サブスクリプションをホストする Azure AD テナントに、Azure Active Directory サービス プリンシパルを作成します。 単なる Guest ユーザーとして Azure AD テナントにログインしている場合、コマンドは失敗します。 Azure への認証は、Azure AD によって行われます。 Azure ログ統合のサービス プリンシパルを作成すると、Azure AD の ID が作成され、Azure サブスクリプションからの読み取りアクセス許可が付与されます。

3. 次のコマンドを実行して、テナント ID を入力します。 コマンドを実行するには、テナント管理者ロールのメンバーである必要があります。

   ``Azlog.exe authorizedirectoryreader tenantId``

   例:

   ``AZLOG.exe authorizedirectoryreader ba2c0000-d24b-4f4e-92b1-48c4469999``

4. 次のフォルダーを調べて、Azure Active Directory 監査ログの JSON ファイルが作成されていることを確認します。

   * **C:\Users\azlog\AzureActiveDirectoryJson**
   * **C:\Users\azlog\AzureActiveDirectoryJsonLD**

この記事で説明した手順については、次のビデオをご覧ください。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Azure-AD-Integration/player]


> [!NOTE]
> JSON ファイルの情報を Security Information and Event Management (SIEM) システムに移行する具体的な手順については、お使いの SIEM のベンダーに問い合わせてください。

[Azure ログ統合に関する MSDN フォーラム](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration)を通して、コミュニティの支援を受けることができます。 このフォーラムで、Azure ログ統合コミュニティのメンバーは、質問、回答、ヒント、コツなどによって相互にサポートを提供しています。 さらに、Azure ログ統合チームがこのフォーラムを監視しており、可能なときにはいつでも支援を提供します。

[サポート要求](../azure-supportability/how-to-create-azure-support-request.md)を出すこともできます。 サポートを依頼しようとしているサービスとして **[ログ統合]** を選択します。

## <a name="next-steps"></a>次のステップ
Azure ログ統合の詳細については、次をご覧ください。

* [Azure ログ用の Microsoft Azure ログ統合](https://www.microsoft.com/download/details.aspx?id=53324): このダウンロード センターのページでは、Azure ログ統合の詳細情報、システム要件、インストール手順などを説明します。
* [Azure ログ統合の概要](security-azure-log-integration-overview.md): この記事では、Azure ログ統合と、その主な機能およびしくみについて紹介します。
* [パートナーの構成手順](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/): このブログ投稿では、Splunk、HP ArcSight、IBM QRadar などのパートナー ソリューションを使用できるように、Azure ログ統合を構成する方法について説明します。
* [Azure ログ統合の FAQ](security-azure-log-integration-faq.md): この記事は、Azure ログ統合について寄せられる質問とその回答です。
* [Azure ログ統合への Security Center の警告の統合](../security-center/security-center-integrating-alerts-with-log-integration.md): この記事では、Azure Security Center の警告を、Azure 診断および Azure 監査ログによって収集された仮想マシンのセキュリティ イベントとともに、ログ分析または SIEM ソリューションと同期させる方法について説明します。
* [Azure 診断および Azure 監査ログの新機能](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/): このブログ投稿では、Azure 監査ログと、Azure リソースの操作に関する分析情報を得るのに役立つその他の機能を紹介します。
