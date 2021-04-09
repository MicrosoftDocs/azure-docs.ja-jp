---
title: Azure Data Lake Analytics のディザスター リカバリーのガイダンス
description: Azure Data Lake Analytics アカウントのディザスター リカバリーを計画する方法について説明します。
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 06/03/2019
ms.openlocfilehash: ce5e96bf464eebb9b91e3ffac9312e89df9cbabe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92220977"
---
# <a name="disaster-recovery-guidance-for-azure-data-lake-analytics"></a>Azure Data Lake Analytics のディザスター リカバリーのガイダンス

Azure Data Lake Analytics は、ビッグ データを簡略化するオンデマンド分析ジョブ サービスです。 ハードウェアのデプロイ、構成、チューニングを行う代わりに、クエリを作成してデータを変換し、価値ある洞察を抽出します。 この分析サービスでは、必要な性能をダイヤルで設定して、どのような規模のジョブでも即座に処理できます。 ジョブの実行中にのみ課金されるコスト効率の良いサービスです。 この記事では、まれに発生するリージョン全体の障害や誤削除からジョブを保護する方法に関するガイダンスを示します。

## <a name="disaster-recovery-guidance"></a>ディザスター リカバリーの ガイダンス

Azure Data Lake Analytics を使用する場合、独自のディザスター リカバリー プランを準備することが重要です。 この記事は、ディザスター リカバリー プランを構築する際に役に立ちます。 独自のプランを作成する際に役立つリソースは他にもあります。
- [Azure アプリケーションの障害とディザスター リカバリー](/azure/architecture/reliability/disaster-recovery)
- [Azure の回復性技術ガイダンス](/azure/architecture/checklist/resiliency-per-service)

## <a name="best-practices-and-scenario-guidance"></a>ベスト プラクティスとシナリオのガイダンス

リージョン内の ADLA アカウントで、U-SQL テーブルや非構造化データの読み取りと書き込みを行う定期的な U-SQL ジョブを実行できます。  これらの手順を実行して、障害に備えます。

1. 障害発生時に使用されるセカンダリ リージョンに ADLA および ADLS アカウントを作成します。

   > [!NOTE]
   > アカウント名がグローバルに一意であるため、どのアカウントがセカンダリであるかを示す一貫性のある名前付けスキームを使用してください。

2. 非構造化データについては、「[Azure Data Lake Storage Gen1 内のデータに対するディザスター リカバリーのガイダンス](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)」を参照してください。

3. ADLA テーブルおよびデータベースに格納されている構造化データについては、データベース、テーブル、テーブル値関数、アセンブリなど、メタデータ アーティファクトのコピーを作成します。 運用環境で変更が行われるときに、これらのアーティファクトを定期的に再同期する必要があります。 たとえば、新しく挿入されたデータは、データをコピーしてセカンダリ テーブルに挿入することで、セカンダリ リージョンにレプリケートされる必要があります。

   > [!NOTE]
   > これらのオブジェクト名はセカンダリ アカウントにスコープ指定され、グローバルに一意ではありません。そのため、運用環境のプライマリ アカウントにあるのと同じ名前が付いていてもかまいません。

障害発生時には、入力パスがセカンダリ エンドポイントを指すように、自分のスクリプトを更新する必要があります。 次に、ユーザーはセカンダリ リージョンの ADLA アカウントにジョブを送信します。 ジョブの出力がセカンダリ リージョンの ADLA および ADLS アカウントに書き込まれます。

## <a name="next-steps"></a>次のステップ

[Azure Data Lake Storage Gen1 内のデータに対するディザスター リカバリーのガイダンス](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)
