---
title: Azure Defender for servers - 利点と機能
description: Azure Defender for servers の利点と機能について説明します。
author: memildin
ms.author: memildin
ms.date: 9/23/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 36c32c4eefdaa1c7604f2b0f19e98cf6a6d4310d
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102096532"
---
# <a name="introduction-to-azure-defender-for-servers"></a>Azure Defender for servers の概要

Azure Defender for servers によって、お使いの Windows マシンと Linux マシンを対象とした脅威検出および高度な防御が追加されます。

Windows の場合、Azure Defender は Azure サービスと統合され、Windows ベースのマシンを監視および保護します。 Security Center は、これらすべてのサービスからのアラートと修復の提案を使いやすい形式で示します。

Linux の場合、Azure Defender は **auditd** (最も一般的な Linux 監査フレームワークの 1 つ) を使用して、Linux マシンから監査レコードを収集します。 auditd はメインライン カーネルに存在します。 


## <a name="what-are-the-benefits-of-azure-defender-for-servers"></a>Azure Defender for servers の利点

Azure Defender for servers には、次のような脅威検出および保護機能が用意されています。

- **Microsoft Defender for Endpoint の統合ライセンス (Windows のみ)** - Azure Defender for servers には、[Microsoft Defender for Endpoint](https://www.microsoft.com/microsoft-365/security/endpoint-defender) が含まれています。 同時に、包括的なエンドポイントの検出と対応 (EDR) 機能が提供されます。 詳細については、[エンドポイントの保護](security-center-wdatp.md)に関する記事をご覧ください。

    Defender for Endpoint で脅威が検出されると、アラートがトリガーされます。 アラートは Security Center に表示されます。 Security Center から、Defender for Endpoint コンソールにピボットし、詳細な調査を実行して攻撃の範囲を明らかにすることもできます。 Microsoft Defender for Endpoint の詳細をご確認ください。

    > [!IMPORTANT]
    > **Microsoft Defender for Endpoint** センサーは、Security Center を使用する Windows サーバーで自動的に有効になります。

- **VM をスキャンする脆弱性評価** - Azure Security Center に含まれている脆弱性スキャナーは、Qualys を利用しています。 

    Qualys のスキャナーは、Azure 仮想マシンとハイブリッド仮想マシンの脆弱性をリアルタイムで特定するための主要なツールの 1 つです。 Qualys ライセンスも Qualys アカウントも必要ありません。すべてが Security Center 内でシームレスに処理されます。 詳細については、「[Azure およびハイブリッドのマシンに対する Azure Defender の統合された脆弱性評価](deploy-vulnerability-assessment-vm.md)」をご覧ください。

- **Just-In-Time (JIT) 仮想マシン (VM) アクセス** - 脅威アクターは、RDP や SSH などの、開いている管理ポートがあるアクセス可能なマシンを積極的に探しています。 すべての仮想マシンは、攻撃の対象となる可能性があります。 VM への侵害が成功すると、これは環境内のリソースをさらに攻撃するためのエントリ ポイントとして使用されます。

    Azure Defender for servers を有効にすると、Just-In-Time VM アクセスを使用して、お使いの VM への受信トラフィックをロックダウンできます。これにより、攻撃に対する露出が減り、VM への接続が必要な場合は簡単にアクセスできます。 詳細については、[JIT VM アクセス](just-in-time-explained.md)に関する記事をご覧ください。

- **ファイルの整合性の監視 (FIM)** - ファイルの整合性の監視 (FIM) は変更の監視とも呼ばれ、オペレーティング システムやアプリケーション ソフトウェアなどのファイルとレジストリを調べて、攻撃の兆候となる変更を確認します。 比較方式を使用して、ファイルの現在の状態がファイルの前回のスキャンと異なっているかどうかが判別されます。 この比較を利用して、有効なものであれ、疑わしいものであれ、ファイルに変更が加えられていないかを判別できます。

    Azure Defender for servers を有効にすると、FIM を使用して、Windows ファイル、Windows レジストリ、および Linux ファイルの整合性を検証できます。 詳細については、「[Azure Security Center のファイルの整合性の監視](security-center-file-integrity-monitoring.md)」をご覧ください。

- **適応型アプリケーション制御 (AAC)** - 適応型アプリケーション制御は、マシンの既知の安全なアプリケーションの許可リストを定義するためのインテリジェントで自動化されたソリューションです。

    適応型アプリケーション制御を有効にして構成した場合、安全なものとして定義したもの以外のアプリケーションが実行されると、セキュリティ アラートが表示されます。 詳細については、「[適応型アプリケーション制御を使用して、マシンの攻撃対象領域を減らす](security-center-adaptive-application.md)」をご覧ください。

- **アダプティブ ネットワークのセキュリティ強化機能 (ANH)** - ネットワーク セキュリティ グループ (NSG) を適用してリソースとの間でやり取りされるトラフィックをフィルター処理することにより、ネットワーク セキュリティ ポスチャを向上させることができます。 ただし、NSG を通過する実際のトラフィックが、定義される NSG ルールのサブセットとなる場合もあります。 このような場合は、実際のトラフィック パターンに基づいて NSG ルールを強化することによって、セキュリティ ポスチャをさらに向上させることができます。

    アダプティブ ネットワークのセキュリティ強化機能によって、NSG ルールをさらに強化するための推奨事項が提供されます。 実際のトラフィック、既知の信頼された構成、脅威インテリジェンス、および侵害に関するその他のインジケーターを考慮する機械学習アルゴリズムを使用し、特定の IP/ポート タプルからのトラフィックのみを許可する推奨事項を提示します。 詳細については、「[アダプティブ ネットワークのセキュリティ強化により、ネットワークのセキュリティ体制を向上させる](security-center-adaptive-network-hardening.md)」をご覧ください。

- **Docker ホストの強化機能** - Azure Security Center では、IaaS Linux VM 上、または Docker コンテナーを実行している他の Linux マシン上でホストされているアンマネージド コンテナーが識別されます。 Security Center によって、こうしたコンテナーの構成が継続的に評価されます。 その後、Center for Internet Security (CIS) Docker Benchmark と比較されます。 Security Center には CIS Docker Benchmark のルールセット全体が含まれており、コンテナーがいずれかのコントロールを満たしていない場合は警告が表示されます。 詳細については、「[Docker ホストのセキュリティを強化する](harden-docker-hosts.md)」をご覧ください。

- **ファイルレス攻撃の検出 (Windows のみ)** - ファイルレス攻撃は、ディスクベースのスキャン手法による検出を避けるために、悪意のあるペイロードをメモリに挿入します。 侵害されたプロセスのメモリ内に存続する攻撃者のペイロードにより、さまざまな悪意のあるアクティビティが実行されます。

  自動メモリ フォレンジック手法は、ファイルレス攻撃の検出を使用して、ファイルレス攻撃のツールキット、手法、および動作を識別します。 このソリューションでは、実行時にマシンが定期的にスキャンされて、プロセスのメモリから分析情報が直接抽出されます。 特定の分析情報には、次のものの識別が含まれます。 

  - よく知られているツールキットと暗号化マイニング ソフトウェア 

  - シェルコード。通常、ソフトウェアの脆弱性の悪用でペイロードとして使用される小さなコードです。

  - プロセスのメモリ内に挿入された悪意のある実行可能ファイル

  ファイルレス攻撃の検出では、ネットワーク アクティビティなどの追加のプロセス メタデータの記述を含む詳細なセキュリティ アラートが生成されます。 これにより、アラートのトリアージ、関連付け、およびダウンストリームの応答時間が短縮されます。 このアプローチにより、イベント ベースの EDR ソリューションが補完され、検出範囲が拡大します。

  ファイルレス攻撃の検出アラートの詳細については、[アラートのリファレンス表](alerts-reference.md#alerts-windows)に関するページを参照してください。

- **Linux auditd アラートと Log Analytics エージェントの統合 (Linux のみ)** - auditd システムは、システム コールの監視を担当するカーネル レベルのサブシステムで構成されます。 指定されたルール セットによってそれらのフィルター処理が行われ、それらに対するメッセージがソケットに書き込まれます。 Security Center は、Log Analytics エージェント内で auditd パッケージの機能を統合します。 この統合により、前提条件なしで、すべてのサポートされている Linux ディストリビューションで auditd イベントの収集が可能になります。

    Linux 用 Log Analytics エージェントを使用して、auditd レコードの収集、拡充、およびイベントへの集約が行われます。 Security Center では、Linux のシグナルを使用してクラウドおよびオンプレミスの Linux マシン上で悪意のある動作を検出する新しい分析が、継続的に追加されています。 Windows の機能と同様に、これらの分析には、不審なプロセス、不審なサインインの試行、カーネル モジュールの読み込み、その他のアクティビティが含まれます。 これらのアクティビティは、マシンが攻撃を受けているか、侵害されたことを示している可能性があります。  

    Linux アラートの一覧については、[アラートのリファレンス表](alerts-reference.md#alerts-linux)に関するページを参照してください。


## <a name="simulating-alerts"></a>アラートのシミュレーション

アラートをシミュレートするには、次のいずれかのプレイブックをダウンロードします。

- Windows の場合:[Azure Security Center プレイブック:セキュリティのアラート](https://github.com/Azure/Azure-Security-Center/blob/master/Simulations/Azure%20Security%20Center%20Security%20Alerts%20Playbook_v2.pdf)

- Linux の場合:[Azure Security Center プレイブック:Linux の検出](https://github.com/Azure/Azure-Security-Center/blob/master/Simulations/Azure%20Security%20Center%20Linux%20Detections_v2.pdf)」をダウンロードします。




## <a name="next-steps"></a>次の手順

この記事では、Azure Defender for servers について説明しました。 

関連資料については、次の記事をご覧ください。 

- アラートは、Security Center によって生成されたか、別のセキュリティ製品の Security Center によって受信されたかにかかわらず、エクスポートすることができます。 アラートを Azure Sentinel、サードパーティの SIEM、またはその他の外部ツールにエクスポートする場合は、[SIEM へのアラートのエクスポート](continuous-export.md)に関するページの手順に従ってください。

- > [!div class="nextstepaction"]
    > [Azure Defender を有効にする](enable-azure-defender.md)