---
title: Azure Sentinel Preview に CEF データを接続する | Microsoft Docs
description: Linux マシンをログ フォワーダーとして使用して、Azure Sentinel に共通イベント形式 (CEF) メッセージを送信する外部ソリューションを接続します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2020
ms.author: yelevin
ms.openlocfilehash: 54fd6c0c085c0055f3114fde606f8f7d2f2e055e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104772061"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>共通イベント形式を使用して外部ソリューションを接続する

CEF メッセージを送信する外部ソリューションを接続するとき、Azure Sentinel と接続するための 3 つの手順があります。

手順 1: [Syslog/CEF フォワーダーをデプロイして CEF に接続する](connect-cef-agent.md) 手順 2:[ソリューション固有の手順を実行する](connect-cef-solution-config.md) 手順 3:[接続を検証する](connect-cef-verify.md)

この記事では、接続のしくみを説明し、前提条件を一覧表示します。また、Syslog に加えて共通イベント形式 (CEF) メッセージを送信するセキュリティ ソリューションのメカニズムをデプロイする手順を示します。 

> [!NOTE] 
> データは、Azure Sentinel を実行しているワークスペースの地域に保存されます。

この接続を確立するには、アプライアンスと Azure Sentinel 間の通信をサポートするために、Syslog フォワーダー サーバーをデプロイする必要があります。  サーバーは、Linux 用の Log Analytics エージェントがインストールされた専用の Linux マシン (VM またはオンプレミス) で構成されます。 

次の図は、Azure で Linux VM を使用した場合の設定を示しています。

 ![Azure での CEF](./media/connect-cef/cef-syslog-azure.png)

あるいは、別のクラウドの VM かオンプレミスのマシンを使用する場合には、以下の設定となります。 

 ![オンプレミスの CEF](./media/connect-cef/cef-syslog-onprem.png)

## <a name="security-considerations"></a>セキュリティに関する考慮事項

組織のセキュリティ ポリシーに従って、コンピューターのセキュリティを構成してください。 たとえば、企業のネットワーク セキュリティ ポリシーに合わせてネットワークを構成し、デーモンのポートとプロトコルを要件に合わせて変更することができます。 [Azure で VM をセキュリティ保護する](../virtual-machines/security-policy.md)方法に関するページと[ネットワーク セキュリティのベスト プラクティス](../security/fundamentals/network-best-practices.md)に関するページの手順を行うと、マシンのセキュリティ構成を改善できます。

Syslog ソースと Syslog フォワーダーの間で TLS 通信を使用するには、TLS で通信するために Syslog デーモン (rsyslog または syslog-ng) を構成する必要があります。[TLS -rsyslog で Syslog トラフィックを暗号化する](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html)方法に関するページと [TLS –syslog-ng でログ メッセージを暗号化する](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298)方法に関するページをご覧ください。
 
## <a name="prerequisites"></a>前提条件

ログ フォワーダーとして使用する Linux マシンで、次のいずれかのオペレーティング システムが実行されていることを確認します。

- 64 ビット
  - マイナー バージョンを含む CentOS 7 と 8 (6 は不可)
  - Amazon Linux 2017.09
  - Oracle Linux 7
  - マイナー バージョンを含む Red Hat Enterprise Linux (RHEL) Server 7 と 8 (6 は不可)
  - Debian GNU/Linux 8 および 9
  - Ubuntu Linux 14.04 LTS、16.04 LTS、および 18.04 LTS
  - SUSE Linux Enterprise Server 12、15

- 32 ビット
  - マイナー バージョンを含む CentOS 7 と 8 (6 は不可)
  - Oracle Linux 7
  - マイナー バージョンを含む Red Hat Enterprise Linux (RHEL) Server 7 と 8 (6 は不可)
  - Debian GNU/Linux 8 および 9
  - Ubuntu Linux 14.04 LTS および 16.04 LTS
 
- デーモンのバージョン
  - Syslog-ng:2.1 - 3.22.1
  - Rsyslog: v8
  
- サポートされている Syslog RFC
  - Syslog RFC 3164
  - Syslog RFC 5424
 
マシンが次の要件も満たしていることを確認します。 

- 容量
  - マシンには、少なくとも **4 つの CPU コアと 8 GB の RAM** が必要です。

    > [!NOTE]
    > - **rsyslog** デーモンを使用している 1 つのログ フォワーダー マシンには、収集される **最大 8500 EPS (1 秒あたりのイベント数)** のサポートされる容量があります。

- アクセス許可
  - マシンに対する管理者特権のアクセス許可 (sudo) が必要です。 

- ソフトウェア要件
  - マシンで python 2.7 または 3 が実行されていることを確認します。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Azure Sentinel がセキュリティ ソリューションおよびアプライアンスから CEF ログを収集する方法について学習しました。 お使いのソリューションを Azure Sentinel に接続する方法については、次の記事を参照してください。

- 手順 1: [Syslog/CEF フォワーダーをデプロイして CEF に接続する](connect-cef-agent.md)
- 手順 2: [ソリューション固有の手順を実行する](connect-cef-solution-config.md)
- 手順 3: [接続を検証する](connect-cef-verify.md)

Azure Sentinel で収集したデータの処理に関する詳細については、次の記事を参照してください。

- [CEF および CommonSecurityLog フィールド マッピング](cef-name-mapping.md)についての説明。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](./tutorial-detect-threats-built-in.md)の概要。