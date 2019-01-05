# Func name map



```json
{
    1 :  "新建任务",
    2 : "删除任务",
    3 : "模糊查询任务",
    4 : "复制任务",
    5  : "获取历史任务列表",
    6 :  "加载历史任务",
    7 :  "上传图像元数据的列表",
    8 :  "逻辑删除图像",
    9 :  "上传图像base64数据",
    10:   "获取图像二进制数据",
    11 :  "获取roi图像",
    12 :  "图像分集合",
    13 :  "查询某个集合中的图像",

    14 :  "获取标注信息",
    15 :  "保存标注信息",

    16 :  "获取标签类",
    17 :  "增加标签类",
    18 :  "修改标签类",
    19 :  "删除标签类",
    20:   "模糊匹配标签类",

    21 : "获取日志列表",

    22 :  "获取测试结",
    23 :  "获取训练批次",
    24 :  "获取热力图",
    25 :  "获取缩略图",

    26 :  "开始测试",
    27 :  "测试进度查询",
    28 :  "获取训练模型列表",
    29 :  "开始训练",
    30 :  "查询训练进度",
    31 :  "停止训练",
    32 :  "完成训练",

    33 :  "新建组织机构",
    34 :  "查询组织机构",
    35 :  "更新组织机构",
    36 :  "删除组织机构",

    37 :  "新建组",
    38 :  "查询组",
    39 :  "更新组",
    40 :  "删除组",

    41 :  "新建角色",
    42 :  "查询角色",
    43 :  "更新角色",
    44 :  "删除角色",
    
    45 :  "新建系统功能",
    46 :  "查询系统功能",
    47 :  "更新系统更能",
    48 :  "删除系统功能",

    49 :  "创建用户",
    50 :  "查询用户信息",
}
```



<系统功能>、<系统功能角色关系> 、<角色> 、<用户角色关系表>     表初始化

```mysql
drop table if exists SYS_FUNCTION;

/*==============================================================*/
/* Table: SYS_FUNCTION                                          */
/*==============================================================*/
create table SYS_FUNCTION
(
   FUNC_ID              int not null auto_increment,
   FUNC_NAME            varchar(20),
   LEVEL                tinyint,
   IS_UI                tinyint comment '1、是，拥有URL
            0、无',
   UI_URL               varchar(128),
   SUPER_ID             int,
   ORDER_ID             int,
   primary key (FUNC_ID)
);



drop table if exists ROLE_FUNC_REL;

/*==============================================================*/
/* Table: ROLE_FUNC_REL                                         */
/*==============================================================*/
create table ROLE_FUNC_REL
(
   ROLE_ID              int not null,
   FUNC_ID              int not null,
   IS_VALID             tinyint,
   primary key (ROLE_ID, FUNC_ID)
);



drop table if exists SYS_ROLE;

/*==============================================================*/
/* Table: SYS_ROLE                                              */
/*==============================================================*/
create table SYS_ROLE
(
   ROLE_ID              int not null auto_increment,
   ROLE_NAME            varchar(30),
   TYPE                 tinyint comment '1、系统管理员
            2、管理级别
            3、用户级别
            
            9、运维级别',
   ORG_ID               bigint(20),
   GROUP_ID             bigint(20),
   LEVEL                tinyint comment '1、
            2、
            3、
            4、',
   IS_DEL               tinyint,
   primary key (ROLE_ID)
);





drop table if exists SYS_USER_ROLE_REL;

/*==============================================================*/
/* Table: SYS_USER_ROLE_REL                                     */
/*==============================================================*/
create table SYS_USER_ROLE_REL
(
   USER_ID              bigint(20) not null,
   ROLE_ID              int not null,
   IS_VALID             tinyint,
   START_TIME           datetime,
   END_TIME             datetime,
   CREATE_DATE          int,
   primary key (USER_ID, ROLE_ID)
);


```



<用户>、<用户和组关系>  表重置

```mysql
drop table if exists SYS_USER;

/*==============================================================*/
/* Table: SYS_USER                                              */
/*==============================================================*/
create table SYS_USER
(
   USER_ID              bigint(20) not null,
   USER_NAME            varchar(30),
   PWD                  varchar(30),
   CREATE_TIME          datetime,
   MODIFY_TIME          datetime,
   FIRST_NAME           varchar(20),
   LAST_NAME            varchar(30),
   FULL_NAME            varchar(50),
   MOBEL                bigint(16),
   EMAIL                varchar(30),
   STATE                tinyint,
   CREATE_DATE          int,
   ORG_ID               bigint(20),
   ORDER_ID             int,
   primary key (USER_ID)
);



drop table if exists USER_GROUP_REL;

/*==============================================================*/
/* Table: USER_GROUP_REL                                        */
/*==============================================================*/
create table USER_GROUP_REL
(
   USER_ID              bigint(20) not null,
   GROUP_ID             bigint(20) not null,
   START_TIME           datetime,
   END_TIME             datetime,
   STATE                tinyint,
   IS_OWENR             tinyint,
   CREATE_DATE          int,
   CREATE_OPER          bigint(20),
   primary key (USER_ID, GROUP_ID)
);





```

