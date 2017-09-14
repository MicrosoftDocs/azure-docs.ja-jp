---
title: "Azure ログとセキュリティ センターの統合 | Microsoft Docs"
description: "Azure Security Center の警告とログの統合を連携させる方法を確認する"
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
ms.date: 08/29/2017
ms.author: barclayn
ms.custom: azlog
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: 46758e9aa8d95bb10d91c315c1d712ab8f607188
ms.contentlocale: ja-jp
ms.lasthandoff: 08/30/2017

---
# <a name="how-to-get-your-security-center-alerts-in-azure-log-integration"></a>Azure Security Center の警告とログの統合を連携させる方法

この記事では、Azure Security Center によって生成されるセキュリティのアラート情報を取得するために、Azure ログの統合サービスを有効にするのに必要な手順について説明します。 この記事の手順を実行する前に、「[Get started](security-azure-log-integration-get-started.md)」 (はじめに) の記事の手順を正常に完了している必要があります。

## <a name="detailed-steps"></a>詳細な手順

次の手順では、必要な Azure Active Directory サービス プリンシパルを作成し、サービス プリンシパルの読み取りアクセス許可をサブスクリプション割り当てます。
1. コマンド プロンプトを開き、**c:\Program Files\Microsoft Azure Log Integration** に移動します。
2. ``azlog createazureid`` コマンドを実行します。

    このコマンドは Azure ログインを要求します。 このコマンドは、管理者、共同管理者、または所有者としてログインしている、Azure サブスクリプションをホストする Azure AD テナントに [Azure Active Directory サービス プリンシパル](../active-directory/develop/active-directory-application-objects.md) を作成します。 単なる Guest ユーザーとして Azure AD テナントにログインしている場合、コマンドは失敗します。 Azure への認証は、Azure Active Directory (AD) によって行われます。 Azlog 統合のサービス プリンシパルを作成すると、Azure AD の ID が作成され、Azure サブスクリプションからの読み取りアクセス許可が付与されます。

3. 次に、サブスクリプションの閲覧者アクセス許可を、先ほど作成されたサービス プリンシパルに割り当てるコマンドを実行します。 SubscriptionID を指定しないと、このコマンドは、サービス プリンシパルの閲覧者ロールを、アクセスできるすべてのサブスクリプションに割り当てようとします。 </br></br>
``azlog authorize <SubscriptionID>`` </br> 以下に例を示します。 </br>
``azlog authorize 0ee55555-0bc4-4a32-a4e8-c29980000000``

    >[!NOTE]
    ```createazureid``` コマンドの実行後すぐに authorize コマンドを実行すると、警告が表示されることがあります。 Azure AD アカウントが作成されてから、そのアカウントが使用できるようになるまで、少し時間がかかります。 ```createazureid``` コマンドを実行した後、60 秒間ほど待ってから authorize コマンドを実行すれば、この警告は表示されません。

4. 次のフォルダーを調べて、監査ログの JSON ファイルがあることを確認します。
 * **c:\Users\azlog\AzureResourceManagerJson**
 * **c:\Users\azlog\AzureResourceManagerJsonLD** </br></br>
5. 次のフォルダーを調べて、Security Center のアラートが存在することを確認します。</br></br>
 * **c:\Users\azlog\AzureSecurityCenterJson**
 * **c:\Users\azlog\AzureSecurityCenterJsonLD** </br></br>

インストールおよび構成中に問題が発生した場合、[サポート要求](/azure-supportability/how-to-create-azure-support-request.md)を作成し、サポートを要求するサービスとして [**ログ統合**] を選択します。

## <a name="next-steps"></a>次のステップ
Azure ログの統合の詳細については、次のドキュメントを参照してください。

* [Azure ログ用の Microsoft Azure ログ統合](https://www.microsoft.com/download/details.aspx?id=53324) – Azure ログ統合の詳細情報、システム要件、およびインストール手順のダウンロード センター。
* [Azure ログ統合の概要](security-azure-log-integration-overview.md) – このドキュメントでは、Azure ログ統合と、その主な機能およびしくみについて紹介します。
* [パートナーの構成手順](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – このブログ投稿では、Splunk、HP ArcSight、IBM QRadar などのパートナー ソリューションを使用できるように、Azure ログ統合を構成する方法について説明します。
* [Azure ログ統合のよく寄せられる質問 (FAQ)](security-azure-log-integration-faq.md) – この FAQ は、Azure ログ統合について寄せられる質問とその回答です。
* [Azure 診断および Azure 監査ログの新機能](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) – このブログ投稿では、Azure 監査ログと、Azure リソースの操作の洞察を得るのに役立つその他の機能を紹介します。

