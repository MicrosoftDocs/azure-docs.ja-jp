---
title: Azure Stack Edge Pro デバイスにおける事前ログ収集について
description: Azure Stack Edge Pro デバイスでの事前ログ収集がどのように行われるか、およびそれを無効にする方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: alkohli
ms.openlocfilehash: bdd44bf81e34b60ee648b71c6dc3bde5a96d3deb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102443014"
---
# <a name="proactive-log-collection-on-your-azure-stack-edge-device"></a>Azure Stack Edge デバイスにおける事前ログ収集

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

事前ログ収集によって Azure Stack Edge デバイス上のシステム正常性インジケーターが収集され、デバイスの問題のトラブルシューティングを効率的に行うことができます。 事前ログ収集は既定で有効になっています。 この記事では、収集されるログ、Microsoft でそのデータが処理される方法、事前ログ収集を無効または有効にする方法について説明します。 

この記事の情報は、Azure Stack Edge Pro GPU、Azure Stack Edge Pro R、および Azure Stack Edge Mini R デバイスに適用されます。

## <a name="about-proactive-log-collection"></a>事前ログ収集について

Microsoft カスタマー サポートとエンジニアリング チームは、Azure Stack Edge デバイスのシステム ログを使用して、操作中に発生する可能性のある問題を効率的に特定し、修正します。 事前ログ収集とは、お客様の Azure Stack Edge アプライアンスによって問題またはイベントが検出されたことを Microsoft に通知する方法です。 追跡対象のイベントについては、「[事前ログ収集インジケーター](#proactive-log-collection-indicators)」をご覧ください。 その問題に関わるログは、Microsoft によって管理および制御される Azure Storage アカウントに自動的にアップロードされます。 Microsoft サポートと Microsoft のエンジニアは、これらのサポート ログを確認して、お客様の問題を解決するための最適な措置を決定します。

> [!NOTE]
> これらのログは、デバッグ目的、および問題が発生した場合にお客様をサポートするためにのみ使用されます。


## <a name="enabling-proactive-log-collection"></a>事前ログ収集を有効にする

事前ログ収集は既定で有効になっています。 事前ログ収集は、ローカル UI を使用してデバイスをアクティブ化する際に無効にすることができます。 

1. デバイスのローカル Web UI で、 **[開始]** ページに移動します。

2. **[アクティブ化]** タイルで、 **[アクティブにする]** を選択します。 

    ![ローカル Web UI の [Cloud details]\(クラウドの詳細\) ページ 1](./media/azure-stack-edge-pro-r-deploy-activate/activate-1.png)

3. **[アクティブ化]** ウィンドウで次の操作を実行します。

   1. [Azure Stack Edge Pro R のアクティブ化キーの取得](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key)に関するセクションの説明に従って取得した **アクティブ化キー** を入力します。

      アクティブ化すると、事前ログ収集は既定で有効になっており、そのデバイスの正常性状態に基づいて、Microsoft によってログが収集されます。 これらのログは、Azure Storage アカウントにアップロードされます。 

      事前ログ収集を無効にして、Microsoft によるログの収集を停止できます。

   1. デバイスで事前ログ収集を無効にする場合は、 **[無効]** を選択します。

   1. **[アクティブ化]** を選びます。

   ![ローカル Web UI の [Cloud details]\(クラウドの詳細\) ページ 2](./media/azure-stack-edge-pro-r-deploy-activate/activate-2.png)

## <a name="proactive-log-collection-indicators"></a>事前ログ収集インジケーター

事前ログ収集を有効にすると、次のいずれかのイベントがデバイスで検出されたときに、ログが自動的にアップロードされます。  


|アラート/エラー/条件  |説明  |
|---------|---------|
|AcsUnhealthyCondition     |Azure 整合サービスは異常です。         |
|IOTEdgeAgentNotRunningCondition      |IoT Edge エージェントが実行されていません。         |
|UpdateInstallFailedEvent | 更新プログラムをインストールできませんでした。        |

 
Microsoft は、前述の一覧に新しいイベントを今後も追加します。 新しいイベントについては、追加の同意は必要ありません。 最新の事前ログ収集インジケーターについては、このページを参照してください。    
 

## <a name="other-log-collection-methods"></a>その他のログ収集方法

検出された特定の問題に関連する特定のログを収集する事前ログ収集以外に、他のログ収集によってシステムの正常性と動作についてより深く理解できます。 通常、これらの他のログは、サポート リクエスト中に収集することも、デバイスから提供されるテレメトリ データに基づいて Microsoft によってトリガーされるように設定することもできます。

## <a name="handling-data"></a>データの処理

事前ログ収集を有効にした場合、お客様は、ここで説明されているように Azure Stack Edge デバイスからのログが Microsoft によって収集されることに同意するものとします。 また、お客様は Microsoft によって管理および制御されている Azure ストレージ アカウントに、これらのログをアップロードして保持することについても承認し、同意するものとします。

Microsoft はこのデータを、システムの正常性と問題のトラブルシューティングにのみ使用します。 お客様の同意なしに、マーケティング、広告、その他の商業目的でデータが使用されることはありません。 

Microsoft が収集するデータは、Microsoft 標準のプライバシーに関する声明に従って処理されます。 お客様が事前ログ収集の同意を取り消す場合、取り消し前の同意によって収集されたデータは影響を受けません。

## <a name="next-steps"></a>次のステップ

[サポート パッケージを収集する](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)方法について説明します。