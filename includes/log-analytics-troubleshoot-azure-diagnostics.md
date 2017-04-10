### <a name="troubleshoot-azure-diagnostics"></a>Azure 診断のトラブルシューティング

次のエラー メッセージが表示される場合は、Microsoft.insights リソース プロバイダーが登録されていません。

`Failed to update diagnostics for 'resource'. {"code":"Forbidden","message":"Please register the subscription 'subscription id' with Microsoft.Insights."}`

リソース プロバイダーを登録するには、Azure Portal で次の手順を実行します。

1.    左側のナビゲーション ウィンドウで、*[サブスクリプション]* をクリックします。
2.    エラー メッセージで示されたサブスクリプションを選択します。
3.    *[リソース プロバイダー]* をクリックします。
4.    *Microsoft.insights* プロバイダーを探します。
5.    *[登録]* リンクをクリックします。

![microsoft.insights リソース プロバイダーの登録](./media/log-analytics-troubleshoot-azure-diagnostics/log-analytics-register-microsoft-diagnostics-resource-provider.png)

*Microsoft.insights* リソース プロバイダーが登録されたら、診断の構成を再試行してください。


PowerShell で次のエラー メッセージが表示された場合は、PowerShell のバージョンを更新する必要があります。

`Set-AzureRmDiagnosticSetting : A parameter cannot be found that matches parameter name 'WorkspaceId'.`

「[Azure PowerShell コマンドレットの使用開始](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/)」の記事に書かれている手順に従い、PowerShell のバージョンを November 2016 (v2.3.0) 以降のリリースに更新してください。
