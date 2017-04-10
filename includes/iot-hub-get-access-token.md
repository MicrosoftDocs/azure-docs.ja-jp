## <a name="obtain-an-azure-resource-manager-token"></a>Azure Resource Manager トークンの取得
Azure Active Directory では、Azure リソース マネージャーを使用してリソース上で実行するすべてのタスクを認証する必要があります。 ここに示す例では、パスワード認証が使用されます。その他の手法については、「[Authenticating Azure Resource Manager requests (Azure Resource Manager 要求の認証)][lnk-authenticate-arm]」を参照してください。

1. Program.cs の **Main** メソッドに次のコードを追加し、アプリケーション ID とパスワードを利用して Azure AD からトークンを取得します。
   
    ```
    var authContext = new AuthenticationContext(string.Format  
      ("https://login.windows.net/{0}", tenantId));
    var credential = new ClientCredential(applicationId, password);
    AuthenticationResult token = authContext.AcquireTokenAsync
      ("https://management.core.windows.net/", credential).Result;
   
    if (token == null)
    {
      Console.WriteLine("Failed to obtain the token");
      return;
    }
    ```
2. **Main** メソッドの終わりに次のコードを追加し、トークンを使用する **ResourceManagementClient** オブジェクトを作成します。
   
    ```
    var creds = new TokenCredentials(token.AccessToken);
    var client = new ResourceManagementClient(creds);
    client.SubscriptionId = subscriptionId;
    ```
3. 使用するリソース グループを作成するか、その参照を取得します。
   
    ```
    var rgResponse = client.ResourceGroups.CreateOrUpdate(rgName,
        new ResourceGroup("East US"));
    if (rgResponse.Properties.ProvisioningState != "Succeeded")
    {
      Console.WriteLine("Problem creating resource group");
      return;
    }
    ```

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx

<!--HONumber=Nov16_HO3-->


