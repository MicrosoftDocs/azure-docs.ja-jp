---
title: "Azure Security Center の警告と Azure ログの統合 | Microsoft Docs"
description: "この記事では、Security Center の警告と Azure ログの統合の概要について説明します。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: d2d088d3-d38d-47ff-a062-c78e0fd59226
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2017
ms.author: terrylan
ms.openlocfilehash: d13e5b87c446e587091551b22d80fe568d5d8093
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="integrating-azure-security-center-alerts-with-azure-log-integration"></a>Azure Security Center の警告と Azure ログの統合
セキュリティ操作およびインシデント対応チームの多くが、セキュリティ情報イベント管理 (SIEM) ソリューションを、セキュリティ警告のトリアージと調査の開始点として使用します。 Azure ログ統合により、Azure Security Center の警告を SIEM ソリューションに統合することができます。

Azure ログ統合は、現在、HP ArcSight、Splunk、および IBM QRadar をサポートしています。

## <a name="what-logs-can-i-integrate"></a>統合できるログ
Azure では、すべてのサービスの広範なログ記録を作成します。 これらのログは、次のように分類されます。

* **コントロール/管理ログ**。Azure Resource Manager の CREATE、UPDATE、および DELETE 操作の可視性を提供します。 これらのコントロール プレーン イベントは、Azure アクティビティ ログに表示されます。
* **データ プレーン ログ**。Azure リソースの使用時に発生したイベントの可視性を提供します。 Windows イベント ログはその一例であり、イベント ビューアーの セキュリティ チャネルからセキュリティ イベント情報を取得できます。 データ プレーン イベント (仮想マシンまたは Azure サービスによって生成) は、Azure 診断ログに表示されます。

Azure ログ統合では、現在、次のログの統合をサポートしています。

* Azure VM ログ
* Azure 監査ログ
* Azure Security Center のアラート

## <a name="install-azure-log-integration"></a>Azure ログ統合のインストール
[Azure ログ統合](https://www.microsoft.com/download/details.aspx?id=53324)をダウンロードします。

Azure ログ統合サービスは、インストール先のマシンから利用統計情報を収集します。  収集される利用統計情報を以下に示します。

* Azure ログ統合の実行中に発生する例外
* 処理されたクエリおよびイベントの数に関するメトリック
* 使用されている Azlog.exe コマンド ライン オプションについての統計情報

> [!NOTE]
> このオプションをオフにして、利用統計情報の収集を無効にすることができます。
>
>

## <a name="integrate-azure-audit-logs-and-security-center-alerts"></a>Azure 監査ログと Security Center のアラートの統合
1. コマンド プロンプトを開き、**cd** により、現在のディレクトリを **C:\Program Files\Microsoft Azure Log Integration** に変更します。
2. **azlog createazureid** コマンドを実行して、Azure サブスクリプションをホストする Azure Active Directory (AD) テナントに [Azure Active Directory サービス プリンシパル](../active-directory/active-directory-application-objects.md) を作成します。

    Azure のログインを求められます。

   > [!NOTE]
   > サブスクリプション所有者またはサブスクリプションの共同管理者である必要があります。
   >
   >

    Azure への認証は、Azure AD によって行われます。  Azure ログ統合のサービス プリンシパルを作成すると、Azure AD の ID が作成され、Azure サブスクリプションからの読み取りアクセス許可が付与されます。
3. **azlog authorize <SubscriptionID>** コマンドを実行して、手順 2 で作成したサービス プリンシパルにサブスクリプションでの閲覧者アクセス許可を割り当てます。 **SubscriptionID** を指定しない場合は、作成者がアクセスできるすべてのサブスクリプションに対する閲覧者のロールが、サービス プリンシパルに割り当てられます。

   > [!NOTE]
   > **createazureid** コマンドの実行後すぐに **authorize** コマンドを実行すると、警告が表示される可能性があります。 Azure AD アカウントが作成されてから、そのアカウントが使用できるようになるまで、少し時間がかかります。 **createazureid** コマンドを実行した後で約 10 秒間待ってから **authorize** コマンドを実行すれば、この警告が表示されることはありません。
   >
   >
4. 次のフォルダーを調べて、監査ログの JSON ファイルがあることを確認します。

   * **c:\Users\azlog\AzureResourceManagerJson**
   * **c:\Users\azlog\AzureResourceManagerJsonLD**
5. 次のフォルダーを調べて、Security Center のアラートが存在することを確認します。

   * **c:\Users\azlog\ AzureSecurityCenterJson**
   * **c:\Users\azlog\AzureSecurityCenterJsonLD**
6. SIEM ファイル フォワーダー コネクタを適切なフォルダーに構成します。 手順は、使用している SIEM によって異なります。

## <a name="next-steps"></a>次のステップ
Azure アクティビティ ログとプロパティ定義の詳細については、次をご覧ください。

* [リソース マネージャーの監査操作](../azure-resource-manager/resource-group-audit.md)

セキュリティ センターの詳細については、次を参照してください。

* [Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md) 」 -- セキュリティの警告の管理と対応の方法について説明しています。
* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md) 」 -- このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/) -- Azure のセキュリティに関する最新のニュースと情報を入手できます。
