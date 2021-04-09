---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 06/30/2020
ms.topic: include
ms.openlocfilehash: 4c09057f606423dc92b3364e502e632a385bf83f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100007816"
---
Security Center では、コンテナー化された環境に対するリアルタイムの脅威の防止が提供され、疑わしいアクティビティに対してはアラートが生成されます。 ユーザーは、この情報を使用して、迅速にセキュリティの問題を修復し、コンテナーのセキュリティを強化することができます。

Security Center では、さまざまなレベルで脅威の防止が提供されます。 

* **ホスト レベル (Azure Defender for servers が提供)** - Azure Defender は、Security Center が他の VM で使用しているものと同じ Log Analytics エージェントを使用して、Web シェルの検出や既知の疑わしい IP アドレスを使用した接続などの不審なアクティビティについて、Linux AKS ノードを監視します。 エージェントは、特権コンテナーの作成、API サーバーへの不審なアクセス、Docker コンテナー内での SSH (Secure Shell) サーバーの実行など、コンテナー固有の分析についても監視します。

    ホストにエージェントをインストールしなかった場合、脅威の防止によってもたらされるメリットとセキュリティ アラートは限定されます。 その場合でも、ネットワーク分析や悪意のあるサーバーとの通信に関連したアラートは通知されます。

    >[!IMPORTANT]
    > 仮想マシン スケール セットで実行されている Azure Kubernetes Service クラスターへの Log Analytics エージェントのインストールは、現在サポートされていません。

    AKS ホスト レベルのアラートの一覧については、[アラートのリファレンス表](../articles/security-center/alerts-reference.md#alerts-containerhost)をご覧ください。


* **AKS クラスター レベル (Azure Defender for Kubernetes が提供)** - クラスター レベルでは、脅威の防止は Kubernetes の監査ログの分析に基づいています。 この **エージェントレス型** 監視を有効にするには、Azure Defender を有効にします。 このレベルのアラートを生成するために、Security Center は、AKS によって取得されたログを使用して、AKS のマネージド サービスを監視します。 このレベルのイベントの例として、公開されている Kubernetes ダッシュボード、高い特権ロールの作成、機微なマウントの作成などがあります。

    >[!NOTE]
    > Azure Kubernetes Service のアクションやデプロイについてのセキュリティ アラートが Security Center から生成されるのは、サブスクリプションの設定で Kubernetes オプションが有効にされた後になります。 

    AKS クラスター レベルのアラートの一覧については、[アラートのリファレンス表](../articles/security-center/alerts-reference.md#alerts-akscluster)に関するページを参照してください。

また、Microsoft のセキュリティ研究員から成るグローバル チームも、脅威の状況を絶えず監視しています。 コンテナー固有のアラートや脆弱性は、それらが検出された時点で追加されます。

> [!TIP]
> コンテナーのアラートをシミュレートするには、[こちらのブログ記事](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270)の手順に従います。