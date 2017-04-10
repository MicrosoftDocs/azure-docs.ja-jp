1. 保護するサーバー上のローカル フォルダー (/tmp など) にインストーラーをコピーします。 ターミナルから次のコマンドを実行します。
  ```
  cd /tmp
  tar -xvzf Microsoft-ASR_UA*release.tar.gz
  ```
2. モビリティ サービスをインストールするには、次のコマンドを実行します。

  ```
  sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P MobSvc.passphrase
  ```

#### <a name="mobility-service-installer-command-line-arguments"></a>モビリティ サービスのインストーラーのコマンド ライン引数

|パラメーターが含まれる必要があります。|型|Description|指定できる値|
|-|-|-|-|
|-t |必須|エージェントの種類<br>(将来のリリースで廃止予定)|*both*|
|-a |必須|エージェントの構成<br>(将来のリリースで廃止予定) |*host*|
|-R |省略可能。|エージェントの役割|エージェント<br>MasterTarget|
|-d |省略可能。|モビリティ サービスをインストールする場所|/usr/local/ASR|
|-i |必須|構成サーバーの IP アドレス|任意の有効な IP アドレス|
|-p |必須|構成サーバーが着信接続をリッスンするポート|443|
|-s |必須|インストールが正常に完了した後にサービスを開始します<br>(将来のリリースで廃止予定)|*y*|
|-c |必須|エージェントとプロセス サーバーの間の通信モード<br>(将来のリリースで廃止予定) |*https*|
|-P |必須|構成サーバーのパスフレーズ|任意の有効な UNC またはローカル ファイル パス|


#### <a name="example"></a>例
```
sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i 192.168.2.53 -p 443 -s y -c https -P /tmp/MobSvc.passphrase
```
