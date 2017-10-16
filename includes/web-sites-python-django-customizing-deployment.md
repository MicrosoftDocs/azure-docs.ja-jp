Azure のアプリケーションで Python を使用することが決まります**2 つの条件に当てはまる場合**:

* ルート フォルダーに requirements.txt ファイル
* ルート フォルダー内の .py ファイルまたは runtime.txt python を指定します。

大文字と小文字であるなどの追加の Python 操作と同様に、ファイルの標準的な同期を実行する Python の特定の展開スクリプトを使用します。

* 仮想環境の自動管理
* Pip を使用して requirements.txt で表示されているパッケージのインストール
* 選択された Python のバージョンに基づく適切な web.config を作成します。
* Django アプリケーション用の静的ファイルを収集します。

スクリプトをカスタマイズすることがなく、既定の展開手順の特定の側面を制御できます。

すべての Python の特定の展開手順をスキップする場合は、この空のファイルを作成できます。

    \.skipPythonDeployment

Django アプリケーションの静的ファイルの収集をスキップする場合は。

    \.skipDjango 

配置をより細かく制御は、次のファイルを作成することで既定の配置スクリプトを上書きできます。

    \.deployment
    \deploy.cmd

使用することができます、 [Azure コマンド ライン インターフェイス][ Azure command-line interface]ファイルを作成します。  プロジェクト フォルダーからこのコマンドを使用します。

    azure site deploymentscript --python

これらのファイルが存在しない場合、Azure は一時展開スクリプトを作成し、それを実行します。  上記のコマンドで作成するパスワードと同じになります。

[Azure command-line interface]: http://azure.microsoft.com/downloads/
