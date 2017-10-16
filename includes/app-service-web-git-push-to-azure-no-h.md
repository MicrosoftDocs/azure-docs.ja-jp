ローカル ターミナル ウィンドウで、ローカル Git リポジトリに Azure リモートを追加します。

```bash
git remote add azure <URI from previous step>
```

アプリをデプロイするために、次のコマンドで Azure リモートにプッシュします。 パスワードの入力を求められたら、Azure Portal へのログインに使用するパスワードではなく、「[デプロイ ユーザーの構成](#configure-a-deployment-user)」で作成したパスワードを入力するようにしてください。

```bash
git push azure master
```

上記のコマンドにより、次の例のような情報が表示されます。
