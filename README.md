# citationgendergap
A tool for calculating the gender ratio of papers cited in academic studies

- Output of this tool: The ratio of male and female authors in an article & uncertainty
<br>

# Tool No.1
- Input from user: PDF with full names (MLA citation style)
- What will this tool do? <br>
Extract names and send names to OpenAI API <br>

# Tool No.2 
- Input from user: PDF with partial names (APA citation style like Swift, T) + DOIs
- What will this tool do? <br>
Step 1: Utilize the OpenCitation API to retrieve names based on the DOI. <br>
Step 2: Tool No.1 <br>

# Tool No.3 
- Input from user: PDF with partial names (APA citation style like Swift, T)  without DOIs
- What will this tool do? <br>
Step 1: Conduct title search to get DOI of this PDF (Google Schoolar) <br>
Step 2: Tool No.2 <br>
Step 3: Tool No.1 <br>

# Tool No.4
- Input from user: DOI of an article
- What will this tool do? <br>
Step 1: Utilize the OpenCitation API to retrieve names based on the DOI. <br>
Step 2: Tool No.1 <br>

# Existing tools to guess gender based on names
## 1. For all languages  
- [model on huggingface: self-claimed 99.17% accuracy, but it is overconfident and biased](https://huggingface.co/datalearningpr/name_to_gender)  
## 2. Chinese name 
- dataset:  
  - [Chinese, English, Japanese](https://github.com/Carina999/Chinese-Names-Corpus)  
  - [20,000,000 people, 9000+ characters, raw data inaccessible](https://github.com/Carina999/ngender/blob/master/ngender/charfreq.csv)  
  
- method:
  - [self-claimed 90% accuracy, randomforest, GBDT, LR, MLP](http://blog.csdn.net/u013719780?viewmode=contents)  
  - [CN](https://blog.csdn.net/Island__lee/article/details/123336752)  
  - [Laplace Smoothing](https://blog.csdn.net/smile_Shujie/article/details/88757738)  
## 3. English


# opencitations API
## API
- input (metadata)
```python
import requests
#cited entity is doi=10.1108/jd-12-2013-0166
API_CALL = "https://opencitations.net/index/coci/api/v1/metadata/10.1108/jd-12-2013-0166" 
#API_CALL = "https://opencitations.net/index/coci/api/v1/metadata/10.1108/jd-12-2013-0166__10.1007/s11192-019-03217-6" # 2 papers
response = requests.get(API_CALL)
# Check if the request was successful 
if response.status_code == 200:
    metadata = response.text
    print(metadata)
else:
    print(f"Request failed with status code: {response.status_code}")
```
- output
1. issue = the number of the issue in which the bibliographic entity has been published
2. reference = references of the paper in the API_CALL 10.1001/jama.295.1.90 (This paper will be referred to as the cited entity)
3. oa_link = the link to the Open Access version of the bibliographic entity (if available)
4. citation_count = How many papers in the dataset have cited this particular cited entity?
5. author=last name, first name, orcid (for Gray, Tanya; Shotton, David, they only have omid:ra/06180954031, and omid is omitted in the API response)
6. volume = the number of the volume in which the bibliographic entity has been published
7. citation[sum(citations)=31] = all DOIs of the citing entities that are citing the cited entity
8. source_id =  the semicolon-separated list of identifiers referring to the source where the cited entity has been published
```
[
    {
        "issue": "2", 
        "reference": "10.1001/jama.295.1.90; 10.1002/asi.4630240406; 10.1002/(sici)1097-4571(198909)40:5<342::aid-asi7>3.0.co;2-u; 10.1007/bf02457980; 10.1007/s10579-012-9211-2; 10.1007/s11192-009-0021-2; 10.1016/j.websem.2012.08.001; 10.1016/j.websem.2013.05.001; 10.1023/a:1021919228368; 10.1038/35079151; 10.1038/493159a; 10.1038/495437a; 10.1038/502295a; 10.1038/502298a; 10.1042/bj20091474; 10.1073/pnas.0407743101; 10.1087/2009202; 10.1101/sqb.1972.036.01.015; 10.1108/eum0000000007123; 10.1108/jd-07-2012-0082; 10.1126/science.149.3683.510; 10.1136/bmj.a568; 10.1136/bmj.b2680; 10.1145/1498765.1498780; 10.1177/030631277400400102; 10.1177/030631277500500106; 10.1371/journal.pcbi.0010034; 10.1371/journal.pcbi.1000361; 10.1371/journal.pntd.0000228; 10.1371/journal.pone.0000308; 10.1523/jneurosci.0003-08.2008; 10.1525/bio.2010.60.5.2; 10.3115/1610075.1610091; 10.5210/fm.v2i4.522; 10.5539/ass.v9n5p18; 10.5860/crln.73.10.8846; 10.7717/peerj.175; 10.1007/978-3-319-03524-6_29; 10.1007/978-3-540-89704-0_17; 10.1007/978-3-642-41242-4_6; 10.1145/1816123.1816198; 10.1145/2362499.2362502; 10.1145/2494266.2494271; 10.1145/1217821.1226695",
        "oa_link": "",
        "citation_count": "31",
        "year": "2015",
        "author": "Peroni, Silvio, 0000-0003-0530-4305; Dutton, Alexander, 0000-0003-1448-3114; Gray, Tanya; Shotton, David",
        "title": "Setting Our Bibliographic References Free: Towards Open Citation Data",
        "doi": "10.1108/jd-12-2013-0166",
        "volume": "71",
        "page": "253-277",
        "source_title": "Journal Of Documentation",
        "citation": "10.1057/s41275-017-0070-x; 10.7554/elife.32822; 10.1093/bib/bbx057; 10.1101/108480; 10.1142/s021964921850034x; 10.1145/3197026.3197050; 10.1177/0961000615616450; 10.1186/s12859-019-2607-x; 10.1007/978-3-030-00668-6_21; 10.1007/978-3-030-01379-0_9; 10.1007/978-3-319-17966-7_10; 10.1007/978-3-319-58694-6_23; 10.1007/978-3-319-68204-4_19; 10.1007/978-3-319-73165-0_24; 10.1007/978-3-319-90548-8_7; 10.3233/ds-190016; 10.3233/ds-190019; 10.3233/sw-160224; 10.3233/sw-180307; 10.3346/jkms.2015.30.11.1545; 10.35668/2520-6524-2019-4-10; 10.1007/978-3-030-30796-7_31; 10.1007/978-3-030-30796-7_8; 10.1007/978-3-030-61244-3_16; 10.1371/journal.pone.0238801; 10.2139/ssrn.3081354; 10.1016/j.techsoc.2018.03.005; 10.7717/peerj-cs.421; 10.1162/qss_a_00183; 10.3390/app12063203; 10.1109/mcse.2019.2952840",
        "source_id": "issn:0022-0418"
    }
]
```

- [publications about opencitations](https://direct.mit.edu/qss/article/1/1/428/15580/OpenCitations-an-infrastructure-organization-for) 
[download dataset](http://opencitations.net/download)

- query data
![image](https://github.com/aaronrkaufman/citationgendergap/assets/89952811/3d4367d2-62b7-4bff-b925-47d00ca5154a)

# citationsr



# paperpile




