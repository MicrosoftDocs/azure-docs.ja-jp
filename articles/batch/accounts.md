---
title: Batch アカウントおよび Azure Storage アカウント
description: Azure Batch アカウントについて、およびそれらがどのように使用されるかについて、開発の観点から説明します。
ms.topic: conceptual
ms.date: 05/12/2020
ms.openlocfilehash: 20a2a28d0eaa2c7997ea93e66d07ecb99bf297a2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "83790909"
---
# <a name="batch-accounts-and-azure-storage-accounts"></a>Batch アカウントおよび Azure Storage アカウント

Azure Batch アカウントは、Batch サービス内で一意に識別されるエンティティです。 ほとんどの Batch ソリューションは、リソース ファイルと出力ファイルの格納に [Azure Storage](../storage/index.yml) を使用するため、各 Batch アカウントは通常、対応するストレージ アカウントに関連付けられています。

## <a name="batch-accounts"></a>Batch アカウント

すべての処理とリソースは、Batch アカウントと関連付けられています。 アプリケーションは、Batch サービスに対する要求を行う際に、Azure Batch アカウント名、アカウントの URL、およびアクセス キーまたは Azure Active Directory トークンを使用して要求を認証します。

1 つの Batch アカウントでは、複数の Batch ワークロードを実行できます。 また、同じサブスクリプション内の異なる Azure リージョンに所在するお使いの複数の Batch アカウント間で、ワークロードを分散することもできます。

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

Batch アカウントは、[Azure portal](batch-account-create-portal.md) またはプログラム ([Batch Management .NET ライブラリ](batch-management-dotnet.md)など) を使用して作成できます。 アカウントを作成する際に、ジョブに関する入出力データまたはアプリケーションの格納に使用する、Azure ストレージ アカウントを関連付けることができます。

## <a name="azure-storage-accounts"></a>Azure Storage アカウント

ほとんどの Batch ソリューションでは、リソース ファイルまたは出力ファイルを格納するために Azure Storage を使用します。 たとえば、Batch タスク (標準タスク、開始タスク、ジョブ準備タスク、ジョブ解放タスクなど) では通常、ストレージ アカウントに存在するリソース ファイルを指定します。 また、ストレージ アカウントでは、処理されるデータと生成される出力データを格納します。

Batch では、次の Azure ストレージ アカウントの種類がサポートされます。

- 汎用 v2 (GPv2) アカウント
- 汎用 v1 (GPv1) アカウント
- BLOB ストレージ アカウント (現在、仮想マシン構成のプールに対してのみサポートされます)

ストレージ アカウントについて詳しくは、「[Azure ストレージ アカウントの概要](../storage/common/storage-account-overview.md)」をご覧ください。

ストレージ アカウントは、Batch アカウントの作成時に (または後で) Batch アカウントに関連付けることができます。 ストレージ アカウントを選択するときに、コストとパフォーマンスの要件を検討してください。 たとえば、GPv2 アカウントおよび BLOB ストレージ アカウントのオプションでは、サポートされる[容量とスケーラビリティの上限](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/)が GPv1 よりも高くなっています (容量の上限の引き上げを希望する場合、Azure サポートにお問い合わせください)。これらのアカウント オプションでは、ストレージ アカウントからの読み取りまたはストレージ アカウントへの書き込みを行う多数の並列タスクが含まれた、Batch ソリューションのパフォーマンスを向上させることができます。

## <a name="next-steps"></a>次のステップ

- [ノードとプール](nodes-and-pools.md)について学習します。
- [Azure portal](batch-account-create-portal.md) を使用して Batch アカウントを作成する方法について学習します。
