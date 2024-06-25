---
lab:
  title: 在 Azure Machine Learning 中執行管線
---

# 在 Azure Machine Learning 中執行管線

您可以利用 Python SDK 執行在 Azure 中建立及操作機器學習解決方案所需的一切工作。 您可以使用管線來協調準備資料、執行定型指令碼和其他工作所需的步驟，而不用個別執行這些工作。

在此練習中，您將以管線作業的形式執行多個指令碼。

## 在您開始使用 Intune 之前

您將需要具有系統管理層級存取權的 [Azure 訂用帳戶](https://azure.microsoft.com/free?azure-portal=true)。

## 佈建 Azure Machine Learning 工作區

Azure Machine Learning *工作區* 提供一個集中位置，可用來管理您需要定型和管理模型的所有資源和資產。 您可以透過 Studio、Python SDK 和 Azure CLI 與 Azure Machine Learning 工作區互動。

您將使用 Azure CLI 來布建工作區和必要的計算，並使用 Python SDK 來執行命令作業。

### 建立工作區和計算資源

若要建立 Azure Machine Learning 工作區、計算執行個體和計算叢集，您將使用 Azure CLI。 所有必要的命令會分組在殼層指令碼中，供您執行。

1. 在瀏覽器中，開啟位於 `https://portal.azure.com/` 的 Azure 入口網站，並使用您的 Microsoft 帳戶登入。
1. 選取頁面頂端搜尋方塊右邊的 \[>_] (*Cloud Shell*) 按鈕。 這會在入口網站底部開啟 Cloud Shell 窗格。
1. 如有要求，請選取 [Bash]****。 第一次開啟 Cloud Shell 時，系統會要求您選擇要使用的殼層類型 (*Bash* 或 *PowerShell*)。
1. 如果系統要求您為 Cloud Shell 建立儲存體，請檢查已指定正確的訂用帳戶，然後選取 [建立儲存體]****。 等候儲存體建立完成。
1. 在終端機中，輸入下列命令來複製此存放庫:

    ```azurecli
    rm -r azure-ml-labs -f
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

    > 使用 `SHIFT + INSERT` 將複製的程式碼貼到 Cloud Shell 中。

1. 複製存放庫之後，請輸入下列命令來變更此實驗室的資料夾，並執行它所包含的 **setup.sh** 指令碼:

    ```azurecli
    cd azure-ml-labs/Labs/09
    ./setup.sh
    ```

    > 忽略表示未安裝延伸模組的任何 (錯誤) 訊息。

1. 等候指令碼完成 - 這通常需要大約 5-10 分鐘的時間。

## 複製實驗室資料

當您建立工作區和必要的計算資源時，您可以開啟 Azure Machine Learning 工作室，並將實驗室資料複製到工作區。

1. 在 Azure 入口網站中，瀏覽至名為 **mlw-dp100-...** 的 Azure Machine Learning 工作區。
1. 選取 Azure Machine Learning 工作區，然後在其 [概觀]**** 頁面中，選取 [啟動工作室]****。 另一個索引標籤會在瀏覽器中開啟，以開啟 Azure Machine Learning 工作室。
1. 關閉任何出現在工作室中的快顯視窗。
1. 在 Azure Machine Learning 工作室中，瀏覽至 [計算]**** 頁面，並確認您在上一個節中建立的計算執行個體和叢集是否存在。 計算執行個體應該正在執行，叢集應該閒置，而且有 0 個節點正在執行。
1. 在 [計算執行個體]**** 索引標籤中，尋找您的計算執行個體，然後選取 [終端機]**** 應用程式。
1. 在終端機中執行下列命令，以在終端機中的計算執行個體上安裝 Python SDK：

    ```
    pip uninstall azure-ai-ml
    pip install azure-ai-ml
    ```

    > 忽略任何 (錯誤) 訊息，指出找不到套件並解除安裝。

1. 執行下列命令，將包含筆記本、資料和其他檔案的 Git 存放庫複製到您的工作區：

    ```
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

1. 在命令完成後，按一下 [檔案]**** 窗格中的 [&#8635;]**** 以重新整理檢視，並確認已建立新的 **/users/*your-user-name*/azure-ml-labs** 資料夾。

## 以管線作業身分執行指令碼

使用 Python SDK 建置和提交管線的程式碼會在筆記本中提供。

1. 開啟 **Labs/09/Run a pipeline job.ipynb** 筆記本。

    > 選取 [驗證]****，如果出現通知要求您進行驗證，請遵循必要的步驟。

1. 確認筆記本使用 **Python 3.8 - AzureML** 核心。
1. 在筆記本中執行所有儲存格。

## 刪除 Azure 資源

當您完成探索 Azure Machine Learning 時，應該刪除您所建立的資源，以避免不必要的 Azure 成本。

1. 關閉 [Azure Machine Learning 工作室] 索引標籤，然後返回 Azure 入口網站。
1. 在 Azure 入口網站的 [首頁]**** 上，選取 [資源群組]****。
1. 選取 **rg-dp100-...** 資源群組。
1. 在資源群組的 [概觀]**** 頁面頂端，選取 [刪除資源群組]****。
1. 輸入資源群組名稱以確認要刪除，然後選取 [刪除]****。
