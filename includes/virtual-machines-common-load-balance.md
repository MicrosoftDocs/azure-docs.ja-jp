

Azure インフラストラクチャ サービスで利用できる負荷分散には 2 つのレベルがあります。

* **DNS レベル**: 異なるデータ センターにある異なるクラウド サービス、異なるデータ センターにある異なる Azure Web サイト、外部エンドポイントへのトラフィックの負荷分散です。 Azure Traffic Manager またはラウンド ロビンの負荷分散方式で実行されます。
* **ネットワーク レベル**: 同じクラウド サービスにある異なる仮想マシンへの着信インターネット トラフィックの負荷分散、または同じクラウド サービスや仮想ネットワーク内にある複数の仮想マシン間のトラフィックの負荷分散です。 Azure ロード バランサーによって実行されます。

## <a name="traffic-manager-load-balancing-for-cloud-services-and-websites"></a>Traffic Manager によるクラウド サービスおよび Web サイトの負荷分散
Traffic Manager を使用すると、ユーザーのトラフィックをエンドポイントまで制御できます。エンドポイントには、クラウド サービス、Web サイト、外部サイト、および他の Traffic Manager プロファイルが含まれます。 Traffic Manager は、インターネット リソースのドメイン名に関するドメイン ネーム システム (DNS) クエリに、インテリジェントなポリシー エンジンを適用することで機能します。 クラウド サービスや Web サイトは、世界中のさまざまなデータセンターで実行できます。

外部エンドポイントの構成または Traffic Manager プロファイルのエンドポイントとしての構成には、REST または Windows PowerShell のいずれかを使用する必要があります。

Traffic Manager は、トラフィックの分散に次の 3 つの異なる負荷分散方法を使用します。

* **フェールオーバー**: すべてのトラフィックにプライマリのエンドポイントを使用するが、プライマリが使用不可になった場合に備えてバックアップのエンドポイントを提供する場合に使用します。
* **パフォーマンス**: 地理的に異なる複数の場所にエンドポイントがあり、遅延が最小という意味で "最も近い" エンドポイントを使用するようにクライアントに要求する場合に使用します。
* **ラウンド ロビン **: 同じデータセンター内にある一連のクラウド サービス、または異なるデータセンター内にあるクラウド サービスや Web サイトの間で負荷を分散する場合に使用します。

詳細については、[Traffic Manager のトラフィック ルーティング方法](../articles/traffic-manager/traffic-manager-routing-methods.md)に関するページを参照してください。

次の図は、異なるクラウド サービス間でトラフィックを分散するラウンド ロビンの負荷分散方法の例を示します。

![負荷分散](./media/virtual-machines-common-load-balance/TMSummary.png)

基本的なプロセスは次のとおりです。

1. インターネット クライアントが、Web サービスに関してドメイン名を照会します。
2. DNS は、この名前照会要求を Traffic Manager に転送します。
3. Traffic Manager はラウンド ロビン リストにある次のクラウド サービスを選択し、DNS 名を返します。 インターネット クライアントの DNS サーバーは、その名前を IP アドレスに解決してインターネット クライアントに送信します。
4. インターネット クライアントは、Traffic Manager が選択したクラウド サービスに接続します。

詳細については、「 [Traffic Manager](../articles/traffic-manager/traffic-manager-overview.md)」を参照してください。

## <a name="azure-load-balancing-for-virtual-machines"></a>Azure の仮想マシンの負荷分散
同じクラウド サービスまたは仮想ネットワーク内の複数の仮想マシンは、プライベート IP アドレスを使用して直接やり取りすることができます。 クラウド サービスまたは仮想ネットワークの外側にあるコンピューターやサービスは、構成されたエンドポイント経由でのみ、クラウド サービスや仮想ネットワーク内の仮想マシンとやり取りすることができます。 エンドポイントとは、Azure クラウド サービス内の仮想マシンまたは Web ロールのパブリック IP アドレスとポートからプライベート IP アドレスとポートへのマッピングです。

Azure ロード バランサーは、特定の種類の着信トラフィックを、負荷分散セットと呼ばれる構成内の複数の仮想マシンまたはサービスにランダムに配分します。 たとえば、複数の Web サーバーまたは Web ロール間で Web 要求のトラフィックの負荷を分散できます。

次の図は、標準の (暗号化されていない) Web トラフィック用の負荷分散されたエンドポイントを示しています。このエンドポイントは、パブリックとプライベートの TCP ポートが 80 である 3 台の仮想マシン間で共有されています。 この 3 台の仮想マシンは、1 つの負荷分散セット内にあります。

![負荷分散](./media/virtual-machines-common-load-balance/LoadBalancing.png)

詳細については、「 [Azure ロード バランサー](../articles/load-balancer/load-balancer-overview.md)」を参照してください。 負荷分散セットの作成手順については、[負荷分散セットの構成](../articles/load-balancer/load-balancer-get-started-internet-arm-ps.md)に関するページを参照してください。

Azure はまた、クラウド サービスや仮想ネットワーク内で負荷を分散することもできます。 これは内部負荷分散と呼ばれ、次の方法で使用できます。

* 多層アプリケーションの異なる層にあるサーバー間で負荷分散する (たとえば、Web 層とデータベース層の間)。
* 負荷分散用のハードウェアやソフトウェアの追加を必要とせずに、Azure でホストされている基幹業務 (LOB) アプリケーションの負荷を分散する。
* オンプレミスのサーバーを含む一連のコンピューターの負荷を分散する。

Azure の負荷分散と同様、内部負荷分散セットを構成すると、内部負荷分散が容易になります。

次の図では、基幹業務アプリケーションの負荷をクロスプレミス仮想ネットワーク内の 3 つの仮想マシン間で分配する内部負荷分散エンドポイントの例を示します。

![負荷分散](./media/virtual-machines-common-load-balance/LOBServers.png)

## <a name="load-balancer-considerations"></a>Load Balancer に関する考慮事項
Load Balancer は、既定で 4 分間のアイドル状態のセッションをタイムアウトするように構成されています。 Load Balancer の背後にあるアプリケーションが 4 分以上接続をアイドル状態のままにし、キープアライブ構成がない場合、接続が切断されます。 [Azure Load Balancer のタイムアウト設定を長くする](../articles/load-balancer/load-balancer-tcp-idle-timeout.md)ように Load Balancer の動作を変更できます。

その他の考慮事項は、Azure Load Balancer でサポートされている分散モードの種類です。 ソース IP アフィニティ (ソース IP、接続先 IP) またはソース IP プロトコル (ソース IP、接続先 IP、およびプロトコル) を構成することができます。 詳細については、「 [Load Balancer 分散モード (ソース IP アフィニティ)](../articles/load-balancer/load-balancer-distribution-mode.md) 」を参照してください。

## <a name="next-steps"></a>次のステップ
負荷分散セットの作成手順については、[内部負荷分散セットの構成](../articles/load-balancer/load-balancer-get-started-ilb-arm-ps.md)に関するページを参照してください。

ロード バランサーの詳細については、「 [内部負荷分散](../articles/load-balancer/load-balancer-internal-overview.md)」を参照してください。



<!--HONumber=Nov16_HO3-->


