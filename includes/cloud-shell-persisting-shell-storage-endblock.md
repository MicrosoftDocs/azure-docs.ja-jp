## <a name="transfer-local-files-to-cloud-shell"></a>Cloud Shell にローカル ファイルを転送する
`clouddrive` ディレクトリは、Azure Portal ストレージ ブレードと同期します。 このブレードを使用して、ファイル共有との間でローカル ファイルを移動できます。 Cloud Shell 内からファイルを更新すると、ブレードを更新するときにファイル ストレージ GUI に反映されます。

### <a name="download-files"></a>ファイルをダウンロードする

![ローカル ファイルの一覧](../articles/cloud-shell/media/persisting-shell-storage/download.png)
1. Azure Portal で、マウントされているファイル共有に移動します。
2. 対象のファイルを選択します。
3. **[ダウンロード]** ボタンを選択します。

### <a name="upload-files"></a>ファイルのアップロード

![更新されるローカル ファイル](../articles/cloud-shell/media/persisting-shell-storage/upload.png)
1. マウントされているファイル共有に移動します。
2. **[アップロード]** ボタンを選択します。
3. アップロードする 1 つまたは複数のファイルを選択します。
4. アップロードを確認します。

Cloud Shell の `clouddrive` ディレクトリでファイルにアクセスできるようになったことがわかります。