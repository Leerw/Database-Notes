# 第六章：数据库设计和E-R模型
## 概念设计
定义了数据库中表示的实体、实体的属性、实体之间的联系、以及实体和联系上的约束
### 从抽象数据到数据库实现的转换过程
#### 逻辑设计阶段
实现数据模型通常是关系数据模型，该阶段通常包括将以实体-联系模型定义的概念模式映射到关系模式
#### 物理设计阶段
指明数据库的物理特征，包括文件组织格式和索引结构的选择
## 设计选择
在设计数据库模式的时候需要避免的两个主要的缺陷：  
冗余（根本原因是可能会导致数据的不一致）和不完整
## 实体-联系模型
三个基本概念：实体集、联系集和属性  
参与：实体集之间的关联：实体集$E1,E2,E3,...,En$参与联系集R  
联系实例：在锁建模的显式世界中命名实体间的<b>一个关联</b>，比如一个教师ID位9527的instructor实体Katz和一个学生ID为12345的student实体Shankar参与到advisor的一个联系实例中。这一联系实例表示在大学中教师Katz指导学生Shankar  
描述属性：<b>联系集的描述属性</b>，比如实体集instructor和student之间的联系集advisor。我们可以将属性date与该联系关联起来，称为具有描述属性date的联系集advisor  
<b>给定一个联系集中的一个联系实例必须是由其参与实体唯一标识的，而不必使用其描述属性</b>  
### 属性
简单属性：不能划分为更小的部分  
复合属性：可以再划分为更小的部分（复合属性可以是有层次的）  
单值属性：属性对一个特定实体只有单独的一个值  
多值属性：对某个特定实体而言，一个属性可能会对应于一组值  
派生属性：可以从别的实体或相关属性派生出来，比如年龄age，就是出生日期birth派生出来的属性
## 约束
#### 映射基数
一对一  
一对多  
多对多  
#### 参与约束
全部参与：如果<b>实体集E</b>中的每个实体都参与到<b>联系集R</b>的至少一个联系中，实体集E在联系集R中的参与称为全部的  
部分参与：E中只有部分实体参与到R的联系中  
#### 码
码：一个足以区分每个实体的属性集。关系模式中的超码、候选码、主码的概念同样适用于实体集  
##### 联系集的主码
<li>如果联系集R没有属性（描述属性）与之关联，那么参与到R中的实体集E1,E2,...,En的主码的并集描述了这个关系R</li>
<li>如果联系集R有属性（描述属性），用A来表示这个属性集，则$E_i$的主码的并集和A的并集描述了这个关系R</li>
<li>如果一个联系集R是多对多的，那么R的主码是参与到R的实体集的主码的并集组成</li>
<li>如果一个联系集R是多对一的，那么R的主码是“多”对应的实体集的主码，比如，如果联系是从student到instructor多对一的，也就是一个instructor可以对应多个student，那么student的主码就是advisor的主码，如果联系是从instructor到student多对一的，也就是一个student可以对应多个instructor，那么advisor的主码就是instructor的主码</li>
<li>如果一个联系集是R一对一的，那么R的主码是两个实体集中的任意一个候选码</li>
## 具有复杂属性的强实体集的表示
### 复合属性
> 我们并不为复合属性<b>自身<b>创建一个单独的属性，而是将它拆分为若干个单独的属性  
```
instructor:
ID
name
    first_name
    middle_initial
    last_name
address
    street
        street_number
        street_name
        apt_number
    city
    state
    zip
{phone_number}
date_of_birth
age ()
```
(其中，instructor的主码是ID)
可以改为
```
instructor(ID, first_name, middle_initial, last_name,  
street_number, street_name, apt_number, city, state, zip, date_of_birth)
```
而把多值属性单独拿出来创建新的关系模式
```
instructor_phone(ID, phone_number)
```
(其中instructor_phone的主码是ID和phone_number)  
注意：由多值属性新创建的关系模式的主码是它所包含的所有属性
### 多值复合属性
>time_slot(<u>time_slot_id</u>, {(day, start_time, end_time)})  

这个实体集有两个属性，一个是主码time_slot_id， 一个是多值属性，而且这个多值属性还是复合的，先按多值属性进行处理，创建一个新的关系模式，假设叫time(<u>time_slot_id</u>, (<u>day</u>, <u>start_time</u>, <u>end_time</u>),然后按复合属性进行处理，new_time(<u>time_slot_id</u>, <u>day</u>, <u>start_time</u>, <u>end_time</u>)  
所以我们最终拥有了两个关系模式
> time_slot(<u>time_slot_id</u>)
> new_time(<u>time_slot_id</u>, <u>day</u>, <u>start_time</u>, <u>end_time</u>)  
很明显第一个关系模式是毫无意义的，该实体集的关系模式只包含一个属性，就是它的主码。可以删掉这个关系，保留新创建的关系。也就是  
> time_slot(<u>time_slot_id</u>, <u>day</u>, <u>start_time</u>, <u>end_time</u>)  

最后，书上是这样说的  

> Although not represented as a constraint on the E-R diagram, we know that there cannot be two meetings of a class that start at the same time of the same day-of-the-week but end at different times; based on this constraint, end time has been omitted from the primary key of the time slot schema.  

## 弱实体集表示
弱实体集A，拥有属性集$a1, a2, ... an$  
A的依赖强实体集B，拥有属性$b1, b2, ... bn$  
则联系集R的属性集为$\{a1, a2, ..., an\}$∪$\{b1, b2, ..., bn\}$  
弱实体集的ER图以虚下划线来标识，关联弱实体集和强实体集的联系集以双菱形表示，弱实体集的主码是由标识实体集（强实体集）的主码加上该弱实体集的分辨符构成。还有外码约束，保证弱实体的每一个元组都有一个表示想听强实体的元组与之对应

## 联系集的表示
### 属性
R是实体集A和实体集B的联系集，则R的属性是A的属性和B的属性的并集
### 主码
二元联系集：  
一对一：任意一个实体集的主码作为主码  
多对多：参与实体集的主码的并集作为主码  
多对一或一对多：多的实体集的主码作为主码  
n元联系集：  
边上没有箭头的：所有参与的实体集的主码的并集作为主码  
因为一个联系集只允许一个箭头，所以对于边上有一个箭头的，选择不在箭头一侧的实体集的主码作为主码  
R上还有外码约束:  
> sec_cource有参照section和cource的外码，teaches有参照instructor和student的外码，takes有参照student和section的外码  
## 模式冗余
cource(<u>cource_id</u>, title, credits)  
section(<u>cource_id</u>, <u>sec_id</u>, <u>semester</u>, <u>year</u>)  
sec_course(<u>cource_id</u>, <u>sec_id</u>, <u>semester</u>, <u>year</u>)  
如上，section和sec_course的属性一摸一样，那就必然造成了冗余，所以，一般情况下，连接弱实体集与其依赖的强实体集的模式是冗余的，而且在基于E-R的关系数据库中不必给出  
## 模式合并
1、ARB，多对一，A全部参与  
我们可以得到3个关系模式：A、B、AB，则把AB合并到A中， consisting of the union of attributes of both schemas  
2、ARB，一对一  
可以和参与联系的任意一个实体集的模式进行合并  
3、部分参与的  
也可以用空值来进行模式的合并  
4、外键约束  
We drop the constraint referencing the entity set into whose schema the relationship set schema is merged, and add the other foreign-key constraints to the combined schema. For example, inst_dept has a foreign key constraint of the attribute dept_name referencing the department relation. This foreign constraint is added to the instructor relation when the schema for inst dept is merged into instructor