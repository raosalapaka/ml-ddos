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
