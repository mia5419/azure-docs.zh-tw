---
title: "在 Azure 資料處理站管線中使用自訂活動"
description: "了解如何建立自訂活動，並在 Azure 資料處理站管線中使用這些活動。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 8dd7ba14-15d2-4fd9-9ada-0b2c684327e9
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 691fd286fa41f26f192354574c460bd88d9c2bce
ms.openlocfilehash: 1222f736a71f38611ae72a2591bfdc893d91eb6e


---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>在 Azure 資料處理站管線中使用自訂活動

> [!div class="op_single_selector"]
> * [Hive](data-factory-hive-activity.md) 
> * [Pig](data-factory-pig-activity.md)
> * [MapReduce](data-factory-map-reduce.md)
> * [Hadoop 串流](data-factory-hadoop-streaming-activity.md)
> * [機器學習服務](data-factory-azure-ml-batch-execution-activity.md)
> * [預存程序](data-factory-stored-proc-activity.md)
> * [資料湖分析 U-SQL](data-factory-usql-activity.md)
> * [.NET 自訂](data-factory-use-custom-activities.md)
>


您可以在 Azure Data Factory 管線中使用兩種活動。

* [資料移動活動](data-factory-data-movement-activities.md)，可在[支援的資料存放區](data-factory-data-movement-activities.md#supported-data-stores-and-formats)之間移動資料。
* [資料轉換活動](data-factory-data-transformation-activities.md) 。 例如︰HDInsight Hive 和 Machine Learning Batch 執行。  

如果您需要以 Azure Data Factory 不支援的資料存放區為目標移動資料，可以利用自己的資料移動邏輯建立自訂 .NET 活動，然後在管線中使用活動。

同樣地，如果您需要以 Data Factory 不支援的方法轉換/處理資料，可以利用自己的資料處理邏輯建立自訂活動，然後在管線中使用活動。

> [!NOTE]
> 目前在 Data Factory 中，資料管理閘道器只支援複製活動和預存程序活動。 您不能使用自訂活動中的閘道器來存取內部部署資料來源。

您可以將自訂 .NET 活動設定為使用 **Azure Batch** 服務或 **Azure HDInsight** 叢集來執行。   

下列逐步解說提供建立自訂 .NET 活動以及在管線中使用自訂活動的逐步指示。 本逐步解說使用 **Azure Batch** 連結服務。 若要改用 Azure HDInsight 連結服務，您可以建立 **HDInsight** 類型 (如果您使用自己的 HDInsight 叢集) 或 **HDInsightOnDemand** 類型 (如果您想要讓 Data Factory 建立隨選 HDInsight 叢集) 的連結服務，再將它用於管線 JSON 的活動區段 (**linkedServiceName**)。 如需使用 Azure HDInsight 來執行自訂活動的詳細資料，請參閱 [使用 Azure HDInsight 連結服務](#use-azure-hdinsight-linked-services) 一節。

> [!IMPORTANT]
> 將 .NET Framework 4.5.2 版設定為 Visual Studio 中您 .NET 自訂活動專案的目標架構。 Data Factory 不支援針對 .NET Framework 4.5.2 版之後的版本編譯的自訂活動。   

## <a name="walkthrough"></a>逐步介紹
### <a name="prerequisites"></a>必要條件
* Visual Studio 2012/2013/2015
* 下載並安裝 [Azure .NET SDK][azure-developer-center]

### <a name="azure-batch-prerequisites"></a>Azure Batch 的必要條件
在逐步解說中，您會將 Azure Batch 當作計算資源使用來執行自訂 .NET 活動。 請參閱 [Azure Batch 基本知識][batch-technical-overview]，以取得 Azure Batch 服務的概觀，另請參閱[開始使用適用於 .NET 的 Azure Batch 程式庫][batch-get-started]，以快速開始使用 Azure Batch 服務。

在教學課程中，您需要建立含 VM 集區的 Azure Batch 帳戶。 步驟如下：

1. 使用 **Azure 入口網站** 建立 [Azure Batch 帳戶](http://manage.windowsazure.com)。 請參閱[建立和管理 Azure Batch 帳戶][batch-create-account]一文以取得指示。 記下 Azure Batch 帳戶名稱和帳戶金鑰。

    您也可以使用 [New-AzureBatchAccount][new-azure-batch-account] Cmdlet 建立 Azure Batch 帳戶。 如需使用此 Cmdlet 的詳細指示，請參閱[使用 Azure PowerShell 管理 Azure Batch 帳戶][azure-batch-blog]。
2. 建立 **Azure Batch 集區**。

   1. 在 [Azure 入口網站](https://portal.azure.com)中，按一下左側功能標中的 [瀏覽]，然後按一下 [Batch 帳戶]。
   2. 選取您的 Azure Batch 帳戶，以開啟 [Batch 帳戶]  刀鋒視窗。
   3. 按一下 [集區]  圖格。
   4. 在 [集區]  刀鋒視窗中，按一下工具列上的 [新增] 按鈕以新增集區。
      1. 輸入集區的識別碼 (**集區識別碼**)。 請注意 **集區的識別碼**；您在建立 Data Factory 解決方案時需要它。
      2. 指定作業系統系列設定的 **Windows Server 2012 R2** 。
      3. 選取 **節點定價層**。
      4. 輸入 **2** 做為 [目標專用] 設定的值。
      5. 輸入 **2** 做為 [每個節點的工作上限] 設定的值。
   5. 按一下 [確定]  以建立集區。

      您也可以使用 [New-AzureBatchPool](https://msdn.microsoft.com/library/mt628690.aspx) Cmdlet 建立 Azure Batch 集區。     

### <a name="high-level-steps"></a>高階步驟
1. **建立自訂活動** 以使用 Data Factory 管線。 此範例中的自訂活動包含資料轉換/處理邏輯。
   1. 在 Visual Studio 中建立 .NET 類別庫專案，加入程式碼以處理輸入資料，然後編譯專案。    
   2. 壓縮輸出資料夾中所有的二進位檔和 PDB (選擇性) 檔案。    
   3. 將 zip 檔案上傳至 Azure Blob 儲存體。 在建立自訂活動一節中有詳細的步驟。
2. **建立使用自訂活動的 Azure Data Factory**：
   1. 建立 Azure Data Factory。
   2. 建立連結的服務。
      1. AzureStorageLinkedService：提供用於存取 Blob 的儲存體認證。
      2. AzureBatchLinkedService：將 Azure Batch 指定為計算。
   3. 建立資料集。
      1. InputDataset：指定輸入 blob 的儲存體容器和資料夾。
      2. OuputDataset：指定輸出 blob 的儲存體容器和資料夾。
   4. 建立使用自訂活動的管線。
   5. 執行與測試管線。
   6. 偵錯管線。

## <a name="create-the-custom-activity"></a>建立自訂活動
若要建立 .NET 自訂活動，您必須利用實作 **IDotNetActivity** 介面的類別建立 **.NET 類別庫**專案。 這個介面只有一個方法： [Execute](https://msdn.microsoft.com/library/azure/mt603945.aspx) ，其簽章為：

```csharp
public IDictionary<string, string> Execute(
        IEnumerable<LinkedService> linkedServices,
        IEnumerable<Dataset> datasets,
        Activity activity,
        IActivityLogger logger)
```


此方法會採用四個參數：

* **linkedServices**。 此屬性是將輸入/輸出資料來源 (例如：Azure Blob 儲存體) 連結到 Data Factory 之連結服務的可列舉清單。 在此範例中，只有一個用於輸入和輸出的 Azure 儲存體類型連結服務。
* **datasets**。 此屬性是資料集的可列舉清單。 您可以使用這個參數取得輸入和輸出資料集定義的位置和結構描述。
* **activity**。 此屬性代表目前的計算實體 - 在此案例中為 Azure Batch。
* **logger**。 此物件可讓您撰寫會呈現為管線的「使用者」記錄檔的偵錯註解。

此方法會傳回未來可用來將自訂活動鏈結在一起的字典。 尚未實作這項功能，因此會從方法傳回空的字典。  

### <a name="procedure"></a>程序
1. 建立 **.NET 類別庫** 專案。
   <ol type="a">
     <li>啟動 <b>Visual Studio 2015</b> 或 <b>Visual Studio 2013</b> 或 <b>Visual Studio 2012</b>。</li>
     <li>按一下 [檔案]<b></b>，指向 [新增]<b></b>，然後按一下 [專案]<b></b>。</li>
     <li>展開 [範本]<b></b>，然後選取 [Visual C#]<b></b>。 在此逐步解說中，您使用 C# 中，但您可以使用任何 .NET 語言來開發自訂活動。</li>
     <li>從右邊的專案類型清單中選取 [類別庫]<b></b>。</li>
     <li>在 [名稱]<b></b> 輸入 <b>MyDotNetActivity</b>。</li>
     <li>在 [位置]<b></b> 選取 <b>C:\ADFGetStarted</b>。</li>
     <li>按一下 [確定] <b></b> 以建立專案。</li>
   </ol>
2. 按一下 [工具]****，指向 [NuGet 封裝管理員]****，然後按一下 [封裝管理員主控台]****。
3. 在 [封裝管理員主控台] 中，執行下列命令以匯入 **Microsoft.Azure.Management.DataFactories**。

    ```PowerShell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
4. 將 **Azure 儲存體** NuGet 封裝匯入專案中。

    ```PowerShell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    > [!NOTE]
    > Data Factory 服務啟動程式需要 4.3 版的 WindowsAzure.Storage。 如果您在自訂活動專案中新增新版的 Azure 儲存體組件的參考，當活動執行時會看到錯誤。 若要解決這個錯誤，請參閱[ Appdomain 隔離](#appdomain-isolation)小節。 
5. 將下列 **using** 陳述式加入至專案的原始程式檔。

    ```csharp
    using System.IO;
    using System.Globalization;
    using System.Diagnostics;
    using System.Linq;

    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Runtime;

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```
6. 將 **namespace** 的名稱變更為 **MyDotNetActivityNS**。

    ```csharp
    namespace MyDotNetActivityNS
    ```
7. 將類別的名稱變更為 **MyDotNetActivity**，並從 **IDotNetActivity** 介面衍生它，如下列程式碼片段所示：

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
8. 對 **MyDotNetActivity** 類別實作 (新增) **IDotNetActivity** 介面的 **Execute** 方法，並將下列範例程式碼複製到方法。

    下列範例會在每個與資料配量相關聯之 Blob 中計算搜尋詞彙 ("Microsoft") 的出現次數。

    ```csharp
    /// <summary>
    /// Execute method is the only method of IDotNetActivity interface you must implement.
    /// In this sample, the method invokes the Calculate method to perform the core logic.  
    /// </summary>
    
    public IDictionary<string, string> Execute(
        IEnumerable<LinkedService> linkedServices,
        IEnumerable<Dataset> datasets,
        Activity activity,
        IActivityLogger logger)
    {
        // to get extended properties (for example: SliceStart)
        DotNetActivity dotNetActivity = (DotNetActivity)activity.TypeProperties;
        string sliceStartString = dotNetActivity.ExtendedProperties["SliceStart"];
    
        // to log all extended properties            
        IDictionary<string, string> extendedProperties = dotNetActivity.ExtendedProperties;
        logger.Write("Logging extended properties if any...");
        foreach (KeyValuePair<string, string> entry in extendedProperties)
        {
            logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
        }
    
        // declare types for input and output data stores
        AzureStorageLinkedService inputLinkedService;
    
        // declare dataset types
        CustomDataset inputLocation;
        AzureBlobDataset outputLocation;
    
        Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);
        inputLocation = inputDataset.Properties.TypeProperties as CustomDataset;
    
        foreach (LinkedService ls in linkedServices)
            logger.Write("linkedService.Name {0}", ls.Name);
    
        // using First method instead of Single since we are using the same
        // Azure Storage linked service for input and output.
        inputLinkedService = linkedServices.First(
            linkedService =>
            linkedService.Name ==
            inputDataset.Properties.LinkedServiceName).Properties.TypeProperties
            as AzureStorageLinkedService;
    
        string connectionString = inputLinkedService.ConnectionString;
    
        // To create an input storage client.
        string folderPath = GetFolderPath(inputDataset);
        string output = string.Empty; // for use later.
    
        // create storage client for input. Pass the connection string.
        CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
        CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();
    
        // initialize the continuation token before using it in the do-while loop.
        BlobContinuationToken continuationToken = null;
        do
        {   // get the list of input blobs from the input storage client object.
            BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
                                     true,
                                     BlobListingDetails.Metadata,
                                     null,
                                     continuationToken,
                                     null,
                                     null);
    
            // Calculate method returns the number of occurrences of
            // the search term (“Microsoft”) in each blob associated
               // with the data slice. definition of the method is shown in the next step.
    
            output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");
    
        } while (continuationToken != null);
    
        // get the output dataset using the name of the dataset matched to a name in the Activity output collection.
        Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
        // convert to blob location object.
        outputLocation = outputDataset.Properties.TypeProperties as AzureBlobDataset;
    
        folderPath = GetFolderPath(outputDataset);
    
        logger.Write("Writing blob to the folder: {0}", folderPath);
    
        // create a storage object for the output blob.
        CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
        // write the name of the file.
        Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    
        logger.Write("output blob URI: {0}", outputBlobUri.ToString());
        // create a blob and upload the output text.
        CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
        logger.Write("Writing {0} to the output blob", output);
        outputBlob.UploadText(output);
    
        // The dictionary can be used to chain custom activities together in the future.
        // This feature is not implemented yet, so just return an empty dictionary.  
    
        return new Dictionary<string, string>();
    }
    ```
9. 新增下列 Helper 方法。 **Execute** 方法會叫用這些 Helper 方法。 **GetConnectionString** 方法會擷取 Azure 儲存體連接字串，**GetFolderPath** 方法會擷取 Blob 位置。 最重要的是， **Calculate** 方法會隔離逐一查看每個 blob 的程式碼。

    ```csharp
    /// <summary>
    /// Gets the folderPath value from the input/output dataset.
    /// </summary>
    
    private static string GetFolderPath(Dataset dataArtifact)
    {
        if (dataArtifact == null || dataArtifact.Properties == null)
        {
            return null;
        }
    
        AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
        if (blobDataset == null)
        {
            return null;
        }
    
        return blobDataset.FolderPath;
    }
    
    /// <summary>
    /// Gets the fileName value from the input/output dataset.   
    /// </summary>
    
    private static string GetFileName(Dataset dataArtifact)
    {
        if (dataArtifact == null || dataArtifact.Properties == null)
        {
            return null;
        }
    
        AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
        if (blobDataset == null)
        {
            return null;
        }
    
        return blobDataset.FileName;
    }
    
    /// <summary>
    /// Iterates through each blob (file) in the folder, counts the number of instances of search term in the file,
    /// and prepares the output text that is written to the output blob.
    /// </summary>
    
    public static string Calculate(BlobResultSegment Bresult, IActivityLogger logger, string folderPath, ref BlobContinuationToken token, string searchTerm)
    {
        string output = string.Empty;
        logger.Write("number of blobs found: {0}", Bresult.Results.Count<IListBlobItem>());
        foreach (IListBlobItem listBlobItem in Bresult.Results)
        {
            CloudBlockBlob inputBlob = listBlobItem as CloudBlockBlob;
            if ((inputBlob != null) && (inputBlob.Name.IndexOf("$$$.$$$") == -1))
            {
                string blobText = inputBlob.DownloadText(Encoding.ASCII, null, null, null);
                logger.Write("input blob text: {0}", blobText);
                string[] source = blobText.Split(new char[] { '.', '?', '!', ' ', ';', ':', ',' }, StringSplitOptions.RemoveEmptyEntries);
                var matchQuery = from word in source
                                 where word.ToLowerInvariant() == searchTerm.ToLowerInvariant()
                                 select word;
                int wordCount = matchQuery.Count();
                output += string.Format("{0} occurrences(s) of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
            }
        }
        return output;
    }
    ```

    GetFolderPath 方法會將路徑傳回資料集所指向的資料夾，而 GetFileName 方法會傳回資料集指向的 blob/檔案名稱。 如果您的 havefolderPath 定義使用如 {Year}、{Month}、{Day} 等變數，方法會以未將變數取代為執行階段值的形式傳回字串。 如需存取 SliceStart、SliceEnd 等的詳細資料，請參閱 [存取延伸屬性](#access-extended-properties) 一節。    

    ```JSON
    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "adftutorial/inputfolder/",
    ```

    Calculate 方法會在輸入檔案 (資料夾中的 blob) 計算關鍵字 Microsoft 的執行個體數目。 搜尋詞彙 ("Microsoft") 已在程式碼中硬式編碼。
10. 編譯專案。 按一下功能表中的 [建置]，然後按一下 [建置方案]。

    > [!IMPORTANT]
    > 將 .NET Framework 4.5.2 版設定為您專案的目標架構：在專案上按一下滑鼠右鍵，然後按一下 [屬性] 來設定目標架構。 Data Factory 不支援針對 .NET Framework 4.5.2 版之後的版本編譯的自訂活動。

11. 啟動 [Windows 檔案總管]，瀏覽至 **bin\debug** 或 **bin\release** 資料夾，視建置類型而定。
12. 建立 zip 檔案 **MyDotNetActivity.zip**，檔案中包含 <project folder>\bin\Debug 資料夾中的所有二進位檔。 您可能會想新增 **MyDotNetActivity.pdb** 檔案，讓您可以取得額外的詳細資訊，例如如果有失敗時，原始程式碼中引起問題的程式碼行號。 自訂活動之 zip 檔案中的所有檔案都必須位於 **最上層** 且不包含任何子資料夾。

    ![二進位輸出檔案](./media/data-factory-use-custom-activities/Binaries.png)
13. 將 **MyDotNetActivity.zip** 當做 Blob 上傳至 Blob 容器：Azure Blob 儲存體中的 **customactivitycontainer**，由 **ADFTutorialDataFactory** 中的 **AzureStorageLinkedService** 連結服務使用。  如果 Blob 容器 **customactivitycontainer** 不存在，請自行建立。

> [!NOTE]
> 如果您將這個 .NET 活動專案加入 Visual Studio 中包含 Data Factory 專案的方案，並從 Data Factory 應用程式專案加入 .NET 活動的參考，您就不需要執行最後兩個步驟，也就是建立 zip 檔案，和手動上傳到 Azure Blob 儲存體。 當您使用 Visual Studio 發佈 Data Factory 實體時，發佈程序會自動完成這些步驟。 請參閱[使用 Visual Studio 建置您的第一個管線](data-factory-build-your-first-pipeline-using-vs.md)和[從 Azure Blob 複製資料到 Azure SQL](data-factory-copy-activity-tutorial-using-visual-studio.md)一文，以了解如何使用 Visual Studio 建立並發佈 Data Factory 實體。  
>
>

### <a name="execute-method"></a>執行方法
本節提供 **Execute** 方法中與程式碼相關的詳細資料和注意事項。

1. 逐一查看輸入集合的成員可在 [Microsoft.WindowsAzure.Storage.Blob](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.aspx) 命名空間中找到。 逐一查看 blob 集合需要使用 **BlobContinuationToken** 類別。 基本上，您必須搭配使用 do-while 迴圈和權杖做為結束迴圈的機制。 如需詳細資訊，請參閱 [如何從 .NET 使用 Blob 儲存體](../storage/storage-dotnet-how-to-use-blobs.md)。 基本迴圈如下所示：

    ```csharp
    // Initialize the continuation token.
    BlobContinuationToken continuationToken = null;
    do
    {   
        // Get the list of input blobs from the input storage client object.
        BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
            true,
            BlobListingDetails.Metadata,
            null,
            continuationToken,
            null,
            null);
        // Return a string derived from parsing each blob.
        output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");
    } while (continuationToken != null);
    ```

    請參閱 [ListBlobsSegmented](https://msdn.microsoft.com/library/jj717596.aspx) 方法的文件以了解詳細資料。
2. 以邏輯方式逐一查看 blob 集的程式碼會在 do-while 迴圈中執行。 在 **Execute** 方法中，執行 do-while 迴圈會將 blob 清單傳遞至名為 **Calculate** 的方法。 此方法會傳回名為 **output** 的字串變數，也就是在區段中逐一查看所有 blob 的結果。

   它會在傳遞給 **Calculate** 方法的 blob 中，傳回搜尋詞彙 (**Microsoft**) 的出現次數。

    ```csharp
    output += string.Format("{0} occurrences of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
    ```
3. 一旦 **Calculate** 方法完成此工作，它必須寫入至新的 blob。 因此對於每個處理過的 blob 集，都可以利用結果撰寫新的 blob。 若要寫入新的 blob，請先找到輸出資料集。
    
    ```csharp
    // Get the output dataset using the name of the dataset matched to a name in the Activity output collection.
    Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
    
    // Convert to blob location object.
    outputLocation = outputDataset.Properties.TypeProperties as AzureBlobDataset;
    ```
4. 程式碼也會呼叫 helper 方法： **GetFolderPath** 來擷取資料夾路徑 (儲存體容器名稱)。

    ```csharp
    folderPath = GetFolderPath(outputDataset);
    ```

   **GetFolderPath** 會將 DataSet 物件轉換成 AzureBlobDataSet，其具有一個名為 FolderPath 的屬性。

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
    
    return blobDataset.FolderPath;
    ```
5. 程式碼會呼叫 **GetFileName** 方法來擷取檔案名稱 (blob 名稱)。  

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
    
    return blobDataset.FileName;
    ```
6. 藉由建立 URI 物件寫入檔案的名稱。 URI 建構函式使用 **BlobEndpoint** 屬性傳回容器名稱。 新增資料夾路徑和檔案名稱以建構輸出 blob URI。  

    ```csharp
    // Write the name of the file.
    Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    ```
7. 已寫入檔案名稱，現在您可以從 Calculate 方法將輸出字串寫入新的 blob：

    ```csharp
    // Create a blob and upload the output text.
    CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
    logger.Write("Writing {0} to the output blob", output);
    outputBlob.UploadText(output);
    ```

## <a name="create-the-data-factory-using-azure-portal"></a>使用 Azure 入口網站建立 Data Factory
在 [建立自訂活動]  區段中，您建立自訂活動，並將包含二進位檔和 PDB 檔案的 zip 檔案上傳到 Azure blob 容器。 在本節中，您將透過使用**自訂活動**的**管線**建立 Azure **Data Factory**。

自訂活動的輸入資料集代表 blob 儲存體中輸入資料夾 (adftutorial\inputfolder) 的 blob (檔案)。 活動的輸出資料集代表 blob 儲存體中輸出資料夾 (adftutorial\outputfolder) 的輸出 blob。

利用下列內容建立名為 **file.txt** 的檔案並將它上傳至 **adftutorial\inputfolder** (adftutorial 是 Azure blob 容器的名稱而 inputfolder 是該容器中的資料夾名稱)。

```
test custom activity Microsoft test custom activity Microsoft
```

即使資料夾有 2 個以上的檔案，輸入資料夾還是會對應至 Azure Data Factory 中的配量。 管線處理每個配量時，自訂活動會為該配量逐一查看輸入資料夾中的所有 blob。

您會看到 adftutorial\output 資料夾中的一個輸出檔案具有 1 行或更多行 (和輸入資料夾中的 blob 數目相同)：

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
```


以下是您會在此節中執行的步驟：

1. 建立 **Data Factory**。
2. **連結服務** 。
3. 代表自訂活動之輸入和輸出的輸入和輸出 **資料集** 。
4. **管線** 。
5. **Data Factory**。 將這些實體發佈至 Azure 時，您會建立一個。

> [!NOTE]
> 建立 **file.txt** 並上傳到 Blob 容器 (如果您尚未完成)。 請參閱前文中的指示。  
>
>

### <a name="step-1-create-the-data-factory"></a>步驟 1：建立 Data Factory
1. 登入 Azure 入口網站之後，執行下列步驟：
   1. 按一下左側功能表上的 [新增]  。
   2. 按一下 [新增] 刀鋒視窗中的 [資料 + 分析]。
   3. 按一下 [資料分析] 刀鋒視窗上的 [Data Factory]。
2. 在 [新增 Data Factory] 刀鋒視窗中，輸入 **CustomActivityFactor** 做為 [名稱]。 Azure Data Factory 的名稱在全域必須是唯一的。 如果您收到錯誤：**Data Factory 名稱 “CustomActivityFactory” 無法使用**，請變更 Data Factory 名稱 (例如 **yournameCustomActivityFactory**)，然後試著重新建立。
3. 按一下 [資源群組名稱] ，並選取現有的資源群組，或建立一個群組。
4. 請確認您使用的是要在其中建立 Data Factory 的正確**訂用帳戶**和**區域**。
5. 按一下 [新增 Data Factory] 刀鋒視窗上的 [建立]。
6. 您會看到 Data Factory 建立在 Azure 入口網站的 [儀表板]  中。
7. 在 Data Factory 成功建立後，您會看到 Data Factory 刀鋒視窗，顯示 Data Factory 的內容。

### <a name="step-2-create-linked-services"></a>步驟 2：建立連結服務
連結服務會將資料存放區或計算服務連結至 Azure Data Factory。 在此步驟中，您會將 Azure 儲存體帳戶和 Azure Batch 帳戶連結到 Data Factory。

#### <a name="create-azure-storage-linked-service"></a>建立 Azure 儲存體連結服務
1. 按一下 **CustomActivityFactory** 的 [DATA FACTORY] 刀鋒視窗上的 [作者和部署] 圖格。 您會看到 [Data Factory 編輯器]。
2. 在命令列上按一下 [新增資料儲存區]，然後選擇 [Azure 儲存體]。 在編輯器中，您應該會看到用來建立 Azure 儲存體連結服務的 JSON 指令碼。

3. 以您的 Azure 儲存體帳戶名稱取代**帳戶名稱**，並以 Azure 儲存體帳戶的存取金鑰取代**帳戶金鑰**。 若要了解如何取得儲存體存取金鑰，請參閱 [檢視、複製和重新產生儲存體存取金鑰](../storage/storage-create-storage-account.md#manage-your-storage-account)

4. 按一下命令列的 [部署]  ，部署連結服務。

#### <a name="create-azure-batch-linked-service"></a>建立 Azure Batch 連結服務
1. 在 [Data Factory 編輯器] 中，從命令列按一下 [新增計算]，然後從功能表選取 [Azure Batch]。
2. 對 JSON 指令碼進行下列變更：

   1. 指定 **accountName** 屬性的 Azure Batch 帳戶名稱。 [Azure Batch 帳戶刀鋒視窗] 中的 **URL** 格式如下：http://**accountname**.region.batch.azure.com。 針對 JSON 中的 **batchUri** 屬性，您必須從該 URL **移除 "accountname"** 並在 **accountName** JSON 屬性使用該**accountname**。
   2. 指定 **accessKey** 屬性的 Azure Batch 帳戶金鑰 。
   3. 針對為滿足 **poolName** 屬性之必要條件而建立的集區指定名稱。 您也可以指定該集區的 ID，而非集區名稱。
   4. 指定 **batchUri** 屬性的 Azure Batch URI。 請參閱 **accountName** 屬性的上述注意事項。 範例︰https://westus.batch.azure.com。  
   5. 指定 **AzureStorageLinkedService** for the **linkedServiceName** 屬性的 Azure Batch 帳戶名稱。

        ```JSON
        {
         "name": "AzureBatchLinkedService",
         "properties": {
           "type": "AzureBatch",
           "typeProperties": {
             "accountName": "myazurebatchaccount",
             "batchUri": "https://westus.batch.azure.com",
             "accessKey": "<yourbatchaccountkey>",
             "poolName": "myazurebatchpool",
             "linkedServiceName": "AzureStorageLinkedService"
           }
         }
        }
        ```

       針對 **poolName** 屬性，您也可以指定該集區的 ID，而非集區名稱。

      > [!NOTE]
      > 與支援 HDInsight 的情況不同，Data Factory 服務不支援 Azure Batch 的隨選選項。 您只能使用 Azure Data Factory 中自己的 Azure Batch 集區。
      >
      >

### <a name="step-3-create-datasets"></a>步驟 3：建立資料集
在此步驟中，您會建立資料集來代表輸入和輸出資料。

#### <a name="create-input-dataset"></a>建立輸入資料集
1. 在 Data Factory 的 [編輯器] 中，按一下工具列上的 [新增資料集] 按鈕，然後從下拉式功能表中選取 [Azure Blob 儲存體]。
2. 使用下列 JSON 程式碼片段取代右窗格中的 JSON：

    ```JSON
    {
     "name": "InputDataset",
     "properties": {
         "type": "AzureBlob",
         "linkedServiceName": "AzureStorageLinkedService",
         "typeProperties": {
             "folderPath": "adftutorial/customactivityinput/",
             "format": {
                 "type": "TextFormat"
             }
         },
         "availability": {
             "frequency": "Hour",
             "interval": 1
         },
         "external": true,
         "policy": {}
     }
    }
    ```

   您稍後會在本逐步解說建立管線，開始時間為：2015-11-16T00:00:00Z，而結束時間為：2015-11-16T05:00:00Z。 排程為每小時產生，因此會有 5 個輸入/輸出配量 (在 **00**:00:00 -> **05**:00:00 之間)。

   輸入資料集的 **frequency** 和 **interval** 設定為 **Hour** 和 **1**，這表示每小時皆可使用輸入配量。 在此範例中，它是 intputfolder 中的相同檔案 (file.txt)。

   以下是每個配量的開始時間，由上述 JSON 程式碼片段中的 SliceStart 系統變數代表。
3. 按一下工具列的 [部署]，建立並部署 **InputDataset**。 確認您在編輯器的標題列看到  [已成功建立資料表] 訊息。

#### <a name="create-an-output-dataset"></a>建立輸出資料表
1. 在 [Data Factory 編輯器] 中，按一下 [新增資料集]，然後從命令列按一下 [Azure Blob 儲存體]。
2. 使用下列 JSON 指令碼取代右窗格中的 JSON 指令碼：

    ```JSON
    {
        "name": "OutputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "{slice}.txt",
                "folderPath": "adftutorial/customactivityoutput/",
                "partitionedBy": [
                    {
                        "name": "slice",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "yyyy-MM-dd-HH"
                        }
                    }
                ]
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```

     輸出位置是 **adftutorial/customactivityoutput/** ，而輸出檔案名稱是 yyyy-MM-dd-HH.txt ，其中 yyyy-MM-dd-HH 是產生配量的年、月、日、時。 如需詳細資訊，請參閱[開發人員參考資料][adf-developer-reference]。

    為每個輸入配量產生輸出 blob/檔案。 以下是為每個配量命名輸出檔案的方式。 所有的輸出檔案會產生於一個輸出資料夾：**adftutorial\customactivityoutput**。

   | 配量 | 開始時間 | 輸出檔案 |
   |:--- |:--- |:--- |
   | 1 |2015-11-16T00:00:00 |2015-11-16-00.txt |
   | 2 |2015-11-16T01:00:00 |2015-11-16-01.txt |
   | 3 |2015-11-16T02:00:00 |2015-11-16-02.txt |
   | 4 |2015-11-16T03:00:00 |2015-11-16-03.txt |
   | 5 |2015-11-16T04:00:00 |2015-11-16-04.txt |

    請記得輸入資料夾中的所有檔案都是包含上述開始時間之配量的一部分。 處理此配量時，自訂活動會掃描每個檔案，並利用搜尋詞彙 (“Microsoft”) 的出現次數在輸出檔案中產生資料行。 如果 inputfolder 中有三個檔案，每小時的配量會有三行在輸出檔案中：2015-11-16-00.txt、2015-11-16:01:00:00.txt 等等。
3. 按一下命令列上的 [部署] 來部署 **OutputDataset**。

### <a name="create-and-run-a-pipeline-that-uses-the-custom-activity"></a>建立並執行使用自訂活動的管線
1. 在 Data Factory 編輯器中，按一下工具列上的 [ **新增管線** ]。 如果看不到此命令，請按一下 [...] (省略符號) 就可看到。
2. 使用下列 JSON 指令碼取代右窗格中的 JSON。

    ```JSON
    {
      "name": "ADFTutorialPipelineCustom",
      "properties": {
        "description": "Use custom activity",
        "activities": [
          {
            "Name": "MyDotNetActivity",
            "Type": "DotNetActivity",
            "Inputs": [
              {
                "Name": "InputDataset"
              }
            ],
            "Outputs": [
              {
                "Name": "OutputDataset"
              }
            ],
            "LinkedServiceName": "AzureBatchLinkedService",
            "typeProperties": {
              "AssemblyName": "MyDotNetActivity.dll",
              "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
              "PackageLinkedService": "AzureStorageLinkedService",
              "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
              "extendedProperties": {
                "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
              }
            },
            "Policy": {
              "Concurrency": 2,
              "ExecutionPriorityOrder": "OldestFirst",
              "Retry": 3,
              "Timeout": "00:30:00",
              "Delay": "00:00:00"
            }
          }
        ],
        "start": "2015-11-16T00:00:00Z",
        "end": "2015-11-16T05:00:00Z",
        "isPaused": false
      }
    }
    ```

    請注意下列幾點：

   * **Concurrency** 設定為 **2**，因此 Azure Batch 集區中會有 2 部 VM 以平行方式處理 2 個配量。
   * activities 區段中有一個活動，它的類型是： **DotNetActivity**。
   * **AssemblyName** 設定為 DLL 的名稱：**MyDotnetActivity.dll**。
   * **EntryPoint** 設定為 **MyDotNetActivityNS.MyDotNetActivity**。
   * **PackageLinkedService** 設定為 **AzureStorageLinkedService**，它會指向包含自訂活動 zip 檔案的 Blob 儲存體。 如果您將不同的 Azure 儲存體帳戶用於輸入/輸出檔案和自訂活動 zip 檔案，您可以建立另一個 Azure 儲存體連結服務。 本文假設您使用相同的 Azure 儲存體帳戶。
   * **PackageFile** 設定為 **customactivitycontainer/MyDotNetActivity.zip**。 其格式為：containerforthezip/nameofthezip.zip。
   * 自訂活動會採用 **InputDataset** 做為輸入和 **OutputDataset** 做為輸出。
   * 自訂活動的 linkedServiceName 屬性會指向 **AzureBatchLinkedService**，這會告知 Azure Data Factory 自訂活動必須在 Azure Batch VM 上執行。
   * **isPaused** 屬性預設為 **false**。 在此範例中，管線會立即執行，因為配量已在過去開始。 您可以將此屬性設為 true，以暫停管線，並將其設回 false，以重新啟動。
   * **start** 時間和 **end**時間相差 **5** 小時，而配量會每小時產生，因此管線會產生 5 個配量。
3. 按一下命令列上的 [部署]  ，部署管線。

### <a name="monitor-the-pipeline"></a>監視管線
1. 在 Azure 入口網站的 [Data Factory] 刀鋒視窗中，按一下 [圖表] 。

    ![[圖表] 磚](./media/data-factory-use-custom-activities/DataFactoryBlade.png)
2. 在 [圖表] 檢視中，現在按一下 [OutputDataset]。

    ![圖表檢視](./media/data-factory-use-custom-activities/diagram.png)
3. 如果已產生 5 個輸出配量，您應該會看到它們都處於就緒狀態。

   ![輸出配量](./media/data-factory-use-custom-activities/OutputSlices.png)
4. 確認 Blob 儲存體的 **adftutorial** 容器中已產生輸出檔案。

   ![自訂活動的輸出][image-data-factory-ouput-from-custom-activity]
5. 如果您開啟輸出檔，您應該會看到類似下面的輸出：

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
    ```
6. 使用 [Azure 入口網站][azure-preview-portal]或 Azure PowerShell Cmdlet 來監視您的 Data Factory、管線和資料集。 在可從入口網站下載的記錄檔中 (尤其是 user-0.log)，您可以從程式碼中的 **ActivityLogger** ，或使用 Cmdlet，以查看自訂活動的訊息。

   ![從自訂活動下載記錄檔][image-data-factory-download-logs-from-custom-activity]

如需有關監視資料集和管線的詳細步驟，請參閱 [監視和管理管線](data-factory-monitor-manage-pipelines.md) 。      

### <a name="data-factory-and-batch-integration"></a>Data Factory 和 Batch 整合
Data Factory 服務會在 Azure Batch 中建立作業，其名為： **adf-poolname:job-xxx**。

![Azure Data Factory - Batch 作業](media/data-factory-use-custom-activities/data-factory-batch-jobs.png)

配量的每個活動執行都會建立一個作業。 如果有 10 個配量就緒可供處理，此作業中會建立 10 個作業。 如果您在集區中有多個計算結點，您可以同時執行多個配量。 如果每個計算結點的最大工作設為 > 1，您也可以在相同的計算中執行多個配量。

![Azure Data Factory - Batch 作業工作](media/data-factory-use-custom-activities/data-factory-batch-job-tasks.png)

下圖說明 Azure Data Factory 與 Batch 工作之間的關聯性。

![Data Factory & Batch](./media/data-factory-use-custom-activities/DataFactoryAndBatch.png)

## <a name="debug-the-pipeline"></a>偵錯管線
偵錯包含一些基本技術：

1. 如果您看到下列錯誤訊息，請確認 CS 檔案中的類別名稱符合您在管線 JSON 中為 **EntryPoint** 屬性指定的名稱。 在上述逐步解說中，類別的名稱是︰MyDotNetActivity，而 JSON 中的 EntryPoint 是︰MyDotNetActivityNS.**MyDotNetActivity**。

    ```
    MyDotNetActivity assembly does not exist or doesn't implement the type Microsoft.DataFactories.Runtime.IDotNetActivity properly
    ```

   如果名稱相符，請確認所有二進位檔皆位於 zip 檔案的 **根資料夾** 中。 也就是說，當您開啟 zip 檔案，您應該會在根資料夾中看到所有檔案，而非在任何子資料夾中看到。   
2. 如果輸入配量不是設定為 [就緒] ，請確認輸入資料夾結構正確，且在於輸入資料夾中有 **file.txt**。
3. 在自訂活動的 **Execute** 方法中，使用可協助您針對問題進行疑難排解的 **IActivityLogger** 物件記錄資訊。 記錄的訊息會顯示在使用者記錄檔 (一或多個名為 user-0.log、user-1.log、user-2.log 等的檔案) 中。

   在 [OutputDataset] 刀鋒視窗中，按一下配量，以查看該配量的 [資料配量] 刀鋒視窗。 您會看到該配量的 [活動執行]。 您會看到一個為該配量執行的活動。 如果您按一下命令列中的 [執行]，您可以為相同的配量啟動另一個活動執行。

   當您按一下活動執行，您會看到包含記錄檔清單的 [活動執行詳細資料]  刀鋒視窗。 您會在 user_0.log 檔案中看到記錄的訊息。 發生錯誤時，您會看到三個活動執行，因為管線/活動 JSON 中的重試計數設定為 3。 當您按一下活動執行，您會看到您可以檢閱的記錄檔來疑難排解錯誤。

   在記錄檔清單中，按一下 [user-0.log] **IActivityLogger.Write**方法的結果。 在右窗格中的是使用 **IActivityLogger.Write** 方法的結果。 如果您無法看到所有訊息，請檢查是否有名為 user_1.log、user_2.log 等等多個記錄檔。否則，程式碼可能會在最後一個記錄的訊息之後失敗。

   您也應該檢查 **system-0.log** 是否有任何系統錯誤訊息和例外狀況。
4. 在 zip 檔案中包含 **PDB** 檔案，錯誤詳細資料才會在錯誤發生時包含**呼叫堆疊**等資訊。
5. 自訂活動之 zip 檔案中的所有檔案都必須位於 **最上層** 且不包含任何子資料夾。
6. 確認 **assemblyName** (MyDotNetActivity.dll)、**entryPoint** (MyDotNetActivityNS.MyDotNetActivity)、**packageFile** (customactivitycontainer/MyDotNetActivity.zip) 和 **packageLinkedService** (應指向包含 zip 檔案的 Azure blob 儲存體) 都設為正確的值。
7. 如果您修正錯誤，並想要重新處理配量，請以滑鼠右鍵按一下 [OutputDataset] 刀鋒視窗中的配量，然後按一下 [執行]。
8. 如果您看到下列錯誤，您使用的 Azure 儲存體封裝的版本即 > 4.3.0。 Data Factory 服務啟動程式需要 4.3 版的 WindowsAzure.Storage。 若您必須使用更新版本的 Azure 儲存體組件，請參閱 [Appdomain 隔離](#appdomain-isolation)小節，以取得因應措施。 

    ```
    Error in Activity: Unknown error in module: System.Reflection.TargetInvocationException: Exception has been thrown by the target of an invocation. ---> System.TypeLoadException: Could not load type 'Microsoft.WindowsAzure.Storage.Blob.CloudBlob' from assembly 'Microsoft.WindowsAzure.Storage, Version=4.3.0.0, Culture=neutral, 
    ```

    如果您可以使用版本 4.3.0 的 Azure 儲存體封裝，請移除對版本 > 4.3.0 Azure.Storage 封裝的現有參考，並從 Nuget 封裝管理員主控台中執行下列命令。 

    ```PowerShell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    建置專案。 從 bin\Debug 資料夾刪除版本 > 4.3.0 的 Azure.Storage 組件。 以二進位檔和 PDB 檔案建立新的 zip 檔案。 以 blob 容器 (customactivitycontainer) 中的 zip 檔案取代舊的 zip 檔案。 重新執行失敗的配量 (以滑鼠右鍵按一下配量並按一下 [執行])。   
8. 自訂活動不會使用來自您套件的 **app.config** 檔案，因此如果您的程式碼會從該組態檔讀取任何連接字串，在執行階段將沒有作用。 最佳做法是使用 Azure Batch 將所有祕密存放在 **Azure KeyVault** 中、使用以憑證為基礎的服務主體來保護 **keyvault**，然後將憑證發佈至 Azure Batch 集區。 接著，.NET 自訂活動便可以在執行階段從 KeyVault 存取密碼。 這是一般解決方案，而且可以擴展至任何類型的密碼，不僅限於連接字串。

   此外，也有較簡單的因應措施 (但並非最佳做法)︰您可以建立一個帶有連接字串設定的 **Azure SQL 連結服務** 、建立一個使用該連結服務的資料集，然後將該資料集以虛擬輸入資料集的形式鏈結至自訂 .NET 活動。 接著，您便可以在自訂活動程式碼中存取連結服務的連接字串，並且這在執行階段應該能夠發揮作用。  

## <a name="update-the-custom-activity"></a>更新自訂活動
如果您更新自訂活動的程式碼，請建置它，並將包含新二進位檔案的 zip 檔案上傳至 Blob 儲存體。

## <a name="copymove-data"></a>複製/移動資料

複製活動會將資料從**來源**資料存放區複製到**接收**資料存放區。 如需受支援成為「複製活動」之來源和接收器的資料存放區清單，請參閱 [支援的資料存放區](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 。

如果您需要將資料移至**複製活動**不支援的資料存放區，或從該資料存放區移動資料，則您可以在 Data Factory 中使用**自訂活動**搭配自己的邏輯來複製/移動資料。 請參閱 GitHub 上的 [HTTP 資料下載程式範例](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HttpDataDownloaderSample) 。  

## <a name="appdomain-isolation"></a>Appdomain 隔離
請參閱 [跨 AppDomain 範例](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) ，此範例示範如何撰寫不受 Azure Data Factory 啟動器之組件版本 (例如 WindowsAzure.Storage v4.3.0、Newtonsoft.Json v6.0.x 等) 限制的 Azure Data Factory 自訂 .NET 活動。

## <a name="access-extended-properties"></a>存取延伸屬性
您可以在活動 JSON 中宣告延伸屬性，如下所示：

```JSON
"typeProperties": {
  "AssemblyName": "MyDotNetActivity.dll",
  "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
  "PackageLinkedService": "AzureStorageLinkedService",
  "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
  "extendedProperties": {
    "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))",
    "DataFactoryName": "CustomActivityFactory"
  }
},
```


程式碼中有兩個延伸屬性：**SliceStart** 和 **DataFactoryName**。 SliceStart 的值以 SliceStart 系統變數為基礎。 如需支援的系統變數清單，請參閱 [系統變數](data-factory-scheduling-and-execution.md#data-factory-functions-and-system-variables) 。 DataFactoryName 值為硬式編碼為 "CustomActivityFactory"。

若要以 **Execute** 方法存取這些延伸屬性，請使用類似以下的程式碼：

```csharp
// to get extended properties (for example: SliceStart)
DotNetActivity dotNetActivity = (DotNetActivity)activity.TypeProperties;
string sliceStartString = dotNetActivity.ExtendedProperties["SliceStart"];

// to log all extended properties                               
IDictionary<string, string> extendedProperties = dotNetActivity.ExtendedProperties;
logger.Write("Logging extended properties if any...");
foreach (KeyValuePair<string, string> entry in extendedProperties)
{
    logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
}
```

## <a name="auto-scaling-feature-of-azure-batch"></a>Azure Batch 的自動調整功能
您也可以建立具有 **自動調整** 功能的 Azure Batch 集區。 例如，您可以用 0 專用 VM 和依據暫止工作數目自動調整的公式，建立 Azure Batch 集區︰

每個暫止工作一次一個 VM (例如︰5 個暫止工作 -> 5 個 VM)：

```
pendingTaskSampleVector=$PendingTasks.GetSample(600 * TimeInterval_Second);
$TargetDedicated = max(pendingTaskSampleVector);
```

無論暫止工作的數目為何，一次最多一個 VM︰

```
pendingTaskSampleVector=$PendingTasks.GetSample(600 * TimeInterval_Second);
$TargetDedicated = (max(pendingTaskSampleVector)>0)?1:0;
```

如需詳細資訊，請參閱 [自動調整 Azure Batch 集區中的計算節點](../batch/batch-automatic-scaling.md) 。

如果集區使用預設的 [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx)，Batch 服務在執行自訂活動之前，可能需要 15-30 分鐘的時間準備 VM。  如果集區使用不同的 autoScaleEvaluationInterval，Batch 服務可能需要 autoScaleEvaluationInterval + 10 分鐘。

## <a name="use-azure-hdinsight-linked-services"></a>使用 Azure HDInsight 連結服務
在逐步解說中，您會使用 Azure Batch 計算來執行自訂活動。 您也可以使用自己的 HDInsight 叢集，或讓 Data Factory 建立隨選 HDInsight 叢集，然後讓自訂活動在 HDInsight 叢集上執行。 以下是使用 HDInsight 叢集的高階步驟。  

1. 建立 Azure HDInsight 連結服務。   
2. 使用 HDInsight 連結服務來取代管線 JSON 中的 **AzureBatchLinkedService** 。

建議您變更管線的**開始**和**結束**時間，以便讓您能夠使用 Azure HDInsight 服務來測試案例。

#### <a name="create-azure-hdinsight-linked-service"></a>建立 Azure HDInsight 連結服務
Azure Data Factory 服務支援建立隨選叢集，並使用它處理輸入來產生輸出資料。 您也可以使用自己的叢集執行相同作業。 當您使用隨選 HDInsight 叢集時，系統會為每個配量建立叢集。 然而，如果您使用自己的 HDInsight 叢集，叢集已經準備好立即處理配量。 因此，在使用隨選叢集時，可能無法像使用自己的叢集那麼快看到輸出資料。

> [!NOTE]
> 在執行階段，.NET 活動的執行個體只在 HDInsight 叢集的一個背景工作角色節點上執行，無法擴展到多個節點上執行。 .NET 活動的多個執行個體可以在 HDInsight 叢集的不同節點上平行執行。
>
>

##### <a name="to-use-an-on-demand-hdinsight-cluster"></a>若要使用隨選 HDInsight 叢集
1. 在 **Azure 入口網站**中，按一下 Data Factory 首頁中的 [製作和部署]。
2. 在 [Data Factory 編輯器] 中，從命令列按一下 [新增計算]，然後從功能表選取 [隨選 HDInsight 叢集]。
3. 對 JSON 指令碼進行下列變更：

   1. 在 **clusterSize** 屬性中，指定 HDInsight 叢集的大小。
   2. 在 **timeToLive** 屬性中，指定客戶閒置多久之後會被刪除。
   3. 在 **version** 屬性中，指定您要使用的 HDInsight 版本。 如果您排除此屬性，則會使用最新版本。  
   4. 在 **linkedServiceName** 指定您在「開始使用」教學課程中建立的 **AzureStorageLinkedService**。

        ```JSON
        {
           "name": "HDInsightOnDemandLinkedService",
           "properties": {
               "type": "HDInsightOnDemand",
               "typeProperties": {
                   "clusterSize": 4,
                   "timeToLive": "00:05:00",
                   "osType": "Windows",
                   "linkedServiceName": "AzureStorageLinkedService",
               }
           }
        }
        ```
4. 按一下命令列的 [部署]  ，部署連結服務。

##### <a name="to-use-your-own-hdinsight-cluster"></a>若要使用您自己的 HDInsight 叢集：
1. 在 **Azure 入口網站**中，按一下 Data Factory 首頁中的 [製作和部署]。
2. 在 [Data Factory 編輯器] 中，從命令列按一下 [新增計算]，然後從功能表選取 [HDInsight 叢集]。
3. 對 JSON 指令碼進行下列變更：

   1. 在 **clusterUri** 屬性中，輸入您的 HDInsight 的 URL。 例如：https://<clustername>.azurehdinsight.net/     
   2. 在 **UserName** 屬性中，輸入具有 HDInsight 叢集存取權的使用者名稱。
   3. 在 **Password** 屬性中，輸入使用者的密碼。
   4. 在 **LinkedServiceName** 屬性輸入 **AzureStorageLinkedService**。 您已在「開始使用」教學課程中建立此連結服務。
4. 按一下命令列的 [部署]  ，部署連結服務。

請參閱 [計算連結服務](data-factory-compute-linked-services.md) 以取得詳細資料。

在 **管線 JSON**中，使用 HDInsight (隨選或自有) 連結服務︰

```JSON
{
  "name": "ADFTutorialPipelineCustom",
  "properties": {
    "description": "Use custom activity",
    "activities": [
      {
        "Name": "MyDotNetActivity",
        "Type": "DotNetActivity",
        "Inputs": [
          {
            "Name": "InputDataset"
          }
        ],
        "Outputs": [
          {
            "Name": "OutputDataset"
          }
        ],
        "LinkedServiceName": "HDInsightOnDemandLinkedService",
        "typeProperties": {
          "AssemblyName": "MyDotNetActivity.dll",
          "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
          "PackageLinkedService": "AzureStorageLinkedService",
          "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
          "extendedProperties": {
            "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
          }
        },
        "Policy": {
          "Concurrency": 2,
          "ExecutionPriorityOrder": "OldestFirst",
          "Retry": 3,
          "Timeout": "00:30:00",
          "Delay": "00:00:00"
        }
      }
    ],
    "start": "2015-11-16T00:00:00Z",
    "end": "2015-11-16T05:00:00Z",
    "isPaused": false
  }
}
```

## <a name="examples"></a>範例
| 範例 | 自訂活動的工作內容 |
| --- | --- |
| [HTTP 資料下載程式](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HttpDataDownloaderSample)。 |使用 Data Factory 的自訂 C# 活動，從 HTTP 端點將資料下載到 Azure Blob 儲存體。 |
| [Twitter 情感分析範例](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-CustomC%23Activity) |叫用 Azure ML 模型，執行情感分析、評分、預測等等。 |
| [執行 R 指令碼](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)。 |在已安裝 R 的 HDInsight 叢集上執行 RScript.exe 來叫用 R 指令碼。 |
| [跨 AppDomain.NET 活動](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) |使用非 Data Factory 啟動器所使用的組件版本 |

## <a name="see-also"></a>另請參閱
[Azure Data Factory 更新：使用 Azure Batch 執行 ADF 自訂 .NET 活動](https://azure.microsoft.com/blog/2015/05/01/azure-data-factory-updates-execute-adf-custom-net-activities-using-azure-batch/)。

[batch-net-library]: ../batch/batch-dotnet-get-started.md
[batch-create-account]: ../batch/batch-account-create-portal.md
[batch-technical-overview]: ../batch/batch-technical-overview.md
[batch-get-started]: ../batch/batch-dotnet-get-started.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[azure-powershell-install]: https://github.com/Azure/azure-sdk-tools/releases


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[new-azure-batch-account]: https://msdn.microsoft.com/library/mt125880.aspx
[new-azure-batch-pool]: https://msdn.microsoft.com/library/mt125936.aspx
[azure-batch-blog]: http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx

[nuget-package]: http://go.microsoft.com/fwlink/?LinkId=517478
[azure-developer-center]: http://azure.microsoft.com/develop/net/
[adf-developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[azure-preview-portal]: https://portal.azure.com/

[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[hivewalkthrough]: data-factory-data-transformation-activities.md

[image-data-factory-ouput-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png



<!--HONumber=Dec16_HO3-->


