# MySQl进阶操作2

## 1.索引选择

+ 唯一索引可以节约性能,普通索引在查到符合值后仍会继续检测,知道发现不符合的.

+ 当有多个索引时,有时mysql会选错索引导致多次扫描回表,可以使用analyze table 更新统计信息.

  + 可以使用**force index**强行指定使用的索引

  + ```
    select * from T force index(a) where a between 1 and 1000
    ```

+ 索引创建

  ```
  alter table T add index index1(email);#扫描次数少,使用空间多
  alter table SUser add index index2(email(6));#使用前6个字节做索引
  ```

  + 对于身份证号,可以倒者存储取前几个字节建立索引,减少消耗,查询时使用reverse

  + ```
    select field_list from t where id_card = reverse('input_id_card_string');
    ```

+ 脏页:数据页和磁盘数据不一样,redo更新完,而磁盘还没更新.

## 2.数据修改问题

+ 数据删除流程:innoDB删除数据是标记删除,然后可以复用,存储数据是按页存储的,因次删除数据不会回收空间,drop table删除表才可以.

+ 删除插入会导致产生空洞.

  ![img](https://static001.geekbang.org/resource/image/80/ea/8083f05a4a4c0372833a6e01d5a8e6ea.png)

由于page A满了，再插入一个ID是550的数据时，就不得不再申请一个新的页面page B来保存数据了。页分裂完成后，page A的末尾就留下了空洞（注意：实际上，可能不止1个记录的位置是空洞）。