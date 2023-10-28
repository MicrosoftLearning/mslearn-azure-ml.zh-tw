---
lab:
  title: 探索用於工作區互動的開發人員工具
---

# 探索用於工作區互動的開發人員工具

您可以使用各種工具來與 Azure Machine Learning 工作區互動。 根據您需要執行的工作，以及開發人員工具的喜好設定，您可以選擇何時要使用的工具。 此實驗室設計為開發人員工具的簡介，通常用於工作區互動。 如果您想要深入瞭解如何使用特定工具，還有其他實驗室可供探索。

## 開始之前

您將需要具有系統管理層級存取權的 [Azure 訂用帳戶](https://azure.microsoft.com/free?azure-portal=true)。

與 Azure Machine Learning 工作區互動的常用開發人員工具如下：

- **Azure CLI 與 Azure** Machine Learning 擴充功能：此命令列方法非常適合基礎結構的自動化。
- **Azure Machine Learning 工作室**：使用方便使用的 UI 來探索工作區及其所有功能。
- 適用于 Azure Machine Learning 的**Python SDK**：用來從 Jupyter 筆記本提交作業和管理模型，適用于資料科學家。

您將探索這些工具中的每一個工具，以瞭解通常使用此工具完成的工作。

## 使用 Azure CLI 布建基礎結構

若要讓資料科學家使用 Azure Machine Learning 來定型機器學習模型，您必須設定必要的基礎結構。 您可以使用 Azure CLI 搭配 Azure Machine Learning 擴充功能來建立 Azure Machine Learning 工作區和資源，例如計算實例。

若要開始，請開啟 Azure Cloud Shell，安裝 Azure Machine Learning 擴充功能並複製 Git 存放庫。

1. 在瀏覽器中，開啟位於 的 `https://portal.azure.com/` Azure 入口網站，使用您的 Microsoft 帳戶登入。
1. 選取搜尋 \[ 方塊右側頁面頂端的>_*] (Cloud Shell*) 按鈕。 這會在入口網站底部開啟 Cloud Shell 窗格。
1. 如果系統詢問，請選取 **[Bash** ]。 第一次開啟 Cloud Shell 時，系統會要求您選擇您想要使用 (*Bash* 或 *PowerShell*) 的殼層類型。
1. 如果系統要求您為 Cloud Shell 建立 **儲存體** ，請檢查是否已指定正確的訂用帳戶，然後選取 [建立儲存體]。 等候儲存體建立。
1. 移除第 1 版和第 2 版 (任何 ML CLI 擴充功能) ，以避免使用此命令與舊版發生任何衝突：
    
    ```azurecli
    az extension remove -n azure-cli-ml
    az extension remove -n ml
    ```

    > 使用 `SHIFT + INSERT` 將複製的程式碼貼入Cloud Shell。

    > 忽略任何 (錯誤，) 表示未安裝延伸模組的訊息。

1. 使用下列命令安裝 Azure Machine Learning (v2) 擴充功能：
    
    ```azurecli
    az extension add -n ml -y
    ```

1. 建立資源群組。 選擇接近您的位置。
    
    ```azurecli
    az group create --name "rg-dp100-labs" --location "eastus"
    ```

1. 建立工作區：
    
    ```azurecli
    az ml workspace create --name "mlw-dp100-labs" -g "rg-dp100-labs"
    ```

1. 等候工作區及其相關聯的資源建立 - 這通常需要大約 5 分鐘的時間。

## 使用 Azure CLI 建立計算實例

定型機器學習模型所需的基礎結構的另一個重要部分是 **計算**。 雖然您可以在本機定型模型，但使用雲端計算會更具延展性和成本效益。

當資料科學家在 Azure Machine Learning 工作區中開發機器學習模型時，他們想要使用虛擬機器來執行 Jupyter Notebook。 針對開發， **計算實例** 是理想的選擇。

建立 Azure Machine Learning 工作區之後，您也可以使用 Azure CLI 建立計算實例。

在此練習中，您將使用下列設定來建立計算實例：

- **計算名稱**： *計算實例的名稱。必須是唯一且少於 24 個字元。*
- **虛擬機器大小**：STANDARD_DS11_V2
- **計算類型** (實例或叢集) ：ComputeInstance
- **Azure Machine Learning 工作區名稱**：mlw-dp100-labs
- **資源群組**：rg-dp100-labs

1. 使用下列命令在工作區中建立計算實例。 如果計算實例名稱包含 「XXXX」，請將它取代為亂數字，以建立唯一的名稱。

    ```azurecli
    az ml compute create --name "ciXXXX" --size STANDARD_DS11_V2 --type ComputeInstance -w mlw-dp100-labs -g rg-dp100-labs
    ```

    如果您收到錯誤訊息，指出名稱為 的計算實例已經存在，請變更名稱，然後重試命令。

## 使用 Azure CLI 建立計算叢集

雖然計算實例很適合用於開發，但當我們想要定型機器學習模型時，計算叢集更適合。 只有在提交作業以使用計算叢集時，才會調整為超過 0 個節點並執行作業。 一旦不再需要計算叢集，它會自動調整大小回 0 個節點，以將成本降至最低。 

若要建立計算叢集，您可以使用 Azure CLI，類似于建立計算實例。

您將使用下列設定來建立計算叢集：

- **計算名稱**：aml-cluster
- **虛擬機器大小**：STANDARD_DS11_V2
- **計算類型**：AmlCompute * (建立計算叢集) *
- **實例數目上限**： *節點數目上限*
- **Azure Machine Learning 工作區名稱**：mlw-dp100-labs
- **資源群組**：rg-dp100-labs

1. 使用下列命令在您的工作區中建立計算叢集。
    
    ```azurecli
    az ml compute create --name "aml-cluster" --size STANDARD_DS11_V2 --max-instances 2 --type AmlCompute -w mlw-dp100-labs -g rg-dp100-labs
    ```

## 使用 Azure Machine Learning 工作室 設定工作站

雖然 Azure CLI 非常適合自動化，但您可能會想要檢閱您所執行命令的輸出。 您可以使用Azure Machine Learning 工作室來檢查是否已建立資源和資產，以及檢查作業是否成功執行，或檢閱作業失敗的原因。 

1. 在Azure 入口網站中，流覽至名為**mlw-dp100-labs**的 Azure Machine Learning 工作區。
1. 選取 Azure Machine Learning 工作區，然後在其 [ **概觀** ] 頁面中，選取 **[啟動 Studio**]。 另一個索引標籤會在瀏覽器中開啟，以開啟Azure Machine Learning 工作室。
1. 關閉任何出現在 Studio 中的快顯視窗。
1. 在Azure Machine Learning 工作室中，流覽至 [**計算**] 頁面，並確認您在上一節中建立的計算實例和叢集是否存在。 計算實例應該正在執行，叢集應該閒置，且有 0 個節點正在執行。

## 使用 Python SDK 來定型模型

既然您已確認已建立必要的計算，您可以使用 Python SDK 來執行定型腳本。 您將在計算實例上安裝並使用 Python SDK，並在計算叢集上定型機器學習模型。

1. 選取**計算實例**的 **[終端機**] 應用程式以啟動終端機。
1. 在終端機中，在終端機中執行下列命令，在計算實例上安裝 Python SDK：

    ```
    pip uninstall azure-ai-ml
    pip install azure-ai-ml
    ```

    > 忽略任何 (錯誤，) 指出未安裝套件的訊息。

1. 執行下列命令，將包含筆記本、資料和其他檔案的 Git 存放庫複製到您的工作區：

    ```
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

1. 當命令完成時，請在 [ **檔案]** 窗格中選取 ** [&#8635;** ]，以重新整理檢視，並確認已建立新的 **Users/*your-user-name/azure-ml-labs* ** 資料夾。
1. 開啟 **Labs/02/Run training script.ipynb** 筆記本。

    > 如果出現通知要求您進行驗證，請選取 [ **驗證** ]，並遵循必要的步驟。

1. 確認筆記本使用 **Python 3.8 - AzureML** 核心。 每個核心都有自己的映射，並預先安裝自己的套件集。
1. 在筆記本中執行所有儲存格。

新的作業將會在 Azure Machine Learning 工作區中建立。 此作業會追蹤作業組態中定義的輸入、所使用的程式碼，以及用來評估模型的計量等輸出。

## 在Azure Machine Learning 工作室中檢閱您的作業歷程記錄

當您將作業提交至 Azure Machine Learning 工作區時，您可以在Azure Machine Learning 工作室中檢閱其狀態。

1. 選取筆記本中提供作為輸出的作業 URL，或流覽至Azure Machine Learning 工作室中的 [**作業**] 頁面。
1. 新的實驗會列出名為 **diabetes-training**。 選取最新的工作 **diabetes-pythonv2-train**。
1. 檢閱**作業的屬性。** 記下作業 **狀態**：
    - **已排入佇列**：作業正在等候計算變成可用。
    - **準備**：計算叢集正在調整大小，或正在計算目標上安裝環境。
    - **執行**中：正在執行定型腳本。
    - **完成**：訓練腳本已執行，且作業會以所有最終資訊進行更新。
    - **已完成**：作業已順利完成並終止。
    - **失敗**：作業失敗且已終止。
1. 在 **[輸出 + 記錄**] 下，您會在 **user_logs/std_log.txt**中找到腳本的輸出。 腳本中 **列印** 語句的輸出會顯示在這裡。 如果因為腳本發生問題而發生錯誤，您也可以在這裡找到錯誤訊息。
1. 在 [ **程式碼**] 下，您會發現您在作業組態中指定的資料夾。 此資料夾包含定型腳本和資料集。

## 刪除 Azure 資源

當您完成探索 Azure Machine Learning 時，應該刪除您所建立的資源，以避免不必要的 Azure 成本。

1. 關閉 [Azure Machine Learning 工作室] 索引標籤，並返回Azure 入口網站。
1. 在 Azure 入口網站的 [首頁] 上，選取 [資源群組]。
1. 選取 **rg-dp100-labs** 資源群組。
1. 在資源群組的 [概觀] 頁面頂端，選取 [刪除資源群組]。 
1. 輸入資源群組名稱以確認要刪除，然後選取 [刪除]。
