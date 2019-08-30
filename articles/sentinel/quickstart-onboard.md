---
title: Azure Sentinel プレビューをオンボードする | Microsoft Docs
description: Azure Sentinel でデータを収集する方法をご確認ください。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/27/2019
ms.author: rkarlin
ms.openlocfilehash: 0c37d6167012af46204cbca29397f5d226b9649b
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2019
ms.locfileid: "69611906"
---
# <a name="on-board-azure-sentinel-preview"></a>Azure Sentinel プレビューをオンボードする

> [!IMPORTANT]
> 現在、Azure Sentinel はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

このクイック スタートでは、Azure Sentinel をオンボードする方法を説明します。 

Azure Sentinel をオンボードするには、まず Azure Sentinel を有効にしてから、データ ソースを接続する必要があります。 Azure Sentinel には、Microsoft Threat Protection ソリューションや Microsoft 365 ソース (Office 365、Azure AD、Azure ATP、Microsoft Cloud App Security) など、すぐに使用できるリアルタイム統合を提供する Microsoft ソリューション用コネクタが多数付属しています。 さらに、Microsoft 以外のソリューション用のより広範なセキュリティ エコシステムへの組み込みコネクタがあります。 一般的なイベント形式 (Syslog や REST-API) を使用して、データ ソースを Azure Sentinel に接続することもできます。  

データ ソースを接続した後、優れた設計のダッシュボードのギャラリーから選択し、データに基づいて分析情報を表示することができます。 これらのダッシュボードは、お客様のニーズに合わせて簡単にカスタマイズすることができます。


## <a name="global-prerequisites"></a>グローバルな前提条件

- アクティブな Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

- Log Analytics ワークスペース。 [Log Analytics ワークスペースの作成](../log-analytics/log-analytics-quick-create-workspace.md)方法をご確認ください

-  Azure Sentinel を有効にするには、Azure Sentinel ワークスペースが存在するサブスクリプションへの共同作成者のアクセス許可が必要です。 
- Azure Sentinel を使用するには、ワークスペースが属しているリソース グループに対する共同作成者または閲覧者のいずれかのアクセス許可が必要です
- 特定のデータ ソースに接続するには、追加のアクセス許可が必要になる可能性があります
 
## Azure Sentinel を有効にする <a name="enable"></a>

1. Azure portal にアクセスします。
2. Azure Sentinel が作成されたときのサブスクリプションが選択されていることをご確認ください。 
3. Azure Sentinel を検索します。 
   ![search](./media/quickstart-onboard/search-product.png)

1. **[+ 追加]** をクリックします。
1. 使用するワークスペースを選択するか、新しいワークスペースを作成します。 複数のワークスペースで Azure Sentinel を実行できますが、データは 1 つのワークスペースに分離されます。

   ![検索](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - Azure Security Center で作成した既定のワークスペースが一覧に表示されない場合、そこに Azure Sentinel をインストールすることはできません。
   > - Azure Sentinel は、次のいずれかのリージョンにデプロイされているワークスペースで実行できます。オーストラリア南東部、カナダ中部、インド中部、米国東部、米国東部 2 EUAP (カナリア)、東日本、東南アジア、英国南部、西ヨーロッパ、米国西部 2。

6. **[Add Azure Sentinel]** (Azure Sentinel の追加) をクリックします。
  

## <a name="connect-data-sources"></a>データ ソースの接続

Azure Sentinel でサービスとアプリへの接続を確立するには、サービスに接続して、Azure Sentinel にイベントとログを転送します。 マシンと仮想マシンの場合、ログを収集し、そのログを Azure Sentinel に転送する Azure Sentinel エージェントをインストールできます。 ファイアウォールとプロキシの場合、Azure Sentinel は Linux Syslog サーバーを利用します。 エージェントがインストールされているなら、エージェントはそこからログ ファイルを収集し、そのログを Azure Sentinel に転送します。 
 
1. **[データ収集]** をクリックします。
2. 接続できるデータ ソースごとにタイルがあります。<br>
たとえば、 **[Azure Active Directory]** をクリックします。 このデータ ソースを接続する場合は、Azure AD から Azure Sentinel にすべてのログをストリーミングします。 サインイン ログや監査ログなど、取得するログの種類を選択できます。 <br>
Azure Sentinel の下部には、各コネクタにインストールする必要のあるダッシュボードに関する推奨事項が示されているため、データ全体の興味深い分析をすぐに得ることができます。 <br> インストール手順を実行するか、[関連する接続のガイドを参照](connect-data-sources.md)して詳細をご確認ください。 データ コネクタについては、「[Microsoft サービスの接続](connect-data-sources.md)」をご覧ください。

データ ソースが接続されると、データは Azure Sentinel にストリーミングされ、操作を開始できます。 [組み込みのダッシュボード](quickstart-get-visibility.md)でログを表示し、Log Analytics でクエリをビルドして[データを調査](tutorial-investigate-cases.md)できます。



## <a name="next-steps"></a>次の手順
このドキュメントでは、データ ソースを Azure Sentinel に接続する方法を説明しました。 Azure Sentinel の詳細については、以下の記事を参照してください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats.md)の概要。
- [一般的なエラー形式のアプライアンス](connect-common-event-format.md)から Azure Sentinel にデータをストリーミングする。
