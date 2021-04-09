---
title: Azure Sentinel に Syslog データを接続する | Microsoft Docs
description: アプライアンスと Azure Sentinel の間で Linux マシン上のエージェントを使用して、Syslog をサポートするマシンまたはアプライアンスを Azure Sentinel に接続します。
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
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: d35a97b0008a7ce3069185dd557a60221776b0ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100595472"
---
# <a name="collect-data-from-linux-based-sources-using-syslog"></a>Syslog を使用して Linux ベースのソースからデータを収集する

Linux 用 Log Analytics エージェント (旧称 OMS エージェント) を使用して、Linux ベースの Syslog 対応マシンまたはアプライアンスから Azure Sentinel にイベントをストリーミングできます。 この操作は、Log Analytics エージェントをマシンに直接インストールできる任意のマシンに対して行うことができます。 マシンのネイティブ Syslog デーモンは、指定された種類のローカル イベントを収集し、それらをローカルでエージェントに転送します。これで、ローカル イベントが Log Analytics ワークスペースにストリーミングされます。

> [!NOTE]
> - アプライアンスが **Syslog を介して Common Event Format (CEF)** をサポートしている場合は、より完全なデータセットが収集され、データは収集時に解析されます。 このオプションを選択し、「[共通イベント形式を使用して外部ソリューションを接続する](connect-common-event-format.md)」の手順に従ってください。
>
> - Log Analytics では、**rsyslog** または **syslog-ng** の各デーモンによって送信されたメッセージの収集がサポートされています。rsyslog は既定のデーモンです。 syslog イベントの収集に関して、バージョン 5 の Red Hat Enterprise Linux (RHEL)、CentOS、Oracle Linux 版の既定の syslog デーモン (**sysklog**) はサポートされません。 このバージョンの各種ディストリビューションから syslog データを収集するには、rsyslog デーモンをインストールし、sysklog を置き換えるように構成する必要があります。

## <a name="how-it-works"></a>しくみ

**Syslog** は、Linux に共通のイベント ログ プロトコルです。 仮想マシンまたはアプライアンスに **Linux 用 Log Analytics エージェント** がインストールされている場合は、インストール ルーチンによって、TCP ポート 25224 でエージェントにメッセージを転送するローカル Syslog デーモンが構成されます。 次に、エージェントは、HTTPS 経由で Log Analytics ワークスペースにメッセージを送信します。ここで、メッセージは、 **[Azure Sentinel] > [ログ]** の Syslog テーブルのイベント ログ エントリに解析されます。

詳細については、「[Azure Monitor の Syslog データ ソース](../azure-monitor/agents/data-sources-syslog.md)」を参照してください。

## <a name="configure-syslog-collection"></a>Syslog 収集の構成

### <a name="configure-your-linux-machine-or-appliance"></a>Linux マシンまたはアプライアンスを構成する

1. Azure Sentinel で **[Data connectors]\(データ コネクタ\)** を選択し、 **[Syslog]** コネクタを選択します。

1. **[Syslog]** ブレードで、 **[Open connector page]\(コネクタ ページを開く\)** を選択します。

1. Linux エージェントをインストールします。 **[Choose where to install the agent:]\(エージェントのインストール先を選択してください:\)** で、以下の操作を実行します。
    
    **Azure Linux VM の場合:**
      
    1. **[Install agent on Azure Linux virtual machine]\(Azure Linux 仮想マシンにエージェントをインストールする\)** を選択します。
    
    1. **[Download & install agent for Azure Linux Virtual machines]\(Azure Linux 仮想マシン用のエージェントをダウンロードしてインストールする\) >** リンクをクリックします。 
    
    1. **[仮想マシン]** ブレードで、エージェントをインストールする仮想マシンをクリックし、 **[接続]** をクリックします。 接続する各 VM に対してこの手順を繰り返します。
    
    **その他の Linux マシンの場合:**

    1. **[Install agent on a non-Azure Linux Machine]\(Azure 以外の Linux マシンにエージェントをインストールする\)** を選択します。

    1. **[Download & install agent for non-Azure Linux machines]\(Azure 以外の Linux マシン用のエージェントをダウンロードしてインストールする\) >** リンクをクリックします。 

    1. **[エージェント管理]** ブレードで、 **[Linux サーバー]** タブをクリックし、 **[Linux 用エージェントのダウンロードとオンボード]** のコマンドをコピーして、Linux マシンで実行します。 
    
   > [!NOTE]
   > 組織のセキュリティ ポリシーに従って、これらのコンピューターのセキュリティ設定を構成してください。 たとえば、組織のネットワーク セキュリティ ポリシーに合わせてネットワーク設定を構成し、デーモンのポートとプロトコルをセキュリティ要件に合わせて変更できます。

### <a name="configure-the-log-analytics-agent"></a>Log Analytics エージェントの構成

1. [Syslog コネクタ] ブレードの下部で、 **[Open your workspace agents configuration]\(ワークスペースのエージェント構成を開く\) >** リンクをクリックします。

1. **[エージェント構成]** ブレードで、 **[Syslog]** タブを選択します。次に、収集するコネクタのファシリティを追加します。 **[Add facility]\(ファシリティの追加\)** を選択し、ファシリティのドロップダウン リストから選択します。
    
    - Syslog アプライアンスがそのログ ヘッダーに含めるファシリティを追加します。 
    
    - 収集するデータで異常な SSH ログイン検出を使用する場合は、 **[auth]** と **[authpriv]** を追加します。 追加情報については、[次のセクション](#configure-the-syslog-connector-for-anomalous-ssh-login-detection)を参照してください。

1. 監視するすべてのファシリティを追加したら、必要な重大度のチェック ボックスがすべてオンになっていることを確認します。

1. **[適用]** を選択します。 

1. 仮想マシンまたはアプライアンスで、指定したファシリティが送信されていることを確認します。

1. **[ログ]** で syslog ログ データを照会するには、クエリ ウィンドウで「`Syslog`」と入力します。

1. 「[Azure Monitor ログ クエリでの関数の使用](../azure-monitor/logs/functions.md)」で説明されているクエリ パラメーターを使用して、Syslog メッセージを解析できます。 その後、新しい Log Analytics 関数としてクエリを保存し、新しいデータ型として使用できます。

> [!NOTE]
> **同じマシンを使用してプレーンな Syslog *と* CEF メッセージの両方を転送する**
>
> 既存の [CEF ログ フォワーダー マシン](connect-cef-agent.md)を使用して、プレーンな Syslog ソースからもログを収集して転送できます。 ただし、両方の形式のイベントを Azure Sentinel に送信しないようにするには、次の手順を実行する必要があります。この結果、イベントが重複することになります。
>
>    [CEF ソースからのデータ収集](connect-common-event-format.md)を既に設定していて、Log Analytics エージェントを前述のように構成している場合:
>
> 1. CEF 形式でログを送信する各マシンで Syslog 構成ファイルを編集して、CEF メッセージの送信に使用されているファシリティを削除する必要があります。 これで、CEF で送信されるファシリティは、Syslog で送信されません。 この方法の詳細については、「[Linux エージェントでの Syslog の構成](../azure-monitor/agents/data-sources-syslog.md#configure-syslog-on-linux-agent)」を参照してください。
>
> 1. これらのマシンで次のコマンドを実行して、エージェントと Azure Sentinel の Syslog 構成との同期を無効にする必要があります。 これで、前の手順で構成に加えた変更が上書きされなくなります。<br>
> `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`

### <a name="configure-the-syslog-connector-for-anomalous-ssh-login-detection"></a>異常な SSH ログイン検出用に Syslog コネクタを構成する

> [!IMPORTANT]
> 異常な SSH ログイン検出は現在、パブリック プレビュー段階です。
> この機能はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure Sentinel は Syslog データに機械学習 (ML) を適用して、異常な Secure Shell (SSH) ログイン アクティビティを識別できます。 シナリオには以下が含まれます。

- あり得ない移動 - 2 つの成功したログイン イベントが、この 2 つのログイン イベントの期間内に到達できない 2 つの場所で発生した場合。
- 予期しない場所 - 成功したログイン イベントの発生した場所が疑わしい場合。 たとえば、最近検出されることのなかった場所などです。
 
この検出には、Syslog データ コネクタの特定の構成が必要です。 

1. 上の手順 2 の [[Log Analytics エージェントの構成]](#configure-the-log-analytics-agent) で、**auth** と **authpriv** の両方が監視対象のファシリティとして選択されていること、すべての重大度が選択されていることを確認します。 

2. Syslog 情報が収集されるまで、十分な時間をかけます。 その後、 **[Azure Sentinel - Logs]\(Azure Sentinel - ログ\)** に移動して、次のクエリをコピーして貼り付けます。
    
    ```kusto
    Syslog
    | where Facility in ("authpriv","auth")
    | extend c = extract( "Accepted\\s(publickey|password|keyboard-interactive/pam)\\sfor ([^\\s]+)",1,SyslogMessage)
    | where isnotempty(c)
    | count 
    ```
    
    必要に応じて **[Time range]\(時間の範囲\)** を変更し、 **[実行]** を選択します。
    
    結果の数が 0 の場合は、コネクタの構成を確認し、クエリに指定した期間内に監視対象のコンピュータで成功したログイン アクティビティがあったことを確認します。
    
    結果の数が 0 より大きい場合、Syslog データは、異常な SSH ログインの検出用に適しています。 この検出は、 **[分析]**  >   **[Rule templates]\(ルール テンプレート\)**  >  **[(Preview) Anomalous SSH Login Detection]\((プレビュー) 異常な SSH ログインの検出\)** で有効にします。

## <a name="next-steps"></a>次のステップ
このドキュメントでは、Syslog オンプレミス アプライアンスを Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。

