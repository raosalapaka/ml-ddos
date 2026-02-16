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

- [Data Preparation](#Data-Preparation)

- [Modeling](#Modeling)

- [Evaluation](#Evaluation)

- [Deployment](#Deployment)

- [Future work](#Future-work)

## Details

### Data Understanding

### Initial exploration

<mark>**Background**</mark>: The data files downloaded from the CICDDoS2019 dataset has the attack data divided by protocols. The approach that was taken was to generate simulated attack data for protocols: DNS, LDAP, MSSQL, NetBIOS, NTP, SNMP, SSDP, UDP, TCP/Syn, TFTP. Features (88 in number) were aggregated from this data (some of the columns are generated) based on the flows which are identified by quintuble <Src IP Address, Destination IP Address, Src Port, Destination Port, Protocol>

Some observations from initial exploration and the actions taken (this is in the capstone_ddos_RS.ipynb notebook)

- The raw files for each protocol were huge with millions of rows making it not practical to analyze with compute resources available. ***Action taken***: The rows were read from each protocol file, 200_000 rows at a time. The data frame lists were sampled so that each protocol had 300_000 rows for analysis. The sampled data was further split into 3 files of approximated 100_000 rows to take care of git storage constraints
- Since the attack rows were genereated using automation and the normal BENIGN data was generated manually, the data for each file was very attack heavy (almost only 1% of total rows). ***Action Taken***: Aggregated BENGIGN rows from each protocol dataset and used the aggregated rows to analyze the data for each protocol. This resulted in BENIGN rows to be approximately 18% of total rows

    ![alt text](<Screenshot 2026-02-15 at 7.50.41 PM.png>)


### Data Preparation



### Modeling



### Deployment



### Future work



