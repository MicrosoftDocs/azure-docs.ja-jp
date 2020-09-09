---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 06/30/2020
ms.topic: include
ms.openlocfilehash: ba695337a1da1e440895f6b9f6fe2eb34f5041e3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85800608"
---
Security Center では、コンテナー化された環境に対するリアルタイムの脅威の防止が提供され、疑わしいアクティビティに対してはアラートが生成されます。 ユーザーは、この情報を使用して、迅速にセキュリティの問題を修復し、コンテナーのセキュリティを強化することができます。

Security Center では、さまざまなレベルで脅威の防止が提供されます。 

* **ホスト レベル** - Log Analytics エージェントでは、Linux の疑わしいアクティビティが監視されます。 ノードまたはそこで実行されているコンテナーを発生源とする不審なアクティビティについては、エージェントによってアラートがトリガーされます。 そのようなアクティビティとしては、たとえば、Web シェルの検出や既知の不審な IP アドレスとの接続が挙げられます。

    コンテナー化された環境のセキュリティについて、より詳しい分析情報を得るために、エージェントは、コンテナー固有の分析情報を監視します。 特権コンテナーの作成、API サーバーへの不審なアクセス、Docker コンテナー内での SSH (Secure Shell) サーバーの実行などのイベントが発生すると、アラートがトリガーされます。

    >[!IMPORTANT]
    > ホストにエージェントをインストールしなかった場合、脅威の防止によってもたらされるメリットとセキュリティ アラートは限定されます。 その場合でも、ネットワーク分析や悪意のあるサーバーとの通信に関連したアラートは通知されます。

    AKS ホスト レベルのアラートの一覧については、[アラートのリファレンス表](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-containerhost)をご覧ください。


* **AKS クラスター レベル**では、脅威の防止は Kubernetes の監査ログの分析に基づきます。 この**エージェントレス**の監視を有効にするには、 **[価格と設定]** ページから、ご利用のサブスクリプションに Kubernetes オプションを追加してください ([価格](https://docs.microsoft.com/azure/security-center/security-center-pricing)に関するページを参照)。 このレベルのアラートを生成するために、Security Center は、AKS によって取得されたログを使用して、AKS のマネージド サービスを監視します。 このレベルのイベントの例として、公開されている Kubernetes ダッシュボード、高い特権ロールの作成、機微なマウントの作成などがあります。

    >[!NOTE]
    > Azure Kubernetes Service のアクションやデプロイについてのセキュリティ アラートが Security Center から生成されるのは、サブスクリプションの設定で Kubernetes オプションが有効にされた後になります。 

    AKS クラスター レベルのアラートの一覧については、[アラートのリファレンス表](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster)に関するページを参照してください。

また、Microsoft のセキュリティ研究員から成るグローバル チームも、脅威の状況を絶えず監視しています。 コンテナー固有のアラートや脆弱性は、それらが検出された時点で追加されます。

> [!TIP]
> コンテナーのアラートをシミュレートするには、[こちらのブログ記事](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270)の手順に従います。