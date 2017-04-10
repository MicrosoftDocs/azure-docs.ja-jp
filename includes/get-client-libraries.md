### <a name="install-via-composer"></a>Composer 経由でインストールする
1. [Git をインストールします][install-git]。 Windows では、Git 実行可能ファイルも PATH 環境変数に追加する必要があります。 
2. プロジェクトのルートに **composer.json** という名前のファイルを作成して、次のコードを追加します。
   
    ```
    {
      "require": {
        "microsoft/windowsazure": "^0.4"
      }
    }
    ```
3. **[composer.phar][composer-phar]** をプロジェクトのルートにダウンロードします。
4. コマンド プロンプトを開き、次のコマンドをプロジェクトのルートで実行します。
   
    ```
    php composer.phar install
    ```

[php-sdk-github]: http://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[download-SDK-PHP]: ../articles/php-download-sdk.md
[composer-phar]: http://getcomposer.org/composer.phar


<!--HONumber=Nov16_HO3-->


