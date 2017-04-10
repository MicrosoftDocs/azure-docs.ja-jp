1. 保護するサーバー上のローカル フォルダー (C:\Temp など) にインストーラーをコピーします。 コマンド プロンプトで管理者として次のコマンドを実行します。

  ```
  cd C:\Temp
  ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted.
  ```
2. モビリティ サービスをインストールするには、次のコマンドを実行します。

  ```
  UnifiedAgent.exe /Role "Agent" /CSEndpoint "IP address of the configuration server" /PassphraseFilePath <Full path to the passphrase file>``
  ```

#### <a name="mobility-service-installer-command-line-arguments"></a>モビリティ サービスのインストーラーのコマンド ライン引数

```
Usage :
UnifiedAgent.exe [/Role <Agent/MasterTarget>] [/InstallLocation <Installation directory>] [/CSIP <IP address>] [/PassphraseFilePath <Passphrase file path>] [/LogFilePath <Log file path>]<br/>
```

  | パラメーターが含まれる必要があります。|型|Description|指定できる値|
  |-|-|-|-|
  |/Role|必須|モビリティ サービスをインストールするかどうかの指定|エージェント </br> MasterTarget|
  |/InstallLocation|必須|モビリティ サービスをインストールする場所|コンピューター上の任意のフォルダー|
  |/CSIP|必須|構成サーバーの IP アドレス| 任意の有効な IP アドレス|
  |/PassphraseFilePath|必須|パスフレーズの場所 |任意の有効な UNC またはローカル ファイル パス|
  |/LogFilePath|省略可能。|インストール ログの場所|コンピューター上の任意の有効なフォルダー|

#### <a name="example"></a>例

```
  UnifiedAgent.exe /Role "Agent" /CSEndpoint "I192.168.2.35" /PassphraseFilePath "C:\Temp\MobSvc.passphrase"
```
