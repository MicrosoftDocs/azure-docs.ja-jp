---
title: SaaS アプリケーションへの自動ユーザー アカウント プロビジョニングについてのレポート
description: 自動ユーザー アカウント プロビジョニング ジョブの状態を確認する方法と、個々のユーザーのプロビジョニングをトラブルシューティングする方法について説明します。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 09/09/2018
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 5d93cec201feeb43700fe849f2fddc8eaf22488a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579486"
---
# <a name="tutorial-reporting-on-automatic-user-account-provisioning"></a>チュートリアル: 自動ユーザー アカウント プロビジョニングについてのレポート

Azure Active Directory (Azure AD) には、エンド ツー エンドの ID ライフ サイクル管理のために、SaaS アプリとその他のシステムのユーザー アカウントのプロビジョニングとプロビジョニング解除の自動化を支援する、[ユーザー アカウント プロビジョニング サービス](user-provisioning.md)が含まれています。 Azure AD では、すべてのアプリケーションとシステム用に事前統合されたユーザー プロビジョニング コネクタがサポートされています。ユーザー プロビジョニングのチュートリアルは[こちら](../saas-apps/tutorial-list.md)です。

この記事では、プロビジョニング ジョブのセットアップ後にそれらの状態を確認する方法と、個々のユーザーとグループのプロビジョニングのトラブルシューティングを行う方法について説明します。

## <a name="overview"></a>概要

プロビジョニング コネクタは、[Azure Portal](https://portal.azure.com) を使用してセットアップおよび構成します。サポートされているアプリケーションに[提供されているドキュメント](../saas-apps/tutorial-list.md)に従ってください。 構成を完了し実行すると、以下の 2 つの方法のいずれかで、プロビジョニング ジョブをレポートできます。

* **Azure portal** - この記事では、主に、[Azure portal](https://portal.azure.com) からレポート情報を取得する方法について説明します。このポータルでは、プロビジョニングの概要レポートと、特定のアプリケーションの詳細なプロビジョニング監査ログの両方が提供されます。
* **監査 API** - Azure Active Directory には、プログラムで詳細なプロビジョニング監査ログを取得することができる監査 API も用意されています。 この API の使用を取り上げたドキュメントについては、[Azure Active Directory 監査 API リファレンス](/graph/api/resources/directoryaudit)を参照してください。 この記事では、API を使用する方法については具体的に説明していませんが、監査ログに記録されるプロビジョニング イベントの種類については詳細に説明しています。

### <a name="definitions"></a>定義

この記事では、以下に定義されている用語を使用します。

* **ソース システム** - Azure AD プロビジョニング サービスの同期元である、ユーザーのリポジトリ。 Azure Active Directory は、事前統合されたほとんどのプロビジョニング コネクタのソース システムです。ただし、いくつかの例外があります (例: Workday Inbound Synchronization)。
* **ターゲット システム** - Azure AD プロビジョニング サービスの同期先である、ユーザーのリポジトリ。 これは、通常は SaaS アプリケーション (例: Salesforce、ServiceNow、G Suite、Dropbox for Business) です。ただし、場合によっては、Active Directory などのオンプレミス システムにすることもできます (例: Workday Inbound Synchronization から Active Directory へ)。

## <a name="getting-provisioning-reports-from-the-azure-portal"></a>Azure portal からプロビジョニング レポートを取得する

特定のアプリケーションに関するプロビジョニング レポート情報を取得するには、まず [Azure portal](https://portal.azure.com) を起動し、**[Azure Active Directory]** &gt; **[エンタープライズ アプリ]** &gt; **[プロビジョニング ログ (プレビュー)]** (**[アクティビティ]** セクション内) に移動します。 プロビジョニングが構成されているエンタープライズ アプリケーションに移動することもできます。 たとえば、LinkedIn Elevate にユーザーをプロビジョニングする場合、アプリケーションの詳細へのナビゲーション パスは、次のようになります。

**[Azure Active Directory] > [エンタープライズ アプリケーション] > [すべてのアプリケーション] > [LinkedIn Elevate]**

ここから、以下で説明するプロビジョニング進行状況バーとプロビジョニング ログの両方にアクセスできます。

## <a name="provisioning-progress-bar"></a>プロビジョニング進行状況バー

[プロビジョニング進行状況バー](application-provisioning-when-will-provisioning-finish-specific-user.md#view-the-provisioning-progress-bar)は、指定したアプリケーションの **[プロビジョニング]** タブに表示されます。 これは、 **[現在の状態]** セクションにあり、現在の初回サイクルまたは増分サイクルの状態を示します。 このセクションには、以下も表示されます。

* ソース システムとターゲット システムの間で同期され、現時点でプロビジョニングの対象となっているユーザーとグループの総数。
* 同期が最後に実行された時間。 同期は通常、[初回サイクル](../app-provisioning/how-provisioning-works.md#provisioning-cycles-initial-and-incremental)が完了した後、20 分から 40 分間隔で行われます。
* [初回サイクル](../app-provisioning/how-provisioning-works.md#provisioning-cycles-initial-and-incremental)が完了したかどうか。
* プロビジョニング プロセスが検疫の対象となったかどうかと、検疫状態になった理由 (たとえば、無効な管理者資格情報のためにターゲット システムとの通信に失敗した)。

**[現在の状態]** は、プロビジョニング ジョブの操作の正常性をチェックするために、管理者が最初に確認するものです。

 ![概要レポート](./media/check-status-user-account-provisioning/provisioning-progress-bar-section.png)

## <a name="provisioning-logs-preview"></a>プロビジョニング ログ (プレビュー)

プロビジョニング サービスによって実行されたアクティビティはすべて、Azure AD の[プロビジョニング ログ](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)に記録されます。 Azure portal で、 **[Azure Active Directory]** &gt; **[エンタープライズ アプリ]** &gt; **[プロビジョニング ログ (プレビュー)]** ( **[アクティビティ]** セクション内) を順に選択して、プロビジョニング ログにアクセスできます。 プロビジョニング データは、ユーザー名か、ソース システムまたはターゲット システムの識別子に基づいて検索できます。 詳細については、[プロビジョニング ログ(プレビュー)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) に関する記事を参照してください。 記録されるアクティビティ イベントの種類には、次のようなものがあります。

## <a name="troubleshooting"></a>トラブルシューティング

プロビジョニング概要レポートとプロビジョニング ログは、管理者がユーザー アカウントのプロビジョニングに関するさまざまな問題をトラブルシューティングするときに重要な役割を果たします。

自動ユーザー プロビジョニングをトラブルシューティングする方法のシナリオ ベースのガイダンスについては、「[アプリケーションに対するユーザーの構成およびプロビジョニングに関する問題](../app-provisioning/application-provisioning-config-problem.md)」を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)