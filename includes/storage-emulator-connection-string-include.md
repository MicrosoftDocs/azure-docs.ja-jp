ストレージ エミュレーターでは、共有キー認証について、単一の固定アカウントと既知の認証キーがサポートされています。 このアカウントとキーのみ、ストレージ エミュレーターで共有キー資格情報として使用できます。 次に例を示します。

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```


> [!NOTE]
> ストレージ エミュレーターでサポートされている認証キーは、クライアント認証コードの機能をテストすることだけを目的としています。 セキュリティ機能は果たしません。 ストレージ エミュレーターで運用環境のストレージ アカウントとキーを使用することはできません。 開発アカウントは運用データで使用できないことにもご注意ください。
> 
> ストレージ エミュレーターでは HTTP 経由の接続のみがサポートされることに注意してください。 ただし、Azure 運用環境のストレージ アカウント内のリソースにアクセスする際は、HTTPS が推奨されるプロトコルです。
> 
> 

#### <a name="connect-to-the-emulator-account-using-a-shortcut"></a>ショートカットを使用してエミュレーター アカウントに接続する
アプリケーションからストレージ エミュレーターに接続する最も簡単な方法は、ショートカット `UseDevelopmentStorage=true`を参照するアプリケーションの構成ファイル内から接続文字列を構成することです。 たとえば、app.config ファイル内でのストレージ エミュレーターへの接続文字列は次のようになります。 

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

#### <a name="connect-to-the-emulator-account-using-the-well-known-account-name-and-key"></a>よく知られたアカウント名とキーを使用してエミュレーター アカウントに接続する
エミュレーターのアカウント名とキーを参照する接続文字列を作成するには、接続文字列内のエミュレーターから使用する各サービスのエンドポイントを指定する必要があることに注意してください。 これは、接続文字列が運用ストレージ アカウントに使用するものと異なるエミュレーター エンドポイントを参照するために必要です。 たとえば、接続文字列の値は次のようになります。

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
TableEndpoint=http://127.0.0.1:10002/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1; 
```

この値は、前述のショートカット `UseDevelopmentStorage=true`と同じです。

#### <a name="specify-an-http-proxy"></a>HTTP プロキシを指定する
また、サービスをストレージ エミュレーターでテストする際に使用する HTTP プロキシを指定することもできます。 これは、ストレージ サービスに対する操作をデバッグするとき、HTTP の要求と応答を調べる場合に便利です。 プロキシを指定するには、 `DevelopmentStorageProxyUri` オプションを接続文字列に追加し、値としてプロキシ URI を設定します。 たとえば、次に示す接続文字列は、ストレージ エミュレーターを指しており、HTTP プロキシを構成しています。

```
UseDevelopmentStorage=true;DevelopmentStorageProxyUri=http://myProxyUri
```



<!--HONumber=Nov16_HO3-->


