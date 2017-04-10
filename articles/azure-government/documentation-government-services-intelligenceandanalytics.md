---
title: Azure Government Intelligence + Analytics | Microsoft Docs
description: "Azure Government アプリケーションの機能の比較と開発におけるガイダンスを示します。"
services: azure-government
cloud: gov
documentationcenter: 
author: MeganYount
manager: zakramer
ms.assetid: 4b7720c1-699e-432b-9246-6e49fb77f497
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 12/06/2016
ms.author: MeganYount
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: 0233aa66bc4f4f135456ec15bd09756e63192b14
ms.lasthandoff: 03/06/2017


---
# <a name="azure-government-intelligence--analytics"></a>Azure Government のインテリジェンスと分析
この記事では、Azure Government 環境でのインテリジェンスと分析に関するサービスのバリエーションと、考慮事項の概要を紹介します。

## <a name="hdinsight"></a>HDInsight
Linux Standard 版の HDInsight は Azure Government で一般提供されています。 HDInsight を使用して Azure Government でデータ中心のソリューションを構築する方法を、<a href=https://channel9.msdn.com/Blogs/Azure/Cognitive-Services-HDInsight-and-Power-BI-on-Azure-Government/>こちら</a>のデモでご覧いただけます。

Linux Premium 版の HDInsight は間もなく提供されます。

### <a name="variations"></a>バリエーション
HDInsight の次の機能は、Azure Government では現在使用できません。

* HDInsight は Windows では利用できません。
* Azure Data Lake Store は、Azure Government で現在使用できません。 Azure Blob Storage は、現在使用できる唯一のストレージ オプションです。

Azure Government では、Log Analytics の URL が異なります。

| サービスの種類 | Azure Public | Azure Government |
| --- | --- | --- |
| HDInsight Cluster | \*.azurehdinsight.net | \*.azurehdinsight.us |

詳細については、[Azure HDInsight のパブリック ドキュメント](../hdinsight/hdinsight-hadoop-introduction.md)を参照してください。

## <a name="power-bi"></a>Power BI
Power BI US Government は、Office 365 US Government Community サブスクリプションの一部として一般提供されています。 Power BI US Government については、<a href=https://powerbi.microsoft.com/en-us/documentation/powerbi-service-govus-overview/>こちら</a>をご覧ください。 Power BI を使用して Azure Government でデータ中心のソリューションを構築する方法を、<a href=https://channel9.msdn.com/Blogs/Azure/Cognitive-Services-HDInsight-and-Power-BI-on-Azure-Government/>こちら</a>のデモでご覧いただけます。

Power BI Embedded は間もなく提供されます。

### <a name="variations"></a>バリエーション

US Government では、Power BI の URL が異なります。

| サービスの種類 | Power BI Commercial | Power BI US Government |
| --- | --- | --- |
| Power BI URL | app.powerbi.com | app.powerbigov.us |

## <a name="next-steps"></a>次のステップ
補足情報と最新情報については、<a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government ブログ</a>を講読してください。

