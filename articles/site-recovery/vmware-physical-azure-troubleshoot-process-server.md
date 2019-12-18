---
title: Azure Site Recovery プロセス サーバーのトラブルシューティング
description: この記事では、Azure Site Recovery プロセス サーバーに関する問題のトラブルシューティングを行う方法について説明します
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: 812cd0293f9627b7438e9870d8985e71dae1d147
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2019
ms.locfileid: "70813415"
---
# <a name="troubleshoot-the-process-server"></a>プロセス サーバーのトラブルシューティング

[Site Recovery](site-recovery-overview.md) プロセス サーバーは、オンプレミスの VMware VM と物理サーバーの Azure に対するディザスター リカバリーを設定するときに使用されます。 この記事では、レプリケーションと接続の問題も含めた、プロセス サーバーに関する問題のトラブルシューティングを行う方法について説明します。

プロセス サーバーの[詳細について確認](vmware-physical-azure-config-process-server-overview.md)してください。

## <a name="before-you-start"></a>開始する前に

トラブルシューティングを開始する前に、次を行ってください。

1. [プロセス サーバーを監視](vmware-physical-azure-monitor-process-server.md)する方法について理解しておく必要があります。
2. 以下のベスト プラクティスをレビューします。
3. [容量に関する考慮事項](site-recovery-plan-capacity-vmware.md#capacity-considerations)に従い、[構成サーバー](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server)または[スタンドアロン プロセス サーバー](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server)用のサイズに関するガイドラインを使用します。

## <a name="best-practices-for-process-server-deployment"></a>プロセス サーバーのデプロイのベスト プラクティス

プロセス サーバーの最適なパフォーマンスのために、一般的なベスト プラクティスを数多くまとめました。

**ベスト プラクティス** | **詳細**
--- |---
**使用方法** | 構成サーバーとスタンドアロン プロセス サーバーは、意図された目的のためにのみ使用するようにします。 マシン上では他には何も実行しないでください。
**IP アドレス** | プロセス サーバーで静的 IPv4 アドレスが使用され、NAT は構成されていないことを確認します。
**メモリと CPU の使用率の制御** |CPU とメモリの使用率は、70% 以下とします。
**空き領域の確保** | 空き領域とは、プロセス サーバー上のキャッシュ ディスク領域を指します。 レプリケーション データは、Azure にアップロードされる前にキャッシュに格納されます。<br/><br/> 空き領域は 25% 以上を維持してください。 20% を下回ると、プロセス サーバーに関連付けられているレプリケートされたマシン用にレプリケーションが調整されます。

## <a name="check-process-server-health"></a>プロセス サーバーの正常性を確認する

トラブルシューティングの最初の手順は、プロセス サーバーの正常性と状態を確認することです。 これを行うには、すべてのアラートをレビューし、必要なサービスが実行されていることを確認し、プロセス サーバーからのハートビートがあることを確認します。 これらの手順を次の図にまとめました。その後に、手順を行うための方法を記載しています。

![プロセス サーバーの正常性のトラブルシューティング](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-process-server-health.png)

## <a name="step-1-troubleshoot-process-server-health-alerts"></a>手順 1:プロセス サーバーの正常性アラートのトラブルシューティング

多くの正常性アラートがプロセス サーバーによって生成されます。 これらのアラートと推奨されるアクションを、次の表にまとめて示します。

**アラートの種類** | **Error** | **トラブルシューティング**
--- | --- | --- 
![Healthy][green] | なし  | プロセス サーバーは接続されており、正常な状態です。
![警告][yellow] | 指定されたサービスが実行されていません。 | 1.サービスが実行されていることを確認します。<br/> 2.サービスが期待どおりに実行されている場合は、下の[接続とレプリケーションの問題のトラブルシューティングを行う](#check-connectivity-and-replication)ための手順に従います。
![警告][yellow]  | 過去 15 分間の CPU 使用率が 80% を超えています。 | 1.新しいマシンを追加しないでください。<br/>2.プロセス サーバーを使用している VM の数が[定義済みの上限](site-recovery-plan-capacity-vmware.md#capacity-considerations)を超えていないことを確認し、[追加のプロセス サーバー](vmware-azure-set-up-process-server-scale.md)を設定することを検討します。<br/>3.下の[接続とレプリケーションの問題のトラブルシューティングを行う](#check-connectivity-and-replication)ための手順に従います。
![重大][red] |  過去 15 分間の CPU 使用率が 95% を超えています。 | 1.新しいマシンを追加しないでください。<br/>2.プロセス サーバーを使用している VM の数が[定義済みの上限](site-recovery-plan-capacity-vmware.md#capacity-considerations)を超えていないことを確認し、[追加のプロセス サーバー](vmware-azure-set-up-process-server-scale.md)を設定することを検討します。<br/>3.下の[接続とレプリケーションの問題のトラブルシューティングを行う](#check-connectivity-and-replication)ための手順に従います。<br/> 4.問題が解決しない場合は、VMware/物理サーバーのレプリケーション用に [Deployment Planner](https://aka.ms/asr-v2a-deployment-planner) を実行します。
![警告][yellow] | 過去 15 分間のメモリ使用率が 80% を超えています。 |  1.新しいマシンを追加しないでください。<br/>2.プロセス サーバーを使用している VM の数が[定義済みの上限](site-recovery-plan-capacity-vmware.md#capacity-considerations)を超えていないことを確認し、[追加のプロセス サーバー](vmware-azure-set-up-process-server-scale.md)を設定することを検討します。<br/>3.警告に関連付けられているすべての手順に従います。<br/> 4.問題が解決しない場合は、下の[接続とレプリケーションの問題のトラブルシューティングを行う](#check-connectivity-and-replication)ための手順に従います。
![重大][red] | 過去 15 分間のメモリ使用率が 95% を超えています。 | 1.新しいマシンを追加しないでください。また、[追加のプロセス サーバー](vmware-azure-set-up-process-server-scale.md)を設定することを検討します。<br/> 2.警告に関連付けられているすべての手順に従います。<br/> 3. 問題が解決しない場合は、下の[接続とレプリケーションの問題のトラブルシューティングを行う](#check-connectivity-and-replication)ための手順に従います。<br/> 4.問題が解決しない場合は、VMware/物理サーバーのレプリケーションの問題用に [Deployment Planner](https://aka.ms/asr-v2a-deployment-planner) を実行します。
![警告][yellow] | 過去 15 分間のキャッシュ フォルダーの空き領域が 30% 未満です。 | 1.新しいマシンを追加しないでください。また、[追加のプロセス サーバー](vmware-azure-set-up-process-server-scale.md)を設定することを検討します。<br/>2.プロセス サーバーを使用している VM の数が[ガイドライン](site-recovery-plan-capacity-vmware.md#capacity-considerations)を超えていないことを確認します。<br/> 3.下の[接続とレプリケーションの問題のトラブルシューティングを行う](#check-connectivity-and-replication)ための手順に従います。
![重大][red] |  過去 15 分間の空き領域が 25% 未満です。 | 1.この問題の警告に関連付けられている手順に従います。<br/> 2. 下の[接続とレプリケーションの問題のトラブルシューティングを行う](#check-connectivity-and-replication)ための手順に従います。<br/> 3.問題が解決しない場合は、VMware/物理サーバーのレプリケーション用に [Deployment Planner](https://aka.ms/asr-v2a-deployment-planner) を実行します。
![重大][red] | プロセス サーバーからのハートビートが 15 分以上ありません。 tmansvs サービスが構成サーバーと通信していません。 | 1) プロセス サーバーが稼働していることを確認します。<br/> 2.tmassvc がプロセス サーバー上で実行されていることを確認します。<br/> 3.下の[接続とレプリケーションの問題のトラブルシューティングを行う](#check-connectivity-and-replication)ための手順に従います。


![テーブル キー](./media/vmware-physical-azure-troubleshoot-process-server/table-key.png)


## <a name="step-2-check-process-server-services"></a>手順 2:プロセス サーバーのサービスの確認

プロセス サーバー上で実行される必要のあるサービスを、次の表にまとめて示します。 プロセス サーバーがどのようにデプロイされたのかに応じて、サービスにはわずかな違いがあります。 

Microsoft Azure Recovery Services エージェント (obengine) 以外のすべてのサービスについて、StartType が **[自動]** または **[自動 (遅延開始)]** に設定されていることを確認します。
 
**Deployment** | **実行中のサービス**
--- | ---
**構成サーバー上のプロセス サーバー** | ProcessServer、ProcessServerMonitor、cxprocessserver、InMage PushInstall、Log Upload Service (LogUpload)、InMage Scout Application Service、Microsoft Azure Recovery Services Agent (obengine)、InMage Scout VX Agent-Sentinel/Outpost (svagents)、tmansvc、World Wide Web Publishing Service (W3SVC)、MySQL、Microsoft Azure Site Recovery Service (dra)
**スタンドアロン サーバーとして実行中のプロセス サーバー** | ProcessServer、ProcessServerMonitor、cxprocessserver、InMage PushInstall、Log Upload Service (LogUpload)、InMage Scout Application Service、Microsoft Azure Recovery Services Agent (obengine)、InMage Scout VX Agent-Sentinel/Outpost (svagents)、tmansvc
**フェールバック用に Azure でデプロイされたプロセス サーバー** | ProcessServer、ProcessServerMonitor、cxprocessserver、InMage PushInstall、Log Upload Service (LogUpload)


## <a name="step-3-check-the-process-server-heartbeat"></a>手順 3:プロセス サーバーのハートビートの確認

プロセス サーバーからハートビートがない場合は (エラー コード 806)、次を実行します。

1. プロセス サーバー VM が稼働していることを確認します。
2. 次のログでエラーを確認します。

    C:\ProgramData\ASR\home\svsystems\eventmanager *.log  C\ProgramData\ASR\home\svsystems\monitor_protection*.log

## <a name="check-connectivity-and-replication"></a>接続とレプリケーションの確認

 初期や継続的に発生するレプリケーションの問題は、ソース マシンとプロセス サーバーの間、またはプロセス サーバーと Azure の間の接続に関する問題が原因であることが少なくありません。 これらの手順を次の図にまとめました。その後に、手順を行うための方法を記載しています。

![接続とレプリケーションのトラブルシューティング](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-connectivity-replication.png)


## <a name="step-4-verify-time-sync-on-source-machine"></a>手順 4:ソース マシンの時刻同期の確認

レプリケートされたマシンのシステム日時が同期していることを確認します。[詳細情報](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)

## <a name="step-5-check-anti-virus-software-on-source-machine"></a>手順 5:ソース マシン上のウイルス対策ソフトウェアの確認

レプリケートされたマシン上のウイルス対策ソフトウェアが Site Recovery をブロックしていないことを確認します。 ウイルス対策プログラムから Site Recovery を除外する必要がある場合は、[この記事](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)を確認してください。

## <a name="step-6-check-connectivity-from-source-machine"></a>手順 6:ソース マシンからの接続の確認


1. 必要であれば、[Telnet クライアント](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx)をソース マシン上にインストールします。 ping は使用しないでください。
2. ソース マシンから、Telnet を使用して HTTPS ポート上のプロセス サーバーを ping します。 既定では、9443 がレプリケーション トラフィック用の HTTPS ポートです。

    `telnet <process server IP address> <port>`

3. 接続が成功したかどうかを確認します。


**接続** | **詳細** | **アクション**
--- | --- | ---
**成功** | Telnet により空の画面が表示されます。プロセス サーバーは到達可能です。 | これ以上必要な操作はありません。
**失敗** | 接続できません | 受信ポート 9443 がプロセス サーバーで許可されていることを確認します。 たとえば、境界ネットワークまたはスクリーン サブネットがある場合です。 接続を再度確認します。
**一部成功** | 接続はできますが、プロセス サーバーに到達できないことがソース マシンから報告されます。 | 次のトラブルシューティングの手順に進みます。

## <a name="step-7-troubleshoot-an-unreachable-process-server"></a>手順 7:到達できないプロセス サーバーのトラブルシューティング

ソース マシンからプロセス サーバーに到達できない場合、エラー 78186 が表示されます。 この問題に対処しないと、アプリ整合性とクラッシュ整合性の復旧ポイントの両方が期待どおりに生成されなくなります。

ソース マシンからプロセス サーバーの IP アドレスに到達できるかを確認することでトラブルシューティングを行い、ソース マシン上で cxpsclient ツールを実行して、エンド ツー エンド接続を確認します。


### <a name="check-the-ip-connection-on-the-process-server"></a>プロセス サーバー上の IP 接続を確認する

telnet が成功してもプロセス サーバーに到達できないことがソース マシンから報告される場合、プロセス サーバーの IP アドレスに到達できるかどうかを確認します。

1. Web ブラウザーで、IP アドレス https://<PS_IP>:<PS_Data_Port>/ への到達を試します。
2. この確認により HTTPS 証明書エラーが表示された場合は、正常です。 エラーを無視すると、400 - Bad Request が表示されます。 これは、サーバーがブラウザーの要求を処理できないこと、および標準 HTTPS 接続が正常であることを意味します。
3. この確認がうまくいかない場合、ブラウザーのエラー メッセージに注意してください。 たとえば、407 エラーはプロキシ認証に関する問題を示すものです。

### <a name="check-the-connection-with-cxpsclient"></a>cxpsclient を使用して接続を確認する

さらに、cxpsclient ツールを実行してエンド ツー エンド接続を確認することができます。

1. 次のようにツールを実行します。

    ```
    <install folder>\cxpsclient.exe -i <PS_IP> -l <PS_Data_Port> -y <timeout_in_secs:recommended 300>
    ```

2. プロセス サーバーで、これらのフォルダーに生成されたログを確認します。

    C:\ProgramData\ASR\home\svsystems\transport\log\cxps.err  C:\ProgramData\ASR\home\svsystems\transport\log\cxps.xfer



### <a name="check-source-vm-logs-for-upload-failures-error-78028"></a>ソース VM のログで、アップロード エラー (エラー 78028) を確認する

ソース マシンからプロセス サービスへのブロックされたデータ アップロードに関する問題が原因で、クラッシュ整合性とアプリ整合性の復旧ポイントの両方が生成されなくなる可能性があります。 

1. ネットワークのアップロード エラーのトラブルシューティングを行うには、このログでエラーを探します。

    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\svagents*.log 

2. この記事の残りの手順を使用すると、データのアップロードの問題の解決に役立ちます。



## <a name="step-8-check-whether-the-process-server-is-pushing-data"></a>ステップ 8:プロセス サーバーによりデータがプッシュされているかの確認

プロセス サーバーにより、データが Azure にアクティブにプッシュされているかどうかを確認します。

  1. プロセス サーバー上で、タスク マネージャーを開きます (Ctrl + Shift + Esc キーを押します)。
  2. **[パフォーマンス]** タブ > **[リソース モニターを開く]** の順に選択します。
  3. **[リソース モニター]** ページで **[ネットワーク]** タブを選択します。 **[ネットワーク活動のプロセス]** で、cbengine.exe により大量のデータがアクティブに送信されているかどうかを確認します。

       ![ネットワーク活動を処理中のボリューム](./media/vmware-physical-azure-troubleshoot-process-server/cbengine.png)

  Cbengine.exe により大量のデータが送信されていない場合は、次のセクションの手順を実行します。

## <a name="step-9-check-the-process-server-connection-to-azure-blob-storage"></a>手順 9:プロセス サーバーの Azure Blob Storage への接続の確認

1. リソース モニターで、 **[cbengine.exe]** を選択します。
2. **[TCP 接続]** の下で、プロセス サーバーから Azure Storage への接続があるかどうかを確認します。

  ![cbengine.exe と Azure Blob Storage の URL 間の接続](./media/vmware-physical-azure-troubleshoot-process-server/rmonitor.png)

### <a name="check-services"></a>サービスを確認する

プロセス サーバーから Azure Blob Storage の URL への接続が存在しない場合は、サービスが実行されているかを確認します。

1. コントロール パネルで、 **[サービス]** をクリックします。
2. 次のサービスが実行中であることを確認します。

    - cxprocessserver
    - InMage Scout VX Agent – Sentinel/Outpost
    - Microsoft Azure Recovery Services エージェント
    - Microsoft Azure Site Recovery サービス
    - tmansvc

3. 実行されていないサービスがあれば、そのサービスを起動または再起動します。
4. プロセス サーバーが接続されていて、到達できることを確認します。 

## <a name="step-10-check-the-process-server-connection-to-azure-public-ip-address"></a>手順 10: プロセス サーバーの Azure パブリック IP アドレスへの接続の確認

1. プロセス サーバーで、 **%programfiles%\Microsoft Azure Recovery Services Agent\Temp** から最新の CBEngineCurr.errlog ファイルを開きます。
2. このファイル内で、**443** または **connection attempt failed** という文字列を検索します。

  ![Temp フォルダー内のエラー ログ](./media/vmware-physical-azure-troubleshoot-process-server/logdetails1.png)

3. 問題が見つかった場合、Azure のパブリック IP アドレスを、ポート 443 を使用して CBEngineCurr.currLog ファイル内で見つけます。

  `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

5. プロセス サーバー内のコマンド ラインで、Telnet を使用して Azure のパブリック IP アドレスを ping します。
6. 接続できない場合、次の手順に従います。

## <a name="step-11-check-process-server-firewall-settings"></a>手順 11: プロセス サーバーのファイアウォール設定の確認 

プロセス サーバー上の IP アドレス ベースのファイアウォールによってアクセスがブロックされているかどうかを確認します。

1. IP アドレスベースのファイアウォール規則の場合:

    a) [Microsoft Azure データセンターの IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)の完全なリストをダウンロードします。

    b) IP アドレス範囲をファイアウォール構成に追加し、Azure (および既定の HTTPS ポートである 443) への通信がファイアウォールで許可されるようにします。

    c) サブスクリプションの Azure リージョンと米国西部の Azure リージョンの IP アドレス範囲を許可します (アクセス制御と ID 管理に使用されます)。

2. URL ベースのファイアウォールを使用する場合は、次の表に示されている URL をファイアウォール構成に追加します。

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  


## <a name="step-12-verify-process-server-proxy-settings"></a>手順 12:プロセス サーバーのプロキシ設定の確認 

1. プロキシ サーバーを使用している場合は、DNS サーバーでプロキシ サーバー名が解決されることを確認します。 構成サーバーの設定時にレジストリ キー **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings** に指定した値を確認します。
2. データを送信するために Azure Site Recovery エージェントで同じ設定が使用されていることを確認します。

    a) **Microsoft Azure Backup** を検索します。

    b) **Microsoft Azure Backup** を開き、 **[アクション]**  >  **[プロパティの変更]** の順に選択します。

    c) **[プロキシの構成]** タブで、プロキシのアドレスはレジストリ設定に表示されるプロキシのアドレスと同じでなければなりません。 同じでない場合は、同じアドレスに変更してください。

## <a name="step-13-check-bandwidth"></a>手順 13:帯域幅の確認

プロセス サーバーと Azure 間の帯域幅を増やし、問題がまだ発生するかどうかを確認します。


## <a name="next-steps"></a>次の手順

さらにサポートが必要な場合は、[Azure Site Recovery フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)に質問を投稿してください。 

[green]: ./media/vmware-physical-azure-troubleshoot-process-server/green.png
[yellow]: ./media/vmware-physical-azure-troubleshoot-process-server/yellow.png
[red]: ./media/vmware-physical-azure-troubleshoot-process-server/red.png
