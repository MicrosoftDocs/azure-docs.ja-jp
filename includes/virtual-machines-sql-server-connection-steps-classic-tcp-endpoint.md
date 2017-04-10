### <a name="create-a-tcp-endpoint-for-the-virtual-machine"></a>仮想マシン用の TCP エンドポイントを作成する
インターネットから SQL Server にアクセスするには、仮想マシンに、着信する TCP 通信をリッスンするエンドポイントが必要です。 この Azure 構成手順により、仮想マシンからアクセスできる TCP ポートに、着信する TCP ポート トラフィックが送信されます。

> [!NOTE]
> 同じクラウド サービスまたは仮想ネットワーク内で接続する場合は、公開されたエンドポイントを作成する必要はありません。 その場合は、次のステップに進むことができます。 詳細については、 [Connection Scenarios (接続のシナリオ)](../articles/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-connect.md#connection-scenarios)を参照してください。
> 
> 

1. Azure ポータルで、 **[仮想マシン (クラシック)]**を選択します。
2. SQL Server 仮想マシンを選択します。
3. **[エンドポイント]** を選択し、**[エンドポイント]** ブレードの上部にある [追加] ボタンをクリックします。
   
    ![ポータルでのエンドポイント作成手順](./media/virtual-machines-sql-server-connection-steps/portal-endpoint-creation.png)
4. **[エンドポイントの追加]** ブレードの **[名前]** に、SQLEndpoint などの名前を入力します。
5. **[プロトコル]** で **[TCP]** を選択します。
6. **[パブリック ポート]** で、**57500** などのポート番号を指定します。
7. **[プライベート ポート]** で、SQL Server のリスニング ポートを指定します。既定値は **1433** です。
8. **[OK]** をクリックしてエンドポイントを作成します。



<!--HONumber=Jan17_HO2-->


