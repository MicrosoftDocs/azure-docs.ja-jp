## <a name="export-an-api-definition"></a>API 定義をエクスポートする
「[関数の OpenAPI 定義の作成](../articles/azure-functions/functions-openapi-definition.md)」で作成した関数の OpenAPI 定義があります。 このプロセスの次の手順では、API 定義をエクスポートして、PowerApps と Microsoft Flow がカスタム API でこの定義を使用できるようにします。

> [!IMPORTANT]
> PowerApps および Microsoft Flow テナントと同じ資格情報を使用して、Azure にサインインする必要があることに注意してください。 これにより、Azure がカスタム API を作成し、PowerApps と Microsoft Flow の両方でその API を使用できるように指定できます。

1. [Azure Portal](https://portal.azure.com) で、関数アプリ名 (**function-demo-energy** など) > **[プラットフォーム機能]** > **[API 定義]** の順にクリックします。

    ![[API の定義]](media/functions-export-api-definition/api-definition.png)

1. **[Export to PowerApps + Flow]\(PowerApps + Flow へのエクスポート\)** をクリックします。

    ![API 定義のソース](media/functions-export-api-definition/export-api-1.png)

1. 右側のウィンドウで、次の表で指定されている設定を使用します。

    |設定|Description|
    |--------|------------|
    |**エクスポート モード**|**[高速]** を選択すると、カスタム API が自動生成されます。 **[手動]** を選択すると、API 定義がエクスポートされますが、その定義は、PowerApps と Microsoft Flow に手動でインポートする必要があります。 詳細については、[PowerApps と Microsoft Flow へのエクスポート](../articles/azure-functions/app-service-export-api-to-powerapps-and-flow.md)に関するページを参照してください。|
    |**Environment**|カスタム API の保存先の環境を選択します。 詳しくは、「[環境の概要 (PowerApps)](https://powerapps.microsoft.com/tutorials/environments-overview/)」または[環境の概要 (Microsoft Flow)](https://us.flow.microsoft.com/documentation/environments-overview-admin/) に関する記事をご覧ください。|
    |**カスタム API 名**|名前を入力します (`Turbine Repair` など)。|
    |**API キー名**|カスタム API UI に表示されるアプリとフローのビルダーの名前を入力します。 役に立つ情報が例に含まれていることに注意してください。|
 
    ![PowerApps および Microsoft Flow にエクスポートする](media/functions-export-api-definition/export-api-2.png)

1. **[OK]**をクリックします。 カスタム API が作成され、指定した環境に追加されます。