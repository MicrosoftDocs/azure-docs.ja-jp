---
title: Azure での Windows VM の時刻同期
description: Windows 仮想マシンの時刻同期。
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: 85abc77757d31f3b1054a0670ea3f65a4fcb2e52
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102555960"
---
# <a name="time-sync-for-windows-vms-in-azure"></a>Azure での Windows VM の時刻同期

時刻同期は、セキュリティとイベントの関連付けで重要になります。 場合によっては、分散トランザクションの実装で使用されます。 複数のコンピューター システム間での時刻の精度は、同期によって実現されます。 同期は、再起動や、時刻をフェッチするコンピューターとタイム ソースとの間のネットワーク トラフィックなど、複数の要因に影響を受ける可能性があります。 

Azure は、Windows Server 2016 を実行しているインフラストラクチャによってサポートされるようになりました。 Windows Server 2016 では、時刻を修正し、ローカル クロックを調整して UTC と同期するために使用されるアルゴリズムが改善されています。  Windows Server 2016 では、正確な時刻のために VM とホストが同期するしくみを制御する VMICTimeSync サービスも改善されました。 この改善には、VM の開始時や VM の復元時の初期時刻の精度の向上と割り込み待ち時間の修正が含まれ、Windows Time (W32time) に与えられるサンプルに利用されます。 


>[!NOTE]
>Windows Time サービスの概要については、こちらの[概要ビデオ](https://aka.ms/WS2016TimeVideo)をご覧ください。
>
> 詳細については、「[Windows Server 2016 の正確な時刻](/windows-server/networking/windows-time-service/accurate-time)」を参照してください。 

## <a name="overview"></a>概要

コンピューター クロックの精度は、そのコンピューター クロックと協定世界時 (UTC) 標準時間との誤差で測定されます。 UTC は、300 年に 1 秒しか誤差が生じない正確な原子時計の世界各国のサンプルによって定義されます。 しかし、UTC を直接読み取るには特殊なハードウェアが必要です。 代わりに、タイム サーバーが UTC と同期され、拡張性と堅牢性を提供するために他のコンピューターからアクセスされます。 すべてのコンピューターで時刻同期サービスが実行されており、サーバーの使用時刻が認識され、コンピューター クロックを修正する必要があるかどうかが定期的に確認され、必要に応じて調整されます。 

Azure ホストは内部の Microsoft タイム サーバーに同期されています。このサーバーでは、GPS アンテナを使用して、Microsoft が所有する Stratum 1 デバイスから時刻を取得します。 Azure の仮想マシンでは、そのホストに依存して正確な時刻 (*ホスト時刻*) を VM に渡すことも、VM でタイム サーバーから直接時刻を取得することも、あるいはこれらの両方を組み合わることもできます。 

仮想マシンとホストとのやりとりがクロックに影響する場合もあります。 [メモリ保持メンテナンス](../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)中に、VM は最大で 30 秒間一時停止します。 たとえば、メンテナンスの開始前に、VM クロックには午前 10 時 00 分 00 秒と示され、この状態が 28 秒間続きます。 VM の再開後、VM のクロックはまだ午前 10 時 00 分 00 秒と示されており、28 秒遅れていることになります。 これを修正するため、VMICTimeSync サービスでは、ホストで何が発生しているのかを監視し、VM で生じた変更の補正を求めます。

VMICTimeSync サービスはサンプル モードまたは同期モードで動作し、時刻を進める効果だけがあります。 サンプル モードでは、W32time が実行されている必要があり、VMICTimeSync サービスはホストを 5 秒ごとにポーリングして、W32time タイム サンプルを提供します。 W32time サービスは、約 30 秒ごとに最新のタイム サンプルを取得し、それを使用してゲストのクロックに影響を与えます。 同期モードは、ゲストが再開された場合、またはゲストのクロックがホストのクロックから 5 秒より大きく遅れた場合に、アクティブになります。 W32time サービスが正常に動作している場合、後者のケースは発生しません。

時刻同期が動作しないと、VM のクロックでエラーが蓄積されます。 VM が 1 つのみであれば、ワークロードで高精度のタイムキーピングが必要でない限り、影響は大きくならない場合があります。 しかし、ほとんどの場合、相互接続された VM が複数あり、それらの VM ではトランザクションを追跡するために時間を使用し、その時間はデプロイ全体で一貫している必要があります。 VM 間の時間が異なると、次のような影響が見られる場合があります。

- 認証は失敗します。 Kerberos または証明書に依存するテクノロジのようなセキュリティ プロトコルでは、システム全体で時間が一貫していることが前提となります。 
- ログ (またはその他のデータ) が時間と一致しない場合、システムで何が発生したかを把握するのは非常に困難です。 異なる時間に同じイベントが発生したように見え、関連付けが困難になります。
- クロックに誤差がある場合、請求額を正しく計算できません。

Windows デプロイでは、ゲスト オペレーティング システムとして Windows Server 2016 を使用することで最良の結果が得られます。時刻の同期で最新の改善を利用できるためです。

## <a name="configuration-options"></a>構成オプション

Azure でホストされている Windows VM の時刻同期を構成するためのオプションが 3 つあります。

- ホスト時刻と time.windows.com。 これは Azure Marketplace イメージに使用される既定の構成です。
- ホストのみ。
- ホスト時刻を使用して、または使用せずに、別の外部のタイム サーバーを使用します。


### <a name="use-the-default"></a>既定値を使用する

既定では、Windows OS VM イメージは次の 2 つのソースと w32time を同期するように構成されています。 

- NtpClient プロバイダー。time.windows.com から情報を取得します。
- VMICTimeSync サービス。VM にホスト時刻を知らせ、VM がメンテナンスのために一時停止した後、修正を行うために使用されます。 Azure ホストでは、Microsoft 所有の Stratum 1 デバイスを使用して、正確な時刻を保持します。

w32time では、階層レベル、ルート遅延、ルート分散、タイム オフセットという優先順位でタイム プロバイダーが選択されます。 ほとんどの場合、Azure VM 上の w32time では、両方のタイム ソースを比較するために行う評価のせいで、ホスト時間が優先されます。 

ドメイン参加コンピューターの場合、ドメイン自体により時刻同期階層が確立されますが、それでもフォレスト ルートではどこかから時刻を取得する必要があります。次の考慮事項が引き続き当てはまります。


### <a name="host-only"></a>ホストのみ 

time.windows.com はパブリック NTP サーバーであり、これと時刻を同期するには、インターネット経由でトラフィックを送信する必要があります。パケット遅延が変化することで、時刻同期の質にマイナスの影響が出ることがあります。ホストのみの同期に切り替えて time.windows.com を削除すると、時刻同期の結果が改善される場合があります。

既定の構成を使用して時刻同期の問題が発生する場合は、ホストのみの時刻同期に切り替えるのが妥当です。 ホストのみの同期を試してみて、VM の時刻同期が改善されるかどうかを確認してください。 

### <a name="external-time-server"></a>外部のタイム サーバー

特定の時刻同期の要件がある場合は、外部のタイム サーバーを使用するオプションもあります。 外部のタイム サーバーでは特定の時刻を指定できます。これは、Microsoft 以外のデータセンターでホストされているコンピューターとの時間の統一性を確保する、あるいは特別な方法でうるう秒を処理する、テスト シナリオで役立つ場合があります。

外部のサーバーと VMICTimeSync サービスおよび VMICTimeProvider を組み合わせることで、既定の構成と同じような結果が得られます。 

## <a name="check-your-configuration"></a>構成を確認する


明示的な NTP サーバー (NTP) またはドメイン時刻同期 (NT5DS) を使用するように NtpClient タイム プロバイダーが構成されているかどうかを確認します。

```
w32tm /dumpreg /subkey:Parameters | findstr /i "type"
```

VM で NTP が使用されている場合、次の出力が表示されます。

```
Value Name                 Value Type          Value Data
Type                       REG_SZ              NTP
```

NtpClient タイム プロバイダーで使用されているタイム サーバーを確認するには、管理者特権でコマンド プロンプトに次のように入力します。

```
w32tm /dumpreg /subkey:Parameters | findstr /i "ntpserver"
```

VM で既定値が使用されている場合、出力は次のようになります。

```
NtpServer                  REG_SZ              time.windows.com,0x8
```


現在、どのタイム プロバイダーが使用されているのかを確認するには次のように入力します。

```
w32tm /query /source
```


次のような意味を持つ項目が出力されます。
    
- **time.windows.com** - 既定の構成では、w32time は time.windows.com から時刻を取得します。 時刻同期の質はインターネットの接続性に左右され、パケット遅延の影響を受けます。 これは、物理マシン上で取得する通常の出力です。
- **VM IC Time Synchronization Provider** - VM はホストの時刻と同期しています。 これは、Azure で実行されている仮想マシン上で取得する通常の出力です。 
- *お使いのドメイン サーバー* - 現在のコンピューターがあるドメインに入っており、そのドメインによって時刻同期階層が定義されます。
- *その他のサーバー* - 別のサーバーから時刻を取得するように w32time が明示的に構成されました。 時間同期の質はこのタイム サーバーの質に左右されます。
- **Local CMOS Clock** - 時計は同期していません。 再起動後、w32time に十分な起動時間が与えられなかった場合や、構成した時刻ソースの一部が利用できないとき、これが出力されることがあります。


## <a name="opt-in-for-host-only-time-sync"></a>ホストのみの時刻同期を選択する

Azure では、ホストの時刻同期を改善するべく継続的に取り組んでおり、時刻同期インフラストラクチャはすべて、Microsoft が所有するデータセンターに必ず併置されます。 第一時刻ソースとして time.windows.com を選択する既定の設定では時刻同期に問題がある場合、次のコマンドでホストのみの時刻同期を選択できます。

VMIC プロバイダーを有効にします。 

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\TimeProviders\VMICTimeProvider /v Enabled /t REG_DWORD /d 1 /f
```

NTPClient プロバイダーを無効にします。

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\TimeProviders\NtpClient /v Enabled /t REG_DWORD /d 0 /f
```

w32time サービスを再起動します。

```
net stop w32time && net start w32time
```


## <a name="windows-server-2012-and-r2-vms"></a>Windows Server 2012 と R2 VM 

Windows Server 2012 と Windows Server 2012 R2 では、時刻同期の既定の設定が異なります。既定の w32time は、正確な時刻よりサービスにかかる間接費が抑えられるように構成されています。 

正確な時刻を選択する新しい既定値を利用するように Windows Server 2012 と 2012 R2 デプロイを移動する場合、次の設定を適用できます。

Windows Server 2016 の設定に合わせ、w32time のポーリングとアップデートの間隔を更新します。

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v MinPollInterval /t REG_DWORD /d 6 /f
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v MaxPollInterval /t REG_DWORD /d 10 /f
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v UpdateInterval /t REG_DWORD /d 100 /f
w32tm /config /update
```

w32time で新しいポーリング間隔を使用するには、それを使用するように NtpServers を設定する必要があります。 サーバーに 0x1 ビットフラグ マスクが付いている場合、このメカニズムがオーバーライドされ、w32time で代わりに SpecialPollInterval が使用されることがあります。 指定した NTP サーバーで 0x8 フラグが使用されているか、フラグが何も設定されていないことを確認してください。

使用している NTP サーバーでどのようなフラグが設定されているのかを確認してください。

```
w32tm /dumpreg /subkey:Parameters | findstr /i "ntpserver"
```

## <a name="next-steps"></a>次のステップ

以下は、時刻同期に関する詳細のリンクです。

- [Windows タイム サービスのツールと設定](/windows-server/networking/windows-time-service/windows-time-service-tools-and-settings)
- [Windows Server 2016 の機能強化](/windows-server/networking/windows-time-service/windows-server-2016-improvements)
- [Windows Server 2016 の正確な時刻](/windows-server/networking/windows-time-service/accurate-time)
- [Windows タイム サービスで精度の高い環境を構成するためのサポート範囲](/windows-server/networking/windows-time-service/support-boundary)
