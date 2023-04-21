# JDsearch
JDsearch: A Personalized Product Search Dataset with Real Queries and Full Interactions
## Introduction
We introduce JDsearch, a new dataset based on a popular Chinese online shopping platform JD.com. JDsearch dataset contains about 170,000 users, 12,000,000 products, and 26,000,000 interactions between them.  In this dataset, we record user real queries and reserve all products belonging to diverse categories, no matter they are cold products or popular ones.

Compared with the previous amazon dataset, our JDsearch dataset has following characteristics:

| Characteristic | Amazon datasets    |  JDsearch dataset |
| -------------- | ------------------ | ----------------- |
| Queries        | Artificial queries |  Real queries     |
| Items          | 5-core items belonging to a single category | All items belonging to diverse categories |
| Users          | 5-core users       | All users with various user history lengths |
| Interactions   | Purchase           | Click, add to cart, follow, purchase |

## Citation
If you use the dataset in your work, please cite:
```
@inproceedings{liu2023JDsearch,
     author = {Jiongnan Liu and Zhicheng Dou and Guoyu Tang and Sulong Xu}, 
     title = {JDsearch: A Personalized Product Search Dataset with Real Queries and Full Interactions}, 
     booktitle = {Proceedings of the {SIGIR} 2023}, 
     publisher = {{ACM}}, 
     year = {2023}, 
     url = {https://doi.org/10.1145/3539618.3591900}, 
     doi = {10.1145/3539618.3591900}}
```


## Dataset Statistics

The basic statistics of JDsearch dataset shows as follow:

|                         | JDsearch      | 
| ----------------------- | ------------- |
| #Users                  | 173,831       |
| #Items                  | 12,872,636    | 
| #Interactions           | 26,667,260    | 
| #Test Queries           | 171,728       | 


## Data Content and Format

### Obtain the data 

For now we provide download link via JD Cloud 
[JD Cloud Disk](http://box.jd.com/sharedInfo/3183DCD7A9646677EEC946AFBC5707A7).
The password is **cm1goz**.

The landing page in Chinese is a login page for JD cloud disk. Using the JD mobile application to scan the QR code can obtain the dataset. If you cannot obtain the dataset through this hyperlink, feel free to contact me through liujn@ruc.edu.cn

Some tiny samples are shown in this Github in `release_data_sample.txt` and `product_data_sample.txt`.


### Dataset Files

The upload format of the dataset is .tar.gz, you can decompress it as follows:

`tar -zxvf JDsearch.tar.gz`

there will exist two files in our decompressed folder `JDsearch`: `release_data.txt` and `product_data.txt`.

#### (1) release_data.txt

This file contains the history behavior information, test query, test query candidates and test labels in it. The format of each line of data in this file is：

`query \t candidate_wid_list \t candidate_label_list \t history_qry_list \t history_wid_list \t history_type_list \t history_time_list \n`

- query: the anonymized term ids of the test query.
- candidate_wid_list: the anonymized ids of candidate products displayed under the test query (at most 200 products).
- candidate_label_list: the corresponding label for the candidate products (0.0 for no interaction, 1.0 for click, 2.0 for add to cart, 3.0 for purchase).
- history_qry_list: the sequence of anonymized term ids of queries issued by users. (-1 for the query-less behavior)
- history_wid_list: the sequence of anonymized ids of products in user histories.
- history_type_list: the sequence of interaction levels in  user histories. (including ORD(purchase), CLICK(click), CART(add to cart) and FLW(follow))
- history_time_list: the sequence of time interval after the last behavior in user histories. (the first one is 0) (including the test query, thus the list's length is one more longer than other history lists)

A pseudo example and its explanation is shown as follows:
```
12^X32^X56     456_457_789    3.0_0.0_0.0    323^X328^X196_-1_12^X54  889_256_345    ORD_CLICK_CART 0_12_4_5
```
- `12^X32^X56`: the test query for the user is `12^X32^X56` (`^X` is the seperator).
- `456_457_789`: the candidate product list under the test query contains three products: `456, 457, 789`.
- `3.0_0.0_0.0`: the corresponding labels for the three products are `3.0, 0.0, 0.0`. `3.0` means The first candidate product `456` is **purchased** by the user. `0.0` means the other products `457,789` are **not interacted** with users.
- `323^X328^X196_-1_12^X54`: the history query list of the user is `323^X328^X196, -1, 12^X54`. The middle `-1` means this behavior is a query-less interaction.
- `889_256_345`: the historical interacted products of the user contains three products: `889, 256, 345`.
- `ORD_CLICK_CART`: the corresponding inteaction level for the above three interactions in user history. The user first purchased product `889`, then clicked product `256`, and finally added the product `345` to the cart.
- `0_12_4_5`: the time timestamp intervals between these four behavoirs (including three history interactions and test search) are `12, 4, 5` respectively. `0` is for the first behavior.



#### (2) product_data.txt

This file contains the metadata about products. The format of each line of data in this file is:

`wid \t name \t brand_id \t brand_name \t cate_id_1 \t cate_name_1 \t cate_id_2 \t cate_name_2 \t cate_id_3 \t cate_name_3 \t cate_id_4 \t cate_name_4 \t shop_id`

- wid: the anonymized id of the product.
- name: the anonymized term ids of the product's name.
- brand_id: the anonymized ids of the product's brand.
- brand_name: the anonymized term ids of the product's brand.
- category_id_{1,2,3,4}: the anonymized ids of the the product's four level categories.
- category_name_{1,2,3,4}: the anonymized term ids of the product's four level categories.
- shop_id: the anonymized ids of the product's shop.

#### Hint

For all the list fields (end with \'list\') in our files, we concat the element in them with \'\_\' as seperator. For all the fields containing term id (query, history_qry_list,name,brand_name,category_name_{1,2,3,4}), we concat the term id in text with \'\030\' as seperator (viewed as `^X` in vim).

Noticing that there exists some missing products in our dataset, `product_data.txt` doesn't contain metadata about these products.

In the history part of data, if a user interacted with mutiple products under a one query, these behaviors will be separated to multiple interactions.

Some users' `candidate_label_list` may don't have labels > 0. In our experiments, these users are removed from test part. However, their history can still be used to train models.

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
