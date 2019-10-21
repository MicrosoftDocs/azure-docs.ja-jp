---
title: Azure Sentinel のオフボード | Microsoft Docs
description: Azure Sentinel インスタンスを削除する方法
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2019
ms.author: rkarlin
ms.openlocfilehash: d3b9284282a7ee14cde2461598c81e6dfdfd9f72
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2019
ms.locfileid: "71316742"
---
# <a name="remove-azure-sentinel-from-your-tenant"></a>テナントから Azure Sentinel を削除する

この記事では、Azure Sentinel が不要になった場合のためにテナントから削除する方法について説明しています。

## <a name="how-to-delete-azure-sentinel"></a>Azure Sentinel を削除する方法

Azure Sentinel のインストール時、バックグラウンドでは、選択したワークスペースに **SecurityInsights** ソリューションがインストールされます。 そのため、最初にすべきことは **SecurityInsights** ソリューションを削除することです。

1.  **[Azure Sentinel]** 、 **[構成]** 、 **[ワークスペースの設定]** 、 **[ソリューション]** の順に進みます。

2.  `SecurityInsights` を選択してクリックします。

    ![SecurityInsights ソリューションを見つける](media/offboard/find-solution.png)

3.  ページの上部で **[削除]** を選択します。

    > [!IMPORTANT]
    > ワークスペースを削除すると、Azure Monitor のテナントからワークスペースと Azure Sentinel が削除されます。

    ![SecurityInsights ソリューションを削除する](media/offboard/delete-solution.png)

## <a name="what-happens-behind-the-scenes"></a>舞台裏では何が起こっているのか?

Azure Sentinel の場合、ソリューションを削除するとき、削除プロセスの最初の段階を完了するために最大 48 時間かかります。

分離が確認されると、オフボード プロセスが開始します。

**次のコネクタの構成が削除されます。**
-   Office 365

-   AWS

-   Microsoft サービス セキュリティ アラート (Azure ATP、Cloud Discovery シャドウ IT レポートを含む Microsoft Cloud App Security、Azure AD Identity Protection、Microsoft Defender ATP、Azure Security Center)

-   [脅威インテリジェンス]

-   一般的なセキュリティ ログ (CEF ベースのログ、Barracuda、Syslog を含む) (Azure Security Center をお持ちの場合、これらのログは引き続き収集されます)

-   Windows セキュリティ イベント (Azure Security Center をお持ちの場合、これらのログは引き続き収集されます)

最初の 48 時間以内に、Azure Sentinel のデータとアラート ルール (リアルタイム自動構成を含む) にアクセスできなくなります。また、クエリを実行できなくなります。

**30 日後、以下のリソースが削除されます。**

-   インシデント (調査メタデータを含む)

-   アラート ルール

-   ブックマーク

プレイブック、保存したブック、保存した検索クエリ、ノートブックは削除されません。 **削除したデータが原因で壊れる場合があります。これらは手動で削除できます。**

サービスの削除後、30 日間の猶予期間が与えられます。その間、ソリューションをもう一度有効にすることができます。データとアラート ルールが復元されますが、構成したが切断されたコネクタについては、もう一度接続する必要があります。

> [!NOTE]
> ソリューションを削除しても、サブスクリプションは Azure Sentinel リソース プロバイダーに登録されたままになります。 **これは手動で削除できます。**




## <a name="next-steps"></a>次の手順
このドキュメントでは、Azure Sentinel サービスを削除する方法について学習しました。 考えが変わり、もう一度インストールすることになったら、次のページを参照してください。
- [Azure Sentinel のオンボード](quickstart-onboard.md)概要

