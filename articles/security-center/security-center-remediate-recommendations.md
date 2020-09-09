---
title: Azure Security Center でレコメンデーションを修復する | Microsoft Docs
description: この記事では、リソースを保護してセキュリティ ポリシーを順守するために、Azure Security Center でレコメンデーションを修復する方法について説明します。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 8be947cc-cc86-421d-87a6-b1e23077fd50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 9beb617ed8626b1fda1c9db98d626ca70ee01755
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2020
ms.locfileid: "86042919"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Azure Security Center の修復レコメンデーション

レコメンデーションは、リソースのセキュリティを強化する方法を提案します。 レコメンデーションに記載されている修復手順に従って、レコメンデーションを実装します。

## <a name="remediation-steps"></a>修復手順 <a name="remediation-steps"></a>

すべてのレコメンデーションを確認したら、最初に修復するレコメンデーションを決定します。 [セキュリティ スコアの影響](security-center-recommendations.md#monitor-recommendations)を使用して、最初にすることの優先順位を決めることをお勧めします。

1. 一覧から、レコメンデーションをクリックします。

1. 「**修復の手順**」セクションの指示に従ってください。 レコメンデーションごとに、独自の指示のセットがあります。 HTTPS 経由のトラフィックのみを許可するようにアプリケーションを構成するための修復手順のスクリーンショットを次に示します。

    ![推奨事項の詳細](./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png)

1. 完了すると、修復が成功したかどうかを知らせる通知が表示されます。

## <a name="quick-fix-remediation"></a>クイック修復のレコメンデーション<a name="one-click"></a>

クイック修復を使用すると、複数のリソースに関するレコメンデーションを迅速に修復できます。 これは、特定のレコメンデーションにのみ使用できます。 クイック修復によって修復が簡単になり、セキュリティ スコアをすばやく向上させ、環境内のセキュリティを強化することができます。

クイック修復レコメンデーションを実装するには、次の手順に従います。

1. **クイック修復**ラベルを含むレコメンデーションの一覧から レコメンデーションをクリックします。

    [![クイック修復を選択します](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png)](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png#lightbox)。

1. **[正常でないリソース]** タブで、推奨事項を実装するリソースを選択し、 **[修復]** をクリックします。

    > [!NOTE]
    > 一覧表示されるリソースの一部は、変更するための適切なアクセス許可を持っていないために無効な場合があります。

1. 確認ボックスで、修復の詳細と影響を確認します。

    ![クイック修復](./media/security-center-remediate-recommendations/security-center-one-click-fix-view.png)

    > [!NOTE]
    > 影響は、 **[修復]** をクリックした後に開かれる **[リソースの修復]** ウィンドウの灰色のボックスに表示されます。 ここには、クイック修復を進めたときに実行される変更内容が表示されます。

1. 必要に応じて関連するパラメーターを挿入し、修復を承認します。

    > [!NOTE]
    > 修復が完了してから **[正常なリソース]** タブにリソースが表示されるまでに数分かかる場合があります。修復アクションを表示するには、[アクティビティ ログ](#activity-log)を確認します。

1. 完了すると、修復が成功したかどうかを知らせる通知が表示されます。

## <a name="quick-fix-remediation-logging-in-the-activity-log"></a>アクティビティ ログでのクイック修復のログ <a name="activity-log"></a>

修復操作では、テンプレートのデプロイまたは REST PATCH API の呼び出しを使用して、リソースに構成を適用します。 これらの操作は、[Azure のアクティビティ ログ](../azure-resource-manager/management/view-activity-logs.md)に記録されます。


## <a name="recommendations-with-quick-fix-remediation"></a>クイック修復を使用したレコメンデーション

|推奨|影響|
|---|---|
|SQL Database の監査を有効にする必要がある|この操作により、これらのサーバーとそのデータベース上で SQL の監査が有効になります。 <br>**注**: <ul><li>選択した SQL Database のリージョンごとに、監査ログを保存するストレージ アカウントが作成され、そのリージョン内のすべてのサーバーによって共有されます。</li><li>適切な監査を行うには、リソース グループまたはストレージ アカウントを削除したり、名前を変更したりしないでください。</li></ul>|
|SQL Managed Instance で Advanced Data Security を有効にする必要がある|この操作により、選択した SQL Managed Instance 上で SQL Advanced Data Security (ADS) が有効になります。 <br>**注**: <ul><li>選択した SQL Managed Instance のリージョンとリソース グループごとに、スキャン結果を保存するストレージ アカウントが作成され、そのリージョン内のすべてのインスタンスによって共有されます。</li><li> ADS には、SQL Managed Instance あたり 15 米ドルの料金がかかります。</li></ul>|
|SQL Managed Instance で脆弱性評価を有効にする必要がある|この操作により、選択した SQL Managed Instance 上で SQL の脆弱性評価が有効になります。 <br>**注**:<ul><li>SQL の脆弱性評価は、SQL Advanced Data Security (ADS) パッケージの一部です。 マネージド インスタンス上で ADS がまだ有効ではない場合は、自動的に有効になります。</li><li>選択した SQL Managed Instance のリージョンとリソース グループごとに、スキャン結果を格納するストレージ アカウントが作成され、そのリージョン内のすべてのインスタンスによって共有されます。</li><li>ADS には、SQL Database あたり 15 米ドルの料金がかかります。</li></ul>||
|SQL Database で Advanced Data Security を有効にする必要があります|この操作により、選択したサーバーとそのデータベース上で Advanced Data Security (ADS) が有効になります。 <br>**注**:<ul><li>選択した SQL Database のリージョンとリソース グループごとに、スキャン結果を格納するストレージ アカウントが作成され、そのリージョン内のすべてのサーバーによって共有されます。</li><li>ADS には、SQL Database あたり 15 米ドルの料金がかかります。</li></ul>||
|SQL Database で脆弱性評価を有効にする必要があります|この操作により、これらの選択したサーバーとそのデータベース上で SQL の脆弱性評価が有効になります。 <br>**注**:<ul><li>SQL の脆弱性評価は、SQL Advanced Data Security (ADS) パッケージの一部です。 SQL Database 上で ADS がまだ有効ではない場合は、自動的に有効になります。</li><li>選択した SQL Database のリージョンとリソース グループごとに、スキャン結果を格納するストレージ アカウントが作成され、そのリージョン内のすべてのインスタンスによって共有されます。</li><li>ADS には、SQL Database あたり 15 米ドルの料金がかかります。</li></ul>||
|SQL Database で透過的なデータ暗号化を有効にする必要があります|この操作により、選択したデータベース上で SQL Database Transparent Data Encryption (TDE) が有効になります。 <br>**注**:既定では、サービスによって管理される TDE キーが使用されます。
|ストレージ アカウントへの安全な転送を有効にする必要がある|この操作により、ストレージ アカウントのセキュリティが更新され、セキュリティで保護された接続での要求のみが許可されます (HTTPS)。 <br>**注**:<ul><li>HTTP を使用するすべての要求は拒否されます。</li><li>Azure ファイル サービスを使用している場合、暗号化されていない接続は失敗します。これには、SMB 2.1、暗号化なしの SMB 3.0、および Linux SMB クライアントの一部の種類を使用するシナリオが含まれます。 詳細情報。</li></ul>|
|Web アプリケーションには HTTPS を介してのみアクセスできるようにする|この操作により、選択したリソースに関する HTTP から HTTPS へのすべてのトラフィックはリダイレクトされます。 <br>**注**:<ul><li>SSL 証明書がない HTTPS エンドポイントは、ブラウザーに "プライバシーエラー" と表示されます。 そのため、カスタム ドメインを持っているユーザーは、SSL 証明書が設定済みであることを確認する必要があります。</li><li>パケットと Web アプリケーションのファイアウォールでアプリ サービスを保護し、HTTPS セッションの転送を許可します。</li></ul>|
|Function App には HTTPS 経由でのみアクセスできるようにする|この操作により、選択したリソースに関する HTTP から HTTPS へのすべてのトラフィックはリダイレクトされます。 <br>**注**:<ul><li>SSL 証明書がない HTTPS エンドポイントは、ブラウザーに "プライバシーエラー" と表示されます。 そのため、カスタム ドメインを持っているユーザーは、SSL 証明書が設定済みであることを確認する必要があります。</li><li>パケットと Web アプリケーションのファイアウォールでアプリ サービスを保護し、HTTPS セッションの転送を許可します。</li></ul>|
|API アプリには HTTPS を介してのみアクセスできるようにする|この操作により、選択したリソースに関する HTTP から HTTPS へのすべてのトラフィックはリダイレクトされます。 <br>**注**:<ul><li>SSL 証明書がない HTTPS エンドポイントは、ブラウザーに "プライバシーエラー" と表示されます。 そのため、カスタム ドメインを持っているユーザーは、SSL 証明書が設定済みであることを確認する必要があります。</li><li>パケットと Web アプリケーションのファイアウォールでアプリ サービスを保護し、HTTPS セッションの転送を許可します。</li></ul>|
|Web アプリケーションのリモート デバッグを無効にする|この操作により、リモート デバッグは無効になります。|
|Function App でリモート デバッグを無効にする|この操作により、リモート デバッグは無効になります。|
|API アプリでリモート デバッグを無効にする|この操作により、リモート デバッグは無効になります。|
|CORS で、すべてのリソースが Web アプリケーションにアクセスすることを許可しない|この操作により、他のドメインは Web アプリケーションにアクセスできなくなります。 特定のドメインを許可するには、[許可されたオリジン] フィールドに (コンマ区切りで) 入力します。 <br>**注**:フィールドを空のままにすると、すべてのクロスオリジン呼び出しがブロックされます。"パラメーター フィールドのタイトル:"許可されたオリジン"|
|すべてのリソースが Function App にアクセスすることを CORS で許可しない|この操作により、他のドメインは関数アプリケーションにアクセスできなくなります。 特定のドメインを許可するには、[許可されたオリジン] フィールドに (コンマ区切りで) 入力します。 <br>**注**:フィールドを空のままにすると、すべてのクロスオリジン呼び出しがブロックされます。"パラメーター フィールドのタイトル:"許可されたオリジン"|
|すべてのリソース API アプリにアクセスすることを CORS で許可しない|この操作により、他のドメインは API アプリケーションにアクセスできなくなります。 特定のドメインを許可するには、[許可されたオリジン] フィールドに (コンマ区切りで) 入力します。 <br>**注**:フィールドを空のままにすると、すべてのクロスオリジン呼び出しがブロックされます。"パラメーター フィールドのタイトル:"許可されたオリジン"|
|監視エージェントを仮想マシン上で有効にする必要がある|この操作により、選択した仮想マシンに監視エージェントがインストールされます。 エージェントが報告する先のワークスペースを選択します。<ul><li>更新ポリシーが自動に設定されている場合は、新しい既存のインスタンスにデプロイされます。</li><li>更新ポリシーが手動に設定されていて、既存のインスタンスにエージェントをインストールする場合は、チェック ボックスをオンにします。 [詳細情報](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set)</li></ul>|
|Key Vault で診断ログを有効にする必要がある|この操作により、キー コンテナーの診断ログが有効になります。 診断ログとメトリックは、選択したワークスペースに保存されます。|
|Service Bus で診断ログを有効にする必要がある|この操作により、サービス バスの診断ログが有効になります。 診断ログとメトリックは、選択したワークスペースに保存されます。|

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Security Center でレコメンデーションを修復する方法について説明しました。 Security Center の詳細については、次のトピックを参照してください。

* 「[Azure Security Center でのセキュリティ ポリシーの設定](tutorial-security-policy.md)」 - Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明しています。
* 「[Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md)」 - Azure リソースの正常性を監視する方法について説明しています。
