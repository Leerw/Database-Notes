---
layout: post
title: 数据库设计
date: 2017-11-08 14:33:00
categories: 数据库
---

数据库设计和E-R模型  

<!--more-->


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

### 映射基数
一对一  
一对多  
多对多  

### 参与约束
全部参与：如果<b>实体集E</b>中的每个实体都参与到<b>联系集R</b>的至少一个联系中，实体集E在联系集R中的参与称为全部的  
部分参与：E中只有部分实体参与到R的联系中  

### 码
码：一个足以区分每个实体的属性集。关系模式中的超码、候选码、主码的概念同样适用于实体集  

#### 联系集的主码
<li>如果联系集R没有属性（描述属性）与之关联，那么参与到R中的实体集E1,E2,...,En的主码的并集描述了这个关系R</li>
<li>如果联系集R有属性（描述属性），用A来表示这个属性集，则$E_i$的主码的并集和A的并集描述了这个关系R</li>
<li>如果一个联系集R是多对多的，那么R的主码是参与到R的实体集的主码的并集组成</li>
<li>如果一个联系集R是多对一的，那么R的主码是“多”对应的实体集的主码，比如，如果联系是从student到instructor多对一的，也就是一个instructor可以对应多个student，那么student的主码就是advisor的主码，如果联系是从instructor到student多对一的，也就是一个student可以对应多个instructor，那么advisor的主码就是instructor的主码</li>

### 从实体集中删除冗余属性
对两个实体集E、F，共同参与一个联系R，如果E中有一个属性a（不是E的主码）是F的主码，那么a在E中是冗余属性，在E中应该删除a属性  
比如：  
instructor(ID,name,dept_name,salary),primary key(ID)  
department(dept_name,building,budget),primary key(dept_name)  
联系集inst_dept  
只有当instructor和dept是多对一的时候也就是一个教师最多对应一个系的时候，那么dept_name才会添加到instructor中去

### 非二元的联系集
在一个联系集外我们至多允许一个箭头

### 弱实体集和强实体集
弱实体集：没有足够的属性以形成主码的实体集  
强实体集：有主码的实体集  
弱实体集在联系中的参与是全部的，标识性联系集不应该有任何描述性属性，因为这种属性中的任何一个都可以与弱实体集关联  
比如：section的标识实体集是course，将section实体和它们对应的course实体关联在一起的sec_course是标识性联系  
分辨符：虽然弱实体集没有主码，但是也需要区分依赖于特定强实体集的弱实体集中的实体的方法，分辨符是使得我们进行这种区分的属性集  
弱实体集的主码：标识实体集的主码加上自己的分辨符  

## 强实体集的表示
### 具有简单属性的强实体集的表示
对于从强实体集转换而来的模式，强实体集的主码就是生成的模式的主码。

### 具有复杂属性的强实体集的表示
#### 复合属性
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

#### 多值复合属性
>time_slot(<u>time_slot_id</u>, {(day, start_time, end_time)})  

这个实体集有两个属性，一个是主码time_slot_id， 一个是多值属性，而且这个多值属性还是复合的，先按多值属性进行处理，创建一个新的关系模式，假设叫time(<u>time_slot_id</u>, (<u>day</u>, <u>start_time</u>, <u>end_time</u>),然后按复合属性进行处理，new_time(<u>time_slot_id</u>, <u>day</u>, <u>start_time</u>, <u>end_time</u>)  
所以我们最终拥有了两个关系模式
> time_slot(<u>time_slot_id</u>)
> new_time(<u>time_slot_id</u>, <u>day</u>, <u>start_time</u>, <u>end_time</u>)  
很明显第一个关系模式是毫无意义的，该实体集的关系模式只包含一个属性，就是它的主码。可以删掉这个关系，保留新创建的关系。也就是  
> time_slot(<u>time_slot_id</u>, <u>day</u>, <u>start_time</u>, <u>end_time</u>)  

最后，书上是这样说的  

> Although not represented as a constraint on the E-R diagram, we know that there cannot be two meetings of a class that start at the same time of the same day-of-the-week but end at different times; based on this constraint, end time has been omitted from the primary key of the time slot schema.  

#### 外码
在<b>多值属性</b>构建的关系模式上建立外码约束，由实体集的主码所生成的属性去参照实体集所形成的关系，例如：instructor_phone关系上的外码约束是属性ID（instructor_phone(ID,phone_number)）参照instructor关系

## 弱实体集表示
弱实体集A，拥有属性集$a1, a2, ... an$  
A的依赖强实体集B，拥有属性$b1, b2, ... bn$  
则联系集R的属性集为$\{a1, a2, ..., an\}$∪$\{b1, b2, ..., bn\}$  
<b>除了创建主码之外，还要在弱实体集生成的关系A上建立外码约束，该约束指明属性$b_1,b_2,...,b_n$参照关系B的主码</b>  
弱实体集的ER图以虚下划线来标识，关联弱实体集和强实体集的联系集以双菱形表示，弱实体集的主码是由标识实体集（强实体集）的主码加上该弱实体集的分辨符构成。还有外码约束，保证弱实体的每一个元组都有一个表示相应强实体的元组与之对应

## 联系集的表示
### 属性
R是实体集A和实体集B的联系集，则R的属性是A的属性和B的属性的并集再并上R自己的描述性属性

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
以advisor为例，advisor是instructor(主码为ID)和student(主码为ID)的两个实体集的联系集  
那么，因为advisor没有描述属性，则advisor有两个属性，instructor和student的主码。由于这两个主码名字相同，将它们重命名为i_ID和s_ID，因为advisor联系集是从student到instructr多对一的，所以关系模式advisor的主码是s_ID，而且advisor中的属性s_ID是外码，参照实体集student中的ID属性，advisor中的属性i_ID是外码，参照实体集instructor中的ID属性

## 模式冗余
cource(<u>cource_id</u>, title, credits)  
section(<u>cource_id</u>, <u>sec_id</u>, <u>semester</u>, <u>year</u>)  
sec_course(<u>cource_id</u>, <u>sec_id</u>, <u>semester</u>, <u>year</u>)  
如上，section和sec_course的属性一摸一样，那就必然造成了冗余，所以，<b>一般情况下，连接弱实体集与其依赖的强实体集的模式是冗余的，而且在基于E-R的关系数据库中不必给出</b>  
## 模式合并
1、ARB，多对一，A全部参与  
我们可以得到3个关系模式：A、B、AB，则把AB合并到A中， consisting of the union of attributes of both schemas  
2、ARB，一对一  
可以和参与联系的任意一个实体集的模式进行合并  
3、部分参与的  
也可以用空值来进行模式的合并  
4、外键约束  
We drop the constraint referencing the entity set into whose schema the relationship set schema is merged, and add the other foreign-key constraints to the combined schema. For example, inst_dept has a foreign key constraint of the attribute dept_name referencing the department relation. This foreign constraint is added to the instructor relation when the schema for inst dept is merged into instructor

## 实体-联系设计问题

### 用实体集还是属性
<b>一个常见的错误是用一个实体集的主码去做另一个实体集的属性，而不是用联系（前面所说的冗余）。例如，即使每名教师只指导一名学生，将student的ID作为instructor的属性也是不正确的。用advisor联系代表学生和教师的关联才是正确的，因为这样可以明确地表示出两者之间的关系，而不是将这种关系隐含在属性中</b>

### 用实体集还是用联系集
原则：当描述发生在实体间的行为时采用联系集，这一方法在决定是否将某些属性表示为联系时可能更适用也很有用

### 联系属性的布局
一个联系的映射基数比率会影响联系属性的布局。因此，<b>一对一或者一对多的联系集的属性可以放到一个参与该联系的实体集中，而不是放到联系集中</b>  
例如：advisor是一个一对多的联系集，一个教师可以指导多个学生，但是每个学生只能有一个导师，表示教师何时成为学生导师的属性date可以与student实体集相关联。  
<b>一对多联系集的属性仅可以重置到参与联系的“多”方的实体集中，而对于一对一的联系集，联系的属性可以放到任意一个参与联系的实体中</b>