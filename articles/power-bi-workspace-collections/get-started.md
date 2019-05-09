---
title: Microsoft Power BI ワークスペース コレクションの概要
description: Power BI ワークスペース コレクションは、アプリケーション開発者が自分のアプリケーションに対話型の Power BI レポートを追加できる Azure サービスです。
services: power-bi-workspace-collections
ms.service: power-bi-workspace-collections
author: markingmyname
ms.author: maghan
ms.topic: conceptual
ms.workload: powerbi
ms.date: 09/25/2017
ms.openlocfilehash: 751d69cd9b52cf88bb54900e67283fdff3d90579
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58520481"
---
# <a name="get-started-with-microsoft-power-bi-workspace-collections"></a>Microsoft Power BI ワークスペース コレクションの概要

**Power BI ワークスペース コレクション**は、アプリケーション開発者が自分のアプリケーションに対話型の Power BI レポートを追加できる Azure サービスです。 **Power BI ワークスペース コレクション**を既存のアプリケーションと連携させるために、ユーザーのサインイン方法を再設計したり変更したりする必要はありません。

> [!IMPORTANT]
> Power BI ワークスペース コレクションは非推奨となっており、2018 年 6 月または契約に定める日までに限り利用できます。 アプリケーションの中断を避けるため、Power BI Embedded への移行をご検討ください。 Power BI Embedded にデータを移行する方法については、[Power BI ワークスペース コレクション コンテンツを Power BI Embedded に移行する方法](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/)に関するページを参照してください。

**Microsoft Power BI ワークスペース コレクション**のリソースは、[Azure Resource Manager API](https://msdn.microsoft.com/library/mt712306.aspx) を使用してプロビジョニングします。 この場合、プロビジョニング対象のリソースは **Power BI ワークスペース コレクション**です。

![Power BI ワークスペース コレクションの一般的なフロー](media/get-started/introduction.png)

## <a name="create-a-workspace-collection"></a>ワークスペース コレクションの作成

**ワークスペース コレクション** はトップレベルの Azure リソースであり、アプリケーションに埋め込まれるコンテンツのコンテナーです。 **ワークスペース コレクション** は、次の 2 つの方法で作成できます。

* Azure Portal を使用して手動で作成する
* Azure Resource Manager API を使用してプログラムで作成する

ここでは、Azure Portal を使用して **ワークスペース コレクション** を作成する手順について説明します。

1. **Azure Portal** を開き、サインインします ([https://portal.azure.com](https://portal.azure.com))。
2. 上のパネルの **[+ 新規]** を選択します。
   
   ![Azure Portal の [+ 新規]](media/get-started/create-workspace-1.png)
3. **[データ + 分析]** で **[Power BI ワークスペース コレクション]** を選択します。
4. Power BI ワークスペース コレクションのサブスクリプションを既にお持ちの場合には、最初のメッセージの下部で **[ワークスペース コレクションを作成する]** を選択します。

5. **[ワークスペース コレクション]** に、必要な情報を入力します。
   
   ![ワークスペース コレクションの作成](media/get-started/create-workspace-2.png)
1. **作成**を選択します。

**ワークスペース コレクション**のプロビジョニングにはわずかに時間がかかります。 処理が完了すると、**ワークスペース コレクション**が表示されます。

   ![Azure Portal でのワークスペース コレクション](media/get-started/create-workspace-3.png)

**作成**結果には、ワークスペースを作成し、そこにコンテンツをデプロイする API を呼び出すために必要な情報が含まれています。

<a name="view-access-keys"/>

## <a name="view-power-bi-api-access-keys"></a>Power BI API のアクセス キーの表示

Power BI REST API を呼び出すために必要な最も重要な情報の 1 つが **アクセス キー**です。 アクセス キーは、API 要求の認証に使用される **アプリ トークン** を生成するために使用します。 **アクセス キー**を表示するには、**[設定]** の **[アクセス キー]** をクリックします。 **アプリ トークン**の詳細については、「[Authenticating and authorizing with Power BI Workspace Collections](app-token-flow.md) (Power BI ワークスペース コレクションでの認証と承認)」をご覧ください。

   ![Azure Portal のワークスペース コレクション設定内のアクセス キー](media/get-started/access-keys.png)

2 つのキーがあることに注意してください。

   ![アクセス キー内の 2 つのキー](media/get-started/access-keys-2.png)

これらのキーをコピーし、アプリケーションに安全に格納します。 これらのキーを使用すると**ワークスペース コレクション**のすべてのコンテンツにアクセスできるため、これらのキーをパスワードと同じように慎重に扱うことが重要です。

キーは 2 つ表示されますが、一度に使用するキーは 1 つのみです。 2 つ目のキーは、サービスへのアクセスを中断することなく定期的にキーを再生成できるようにするために提供されます。

アプリケーション用の Power BI のインスタンスと **アクセス キー**が準備できたら、レポートをアプリにインポートできます。 レポートのインポート方法を説明する前に、次のセクションで、アプリに埋め込む Power BI データセットとレポートの作成について説明します。

## <a name="working-with-workspaces"></a>ワークスペースの使用

ワークスペース コレクションを作成したら、レポートとデータセットを格納するワークスペースを作成する必要があります。 ワークスペースを作成するには、[Post Workspace REST API](https://msdn.microsoft.com/library/azure/mt711503.aspx) を使用する必要があります。

## <a name="create-power-bi-datasets-and-reports-to-embed-into-an-app-using-power-bi-desktop"></a>Power BI Desktop を使用した、アプリに埋め込む Power BI データセットとレポートの作成

アプリケーション用の Power BI インスタンスを作成し、**アクセス キー**の準備ができたら、埋め込む Power BI データセットとレポートを作成する必要があります。 データセットとレポートは、 **Power BI Desktop**を使用して作成できます。 [Power BI Desktop は無料で](https://go.microsoft.com/fwlink/?LinkId=521662)ダウンロードできます。 または、 [Retail Analysis Sample PBIX](https://go.microsoft.com/fwlink/?LinkID=780547)をダウンロードしてすぐに開始できます。

> [!NOTE]
> **Power BI Desktop** の使用方法の詳細については、「[Power BI Desktop の概要](https://powerbi.microsoft.com/guided-learning/powerbi-learning-0-2-get-started-power-bi-desktop)」を参照してください。

**Power BI Desktop** でデータ ソースに接続する場合、データのコピーを **Power BI Desktop** にインポートする方法と、**DirectQuery** を使ってデータ ソースに直接接続する方法があります。

**インポート**する方法と **DirectQuery** を使う方法の違いを次に示します。

| [インポート] | DirectQuery |
| --- | --- |
| テーブル、列、 *データ* が **Power BI Desktop**にインポートまたはコピーされます。 視覚化の際、 **Power BI Desktop** はデータのコピーを照会します。 基になるデータに加えられた変更を表示するには、更新する (現在の完全なデータセットをもう一度インポートする) 必要があります。 |*テーブルと列*のみが **Power BI Desktop** にインポートまたはコピーされます。 視覚化の際、**Power BI Desktop** は基になるデータ ソースを照会します。そのため、常に最新のデータが表示されます。 |

データ ソースに接続する方法の詳細については、「 [データ ソースへの接続](connect-datasource.md)」を参照してください。

**Power BI Desktop** で作業を保存すると、PBIX ファイルが作成されます。 このファイルには、レポートが含まれます。 さらに、データをインポートした場合は、完全なデータセットが PBIX に含まれます。また、**DirectQuery** を使用した場合は、データセット スキーマのみが PBIX に含まれます。 [Power BI インポート API](https://msdn.microsoft.com/library/mt711504.aspx) を使用して、PBIX をワークスペースにプログラムでデプロイします。

> [!NOTE]
> **Power BI ワークスペース コレクション**には、データセットが指し示すサーバーとデータベースを変更したり、データベースへの接続にデータセットが使用するサービス アカウント資格情報を設定したりするための追加の API が用意されています。 「[Post SetAllConnections](https://msdn.microsoft.com/library/mt711505.aspx)」および「[Patch Gateway Datasource](https://msdn.microsoft.com/library/mt711498.aspx)」を参照してください。

## <a name="create-power-bi-datasets-and-reports-using-apis"></a>API を使用した Power BI データセットとレポートの作成

### <a name="datasets"></a>データセット

REST API を使用して Power BI ワークスペース コレクション内にデータセットを作成できます。 次に、そのデータセットにデータをプッシュできます。 これを使用すると、Power BI Desktop を使用せずにデータを処理できます。 詳細については、「[Post Datasets (データセットのポスト)](https://msdn.microsoft.com/library/azure/mt778875.aspx)」を参照してください。

### <a name="reports"></a>Reports

JavaScript API を使用して、アプリケーションで直接データセットからレポートを作成できます。 詳細については、「[Create a new report from a dataset in Power BI Embedded](create-report-from-dataset.md) (Power BI ワークスペース コレクションにおけるデータセットからの新しいレポートの作成)」を参照してください。

## <a name="see-also"></a>関連項目

[Get started with Microsoft Power BI Embedded sample (Microsoft Power BI Embedded のサンプルの使用)](get-started-sample.md)  
[Power BI ワークスペース コレクションでの認証と承認](app-token-flow.md)  
[レポートの埋め込み](embed-report.md)  
[Power BI ワークスペース コレクションにおけるデータセットからの新しいレポートの作成](create-report-from-dataset.md)
[レポートの保存](save-reports.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[JavaScript による埋め込みのサンプル](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

ご質問は、 [Power BI コミュニティ](https://community.powerbi.com/)で尋ねてみてください。

