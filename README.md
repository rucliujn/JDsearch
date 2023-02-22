# JDsearch
JDsearch: A Personalized Product Search Dataset with Real Queries and Full Interactions
### Introduction
We intoduce JDsearch, a new dataset based on a popular Chinese online shopping platform JD.com. JDsearch dataset contains about 170,000 users, 12,000,000 products, and 22,000,000 interactions between them.  In this dataset, we record user real queries and reserve all products belonging to diverse categories, no matter they are cold products or popular ones.

Compared with the previous amazon dataset, our JDsearch dataset has following characteristics:

| Characteristic | Amazon datasets    |  JDsearch dataset |
| -------------- | ------------------ | ----------------- |
| Queries        | Artificial queries |  Real queries     |
| Items          | 5-core items belonging to a single category | All items belonging to diverse categories |
| Users          | 5-core users       | All users with various user history lengths |
| Interactions   | Purchase           | Click, add to cart, follow, purchase |

### Dataset Statistics

The basic statistics of JDsearch dataset shows as follow:

|                         | JDsearch      | 
| ----------------------- | ------------- |
| #Users                  | 173,831       |
| #Items                  | 12,872,636    | 
| #Interactions           | 26,667,260    | 
| #Test Queries           | 171,728       | 


### Data Content and Format

#### Obtain the data 

For now we provide download link via JD Cloud 
[JD Cloud Disk](http://box.jd.com/sharedInfo/CF73552490211634EEC946AFBC5707A7).
The password is **i4wq3c**.

#### Dataset Files

The upload format of the dataset is .tar.gz, you can decompress it as follows:

`tar -zxvf JDsearch.tar.gz`

there will exist two files in our decompressed folder `JDsearch`: `release_data.txt` and `product_data.txt`.

The first one is `release_data.txt` which contains the history behavior information, test query, test query candidates and test labels in it. The format of each line of data in this file is：

`query \t candidate_wid_list \t candidate_label_list \t history_qry_list \t history_wid_list \t history_type_list \t history_time_list \n`

- query: the anonymized term ids of the last query.
- candidate_wid_list: the anonymized ids of candidate products displayed under the test query (at most 200 products).
- candidate_label_list: the corresponding label for the candidate products (0.0 for no interaction, 1.0 for click, 1.5 for add to cart, larger than 2.0 for purchase).
- history_qry_list: the sequence of anonymized term ids of queries issued by users. (-1 for the query-less behavior)
- history_wid_list:the sequence of anonymized ids of products in user histories.
- history_type_list: the sequence of interaction levels in  user histories. (including ORD(purchase), CLICK(click) and CART(add to cart))
- history_time_list: the sequence of time interval after last behavior in user histories. (the first one is 0) 

The second one is `product_data.txt` which contains the metadata about products. The format of each line of data in this file is:

`wid \t name \t brand_id \t brand_name \t cate_id_1 \t cate_name_1 \t cate_id_2 \t cate_name_2 \t cate_id_3 \t cate_name_3 \t cate_id_4 \t cate_name_4 \t shop_id`

- wid: the anonymized id of the product.
- name:　the anonymized term ids of the product's name.
- brand_id: the anonymized ids of the product's brand.
- brand_name: the anonymized term ids of the product's brand.
- category_id_{1,2,3,4}: the anonymized ids of the the product's four level categories.
- category_name_{1,2,3,4}: the anonymized term ids of the product's four level categories.
- shop_id: the anonymized ids of the product's shop.

For all the list fileds (end with \'list\') in our files, we concat the element in them with \'\_\' as seperator. For all the fields containing term (history_qry_list,name,brand_name,category_name_{1,2,3,4}), we concat the term id in text with \'\030\' as seperator.

Noticing that there exists some missing products in our dataset, `product_data.txt` doesn't contain metadata about these products.

### Baseline models

We test several existing ad-hoc and personalized product search models in our dataset.

Model | MRR | Prec | NDCG |
----- | --- | ---- | ---- |
BM25 | 0.1114 | 0.0402 | 0.0940 |
QEM | 0.1774 | 0.0728 | 0.1705  |
HEM | 0.1955 | 0.0847 | 0.1905 |
DREM | 0.1647 | 0.0632 | 0.1578 | 
HGN | 0.1662 | 0.0634 | 0.1591 |
AEM | 0.1971 | 0.0851 | 0.1920 |
ZAM | 0.1969 | 0.0849 | 0.1920 |
TEM | **0.2229** | **0.1049** | **0.2192** | 

### License

This repository is liciensed under Apache-2.0 License.

The JDsearch dataset is liciensed under [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/).



### FAQ




### 
