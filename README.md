# End-to-End-Azure-Data-Engineering-Project
End-to-End Azure Data Engineering Project using ADF, Databricks, Synapse, and Power BI.

## Table of Contents

- [Overview](#overview)
- [Project Highlights](#project-highlights)
- [Architecture](#architecture)
- [Technologies Used](#technologies-used)
- [Prerequisites](#prerequisites)
- [Setup Instructions](#setup-instructions)
- [Project Structure](#project-structure)
- [Usage](#usage)
- [Contributing](#contributing)
- [License](#license)
- [Acknowledgements](#acknowledgements)
- [Contact](#contact)

## Overview

Welcome to the **Azure Data Engineering Project**! This project is designed to aid aspiring data engineers in preparing for the **DP-203: Microsoft Certified Azure Data Engineer Associate** exam. It offers a comprehensive, real-time solution that leverages key Azure services to build an end-to-end data pipeline, providing hands-on experience with essential data engineering tools and best practices.

## Project Highlights

- **Azure Data Factory (ADF):** Created dynamic pipelines to efficiently ingest data from GitHub APIs.
- **Medallion Architecture:** Organized data into Bronze (Raw), Silver (Transformed), and Gold (Serving) layers to ensure quality and accessibility.
- **Azure Databricks:** Performed complex data transformations using Spark to optimize data processing workflows.
- **Azure Synapse Analytics:** Built a robust data warehouse for detailed data analysis and reporting.
- **Power BI Integration:** Connected Synapse Analytics with Power BI to create interactive and insightful dashboards.
- **Dataset Management:** Managed comprehensive datasets with schema validation and data governance best practices.
- **Dynamic Pipelines:** Implemented parameterization and looping to handle multiple datasets dynamically.
- **Real-Time Scenarios:** Addressed practical challenges commonly encountered in data engineering roles and interviews.


## Architecture

The project follows the **Medallion Architecture**, which organizes data into three distinct layers to maintain quality, scalability, and accessibility:

1. **Bronze (Raw) Layer:**
   - **Purpose:** Store raw data as-is from the source without any transformations.
   - **Source:** GitHub APIs.
   - **Tools:** Azure Data Factory.

2. **Silver (Transformed) Layer:**
   - **Purpose:** Clean, transform, and enrich the raw data for analysis.
   - **Tools:** Azure Databricks.

3. **Gold (Serving) Layer:**
   - **Purpose:** Serve refined data to stakeholders through a data warehouse.
   - **Tools:** Azure Synapse Analytics, Power BI.

![image](https://github.com/user-attachments/assets/487fc52d-b1f4-4178-ac28-d0497600ca95)


## Technologies Used

- **Azure Data Factory (ADF):** Orchestration and data integration.
- **Azure Databricks:** Data processing and transformation.
- **Azure Synapse Analytics:** Data warehousing and analytics.
- **Power BI:** Data visualization and reporting.
- **GitHub APIs:** Data source for ingestion.
- **Azure Data Lake Storage Gen2:** Storage solution for raw and processed data.

## Prerequisites

Before you begin, ensure you have met the following requirements:

- **Azure Account:** An active Azure subscription. [Create a free Azure account](https://azure.microsoft.com/free/) if you don't have one.
- **GitHub Account:** To access and interact with GitHub APIs.
- **Basic Knowledge:** Familiarity with Azure services, SQL, Spark, and data engineering concepts.
- **Tools:**
  - **Azure Portal Access**
  - **Azure Data Factory Studio**
  - **Azure Databricks Workspace**
  - **Azure Synapse Workspace**
  - **Power BI Desktop**

## Setup Instructions

Follow these steps to set up the project environment:

### 1. Create an Azure Data Factory Instance

1. **Navigate to Azure Portal:**
   - Log in to the [Azure Portal](https://portal.azure.com/).

2. **Create Data Factory:**
   - Click on **"Create a resource"**.
   - Search for **"Data Factory"** and select it.
   - Click **"Create"** and fill in the required details:
     - **Subscription:** Choose your Azure subscription.
     - **Resource Group:** Create a new resource group or select an existing one.
     - **Name:** Enter a unique name (e.g., `ADF-Project-YourName`).
     - **Region:** Select your preferred region.
   - Click **"Review + create"** and then **"Create"**.

### 2. Set Up Azure Data Lake Storage Gen2

1. **Create Storage Account:**
   - In the Azure Portal, click on **"Create a resource"**.
   - Search for **"Storage account"** and select it.
   - Click **"Create"** and fill in the details:
     - **Subscription:** Your Azure subscription.
     - **Resource Group:** Same as Data Factory.
     - **Storage account name:** Unique name (e.g., `datalakeyourname`).
     - **Performance:** Standard.
     - **Account kind:** StorageV2 (general-purpose v2).
     - **Replication:** Locally-redundant storage (LRS) or as per your preference.
     - **Data Lake Storage Gen2:** Enable hierarchical namespace.
   - Click **"Review + create"** and then **"Create"**.

2. **Create Containers:**
   - Navigate to your storage account.
   - Under **"Data storage"**, click on **"Containers"**.
   - Create three containers named:
     - `bronze`
     - `silver`
     - `gold`

### 3. Configure Linked Services in Azure Data Factory

1. **Access ADF Studio:**
   - Navigate to your Data Factory instance.
   - Click on **"Launch Studio"** to open Azure Data Factory Studio.

2. **Create Linked Services:**
   - Go to the **"Manage"** tab.
   - Under **"Connections"**, select **"Linked services"**.
   - Click **"+ New"** and create the following linked services:
     - **HTTP Linked Service:**
       - **Connector:** HTTP
       - **Name:** `HTTP_LinkedService`
       - **Base URL:** `https://raw.githubusercontent.com/YourGitHubUsername/YourRepoName/main/`
       - **Authentication Type:** Anonymous
       - **Test Connection** and **Create**.
     - **Azure Data Lake Storage Gen2 Linked Service:**
       - **Connector:** Azure Data Lake Storage Gen2
       - **Name:** `ADLS_LinkedService`
       - **Storage Account Name:** Select your storage account.
       - **Test Connection** and **Create**.

### 4. Create Datasets

1. **Source Dataset (HTTP):**
   - Go to the **"Author"** tab.
   - Under **"Datasets"**, click **"+ New"**.
   - Select **"HTTP"** as the data source.
   - **Name:** `DS_HTTP`
   - **Linked Service:** `HTTP_LinkedService`
   - **File Format:** DelimitedText (CSV)
   - **Relative URL:** Leave blank for parameterization.
   - **Schema:** Import schema from a sample file if available.
   - **Parameters:** Add a parameter for `relativeURL`.
   - **Dynamic Content:** Use the `relativeURL` parameter in the dataset.

2. **Sink Dataset (ADLS Gen2):**
   - Click **"+ New"** under **"Datasets"**.
   - Select **"Azure Data Lake Storage Gen2"**.
   - **Name:** `DS_ADLS`
   - **Linked Service:** `ADLS_LinkedService`
   - **File Format:** DelimitedText (CSV)
   - **Folder Path:** Parameterized for Bronze, Silver, and Gold layers.
   - **Parameters:** Add parameters for `folderPath` and `fileName`.
   - **Dynamic Content:** Use the parameters in the dataset.

### 5. Build Pipelines

1. **Static Pipeline (Initial Setup):**
   - Under **"Pipelines"**, click **"+ New pipeline"**.
   - **Name:** `CopyRawData`
   - **Activities:**
     - **Copy Activity:**
       - **Name:** `Copy_Raw_Data`
       - **Source:** `DS_HTTP` with the specific `relativeURL`.
       - **Sink:** `DS_ADLS` pointing to the `bronze` container.
   - **Debug and Publish:** Test the pipeline and publish your changes.

2. **Dynamic Pipeline:**
   - **Name:** `Dynamic_Copy_Pipeline`
   - **Parameters:** Add parameters for file names and paths.
   - **Activities:**
     - **ForEach Activity:**
       - **Items:** Array of file names (e.g., `["products.csv", "customers.csv"]`).
       - **Inside ForEach:**
         - **Copy Activity:** Use parameters to dynamically set `relativeURL` and `sink` paths.
   - **Debug and Publish:** Test the dynamic pipeline with multiple files.

### 6. Transform Data with Azure Databricks

1. **Create Databricks Workspace:**
   - In the Azure Portal, create an **Azure Databricks** workspace.
   - Launch the workspace and create a new cluster.

2. **Develop Transformation Scripts:**
   - Import data from the `silver` layer.
   - Perform data cleaning, enrichment, and transformations using Spark.
   - Write transformed data to the `gold` layer.

### 7. Set Up Azure Synapse Analytics

1. **Create Synapse Workspace:**
   - In the Azure Portal, create an **Azure Synapse Analytics** workspace.
   - Set up necessary SQL pools.

2. **Ingest Data:**
   - Load transformed data from the `gold` layer into Synapse.
   - Create necessary tables and views for analysis.

### 8. Integrate with Power BI

1. **Connect Synapse to Power BI:**
   - Open **Power BI Desktop**.
   - Connect to **Azure Synapse Analytics**.
   - Import data and create interactive dashboards.

2. **Publish Dashboards:**
   - Publish your Power BI reports to the Power BI service for sharing with stakeholders.



