---
title: Azure Defender for Storage - 利点と機能
description: Azure Defender for Storage の利点と機能について説明します。
author: memildin
ms.author: memildin
ms.date: 02/04/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 42aa07ccf8d886dc7eb7109bc405c730331b2c3b
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095631"
---
# <a name="introduction-to-azure-defender-for-storage"></a>Azure Defender for Storage の概要

**Azure Defender for Storage** は、ストレージ アカウントに対する通常とは異なる潜在的に有害なアクセスの試行、すなわちストレージ アカウントの悪用を検出する、Azure ネイティブのセキュリティ インテリジェンス レイヤーです。 セキュリティ AI の高度な機能と [Microsoft の脅威インテリジェンス](https://go.microsoft.com/fwlink/?linkid=2128684)を利用して、コンテキストに応じたセキュリティ アラートと推奨事項を提供します。

セキュリティ アラートは、アクティビティで異常が発生したときにトリガーされます。 これらのアラートは Azure Security Center と統合されます。また、アラートは不審なアクティビティの詳細や、脅威の調査および修復方法に関する推奨事項と共に、サブスクリプション管理者にメールで送信されます。

## <a name="availability"></a>可用性

|側面|詳細|
|----|:----|
|リリース状態:|一般提供 (GA)|
|価格:|**Azure Defender for Storage** の課金については、「[Security Center の価格](https://azure.microsoft.com/pricing/details/security-center/)」をご覧ください|
|保護されるストレージの種類:|[Blob Storage](https://azure.microsoft.com/services/storage/blobs/)<br>[Azure Files](../storage/files/storage-files-introduction.md)<br>[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)|
|クラウド:|![Yes](./media/icons/yes-icon.png) 商用クラウド<br>![Yes](./media/icons/yes-icon.png) US Gov<br>![いいえ](./media/icons/no-icon.png) China Gov、その他の Gov|
|||


## <a name="what-are-the-benefits-of-azure-defender-for-storage"></a>Azure Defender for Storage の利点

Azure Defender for Storage の利点は次のとおりです。

- **Azure ネイティブのセキュリティ** - 1 回クリックして有効にすれば、Azure Blob、Azure Files、Data Lake に格納されているデータが Defender for Storage によって保護されます。 Defender for Storage は Azure ネイティブのサービスとして、Azure で管理されるすべてのデータ資産に一元的なセキュリティを提供し、Azure Sentinel など他の Azure セキュリティ サービスとも連携します。
- **豊富な検出スイート** - Microsoft の脅威インテリジェンスを利用した Defender for Storage の検出機能は、匿名アクセス、資格情報の漏洩、ソーシャル エンジニアリング、特権の乱用、悪意のあるコンテンツなど、ストレージに関する代表的な脅威をカバーしています。
- **包括的な対応** - 特定された脅威は、Security Center のオートメーション ツールによって簡単に防止、対応できます。 詳細については、「[Security Center のトリガーへの応答を自動化する](workflow-automation.md)」を参照してください。

:::image type="content" source="media/defender-for-storage-introduction/defender-for-storage-high-level-overview.png" alt-text="Azure Defender for Storage の機能概要":::


## <a name="what-kind-of-alerts-does-azure-defender-for-storage-provide"></a>Azure Defender for Storage で提供されるアラートの種類

セキュリティ アラートは、次のような場合にトリガーされます。

- **疑わしいアクセス パターン** - Tor 出口ノードからの成功したアクセスや、Microsoft の脅威インテリジェンスによって不審と判断された IP からの成功したアクセスなど
- **不審なアクティビティ** - 変則的なデータ抽出や、通常と異なるアクセス許可の変更など
- **悪意のあるコンテンツのアップロード** (ハッシュ評価分析に基づく) 潜在的マルウェア ファイルやフィッシング コンテンツのホスティングなど

アラートには、それらをトリガーするインシデントの詳細と、脅威の調査や修復方法に関する推奨事項が含まれています。 アラートは、Azure Sentinel、他のサードパーティ製 SIEM、またはその他の外部ツールにエクスポートできます。

> [!TIP]
> サブスクリプション レベルで [Azure Defender for Storage を構成する](../storage/common/azure-defender-storage-configure.md?tabs=azure-security-center)ことをお勧めしますが、[個別のストレージ アカウントで構成する](../storage/common/azure-defender-storage-configure.md?tabs=azure-portal)こともできます。


## <a name="what-is-hash-reputation-analysis-for-malware"></a>マルウェアのハッシュ評価分析とは

アップロードされたファイルが疑わしいかどうかを判断するため、Azure Defender for Storage では、[Microsoft 脅威インテリジェンス](https://go.microsoft.com/fwlink/?linkid=2128684)でサポートされているハッシュ評価分析が使用されます。 脅威の防止ツールでは、アップロードされたファイルがスキャンされるのではなく、ストレージ ログが調べられ、新しくアップロードされたファイルのハッシュと、既知のウイルス、トロイの木馬、スパイウェア、ランサムウェアのハッシュが比較されます。 

ファイルにマルウェアが含まれている疑いがある場合、Security Center ではアラートが表示されます。また、必要に応じて、疑わしいファイルの削除の承認を求めるメールをストレージの所有者に送信できます。 ハッシュ評価分析によってマルウェアが含まれることが示されているファイルの自動削除を設定するには、["マルウェアがストレージ アカウントにアップロードされた可能性" を含むアラートでトリガーするワークフローの自動化](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-respond-to-potential-malware-uploaded-to-azure-storage/ba-p/1452005)をデプロイします。

> [!NOTE]
> Security Center の脅威防止機能を有効にするには、Azure Defender を、適用可能なワークロードを含むサブスクリプションで有効にする必要があります。
>
> **Azure Defender for Storage** は、サブスクリプション レベルまたはリソース レベルで有効にできます。

## <a name="trigger-a-test-alert-for-azure-defender-for-storage"></a>Azure Defender for Storage のテスト アラートをトリガーする

環境内の Azure Defender for Storage のセキュリティ アラートをテストするには、次の手順に従って、"Access from a Tor exit node to a storage account (Tor 出口ノードからストレージ アカウントへのアクセス)" というアラートを生成します。

1. Azure Defender for Storage が有効になっているストレージ アカウントを開きます。
1. サイドバーから [コンテナー] を選択し、既存のコンテナーを開くか、新しいコンテナーを作成します。

    :::image type="content" source="media/defender-for-storage-introduction/opening-storage-container.png" alt-text="Azure Storage アカウントから BLOB コンテナーを開く" lightbox="media/defender-for-storage-introduction/opening-storage-container.png":::

1. ファイルをそのコンテナーにアップロードします。

    > [!CAUTION]
    > 機密データを含むファイルはアップロードしないでください。

1. アップロードしたファイルのコンテキスト メニューを使用して、[SAS の生成] を選択します。

    :::image type="content" source="media/defender-for-storage-introduction/generate-sas.png" alt-text="BLOB コンテナー内のファイルの [SAS の生成] オプション":::

1. 既定のオプションをそのまま使用し、 **[SAS トークンおよび URL を生成]** を選択します。

1. 生成された SAS URL をコピーします。

1. ローカル コンピューターで、Tor ブラウザーを開きます。

    > [!TIP]
    > Tor は、Tor Project サイト [https://www.torproject.org/download/](https://www.torproject.org/download/) からダウンロードできます。

1. Tor ブラウザーで SAS URL に移動します。

1. 手順 3. でアップロードしたファイルをダウンロードします。

    2 時間以内に、Security Center に次のセキュリティ アラートが表示されます。

    :::image type="content" source="media/defender-for-storage-introduction/tor-access-alert-storage.png" alt-text="Tor 出口ノードからのアクセスに関するセキュリティ アラート":::

## <a name="next-steps"></a>次の手順

この記事では、Azure Defender for Storage について説明しました。

関連資料については、次の記事をご覧ください。 

- アラートは、Security Center によって生成されたか、別のセキュリティ製品の Security Center によって受信されたかにかかわらず、エクスポートすることができます。 アラートを Azure Sentinel、サードパーティの SIEM、またはその他の外部ツールにエクスポートする場合は、[SIEM へのアラートのエクスポート](continuous-export.md)に関するページの手順に従ってください。
- [Advanced Defender for Storage を有効にする方法](../storage/common/azure-defender-storage-configure.md)
- [Azure Defender for Storage アラートの一覧](alerts-reference.md#alerts-azurestorage)
- [Microsoft の脅威インテリジェンスの機能](https://go.microsoft.com/fwlink/?linkid=2128684)