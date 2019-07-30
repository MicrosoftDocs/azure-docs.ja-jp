---
title: Microsoft Azure StorSimple データ マネージャーの概要 | Microsoft Docs
description: StorSimple Data Manager サービスの概要について説明します
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/21/2018
ms.author: alkohli
ms.openlocfilehash: 2ffe17bf7ef4f01c18d2c26f4a045add7302272d
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876101"
---
# <a name="storsimple-data-manager-solution-overview"></a>StorSimple Data Manager ソリューションの概要

## <a name="overview"></a>概要

Microsoft Azure StorSimple では、クラウド ストレージがオンプレミス ソリューションの拡張として使われ、オンプレミス ストレージとクラウドをまたがってデータが自動的に階層化されます。 最大の効率性とコスト削減のため、データは重複除去および圧縮された形式でクラウドに格納されます。 データは StorSimple 形式で格納されるため、ユーザーが使う可能性のある他のクラウド アプリケーションですぐに利用することはできません。

StorSimple Data Manager を使うと、クラウドの StorSimple 形式のデータにシームレスにアクセスして使うことができます。 そのために、Data Manager は StorSimple 形式をネイティブな BLOB とファイルに変換します。これらは、Azure Media Services、Azure HDInsights、Azure Machine Learning などの他のサービスで使うことができます。

この記事では、StorSimple Data Manager ソリューションの概要について説明します。 また、このサービスを使用して、StorSimple データおよびその他の Azure サービスをクラウドで使用するアプリケーションを作成する方法についても説明します。

## <a name="how-it-works"></a>動作のしくみ

StorSimple Data Manager サービスは、StorSimple 8000 シリーズのオンプレミス デバイスからクラウド内の StorSimple データを識別します。 クラウド内の StorSimple データは、重複除去および圧縮された StorSimple 形式になっています。 Data Manager サービスでは、StorSimple 形式のデータを抽出して、Azure BLOB や Azure Files などの他の形式に変換するための API が提供されています。 この変換されたデータは、Azure HDInsight および Azure Media Services ですぐに利用できます。 したがって、データ変換により、これらのサービスは、StorSimple 8000 シリーズ オンプレミス デバイスからの変換された StorSimple データで動作できます。 次の図はこの流れを示したものです。

![概要図](./media/storsimple-data-manager-overview/storsimple-data-manager-overview2.png)


## <a name="data-manager-use-cases"></a>Data Manager のユース ケース

Azure Functions、Azure Automation、Azure Data Factory で Data Manager を使うと、StorSimple に渡されるデータに対してワークフローを実行できます。 StorSimple に格納したメディア コンテンツを Azure Media Services で処理したり、そのデータに対して機械学習アルゴリズムを実行したり、Hadoop クラスターを起動して StorSimple に格納したデータを分析したりすることができます。 Azure で利用可能な広範なサービスと StorSimple のデータを組み合わせることで、データの持つ力を解放できます。


## <a name="region-availability"></a>利用可能なリージョン

StorSimple Data Manager は、次の 7 つのリージョンで利用できます。

 - 東南アジア
 - East US
 - 米国西部
 - 米国西部 2
 - 米国中西部
 - 北ヨーロッパ
 - 西ヨーロッパ

ただし、StorSimple Data Manager は、以下のリージョンのデータの変換に使うことができます。 

![データに利用可能なリージョン](./media/storsimple-data-manager-overview/data-manager-job-definition-different-regions-m.png)

上のいずれかのリージョンにリソースを展開すると、下の一覧のリージョンで変換処理を開始できるため、下のセットの方が大きくなっています。 そのため、19 のリージョンのいずれかにデータが存在すれば、このサービスを使ってデータを変換できます。


## <a name="choosing-a-region"></a>リージョンの選択

推奨事項は次のとおりです。
 - ソース ストレージ アカウント (StorSimple デバイスに関連付けられているアカウント) とターゲット ストレージ アカウント (ネイティブ形式のデータを格納するアカウント) を、同じ Azure リージョンにします。
 - StorSimple のストレージ アカウントが含まれているリージョンで、Data Manager とジョブ定義を起動します。 それが不可能な場合は、最も近い Azure リージョンで Data Manager を起動した後、StorSimple のストレージ アカウントと同じリージョンでジョブ定義を作成します。 

    StorSimple のストレージ アカウントが、ジョブ定義の作成をサポートしている 26 のリージョンにない場合は、StorSimple Data Manager を実行しないことをお勧めします。待機時間が長くなり、送信料金が上がる可能性があります。
    
Microsoft は、Azure サービスをすべての領域で常に使用できるようにする作業に取り組んでいます。 そうはいっても、特定のリージョンで計画されていないサービスの停止が短期間発生する可能性はあります。 このような場合、Data Manager とジョブ定義を停止の影響を受けないリージョンで表示し、変換ジョブを実行します。 そのようなシナリオではある程度の待ち時間が追加される場合がありますが、これはリージョンの停止というまれなイベントにおける復旧戦略になります。

## <a name="security-considerations"></a>セキュリティに関する考慮事項

StorSimple Data Manager では、StorSimple 形式からネイティブ形式に変換するためにサービス データ暗号化キーが必要です。 サービス データ暗号化キーは、最初のデバイスを StorSimple サービスに登録するときに生成されます。 このキーについて詳しくは、[StorSimple のセキュリティ](storsimple-8000-security.md) に関するページをご覧ください。

入力として提供されるサービス データ暗号化キーは、Data Manager を作成するときに作成されるキー コンテナーに格納されます。 コンテナーは、StorSimple Data Manager と同じ Azure リージョンに存在します。 Data Manager サービスを削除すると、このキーも削除されます。

このキーは、変換を実行するためにコンピューティング リソースで使われます。 これらのコンピューティング リソースは、ジョブ定義と同じ Azure リージョンに配置されます。 このリージョンは、Data Manager を起動するリージョンと同じ場合も異なる場合もあります。

Data Manager のリージョンがジョブ定義のリージョンと異なる場合は、これらの各リージョンにどのようなデータ/メタデータが存在するかを理解しておくことが重要です。 次の図では、Data Manager とジョブ定義が異なるリージョンにある場合の影響を示します。

![異なるリージョンのサービスとジョブ定義](./media/storsimple-data-manager-overview/data-manager-job-different-regions.png)

## <a name="managing-personal-information"></a>個人情報の管理

StorSimple データ マネージャーは、個人情報の収集も表示もしません。 詳細については、[セキュリティ センター](https://www.microsoft.com/trustcenter)にある Microsoft のプライバシー ポリシーを確認してください。

## <a name="known-limitations"></a>既知の制限事項

現在、このサービスには、次のような制限があります。
- StorSimple Data Manager は現在、bitlocker で暗号化されたボリュームに対しては動作しません。 暗号化されたドライブに対してこのサービスを実行しようとすると、ジョブが失敗したというメッセージが表示されます。
- ファイル (ACL を含む) のメタデータが、変換されたデータに 保持されない場合があります。
- このサービスは、NTFS ボリュームに対してのみ動作します。
- ファイル パスの長さは、256 文字未満にする必要があります。そうしないと、ジョブが失敗します。

## <a name="next-steps"></a>次の手順

[StorSimple Data Manger UI を使用してデータを変換します](storsimple-data-manager-ui.md)。
