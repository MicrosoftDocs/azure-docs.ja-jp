Android SDK の開発が継続中であるため、Android Studio にインストールされている Android SDK のバージョンが、コードのバージョンと一致しない可能性があります。 このチュートリアルで参照している Android SDK は、記事の執筆時点で最新のバージョンであるバージョン 23 です。 SDK の新しいリリースが登場するにつれてバージョン番号が大きくなる可能性があるので、入手可能な最新バージョンを使用することをお勧めします。

バージョンの不一致の場合に見られる現象は、次の&2; つです。

- プロジェクトをビルドまたは再ビルドすると、"**failed to find target Google Inc.:Google APIs:n (ターゲット Google Inc.:Google APIs:n が見つかりませんでした)**" などの Gradle エラー メッセージが表示される場合があります。
- `import` ステートメントに基づく解決が必要なコード内の標準の Android オブジェクトによって、エラー メッセージが生成される場合があります。

このいずれかが発生した場合、Android Studio にインストールされている Android SDK のバージョンが、ダウンロードしたプロジェクトの SDK ターゲットと一致していない可能性があります。 バージョンを確認するには、次の変更を加えます。

1. Android Studio で、**[Tools (ツール)]** > **[Android]** > **[SDK Manager (SDK マネージャー)]** の順にクリックします。 SDK プラットフォームの最新バージョンをまだインストールしていない場合は、これをクリックしてインストールします。 バージョン番号をメモしておきます。
2. **[Project Explorer (プロジェクト エクスプローラー)]** タブの **[Gradle Scripts (Gradle スクリプト)]** で、**build.gradle (modeule: app)** ファイルを開きます。 **[compileSdkVersion]** と **[buildToolsVersion]** が、インストールされた最新の SDK バージョンに設定されていることを確認します。 タグは、次のようになります。

             compileSdkVersion 'Google Inc.:Google APIs:23'
            buildToolsVersion "23.0.2"
3. Android Studio の Project Explorer でプロジェクト ノードを右クリックして **[Properties (プロパティ)]** を選択し、左の列から **[Android]** を選択します。 **[Project Build Target (プロジェクト ビルド ターゲット)]** が **[targetSdkVersion]** と同じ SDK バージョンに設定されていることを確認します。

Android Studio では Eclipse と異なり、ターゲットの SDK バージョンと最小の SDK バージョンを指定するためにマニフェスト ファイルを使用しません。


<!--HONumber=Dec16_HO2-->


