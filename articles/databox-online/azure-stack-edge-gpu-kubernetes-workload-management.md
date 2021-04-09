---
title: Azure Stack Edge Pro デバイスでの Kubernetes ワークロード管理について | Microsoft Docs
description: Azure Stack Edge Pro デバイスで Kubernetes ワークロードを管理する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: b962d66349bbed112114c010e8d185ba16c74c8d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102443082"
---
# <a name="kubernetes-workload-management-on-your-azure-stack-edge-pro-device"></a>Azure Stack Edge Pro デバイスでの Kubernetes ワークロード管理

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Stack Edge Pro デバイスでは、コンピューティング ロールを構成するときに Kubernetes クラスターが作成されます。 この Kubernetes クラスターが作成されると、コンテナー化されたアプリケーションを Kubernetes クラスターのポッドにデプロイできます。 Kubernetes クラスターにワークロードをデプロイするには、別の方法があります。 

この記事では、Azure Stack Edge Pro デバイスでワークロードをデプロイするために使用できるさまざまな方法について説明します。

## <a name="workload-types"></a>ワークロードの種類

Azure Stack Edge Pro デバイスにデプロイできる 2 つの一般的なワークロードの種類は、ステートレス アプリケーションまたはステートフル アプリケーションです。

- **ステートレス アプリケーション** では、状態は保持されず、永続ストレージにデータが保存されません。 すべてのユーザーとセッションのデータは、クライアントに残ります。 ステートレス アプリケーションの例には、Nginx などの Web フロントエンドやその他の Web アプリケーションなどがあります。

    Kubernetes デプロイを作成して、クラスターにステートレス アプリケーションをデプロイできます。 

- **ステートフル アプリケーション** では、その状態を保存する必要があります。 ステートフル アプリケーションでは、永続ボリュームなどの永続ストレージを使用して、サーバーまたは他のユーザーが使用するデータが保存されます。 ステートフル アプリケーションの例には、[Azure SQL Edge](../azure-sql-edge/overview.md) や MongoDB などのデータベースが含まれます。

    Kubernetes デプロイを作成して、ステートフル アプリケーションをデプロイできます。 

## <a name="deployment-flow"></a>デプロイ フロー

Azure Stack Edge Pro デバイスにアプリケーションをデプロイするには、次の手順に従います。 
 
1. **アクセスを構成する**: まず、PowerShell 実行空間を使用してユーザーを作成し、名前空間を作成し、その名前空間へのアクセス権をユーザーに付与します。
2. **記憶域を構成する**: 次に、Azure portal で Azure Stack Edge リソースを使用して、デプロイするステートフル アプリケーションの静的プロビジョニングまたは動的プロビジョニングのいずれかを使用して、永続ボリュームを作成します。
3. **ネットワークを構成する**: 最後に、サービスを使用して、Kubernetes クラスターの外部およびクラスター内にアプリケーションを公開します。
 
## <a name="deployment-types"></a>デプロイのタイプ

ワークロードをデプロイするには、主に 3 つの方法があります。 これらの各デプロイ方法では、デバイス上の個別の名前空間に接続して、ステートレス アプリケーションまたはステートフル アプリケーションをデプロイできます。

![Kubernetes ワークロードのデプロイ](./media/azure-stack-edge-gpu-kubernetes-workload-management/kubernetes-workload-management-1.png)

- **ローカル デプロイ**: このデプロイは、Kubernetes `yamls` をデプロイできる `kubectl` などのコマンドライン アクセス ツールを介して行われます。 Azure Stack Edge Pro の Kubernetes クラスターには、`kubeconfig` ファイルを介してアクセスします。 詳細については、[kubectl 経由の Kubernetes クラスターへのアクセス](azure-stack-edge-gpu-create-kubernetes-cluster.md)に関するページを参照してください。

- **IoT Edge のデプロイ**: これは、Azure IoT Hub に接続する IoT Edge によって行われます。 `iotedge` 名前空間を使用して、Azure Stack Edge Pro デバイス上の Kubernetes クラスターに接続します。 この名前空間にデプロイされた IoT Edge エージェントが、Azure への接続を担当します。 Azure DevOps CI/CD を使用して `IoT Edge deployment.json` 構成を適用します。 名前空間と IoT Edge の管理は、クラウド オペレーターを通じて行われます。

- **Azure Arc 対応 Kubernetes のデプロイ**: Azure Arc 対応 Kubernetes は、Kubernetes クラスターにアプリケーションをデプロイできるようにするためのハイブリッド管理ツールです。 `azure-arc namespace` を使用して、Azure Stack Edge Pro デバイス上の Kubernetes クラスターに接続します。 この名前空間にデプロイされたエージェントが、Azure への接続を担当します。 GitOps ベースの構成管理を使用して、デプロイ構成を適用します。 
    
    また、Azure Arc 対応 Kubernetes を使用すると、コンテナーに対して Azure Monitor を使用して、クラスターを表示および監視することもできます。 詳細については、[Azure Arc 対応 Kubernetes の概要](../azure-arc/kubernetes/overview.md)に関するページを参照してください。
    
    2021 年 3 月以降、Azure Arc 対応 Kubernetes がユーザーに一般提供され、標準の使用料金が適用されます。 プレビュー版のお客様は、特典として、Azure Stack Edge デバイスに対して Azure Arc 対応 Kubernetes を無料でご利用いただけます。 プレビュー オファーを利用するには、[サポート リクエスト](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)を作成します。

    1. **[問題の種類]** で **[課金]** を選択します。
    2. **[サブスクリプション]** でご使用のサブスクリプションを選択します。
    3. **[サービス]** で、 **[使用中のサービス]** を選択してから、 **[Azure Stack Edge]** を選択します。
    4. **[リソース]** で、お使いのリソースを選択します。
    5. **[Summary]\(要約\)** に、問題の説明を入力します。
    6. **[問題の種類]** で、 **[Unexpected Charges]\(予期しない請求\)** を選択します。
    7. **[問題のサブタイプ]** で、 **[Help me understand charges on my free trial]\(無料試用版への請求について説明してほしい\)** を選択します。


## <a name="choose-the-deployment-type"></a>デプロイの種類の選択

アプリケーションのデプロイ時には、次の情報を考慮してください。

- **1 つまたは複数の種類**: 1 つのデプロイ オプションを選択することも、異なるデプロイ オプションを組み合わせて選択することもできます。
- **クラウドとローカル**: アプリケーションに応じて、kubectl を使用したローカル デプロイ、または IoT Edge と Azure Arc を使用したクラウド デプロイを選択できます。 
    - ローカル デプロイを選択すると、Azure Stack Edge Pro デバイスがデプロイされているネットワークに制限されます。
    - デプロイできるクラウド エージェントがある場合は、クラウド オペレーターをデプロイし、クラウド管理を使用する必要があります。
- **IoT と Azure Arc**: デプロイの選択は、製品シナリオの意図によっても異なります。 IoT または IoT エコシステムとより緊密に統合されたアプリケーションまたはコンテナーをデプロイする場合は、IoT Edge を選択してアプリケーションをデプロイします。 既存の Kubernetes デプロイがある場合は、Azure Arc を選択することをお勧めします。


## <a name="next-steps"></a>次のステップ

kubectl を使用してアプリをローカルにデプロイするには、次を参照してください。

- [kubectl を使用して Azure Stack Edge Pro にステートレス アプリケーションをデプロイする](azure-stack-edge-j-series-deploy-stateless-application-kubernetes.md)。

IoT Edge を使用してアプリをデプロイするには、次を参照してください。

- [IoT Edge を使用して、Azure Stack Edge Pro にサンプル モジュールをデプロイする](azure-stack-edge-gpu-deploy-sample-module.md)。

Azure Arc を使用してアプリをデプロイするには、次を参照してください。

- [Azure Arc を使用してアプリケーションをデプロイする](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md)。
