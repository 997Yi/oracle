#### 建管理员表

```sql
create table tAdmin
(cAdminID char(6) primary key,
cAdminPwd char(6) not null,
cAdminName char(10) not null
cAsminSex char(2),
cAdminAddTime char(10));

```

### 建读者表

```sql
create table tUser 
(cUsersID char(10)primary key,
cUsersPwd char(6)not null,
cUsersNo char(11)not null,
cUsersName char(10) not null,
cUsersSex char(2),
cUsersDepart char(20),
cUsersClass char(6),
cUsersTel char(11));
```

### 建图书表

```sql
create table book tBook
(cBooksISBN char(13)primary key,
cBooksName char(20)not null,
cBooksAuthor char(10),
cBooksType char(10)not null,
cBooksPrice int,
cBooksPublisher char(20), 
cBooksStore  int);  
```

### 建借书表

```sql
create table tBorrow
(cUsersNo char(6)not null,
cUserName char(10) not null,
cBooksISBN char(13) not null,
cBookName char(20) not null ,
cUsersBorrow int,
primary key( cUsersNo,cBooksISBN))

```

### 建还书表

```sql
create table tReturn
(cUsersNo char(6)not null,
cUserName char(10) not null,
cBooksISBN char(13) not null,
cBookName char(20) not null,
cBorrowTime char(10) not null,
cReturnTime char(10) not null，
primary key( cUsersNo,cBooksISBN))

```

### 数据的存储与修改

```sql
--管理员注册
insert into tAdmin 
values('070411','123456','李志敏', '男'，'2011-6-27');
insert into tAdmin 
values('070448','123456','赵林宁', '女'，'2011-6-27');
--管理员注销
delete from tAdmin where cAdminID ='070411';
--增加读者
insert into tUser(cUsersID,cUsersPwd,cUsersNo,cUsersName, cUsersSex, cUsersDepart,cUsersClass,cUsersTel)
values('070421','123456','20084070421','郑春龙','男','计科系','080704','15138670895');
insert into tUser(cUsersID,cUsersPwd,cUsersNo,cUsersName, cUsersSex, cUsersDepart,cUsersClass,cUsersTel)
values('070433','123456','20084070433','晋亚丽','女','计科系','080704','15238625639');
--删除读者
delete from tUser where cUsersID ='20084070421';
--修改读者信息
update tUser 
set cUsersName='张三', cUsersSex='男' where cUsersID ='20084070433'
--增加图书
insert into book(cBooksISBN, cBooksName, cBooksAuthor, cBooksType, cBooksPrice, cBooksPublisher, cBooksStore)
values ('9787802062373','《围城》','三毛','小说','29.80','人民日报出版社',3);
insert into book(cBooksISBN, cBooksName, cBooksAuthor, cBooksType, cBooksPrice, cBooksPublisher, cBooksStore)
values ('3625654585625','Java程序设计','张墨华','计算机','56.50','清华大学出版社',7);
--删除图书
delete  from tBook where cBooksISBN=9787802062373;
--修改图书信息
update tBook 
set cBooksName ='《雾》',bauthor='巴金' where cBooksISBN ='9787802062373';
--读者借阅图书
insert into tBorrow (cUsersNo,cUserName,cBooksISBN,cBooksName, cUsersBorrow);
values('070411','李志敏','5625658565452','围城','2011-6-27')
--读者归还图书
insert into tReturn(cUsersNo,cUserName,cBooksISBN,cBooksName, cBorrwTime, cReturnTime);
values('070411','李志敏','5625658565452','围城','2011-6-27''2011-7-20');

```

### 查询

```sql
--查询姓李的学生信息
select * from tUser where tUsersName like’李%’;
--查询借书同学的信息
select cUsersName  from tBorrow  ORDER BY  cUsersID;
--查询所有借书同学的姓名、手机号及所在的院系
select tBorrow.cUsersName，cUsersTel,cUsersDepart from tUser,tBorrow where tUser.cUsersName =tBorrow.cUsersName;
--查询借某本书的同学的学号、姓名、及联系方式
Select cUsersID,cUsersName,cUsersTel from tUser where cUsersID in(select cUsersID from tBorrow where cBooksISBN=’5623562514865’)

```

### 创建视图

```sql
--如创建视图view图书信息视图
create view i_book
as
select*from tBook
where cBooksType like '计算机'
--读者信息视图view_count,包含每个读者的姓名、学号和借书数目的信息
create view view_count(cUsersName, cUsersNo, cUsersBorrow,)
as
select cUsersNo count(*) from. tBorrow group by cUsersName

```

### 创建索引

```sql
--如在tBook上创建唯一性索引
Create unique index tBook_ cBooksType  on  tBook(cBooksType)
--在tUser表上的cUserSex列上创建一个位图索引
Create bitmap index tUser_sex on tUser(cUserSex)

```

### 创建触发器

```sql
--创建一个触发器，禁止在周日对图书表进行修改
Create or replace trigger trg_emp_Sunday
Begin 
If to_char(sysdate, 'DY ', 'nls_date_language=american') In ('SUN')
Then
 raise_application_error(-20200, 'Can''t operate on Sunday.')；
End If;
End   trigger_trg_emp_Sunday；
--借书时的触发器，用来更新图书数量
create or replace trigger trg_borrow
after insert on borrow
for each row
  begin
    update borrowCard set quantity=quantity + 1 where id =:new.bcid;
    update book set inNumber=inNumber-1 where isbn=:ISBN;
end;
--还书时的触发器，更新图书数量 并计算罚款
create or replace 5555555555000 trg_due
after update of dueDate ON borrow
for each row
  declare
      v_days number;
      v_dayFine number(6,2);
      v_fineMoney number(6,2);
  begin
      update borrowCard set quantity=quantity-1 where id=:new.bcid;
      update book set inNumber=inNumber+1 where isbn=:new.isbn;
      select days,fine into v_days,v_dayfine from Borrowlevel where id =(select 
      blid from borrowCard where id=:new.bcid);
      if(:new.dueDate-:new.borrowDate-v_days)*v_dayFine;
      insert into ticket(id,bcid,isbn,fineMoney,fineDate)
      values(seq_ticket.nextval,:new.bcid,:new.isbn,v_fineMoney,:new.dueDate);=
  end if;
  end;
```

### 创建函数

```sql
create or replace function func_due_date(
 p_borrowCardId varchar2,
 p_borrowdate date default sysdate)
 return date
 as
   v_day number;
   begin
     select days into v_day from borrowlevel where id = (select blid from borrowcard where id = p_borrowCardId);
     return p_borrowDate+v_day;
     exception
       when no_data_found then
         raise_application_error(-20000,'借阅证号码错误！');
end;
create or replace procedure proc_borrow(
       p_borrowCardID varchar2, p_ISBN varchar2,
       p_borrowDate Date default sysdate
)
 As
       v_borrowNumber Number;
       v_borrowLevelId varchar2(20);
       v_days Number;
       v_quantity number;
       v_stockNumber number;
       v_inNumber number;
       v_expireDate date;
        begin
         select quantity,blid Into v_borrowNumber,v_borrowLevelid from borrowcard
         where id = p_borrowcardID;
         select days,quantity into v_quantity from borrowlevel
         where id = v_borrowLevelId;
         if v_borrownumber = v_quantity then
            raise_application_error(-20000,'对不起，您已经达到借阅最大次数了！');
           end if;
         select stocknumber,innumber into v_stocknumber,v_innumber from book
         where isbn = p_isbn;
         if v_innumber<=0 then
           raise_application_error(-20001,'对不起，该书已经没有库存了！');
           end if;
           v_expireDate:=p_borrowdate+v_days;
           insert into borrow(id,bcid,isbn,borrowDate,expireDate)
           values(seq_borrow.nextval,p_borrowcardId,p_isbn,p_borrowDate,
           v_expireDate);
       end;
       create or replace procedure proc_due(
       p_borrowCardId varchar2,p_isbn varchar2,p_dueDate date default sysdate
) 
     as
      v_number number;
      begin
        select count(*) into v_number from borrow where bcid=p_borrowCardId
        and isbn = p_isbn;
        if v_number = 0 then
          raise_application_error(-20000,'对不起，没有相应借书记录！');
          end if;
          update borrow set dueDate=p_dueDate where bcid = p_borrowCardId and isbn=p_isbn;
      end;
      create or replace procedure proc_BorrowBook(p_borrowCardId varchar2, p_bookCursor out sys_refcursor
) 
  AS
  begin
    open p_bookCursor for select book.isbn,name,borrowDate,expireDate from book,borrow where book.isbn=borrow.isbn 
    and bcid=p_borrowCardId and dueDate is null;
    end;-

```

### 备份方案

```sh
#采用ORACLE的EXP工具，实现ORACLE的备份；采用LINUX的服务crond实现定时功能。
#1 编辑SH，实现备份功能
#vi oracle_backup.sh输入以下内容
#!/bin/sh
ORACLE_BACKUP_HOME=/home/oracle/backup   #定义ORACLE备份根目录
BACKUP_DATA=$ORACLE_BACKUP_HOME/day   #定义ORACLE备份数据文件根目录
BACKUP_LOG=$BACKUP_DATA/log  #定义ORACLE备份日志文件根目录
export ORACLE_BACKUP_HOME  BACKUP_DATA BACKUP_LOG 
DATA_FILE_NAME=data_backup          #定义ORACLE备份日志文件名字前缀
LOG_FILE_NAME=log_backup             #定义ORACLE备份日志文件名字前缀
export DATA_FILE_NAME LOG_FILE_NAME
BACKUP_AMOUNT=4        #定义ORACLE备份文件保存数量
export BACKUP_AMOUNT
datafile_amount=$(find $BACKUP_DATA -type f -name $DATA_FILE_NAME'_'*.dmp|wc -l)   #查询ORACLE备份数据文件根目录下备份数据文件的数量
logfile_amount=$(find $BACKUP_LOG -type f -name $LOG_FILE_NAME'_'*.log|wc -l)      #查询ORACLE备份日志文件根目录下备份日志文件的数量
del_datafile_count=$(($datafile_amount-$BACKUP_AMOUNT+1));   #计算需要删除ORACLE备份数据文件的数量
del_logfile_count=$(($datafile_amount-$BACKUP_AMOUNT+1));         #计算需要删除ORACLE备份日志文件的数量
if(($datafile_amount>=$BACKUP_AMOUNT));then
       echo $BACKUP_DATA"路径下文件太多，正在清除备份数据文件"
       for((i=0;i<$del_datafile_count;i++))
       do
       ls -t $BACKUP_DATA/$DATA_FILE_NAME'_'*.dmp| awk 'END{if(NR>=$BACKUP_AMOUNT){system("rm -rf "$NF);system("echo  $BACKUP_DATA路径下,已删除文件"$NF)}}'#删除修改时间最早的一个数据文件
       done
fi
if(($logfile_amount>=$BACKUP_AMOUNT));then
       echo $BACKUP_LOG"路径下文件太多，正在清除备份日志文件"
       for((i=0;i<$del_logfile_count;i++))
       do
       ls -t $BACKUP_LOG/$LOG_FILE_NAME'_'*.log| awk 'END{if(NR>=$BACKUP_AMOUNT){system("rm -rf "$NF);system("echo  $BACKUP_LOG路径下,已删除文件"$NF)}}'#删除修改时间最早的一个日志文件
       done
fi
rq=`date +"%Y%m%d%s"`      #获取当前系统时间
su - oracle -c "/oracle/product/10.2.0/db_1/bin/exp sgedptwo/sgdb321@SGEMDP file=$BACKUP_DATA/$DATA_FILE_NAME'_'$rq.dmp log=$BACKUP_LOG/$LOG_FILE_NAME'_'$rq.log" #备份ORACLE数据库并记录日志
2 授予ORACLE用户使用备份目录权限
# mkdir /home/oracle/backup
# mkdir /home/oracle/backup/day
# mkdir /home/oracle/backup/day/log
#chown –R /home/oracle/backup

```

### 用户权限

```sql
--图书管理员权限
grant select, update, insert on product to bookAdmin;
--系统管理员权限
grant all on product to sysAdmin;

```

