# OVERVIEW

This README has the analysis for detecting distributed denial of service (DDoS) attacks and classifying network traffic as nornal traffic (BENIGN) or as attack (DoS) traffic. The research question that this analysis is attempting to answer is "Given network data, does it indicate a DDoS attack or is it normal network traffic?"

Link to repository: [Git Repository](https://github.com/raosalapaka/ml-ddos.git)

Link to github: [Github](https://github.com/raosalapaka/ml-ddos)

Link to jupyter notebook: [Capstone DDoS Project](https://github.com/raosalapaka/ml-ddos/blob/main/capstone_ddos_RS.ipynb), [Capstone DDoS LDAP](https://github.com/raosalapaka/ml-ddos/blob/main/capstone_ddos_ldap.ipynb)

Following datasets were considered for this project:
- [Kaggle comprehensive dataset for ddos attack](https://www.kaggle.com/datasets/amanverma1999/a-comprehensive-dataset-for-ddos-attack)
- [Kaggle ddos dataset](https://www.kaggle.com/datasets/devendra416/ddos-datasets)
- [CICDDoS2019 dataset](http://cicresearch.ca//CICDataset/CICDDoS2019/)

The dataset used for this analysis is the [CICDDoS2019 dataset](http://cicresearch.ca//CICDataset/CICDDoS2019/) which is provided by the [Canadian Institute of Cybersecurity, University of New Brunswick](https://www.unb.ca/cic/datasets/ddos-2019.html). The main reason was the richness of various features that were captured in simulated attacks on many different network protocols (like LDAP, NetBIOS, UDP, Syn/TCP, NTP, etc.). A good analysis on how the data was generated and the taxonomy is captured in this paper: [Developing Realistic Distributed Denial of Service DDoS Attack Dataset and Taxonomy](https://www.researchgate.net/profile/Arash-Habibi-Lashkari/publication/336953914_Developing_Realistic_Distributed_Denial_of_Service_DDoS_Attack_Dataset_and_Taxonomy/links/5de66c9592851c83645fad89/Developing-Realistic-Distributed-Denial-of-Service-DDoS-Attack-Dataset-and-Taxonomy.pdf)


## Github directory structure
ml-ddos is the root git directory and has the following:

    README.md file: this file

    capstone_ddos_RS.ipynb: this is the notebook that does the initial data analysis of the files in CICDDoS2019 dataset. The output from this notebook is sampled data for each protocol to constrain the number of data rows to be more manageable for both git and the analysis

    capstone_ddos_ldap.ipynb: this notebook does the initial analysis on the ldap dataset and compares various models

    ddos_dataset_and_taxonomy.pdf: the paper describing the datset

    data/CICDDoS2019/sampled_data: has the data files split in chunks of 3 files for each protocol. This is done to work around the size constraints of git storage

# Classifying DDoS network attacks

## Summary

**CRISP-DM** framework was followed to do the analysis.

- **Business Understanding**: DDoS attacks can disrupt a cloud service which in turn can lead to multiple issues that can pose a material revenue risk for a business. It can lead to many issues including which could have direct impact on businesss, not limited to the following:

    -  increase in service outage resulting in erosion of customer trust
    - loss of revenue (sales may be disrupted because of outage),
    - trigger SLA breaches for paying customers (especially enterprise customers) which usually carry some kind of fines
    - reputation and brand damage
    - increased engineering costs in mitigating the issue (which could be prevented by early detection systems)
    - lately there is also ransomware scenarios where attackers can demand compensation for stopping attacks

- [Data Understanding](#Data-Understanding)
    - The dataset has 88 columns per protocol file (mostly numeric)
    - The dataset has no missing values
    - The target column (Label) is not balanced

- [Data Preparation](#Data-Preparation)
    - cleaned data by dropping columns
    - cleaned data by imputing values marked as float.inf
    - converted target column to binary

- [Modeling](#Modeling)
    - Split data with 25% reserved for test
    - Stratified data by y column
    - Ran estimators both LogisticRegression, and ensemble estimators (Bagging and Boosting)
    - For now only ran Base constructors with default parameters

- [Evaluation](#Evaluation)
    - Used metrics of accuracy, roc-auc, precision-recall to evaluate
    - The estimators all performed extremely well on the test data for ldap
    - The estimators do not generalize well (poor performance on protocol like UDPLag)
    - Precision was almost perfect - likely because of the large numbers of rows skewed to attack data

- [Deployment](#Deployment)
    - best model was Bagging and RandomForest classifiers
    - Deployment section identifies the important features

- [Future work](#Future-work)
    - several work items identified to find tune and better generalize the estimators
    - eventually, use the flows in a specific time span (using timestamp column) to adapt the data to detect real time attacks using time-series models 

## Details

### Data Understanding

### Initial exploration

<mark>**Background**</mark>: The data files downloaded from the CICDDoS2019 dataset has the attack data divided by protocols. The approach that was taken was to generate simulated attack data for protocols: DNS, LDAP, MSSQL, NetBIOS, NTP, SNMP, SSDP, UDP, TCP/Syn, TFTP. Features (88 in number) were aggregated from this data (some of the columns are generated) based on the flows which are identified by quintuble <Src IP Address, Destination IP Address, Src Port, Destination Port, Protocol>

Some observations from initial exploration and the actions taken (this is in the capstone_ddos_RS.ipynb notebook)

- The raw files for each protocol were huge with millions of rows making it not practical to analyze with compute resources available. ***Action taken***: The rows were read from each protocol file, 200_000 rows at a time. The data frame lists were sampled so that each protocol had 300_000 rows for analysis. The sampled data was further split into 3 files of approximated 100_000 rows to take care of git storage constraints
- Since the attack rows were genereated using automation and the normal BENIGN data was generated manually, the data for each file was very attack heavy (almost only 1% of total rows). ***Action Taken***: Aggregated BENGIGN rows from each protocol dataset and used the aggregated rows to analyze the data for each protocol. This resulted in BENIGN rows to be approximately 18% of total rows

    ![alt text](<Screenshot 2026-02-15 at 7.50.41 PM.png>)
- Some of the columns had white space which was removed to clean up the data
- Mostly there were no missing values.

### Data Preparation

For the initial analysis, LDAP data was analyzed. Following steps were taken to prepare the data

- On analyzing the features, following columns were dropped (reasons explained)
    - Source IP, Destination IP: Since the rows were aggregated by flow ids which include these in the quintuple, the specific IP Addresses by themselves do not provide any signal
    - SimillarHTTP: mainly seems to have certificate information for connections which are not pertinent to the network layers 3/4 that is being analyzed
    - Flow ID: The ID by itself is just a number and does not provide any information. The columns are aggregated per flow which is more meaningful
    - Timestamp: For now only static analysis is being done, meaning there is no real-time detection of the attacks. Timestamp will be used in future when features like bursty traffic, etc. can be analyzed to do real time detection using time series data
    - Unnamed: this is again an id that seems to be left behind in the dataset and does not provide any signal

- Some columns like 'Flow Bytes/s' had float32.inf values which had to be cleaned up. There were approximated 4K rows with these values. Imputed the values for these cells by replacing with the max value for that column
- Converted the binary 'Label' column (target column) to 0 and 1 values where 0 value denotes BENIGN traffic 

### Modeling

Following classifiers with default parameters were considered for the initial analysis of the ldap data:

 - LogisticRegression
 - BaggingClassifier
 - RandomForestClassifier
 - AdaBoostClassifier
 - GrandientBoostingClassifier

The estimators were trained with 75% of the available data and tested for accuracy with 25%. The data was scaled with StandardScaler for LogisticRegression estimator

All estimators did very well with this data. Following table gives a summary of the results (sorted on descending accuracy)

![alt text](<Screenshot 2026-02-15 at 9.08.06 PM.png>)

### Evaluation

Following metrics were considered while evaluating the estimators:

- accuracy
- roc-auc
- precision recall

Results:

- All classifiers performed very well (>99.9% accuracy). 
- BaggingClassifier and RandomForests performed slightly better

Following shows the confusion matrices and the roc-auc and precision-recall curves for the different estimators

![alt text](<Screenshot 2026-02-15 at 9.14.54 PM.png>)

Following shows the scores and plot comparing the scores for the different estimators

![alt text](<Screenshot 2026-02-15 at 9.16.24 PM.png>)
Conclusions:

- The data seems to be highly separable on the features captured in the dataset
- Precision/Recall is almost perfect
- The above could mean that this model will likely not generalize well

To test, ran the model against test data generated from a different protocol file. The results were not good confirming the belief that the models trained on one protocol file do not generalize well to other protocols.

Result of the estimators trained on ldap protocol when run on DNS, slightly worse results with best accuracy ~96%:

![alt text](<Screenshot 2026-02-15 at 9.52.54 PM.png>)

Result of the estimators trained on ldap protocol and when on UDPLag, very bad results - with best accuracy ~25%:

![alt text](<Screenshot 2026-02-15 at 9.53.51 PM.png>)

### Deployment

The ensemble classifiers which performed the best identified the following as the most important features:

![alt text](<Screenshot 2026-02-15 at 9.45.26 PM.png>)

### Future work

Several items still need to be done:

- analyze to find attributes that separate the data so well in the dataset. Attempt to generalize the models without the features that separate the data
- find an approach to generalize the estimators to other protocol data
- analyze other protocols data with estimators
- fine tune with gridsearch to get better results
- Organize git directory better for this project


