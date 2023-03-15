---
lab:
  title: 將模型部署至受控線上端點
---

# 將模型部署至受控線上端點

若要取用應用程式中的模型，並取得即時預測，您會想要將模型部署至受控線上端點。 您可以輕鬆地部署 MLflow 模型，因為您不需要定義環境或建立評分腳本。

在此練習中，您會將 MLflow 模型部署到受控線上端點，並在範例資料上進行測試。 

## 開始之前

您將需要具有系統管理層級存取權的 [Azure 訂用帳戶](https://azure.microsoft.com/free?azure-portal=true)。

## 佈建 Azure Machine Learning 工作區

Azure Machine Learning *工作區* 提供一個集中位置，可用來管理您需要定型和管理模型的所有資源和資產。 您可以透過 Studio、Python SDK 和 Azure CLI 與 Azure Machine Learning 工作區互動。 

您將使用 Azure CLI 來布建工作區和必要的計算，並使用 Python SDK 來執行命令作業。

### 建立工作區和計算資源

若要建立 Azure Machine Learning 工作區、計算實例和計算叢集，您將使用 Azure CLI。 所有必要的命令會分組在殼層腳本中，供您執行。

1. 在瀏覽器中，開啟位於 `https://portal.azure.com/` 的 Azure 入口網站，以您的 Microsoft 帳戶登入。
1. \[選取搜尋方塊右上方頁面頂端的>_] * (* Cloud Shell) 按鈕。 這會在入口網站底部開啟 Cloud Shell 窗格。
1. 如果系統詢問，請選取 **[Bash** ]。 第一次開啟 Cloud Shell 時，系統會要求您選擇您想要使用 (*Bash* 或 *PowerShell*) 的殼層類型。 
1. 檢查是否已指定正確的訂用帳戶 **，如果系統** 要求您為 Cloud Shell 建立儲存體，請選取 [建立儲存體]。 等候建立儲存體。
1. 在終端機中，輸入下列命令來複製此存放庫：

    ```azurecli
    rm -r azure-ml-labs -f
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

    > 使用 `SHIFT + INSERT` 將複製的程式碼貼到Cloud Shell。 

1. 複製存放庫之後，請輸入下列命令來變更此實驗室的資料夾，並執行它所包含的 **setup.sh** 腳本：
    
    ```azurecli
    cd azure-ml-labs/Labs/11
    ./setup.sh
    ```

    > 忽略任何 (錯誤，) 表示未安裝延伸模組的訊息。 

1. 等候腳本完成 -這通常需要大約 5-10 分鐘的時間。 

## 複製實驗室資料

當您建立工作區和必要的計算資源時，您可以開啟Azure Machine Learning 工作室，並將實驗室資料複製到工作區。 

1. 在Azure 入口網站中，流覽至名為**mlw-dp100-labs**的 Azure Machine Learning 工作區。
1. 選取 Azure Machine Learning 工作區，然後在其 [概 **觀** ] 頁面中，選取 **[啟動 Studio**]。 另一個索引標籤會在瀏覽器中開啟，以開啟Azure Machine Learning 工作室。
1. 關閉出現在 Studio 中的任何快顯視窗。
1. 在Azure Machine Learning 工作室中，流覽至 [**計算**] 頁面，並確認您在上一節中建立的計算實例和叢集是否存在。 計算實例應該正在執行，叢集應該閒置，而且有 0 個節點正在執行。
1. 在 [ **計算實例] 索引標籤** 中，尋找您的計算實例，然後選取 **[終端機** ] 應用程式。
1. 在終端機中，在終端機中執行下列命令，在計算實例上安裝 Python SDK：
    
    ```
    pip uninstall azure-ai-ml
    pip install azure-ai-ml
    ```

    > 忽略任何 (錯誤) 訊息，指出找不到套件並卸載。

1. 執行下列命令，將包含筆記本、資料和其他檔案的 Git 存放庫複製到您的工作區：
    
    ```
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```
 
1. 當命令完成時，按一下 [ **檔案** ] 窗格中 ** 的 [&#8635;** ]，以重新整理檢視，並確認已建立新的 **Users/*your-user-name*/azure-ml-labs** 資料夾。 

## 將模型部署至線上端點

在筆記本中提供用來建立端點並部署具有 Python SDK 的 MLflow 模型的程式碼。 

1. 開啟 **Labs/11/部署至線上 endpoint.ipynb** 筆記本。

    > 如果出現通知要求您進行驗證，請選取 [ **驗證** ]，並遵循必要的步驟。 

1. 確認筆記本使用 **Python 3.8 - AzureML** 核心。 
1. 執行筆記本中的所有儲存格。

## 刪除 Azure 資源

當您完成探索 Azure Machine Learning 時，應該刪除您所建立的資源，以避免不必要的 Azure 成本。

1. 關閉 [Azure Machine Learning 工作室] 索引標籤，並返回Azure 入口網站。
1. 在 Azure 入口網站的 [首頁] 上，選取 [資源群組]。
1. 選取 **rg-dp100-labs** 資源群組。
1. 在資源群組的 [概觀] 頁面頂端，選取 [刪除資源群組]。 
1. 輸入資源群組名稱以確認您想要將其刪除，然後選取 [刪除]。