---
lab:
  title: 將模型部署到批次端點
---

# 將模型部署到批次端點

在許多情況下，推斷以批次處理方式進行，運用預測模型來為大量案例評分。 若要在 Azure 機器學習 中實作這種推斷解決方案，您可以建立批次端點。

在此練習中，您會將 MLflow 模型部署至批次端點，並藉由提交作業來測試範例數據。

## 在您開始使用 Intune 之前

您將需要具有系統管理層級存取權的 [Azure 訂用帳戶](https://azure.microsoft.com/free?azure-portal=true)。

## 佈建 Azure Machine Learning 工作區

Azure 機器學習 *工作區*提供一個集中位置，用於管理您需要定型和管理模型的所有資源和資產。 您可以透過 Studio、Python SDK 和 Azure CLI 與 Azure 機器學習 工作區互動。

您將使用 Azure CLI 來佈建工作區和必要的計算，並使用 Python SDK 來執行命令作業。

### 建立工作區和計算資源

若要建立 Azure 機器學習 工作區、計算實例和計算叢集，您將使用 Azure CLI。 所有必要的命令都會分組在殼層腳本中，讓您執行。

1. 在瀏覽器中，開啟 位於 `https://portal.azure.com/`的 Azure 入口網站，使用您的 Microsoft 帳戶登入。
1. 選取 \[搜尋方塊右側頁面頂端的 [>_] [*Cloud Shell*] 按鈕。 這會在入口網站底部開啟 Cloud Shell 窗格。
1. 如果系統詢問，請選取 **[Bash** ]。 第一次開啟 Cloud Shell 時，系統會要求您選擇要使用的殼層類型（*Bash* 或 *PowerShell*）。
1. 如果系統要求您為 Cloud Shell 建立記憶體，請檢查是否已指定正確的訂用帳戶，然後選取 **[建立記憶體** ]。 等候建立記憶體。
1. 在終端機中，輸入下列命令以複製此存放庫：

    ```azurecli
    rm -r azure-ml-labs -f
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

    > 使用 `SHIFT + INSERT` 將複製的程式代碼貼到 Cloud Shell 中。 

1. 複製存放庫之後，輸入下列命令以變更為此實驗室的資料夾，並執行 **它所包含的 setup.sh** 腳本：

    ```azurecli
    cd azure-ml-labs/Labs/11
    ./setup.sh
    ```

    > 忽略指出未安裝延伸模組的任何 （錯誤） 訊息。

1. 等候腳本完成 - 這通常需要大約 5-10 分鐘的時間。

## 複製實驗室材料

當您建立工作區和必要的計算資源時，您可以開啟 Azure Machine Learning 工作室 並將實驗室數據複製到工作區。

1. 在 Azure 入口網站 中，流覽至名為 **mlw-dp100 的 Azure 機器學習 工作區-...**。
1. 選取 Azure 機器學習 工作區，然後在其 [概觀] **頁面中，選取 [**啟動工作室**]。** 另一個索引標籤會在瀏覽器中開啟，以開啟 Azure Machine Learning 工作室。
1. 關閉任何出現在 Studio 中的彈出視窗。
1. 在 Azure Machine Learning 工作室 內，流覽至 **[計算**] 頁面，並確認您在上一節中建立的計算實例和叢集是否存在。 計算實例應該正在執行，叢集應該閑置，且有0個節點正在執行。
1. 在 [ **計算實例] 索引標籤中** ，尋找您的計算實例，然後選取 [ **終端** 機] 應用程式。
1. 在終端機中，在終端機中執行下列命令，在計算實例上安裝 Python SDK：

    ```

    pip uninstall azure-ai-ml
    pip install azure-ai-ml
    ```

    > 忽略指出找不到並卸載套件的任何 （錯誤） 訊息。

1. 執行下列命令，將包含筆記本、資料和其他檔案的 Git 存放庫複製到您的工作區：

    ```
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```
 
1. 當命令完成時，**按兩下 [檔案**] 窗格中的 [&#8635]，**重新整理檢視，並確認已建立新的**Users/*your-user-name*/azure-ml-labs** 資料夾。**

## 將模型部署至批次端點

在筆記本中會提供用來建立端點及部署 MLflow 模型與 Python SDK 的程式代碼。

1. **開啟 Labs/11/部署至 batch endpoint.ipynb** 筆記本。

    > 如果出現通知要求您進行驗證，請選取 **[驗證** ]，並遵循必要的步驟。

1. 確認筆記本使用 **Python 3.8 - AzureML** 核心。
1. 在筆記本中執行所有儲存格。

## 刪除 Azure 資源

當您完成探索 Azure 機器學習 時，應該刪除您建立的資源，以避免不必要的 Azure 成本。

1. 關閉 [Azure Machine Learning 工作室] 索引標籤，並返回 Azure 入口網站。
1. 在 Azure 入口網站的 [首頁]**** 上，選取 [資源群組]****。
1. **選取 rg-dp100-...** 資源群組。
1. 在資源群組的 [概觀]**** 頁面頂端，選取 [刪除資源群組]****。
1. 輸入資源群組名稱以確認要刪除，然後選取 [刪除]****。
