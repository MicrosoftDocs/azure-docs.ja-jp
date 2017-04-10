## <a name="delete-a-recovery-services-vault-powershell"></a>Recovery Services コンテナーを削除する (PowerShell)

1. Recovery Services コンテナーを取得する

        $vault = Get-AzureRmRecoveryServicesVault -Name "ContosoVault"

2. コンテナーを削除する

        Remove-AzureRmRecoveryServicesVault -Vault $vault

>[!WARNING]
>
> コンテナーを誤って削除すると、すべてのデータが失われるため、上記のコマンドを使用するときは細心の注意が必要です。 これは永続的な操作であり、元に戻す方法はありません。  


