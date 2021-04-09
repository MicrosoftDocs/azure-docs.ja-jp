---
title: クイック スタート:Azure Sentinel でオンボードする
description: このクイックスタートでは、最初に Azure Sentinel を有効にし、それからデータ ソースを接続することで Sentinel をオンボードする方法について説明します。
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: quickstart
ms.date: 10/14/2020
ms.custom: references_regions
ms.openlocfilehash: a169b93b20df14e536d8764b5fb4c889d645a1fa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100570413"
---
# <a name="quickstart-on-board-azure-sentinel"></a>クイック スタート:Azure Sentinel をオンボードする

このクイックスタートでは、Azure Sentinel をオンボードする方法について説明します。 

Azure Sentinel をオンボードするには、まず Azure Sentinel を有効にしてから、データ ソースを接続する必要があります。 Azure Sentinel には、Microsoft 365 Defender (旧 Microsoft Threat Protection) ソリューション、Microsoft 365 ソース (Office 365 を含む)、Azure AD、Microsoft Defender for Identity (旧 Azure ATP)、Microsoft Cloud App Security、Azure Security Center からの Azure Defender のアラートなど、すぐに使用できる、かつリアルタイムの統合を提供する Microsoft ソリューション用コネクタが多数付属しています。 さらに、Microsoft 以外のソリューション用のより広範なセキュリティ エコシステムへの組み込みコネクタがあります。 一般的なイベント形式 (CEF)、Syslog または REST-API を使用して、使用中のデータ ソースを Azure Sentinel に接続することもできます。 

データ ソースを接続した後、優れた設計のブックのギャラリーから選択し、データに基づいて分析情報を表示できます。 これらのブックは、ニーズに合わせて簡単にカスタマイズできます。

>[!IMPORTANT] 
> Azure Sentinel の使用に伴う料金については、「[Azure Sentinel の価格](https://azure.microsoft.com/pricing/details/azure-sentinel/)」を参照してください。

## <a name="global-prerequisites"></a>グローバルな前提条件

- アクティブな Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

- Log Analytics ワークスペース。 [Log Analytics ワークスペースの作成方法](../azure-monitor/logs/quick-create-workspace.md)を確認してください。 Log Analytics ワークスペースの詳細については、「[Azure Monitor ログのデプロイの設計](../azure-monitor/logs/design-logs-deployment.md)」を参照してください。

- Azure Sentinel を有効にするには、Azure Sentinel ワークスペースが存在するサブスクリプションへの共同作成者のアクセス許可が必要です。 
- Azure Sentinel を使用するには、ワークスペースが属しているリソース グループに対する共同作成者または閲覧者のいずれかのアクセス許可が必要です。
- 特定のデータ ソースに接続するには、追加のアクセス許可が必要になる場合があります。
- Azure Sentinel は有料サービスです。 価格情報については、[Azure Sentinel の概要](https://go.microsoft.com/fwlink/?linkid=2104058)に関するページをご覧ください。

### <a name="geographical-availability-and-data-residency"></a>リージョン別の提供状況とデータの保存場所

- Azure Sentinel は、中国およびドイツ (ソブリン) リージョンを除く、[Log Analytics のほとんどの GA リージョン](https://azure.microsoft.com/global-infrastructure/services/?products=monitor)のワークスペースで実行できます。 Log Analytics の新しいリージョンでは、Azure Sentinel サービスのオンボードに時間がかかる場合があります。 

- インシデント、ブックマーク、分析ルールなど、Azure Sentinel によって生成されたデータには、顧客の Log Analytics ワークスペースをソースとする顧客データが含まれている可能性があります。 Azure Sentinel で生成されたこのデータは、ワークスペースが配置されている地理的な場所に応じて、次の表に示す地理的な場所に保存されます。

    | ワークスペースの地理的な場所 | Azure Sentinel で生成されたデータの地理的な場所 |
    | --- | --- |
    | United States<br>インド<br>ブラジル<br>アフリカ<br>韓国<br>アラブ首長国連邦 | United States |
    | ヨーロッパ<br>フランス<br>スイス | ヨーロッパ |
    | オーストラリア | オーストラリア |
    | イギリス | イギリス |
    | Canada | Canada |
    | 日本 | 日本 |
    |

## <a name="enable-azure-sentinel"></a>Azure Sentinel を有効にする <a name="enable"></a>

1. Azure portal にサインインします。 Azure Sentinel が作成されたときのサブスクリプションが選択されていることをご確認ください。

1. **Azure Sentinel** を検索して選択します。

   ![サービスの検索](./media/quickstart-onboard/search-product.png)

1. **[追加]** を選択します。

1. 使用するワークスペースを選択するか、新しいワークスペースを作成します。 複数のワークスペースで Azure Sentinel を実行できますが、データは 1 つのワークスペースに分離されます。

   ![ワークスペースの選択](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - Azure Security Center で作成した既定のワークスペースが一覧に表示されない場合、そこに Azure Sentinel をインストールすることはできません。
   >

   >[!IMPORTANT]
   >
   > - Azure Sentinel がワークスペースにデプロイされた後に、そのワークスペースを他のリソース グループやサブスクリプションに移動することは、**現在はサポートされていません**。 
   >
   >   ワークスペースを既に移動している場合は、 **[Analytics]** の下のアクティブなルールをすべて無効にし、5 分後に再び有効にします。 これは、ほとんどの場合に効果的です。ただし、繰り返しますが、サポートされておらず、ご自身の責任で行ってください。

1. **[Add Azure Sentinel]\(Azure Sentinel の追加\)** を選択します。

## <a name="connect-data-sources"></a>データ ソースの接続

Azure Sentinel でサービスとアプリからのデータを取り込むには、サービスに接続して、Azure Sentinel にイベントとログを転送します。 物理マシンと仮想マシンの場合、ログを収集して Azure Sentinel に転送する Log Analytics エージェントをインストールできます。 ファイアウォールとプロキシの場合は、Azure Sentinel によって Log Analytics エージェントが Linux Syslog サーバーにインストールされます。エージェントがここでログ ファイルを収集して Azure Sentinel に転送します。 
 
1. メイン メニューで **[Data connectors]\(データ コネクタ\)** を選択します。 これにより、データ コネクタ ギャラリーが開きます。

1. このギャラリーは、接続できるすべてのデータ ソースの一覧です。 データ ソースを選択してから、 **[Open connector page]\(コネクタ ページを開く\)** ボタンを選択します。

1. コネクタ ページには、コネクタを構成するための手順や、必要になる可能性のあるその他の手順が表示されます。<br>
たとえば、Azure AD から Azure Sentinel にログをストリーム配信できる **Azure Active Directory** データ ソースを選択した場合、取得するログの種類 (サインイン ログまたは監査ログ、あるいはその両方) を選択できます。 <br> インストール手順を実行するか、[関連する接続のガイドを参照](connect-data-sources.md)して詳細をご確認ください。 データ コネクタについては、「[Microsoft サービスの接続](connect-data-sources.md)」をご覧ください。

1. コネクタ ページの **[次の手順]** タブには、データ コネクタに付随する組み込みのブック、サンプル クエリ、および分析ルール テンプレートが表示されます。 これらはそのまま使用することも、変更することもできます。どちらの場合も、データに関する興味深い分析情報をすぐに得ることができます。 <br>

データ ソースが接続されると、データは Azure Sentinel にストリーミングされ、操作を開始できます。 [組み込みのブック](quickstart-get-visibility.md)でログを表示したり、Log Analytics でクエリを作成して[データを調査](tutorial-investigate-cases.md)したりできます。

## <a name="next-steps"></a>次のステップ
このドキュメントでは、Azure Sentinel をオンボードし、データ ソースを接続する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
- [一般的なイベント形式のアプライアンス](connect-common-event-format.md)から Azure Sentinel へのデータのストリーム配信。
