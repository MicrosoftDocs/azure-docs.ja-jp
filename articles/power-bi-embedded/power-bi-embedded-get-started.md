---
title: "Microsoft Power BI Embedded の概要"
description: "Power BI Embedded、対話型の Power BI レポートをビジネス インテリジェンス アプリケーションに追加する"
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.assetid: 4787cf44-5d1c-4bc3-b3fd-bf396e5c1176
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 03/11/2017
ms.author: asaxton
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 4afa8d2c7f8ec1942521ba5fa131967dfd581c91
ms.lasthandoff: 03/14/2017

---
# <a name="get-started-with-microsoft-power-bi-embedded"></a>Microsoft Power BI Embedded の概要

**Power BI Embedded** は、アプリケーション開発者が自分のアプリケーションに対話型の Power BI レポートを追加できるようにする Azure サービスです。 **Power BI Embedded** を既存のアプリケーションと連携させるために、ユーザーのサインイン方法を再設計したり変更したりする必要はありません。

**Microsoft Power BI Embedded** のリソースは、 [Azure ARM API](https://msdn.microsoft.com/library/mt712306.aspx)を通じてプロビジョニングされます。 この場合、プロビジョニング対象のリソースは **Power BI ワークスペース コレクション**です。

![](media/power-bi-embedded-get-started/introduction.png)

## <a name="create-a-workspace-collection"></a>ワークスペース コレクションの作成

**ワークスペース コレクション** はトップレベルの Azure リソースであり、アプリケーションに埋め込まれるコンテンツのコンテナーです。 **ワークスペース コレクション** は、次の&2; つの方法で作成できます。

* Azure ポータルを使用して手動で作成する
* Azure Resource Manager (ARM) API を使用してプログラムで作成する

ここでは、Azure ポータルを使用して **ワークスペース コレクション** を作成する手順について説明します。

1. **Azure ポータル**( [http://portal.azure.com](http://portal.azure.com)) を開き、サインインします。
2. 上のパネルの **[+ 新規]** をクリックします。
   
   ![](media/power-bi-embedded-get-started/create-workspace-1.png)
3. **[データ + 分析]** の **[Power BI Embedded]** をクリックします。
4. **ワークスペース コレクション ブレード**で、必要な情報を入力します。 **価格**については、「 [Power BI Embedded の価格](http://go.microsoft.com/fwlink/?LinkID=760527)」を参照してください。
   
   ![](media/power-bi-embedded-get-started/create-workspace-2.png)
5. **[作成]**をクリックします。

**ワークスペース コレクション** のプロビジョニングには数分かかります。 処理が完了すると、 **ワークスペース コレクション ブレード**が表示されます。

   ![](media/power-bi-embedded-get-started/create-workspace-3.png)

この **作成ブレード** には、ワークスペースを作成し、コンテンツをデプロイする API を呼び出すために必要な情報が含まれています。

<a name="view-access-keys"/>

## <a name="view-power-bi-api-access-keys"></a>Power BI API のアクセス キーの表示

Power BI REST API を呼び出すために必要な最も重要な情報の&1; つが **アクセス キー**です。 アクセス キーは、API 要求の認証に使用される **アプリ トークン** を生成するために使用します。 **アクセス キー**を表示するには、**[設定]** ブレードの **[アクセス キー]** をクリックします。 **app tokens**の詳細については、「 [Power BI Embedded での認証と承認](power-bi-embedded-app-token-flow.md)」をご覧ください。

   ![](media/power-bi-embedded-get-started/access-keys.png)

2 つのキーがあることに注意してください。

   ![](media/power-bi-embedded-get-started/access-keys-2.png)

これらのキーをコピーし、アプリケーションに安全に格納します。 これらのキーを使用すると **ワークスペース コレクション**のすべての内容にアクセスできるため、これらのキーをパスワードと同じように慎重に扱うことが重要です。

キーは&2; つ表示されますが、一度に使用するキーは&1; つのみです。 2 つ目のキーは、サービスへのアクセスを中断することなく定期的にキーを再生成できるようにするために提供されます。

アプリケーション用の Power BI のインスタンスと **アクセス キー**が準備できたら、レポートをアプリにインポートできます。 レポートのインポート方法を説明する前に、次のセクションで、アプリに埋め込む Power BI データセットとレポートの作成について説明します。

## <a name="working-with-workspaces"></a>ワークスペースの使用

ワークスペース コレクションを作成したら、レポートとデータセットを格納するワークスペースを作成する必要があります。 ワークスペースを作成するには、[Post Worksapce REST API](https://msdn.microsoft.com/library/azure/mt711503.aspx) を使用する必要があります。

## <a name="create-power-bi-datasets-and-reports-to-embed-into-an-app-using-power-bi-desktop"></a>Power BI Desktop を使用した、アプリに埋め込む Power BI データセットとレポートの作成

アプリケーション用の Power BI のインスタンスと **アクセス キー**が準備できたら、埋め込む Power BI データセットとレポートを作成する必要があります。 データセットとレポートは、 **Power BI Desktop**を使用して作成できます。 [Power BI Desktop は無料で](https://go.microsoft.com/fwlink/?LinkId=521662)ダウンロードできます。 または、 [Retail Analysis Sample PBIX](http://go.microsoft.com/fwlink/?LinkID=780547)をダウンロードしてすぐに開始できます。

> [!NOTE]
> **Power BI Desktop** の使用方法の詳細については、「[Power BI Desktop の概要](https://powerbi.microsoft.com/guided-learning/powerbi-learning-0-2-get-started-power-bi-desktop)」を参照してください。

**Power BI Desktop** でデータ ソースに接続する場合、データのコピーを **Power BI Desktop** にインポートする方法と、**DirectQuery** を使ってデータ ソースに直接接続する方法があります。

**インポート**する方法と **DirectQuery** を使う方法の違いを次に示します。

| [インポート] | DirectQuery |
| --- | --- |
| テーブル、列、 *データ* が **Power BI Desktop**にインポートまたはコピーされます。 視覚化の際、 **Power BI Desktop** はデータのコピーを照会します。 基になるデータに加えられた変更を表示するには、更新する (現在の完全なデータセットをもう一度インポートする) 必要があります。 |*テーブルと列*のみが **Power BI Desktop** にインポートまたはコピーされます。 視覚化の際、**Power BI Desktop** は基になるデータ ソースを照会します。そのため、常に最新のデータが表示されます。 |

データ ソースに接続する方法の詳細については、「 [データ ソースへの接続](power-bi-embedded-connect-datasource.md)」を参照してください。

**Power BI Desktop** で作業を保存すると、PBIX ファイルが作成されます。 このファイルには、レポートが含まれます。 さらに、データをインポートした場合は、完全なデータセットが PBIX に含まれます。また、**DirectQuery** を使用した場合は、データセット スキーマのみが PBIX に含まれます。 [Power BI インポート API](https://msdn.microsoft.com/library/mt711504.aspx) を使用して、PBIX をワークスペースにプログラムでデプロイします。

> [!NOTE]
> **Power BI Embedded** には、データセットが指し示すサーバーとデータベースを変更したり、データベースへの接続にデータセットが使用するサービス アカウント資格情報を設定したりするための追加の API が用意されています。 「[Post SetAllConnections](https://msdn.microsoft.com/library/mt711505.aspx)」および「[Patch Gateway Datasource](https://msdn.microsoft.com/library/mt711498.aspx)」を参照してください。

## <a name="create-power-bi-datasets-and-reports-using-apis"></a>API を使用した Power BI データセットとレポートの作成

### <a name="datsets"></a>データセット

REST API を使用して Power BI Embedded 内でデータセットを作成できます。 次に、そのデータセットにデータをプッシュできます。 これを使用すると、Power BI Desktop を使用せずにデータを処理できます。 詳細については、「[Post Datasets (データセットのポスト)](https://msdn.microsoft.com/library/azure/mt778875.aspx)」を参照してください。

### <a name="reports"></a>レポート

JavaScript API を使用して、アプリケーションで直接データセットからレポートを作成できます。 詳細については、「[Create a new report from a dataset in Power BI Embedded (Power BI Embedded におけるデータセットからの新しいレポートの作成)](power-bi-embedded-create-report-from-dataset.md)」を参照してください。

## <a name="see-also"></a>関連項目

[Get started with Microsoft Power BI Embedded sample (Microsoft Power BI Embedded のサンプルの使用)](power-bi-embedded-get-started-sample.md)  
[Power BI Embedded での認証と承認](power-bi-embedded-app-token-flow.md)  
[レポートの埋め込み](power-bi-embedded-embed-report.md)  
[Power BI Embedded におけるデータセットからの新しいレポートの作成](power-bi-embedded-create-report-from-dataset.md)
[レポートの保存](power-bi-embedded-save-reports.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[JavaScript 埋め込みサンプル](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
ご質問は、 [Power BI コミュニティ](http://community.powerbi.com/)で尋ねてみてください。


