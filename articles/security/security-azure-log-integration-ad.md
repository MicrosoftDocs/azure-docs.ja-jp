---
title: Azure Active Directory 監査ログとの Azure ログ統合 | Microsoft Docs
description: Azure ログ統合サービスをインストールし、Azure 監査ログを統合する方法について説明します
services: security
documentationcenter: na
author: Barclayn
manager: barbkess
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 01/14/2019
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 473f9db7eb507fa910657696590bb656a8aff52f
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2019
ms.locfileid: "56109879"
---
# <a name="integrate-azure-active-directory-audit-logs"></a>Azure Active Directory 監査ログの統合

Azure Active Directory (Azure AD) 監査イベントを利用すると、Azure Active Directory で発生した特権アクションを識別できます。 [Azure Active Directory 監査レポートのイベント](../active-directory/reports-monitoring/concept-audit-logs.md)に関する記事を確認することで、追跡できるイベントのタイプを確認できます。


>[!IMPORTANT]
> Azure ログの統合機能は、2019 年 6 月 1 日までに廃止される予定です。 AzLog ダウンロードは、2018 年 6 月 27 日に無効になりました。 今後必要な対応のガイダンスについては、[Azure 監視を使って SIEM ツールと統合する](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)方法に関する投稿を確認してください。 

## <a name="steps-to-integrate-azure-active-directory-audit-logs"></a>Azure Active Directory 監査ログを統合する手順

> [!NOTE]
> [作業の開始](security-azure-log-integration-get-started.md)に関する記事を読んで該当する手順を完了してから、この記事の手順を試してください。

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

## <a name="next-steps"></a>次の手順
Azure ログ統合の詳細については、次をご覧ください。

* [Azure ログ用の Microsoft Azure Log Integration](https://www.microsoft.com/download/details.aspx?id=53324): このダウンロード センター ページでは、Azure Log Integration の詳細情報、システム要件、インストール手順などを説明します。
* [Azure Log Integration の概要](security-azure-log-integration-overview.md): この記事では、Azure Log Integration と、その主な機能およびしくみについて紹介します。
* [Azure Log Integration の FAQ](security-azure-log-integration-faq.md):この記事は、Azure ログ統合について寄せられる質問とその回答です。
* [Azure 診断と Azure 監査ログの新機能](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/): このブログ投稿では、Azure 監査ログと、Azure リソースの操作に関する分析情報を得るために役立つその他の機能を紹介します。
