
特定関数アプリのすべての関数のコードは、ホスト構成ファイルおよび 1 つまたは複数のサブフォルダーを含むルート フォルダーに格納され、次の例に示すように、各フォルダーには関数ごとのコードが含まれます。

```
wwwroot
 | - host.json
 | - mynodefunction
 | | - function.json
 | | - index.js
 | | - node_modules
 | | | - ... packages ...
 | | - package.json
 | - mycsharpfunction
 | | - function.json
 | | - run.csx
```

*host.json* ファイルにはランタイム固有の構成が含まれ、関数アプリのルート フォルダーに格納されています。 利用可能な設定に関する詳細については、WebJobs.Script リポジトリ wiki の [host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json) を参照してください。

各関数には、1 つまたは複数のコード ファイル、function.json 構成、およびその他の依存関係を含むフォルダーが割り当てられています。



<!--HONumber=Nov16_HO4-->


