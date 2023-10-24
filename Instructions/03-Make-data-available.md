---
lab:
  title: 在 Azure Machine Learning 中提供資料
---

# 在 Azure Machine Learning 中提供資料

雖然在本地檔案系統上使用其資料相當常見，但在企業環境中，將資料儲存在可由多名資料科學家和機器學習工程師存取的中央位置會更有效率。

在此練習中，您將探索資料*存放區和**資料資產*，這是用來抽象 Azure Machine Learning 中資料存取的主要物件。

## 開始之前

您將需要具有系統管理層級存取權的 [Azure 訂用帳戶](https://azure.microsoft.com/free?azure-portal=true)。

## 佈建 Azure Machine Learning 工作區

Azure Machine Learning *工作區* 提供一個集中位置，可用來管理您需要定型和管理模型的所有資源和資產。 您可以透過 Studio、Python SDK 和 Azure CLI 與 Azure Machine Learning 工作區互動。

您將使用使用 Azure CLI 來布建工作區和必要資源的 Shell 腳本。 接下來，您將使用Azure Machine Learning 工作室中的Designer來定型和比較模型。

### 建立工作區和計算資源

若要建立 Azure Machine Learning 工作區和計算資源，您將使用 Azure CLI。 所有必要的命令會分組在殼層腳本中，供您執行。

1. 在瀏覽器中，開啟位於 `https://portal.azure.com/` 的 Azure 入口網站，以您的 Microsoft 帳戶登入。
1. \[選取搜尋方塊右上方頁面頂端的>_] * (* Cloud Shell) 按鈕。 這會在入口網站底部開啟 Cloud Shell 窗格。
1. 如果系統詢問，請選取 **[Bash** ]。 第一次開啟 Cloud Shell 時，系統會要求您選擇您想要使用 (*Bash* 或 *PowerShell*) 的殼層類型。
1. 檢查是否已指定正確的訂用帳戶 **，如果系統** 要求您為 Cloud Shell 建立儲存體，請選取 [建立儲存體]。 等候建立儲存體。
1. 在終端機中輸入下列命令以複製此存放庫：

    ```azurecli
    rm -r azure-ml-labs -f
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

    > 使用 `SHIFT + INSERT` 將複製的程式碼貼到Cloud Shell。

1. 在複製存放庫之後輸入下列命令，以變更為此實驗室的資料夾，並執行它所包含的 **setup.sh** 腳本：

    ```azurecli
    cd azure-ml-labs/Labs/03
    ./setup.sh
    ```

    > 忽略任何 (錯誤，) 表示未安裝延伸模組的訊息。

1. 等候腳本完成 -這通常需要大約 5-10 分鐘的時間。

## 探索預設資料存放區

當您建立 Azure Machine Learning 工作區時，會自動建立儲存體帳戶並聯機到您的工作區。 您將探索儲存體帳戶的連線方式。

1. 在Azure 入口網站中，流覽至名為**rg-dp100 的新**資源群組-...。
1. 選取資源群組中的儲存體帳戶。 名稱通常會以您為工作區提供的名稱開頭， (不含連字號) 。
1. 檢閱儲存體帳戶的 [概 **觀** ] 頁面。 請注意，儲存體帳戶有數個 **資料儲存體** 選項，如 [概觀] 窗格和左側功能表中所示。
1. 選取 **[容器** ] 以探索儲存體帳戶的 Blob 儲存體部分。
1. 請注意 **azureml-blobstore-...** 容器。 資料資產的預設資料存放區會使用此容器來儲存資料。
1. 使用畫面頂端的 [&#43; **容器** ] 按鈕，建立新的容器並將其命名為 `training-data` 。
1. 從左側功能表中選取 [ **檔案共用** ]，以探索儲存體帳戶的 [檔案共用] 部分。
1. 請注意程式 **代碼-...** 檔案共用。 工作區中的任何筆記本都儲存在這裡。 複製實驗室資料之後，您可以在程式 **代碼-.../Users/*your-user-name*/azure-ml-labs** 資料夾中找到此檔案共用中的檔案。

## 複製存取金鑰

若要在 Azure Machine Learning 工作區中建立資料存放區，您需要提供一些認證。 提供工作區存取 Blob 儲存體的簡單方式是使用帳戶金鑰。

1. 在 [儲存體帳戶] 中，從左側功能表中選取 [ **存取金鑰] 索引卷** 標。
1. 請注意，提供兩個金鑰：key1 和 key2。 每個索引鍵都有相同的功能。 
1. 針對**key1**底下的 **[金鑰**] 欄位選取 [**顯示**]。
1. 將 **[金鑰** ] 欄位的值複製到記事本。 您稍後需要將此值貼到筆記本中。
1. 從頁面頂端複製儲存體帳戶的名稱。 名稱應以 **mlwdp100storage 開頭...** 您稍後也需要將此值貼到筆記本中。

> **注意**：將帳戶金鑰和名稱複製到記事本，以避免在Word) 發生自動大寫 (。 索引鍵會區分大小寫。

## 複製實驗室資料

若要使用 Python SDK 建立資料存放區和資料資產，您必須將實驗室資料複製到工作區。

1. 在Azure 入口網站中，流覽至名為**mlw-dp100-labs**的 Azure Machine Learning 工作區。
1. 選取 Azure Machine Learning 工作區，然後在其 [概 **觀** ] 頁面中，選取 **[啟動 Studio**]。 另一個索引標籤會在瀏覽器中開啟，以開啟Azure Machine Learning 工作室。
1. 關閉出現在 Studio 中的任何快顯視窗。
1. 在Azure Machine Learning 工作室中，流覽至 [**計算**] 頁面，並確認您在上一節中建立的計算實例和叢集是否存在。 計算實例應該正在執行，叢集應該閒置，而且有 0 個節點正在執行。
1. 在 [ **計算實例] 索引標籤** 中，尋找您的計算實例，然後選取 **[終端機** ] 應用程式。
1. 在終端機中，在終端機中執行下列命令，在計算實例上安裝 Python SDK：

    ```azurecli
    pip uninstall azure-ai-ml
    pip install azure-ai-ml
    pip install mltable
    ```

    > 忽略任何 (錯誤，) 指出未安裝套件的訊息。

1. 執行下列命令，將包含筆記本、資料和其他檔案的 Git 存放庫複製到您的工作區：

    ```azurecli
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

1. 當命令完成時，按一下 [ **檔案** ] 窗格中 ** 的 [&#8635;** ]，以重新整理檢視，並確認已建立新的 **Users/*your-user-name*/azure-ml-labs** 資料夾。

**或者**，在另一個瀏覽器索引標籤中，流覽回[Azure 入口網站](https://portal.azure.com?azure-portal=true)。 再次探索儲存體帳戶中的檔案共用 **程式碼-...** ，以在新建立的 **azure-ml-labs** 資料夾中尋找複製的實驗室資料。

## 建立資料存放區和資料資產

使用 Python SDK 建立資料存放區和資料資產的程式碼會在筆記本中提供。

1. 開啟 **Labs/03/Work with data.ipynb** 筆記本。

    > 如果出現通知要求您進行驗證，請選取 [ **驗證** ]，並遵循必要的步驟。

1. 確認筆記本使用 **Python 3.8 - AzureML** 核心。
1. 執行筆記本中的所有儲存格。

## 選擇性：探索資料資產

**您可以選擇**性地探索資料資產如何儲存在相關聯的儲存體帳戶中。

1. 流覽至Azure Machine Learning 工作室中的 [**資料**] 索引標籤，以探索資料資產。
1. 選取 **糖尿病本機** 資料資產名稱，以探索其詳細資料。 

    在 **[糖尿病本機**資料資產**的資料來源**] 下，您會發現檔案已上傳至何處。 從**LocalUpload/...** 開始的路徑會顯示儲存體帳戶容器**azureml-blobstore**內的路徑-...。您可以流覽至Azure 入口網站中的該路徑，以確認檔案存在。

## 刪除 Azure 資源

當您完成探索 Azure Machine Learning 時，應該刪除您所建立的資源，以避免不必要的 Azure 成本。

1. 關閉 [Azure Machine Learning 工作室] 索引標籤，並返回Azure 入口網站。
1. 在 Azure 入口網站的 [首頁] 上，選取 [資源群組]。
1. 選取 **rg-dp100-...** 資源群組。
1. 在資源群組的 [概觀] 頁面頂端，選取 [刪除資源群組]。
1. 輸入資源群組名稱以確認要刪除，然後選取 [刪除]。
