プロセス サーバーの登録を解除する手順は、構成サーバーとの接続状態によって異なります。

### <a name="unregister-a-process-server-that-is-in-a-connected-state"></a>接続状態のプロセス サーバーの登録解除

1. 管理者としてプロセス サーバーにリモート接続します。
2. **[コントロール パネル]** を開き、**[プログラム]、[プログラムのアンインストール]** の順に開きます。
3. **Microsoft Azure Site Recovery Configuration/Process Server (Microsoft Azure Site Recovery 構成/プロセス サーバー)** という名前のプログラムをアンインストールします。
4. 手順 3. が完了すると、**Microsoft Azure Site Recovery Configuration/Process Server Dependencies (Microsoft Azure Site Recovery 構成/プロセス サーバーの依存関係)** をアンインストールできます。

### <a name="unregister-a-process-server-that-is-in-a-disconnected-state"></a>切断状態のプロセス サーバーの登録解除

> [!WARNING]
> プロセス サーバーがインストールされていた仮想マシンを回復する方法がない場合は、以下の手順を使用します。

1. 管理者として構成サーバーにログオンします。
2. 管理コマンド プロンプトを開き、`%ProgramData%\ASR\home\svsystems\bin` ディレクトリを参照します。
3. 次のコマンドを実行します。

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
4. システムからプロセス サーバーの詳細が消去されます。


<!--HONumber=Feb17_HO1-->


