---
title: "Azure Container Instances のコンテナー グループ"
description: "Azure Container Instances のコンテナー グループのしくみを理解します"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: seanmck
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: 5b2abb76fc2bfcf719292db1fdafc8b353d13a3c
ms.contentlocale: ja-jp
ms.lasthandoff: 08/09/2017

---

# <a name="container-groups-in-azure-container-instances"></a>Azure Container Instances のコンテナー グループ

Azure Container Instances の最上位のリソースは、コンテナー グループです。 この記事では、コンテナー グループとは何か、およびそれらによってどのような種類のシナリオが有効になるかを説明します。

## <a name="how-a-container-group-works"></a>コンテナー グループのしくみ

コンテナー グループは、同じホスト コンピューター上にスケジュール設定され、ライフ サイクル、ローカル ネットワーク、およびストレージ ボリュームを共有するコンテナーのコレクションです。 それは、[Kubernetes](https://kubernetes.io/docs/concepts/workloads/pods/pod/) と [DC/OS](https://dcos.io/docs/1.9/deploying-services/pods/) における "*ポッド*" の概念に似ています。

次の図は、複数のコンテナーを含むコンテナー グループの例を示しています。

![コンテナー グループの例][container-groups-example]

以下の点に注意してください。

- グループは、単一のホスト コンピューター上にスケジュール設定されます。
- グループは、単一のパブリック IPアドレスを公開し、1 つの公開ポートを持ちます。
- このグループは、2 つのコンテナーで構成されています。 片方のコンテナーはポート 80 でリッスンし、他方のポートはポート 5000 でリッスンします。
- このグループには、ボリューム マウントとして 2 つの Azure ファイル共有が含まれています。各コンテナーは共有のいずれかをローカルにマウントします。

### <a name="networking"></a>ネットワーク

コンテナー グループは、IP アドレスと、その IP アドレス上のポートの名前空間を共有します。 外部クライアントがグループ内のコンテナーにアクセスできるようにするには、IP アドレスのポートをコンテナーから公開する必要があります。 グループ内のコンテナーがポートの名前空間を共有するため、ポートのマッピングはサポートされません。 グループ内のコンテナーは、ポートの localhost 経由で相互にアクセスできます。これは、これらのポートがグループの IP アドレスで外部的に公開されていない場合でも同じです。

### <a name="storage"></a>Storage

コンテナー グループにマウントする外部ボリュームを指定できます。 これらのボリュームは、グループの個別のコンテナーの特定のパスにマップできます。

## <a name="common-scenarios"></a>一般的なシナリオ

単一の機能タスクを少数のコンテナー イメージに分割する場合は、マルチコンテナー グループが便利です。これにより、異なるチームによる配信が可能になり、別々のリソース要件を適用できます。 次のような使用例が考えられます。

- アプリケーション コンテナーとログ記録コンテナー。 ログ記録コンテナーは、メイン アプリケーションによって出力されるログとメトリックを収集し、長期保存される記憶域に書き込みます。
- アプリケーションと監視コンテナー。 監視コンテナーは、要求を定期的にアプリケーションに送信して、アプリケーションが実行中であり、正常に応答していることを確認します。そうでない場合はアラートを発生させます。
- Web アプリケーションにサービスを提供するコンテナーとソース管理から最新のコンテンツをプルするコンテナー。

## <a name="next-steps"></a>次のステップ

Azure Resource Manager テンプレートを使用して[マルチコンテナー グループをデプロイする](container-instances-multi-container-group.md)方法を確認します。

<!-- IMAGES -->

[container-groups-example]: ./media/container-instances-container-groups/container-groups-example.png
