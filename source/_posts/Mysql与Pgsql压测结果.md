---
title: Mysql与Pgsql压测结果
tags: [压测,MySQL,PGSQL,pgloader]
categories: [数据库]
cover: /images/jt/blog/1344988.jpeg
banner: /images/jt/blog/1344988.jpeg
poster:
  topic: Mysql与Pgsql压测结果
  headline: 数据库压测
date: 2024-07-19 11:40:11
description:
---
# MySQL与PgSQL压测

## 服务器资源

10核20线程

## 执行条件

10线程，100次循环，

tb_sale_order、tb_sale_order_item、tf_item_sku 数据量各400万

一一对应连表也是400万

# MySQL
{% tabs active:2 align:center %}

<!-- tab MySQL表信息 -->

tb_sale_order

```scheme
-- zongheng_111.tb_sale_order definition

CREATE TABLE `tb_sale_order` (
  `ORDER_ID` bigint NOT NULL COMMENT '订单ID',
  `ORDER_NO` varchar(32) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin NOT NULL COMMENT '订单编号',
  `ORDER_CREATE_TIME` datetime DEFAULT NULL COMMENT '订单创建时间',
  `DELIVER_TIME` datetime DEFAULT NULL COMMENT '订单发货时间',
  `ORDER_CREATE_NAME` varchar(32) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT NULL COMMENT '订单创建人名称',
  `ORDER_APPROVAL_TIME` datetime DEFAULT NULL COMMENT '订单审批时间(山东烟草)',
  `COMPANY_ID` int NOT NULL COMMENT '销售主体标识',
  `CUSTOMER_COMPANY_ID` int DEFAULT NULL COMMENT '客户标识',
  `PLATFORM_ID` int NOT NULL COMMENT '平台ID',
  `SHOP_ID` int NOT NULL COMMENT '店铺',
  `ACCOUNT_MANAGER_STAFF_ID` int DEFAULT NULL COMMENT '客户经理标识',
  `PRODUCT_STAFF_ID` int DEFAULT NULL,
  `ORDER_TYPE` char(1) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin NOT NULL COMMENT '订单类型',
  `ORDER_PRICE` decimal(14,6) DEFAULT NULL COMMENT '销售总金额',
  `ORDER_FREIGHT` decimal(14,6) DEFAULT NULL COMMENT '订单总运费',
  `CONSIGNEE_USER_NAME` varchar(64) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin NOT NULL COMMENT '收货人姓名',
  `CONSIGNEE_USER_PHONE` varchar(32) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin NOT NULL COMMENT '收货人联系电话',
  `CONSIGNEE_PROVINCE_ID` int DEFAULT NULL COMMENT '收货地址-省',
  `CONSIGNEE_CITY_ID` int DEFAULT NULL COMMENT '收货地址-市',
  `CONSIGNEE_DISTRICT_ID` int DEFAULT NULL COMMENT '收货地址-区/县',
  `CONSIGNEE_TOWN_ID` int DEFAULT NULL,
  `CONSIGNEE_DETAIL_ADDRESS` varchar(128) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT NULL COMMENT '收货地址-详细地址',
  `CONSIGNEE_ALL_ADDRESS` varchar(255) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT NULL COMMENT '收货地址全拼',
  `CREATE_TIME` datetime NOT NULL COMMENT '数据在中台生成的时间',
  `DEDUCTED_TAG` char(1) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT NULL COMMENT '被扣减标记;0:未被扣减 1:已被扣减',
  `TENANT_ID` int NOT NULL COMMENT '租户标识',
  `START_DATE` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '开始时间',
  `END_DATE` datetime NOT NULL DEFAULT '2099-12-31 23:59:59' COMMENT '结束时间',
  `OPERATE_TAG` char(1) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT NULL COMMENT '操作标记',
  `UPDATE_USER` int DEFAULT NULL COMMENT '更新人',
  `UPDATE_TIME` datetime DEFAULT NULL COMMENT '更新时间',
  `CREATE_USER` int NOT NULL COMMENT '创建人',
  `BATCH_NO` bigint DEFAULT NULL COMMENT '批次号编码',
  `ORDER_SOURCE` char(1) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin NOT NULL DEFAULT '0' COMMENT '订单来源',
  `CONTROL_STATUS` char(1) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT '0' COMMENT '订单状态',
  `CONSIGNEE_PROVINCE_NAME` varchar(16) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT NULL COMMENT '省-名称',
  `CONSIGNEE_CITY_NAME` varchar(16) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT NULL COMMENT '城市-名称',
  `CONSIGNEE_DISTRICT_NAME` varchar(16) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT NULL COMMENT '区/县-名称',
  `CONSIGNEE_TOWN_NAME` varchar(16) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT NULL COMMENT '街道-名称',
  `SYNC_STATUS` int NOT NULL DEFAULT '0' COMMENT '销售订单同步状态。0-未同步  1-已同步',
  `SYNC_TIME` datetime DEFAULT NULL COMMENT '销售订单同步金蝶时间',
  `MNG_ID` int DEFAULT NULL COMMENT '项目id',
  `ORDER_DELIVER_TYPE` char(1) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT NULL COMMENT '订单销售类型：0-直运单; 1-标准销售单',
  `OPERATE_STAFF_ID` int DEFAULT NULL COMMENT '店铺运营id',
  `MODEL` char(1) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT NULL COMMENT '模式',
  `ITEM_MATCH_STATUS` char(1) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT '1' COMMENT '商品匹配状态：0-未匹配  1-已匹配',
  `REMARK` varchar(255) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT NULL COMMENT '备注说明',
  `CUSTOMER_REMARKS` varchar(256) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT NULL COMMENT '客户备注',
  `INVALID_STATUS` char(1) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT '1' COMMENT '销售订单作废状态：0-作废  1-正常',
  `INVALID_REMARK` varchar(255) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT NULL COMMENT '作废说明',
  `HC_ACCOUNT` varchar(32) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT NULL COMMENT '慧采账号',
  `PRODUCT_MATCH_ID` bigint DEFAULT NULL COMMENT '商品匹配标识',
  PRIMARY KEY (`ORDER_ID`,`START_DATE`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_bin COMMENT='销售订单';
```

tb_sale_order_item

```scheme
-- zongheng_111.tb_sale_order_item definition

CREATE TABLE `tb_sale_order_item` (
  `SALE_ORDER_ITEM_ID` bigint NOT NULL COMMENT '销售订单商品表标识',
  `ORDER_ID` bigint NOT NULL COMMENT '订单ID',
  `ORDER_NO` varchar(32) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin NOT NULL COMMENT '订单编号',
  `ITEM_SKU_ID` bigint NOT NULL COMMENT '商品标识',
  `OWNER_SKU_CODE` varchar(64) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT NULL COMMENT '商品编码',
  `ORIGIN_SKU_NAME` varchar(256) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT NULL COMMENT '原商品名称',
  `SALE_PRICE` decimal(14,6) NOT NULL COMMENT '销售单价',
  `SALE_QUANTITY` int NOT NULL COMMENT '销售数量',
  `DEDUCTION_QUANTITY` int DEFAULT NULL COMMENT '已扣减数量',
  `SALE_TAX_RATE` decimal(10,2) DEFAULT NULL COMMENT '销售税率',
  `PURCHASE_STATUS` char(1) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin NOT NULL COMMENT '采购状态',
  `PURCHASED_QUANTITY` int NOT NULL COMMENT '已采购数量',
  `PURCHASE_APPLY_QUANTITY` int NOT NULL COMMENT '已申请采购数量',
  `INVOIC_STATUS` char(1) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin NOT NULL COMMENT '开票状态',
  `INVOICED_QUANTITY` int DEFAULT NULL COMMENT '已开票数量',
  `INVOIC_APPLY_QUANTITY` int DEFAULT NULL COMMENT '已申请开票数量',
  `PAYMENT_STATUS` char(1) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin NOT NULL COMMENT '回款状态',
  `PAYMENT_MONEY` decimal(14,6) DEFAULT NULL COMMENT '已回款金额',
  `OUTBOUND_STATUS` char(1) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin NOT NULL COMMENT '出库状态',
  `OUTBOUND_QUANTITY` int DEFAULT NULL COMMENT '已出库数量',
  `DATA_TYPE` char(1) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin NOT NULL COMMENT '商品来源类型;1.订单自带 2.补充采购',
  `MATCH_TYPE` char(1) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin NOT NULL DEFAULT '0' COMMENT '店铺商品匹配类型 0-无供应商商品匹配   1-有供应商商品匹配   2-慧采商品只有当这个值为2时hc_account才有值',
  `TENANT_ID` int NOT NULL COMMENT '租户标识',
  `CREATE_USER` int DEFAULT NULL COMMENT '创建人',
  `CREATE_TIME` datetime DEFAULT NULL COMMENT '创建时间',
  `START_DATE` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '开始时间',
  `END_DATE` datetime NOT NULL DEFAULT '2099-12-31 23:59:59' COMMENT '结束时间',
  `OPERATE_TAG` char(1) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT NULL COMMENT '操作标记',
  `UPDATE_USER` int DEFAULT NULL COMMENT '更新人',
  `UPDATE_TIME` datetime DEFAULT NULL COMMENT '更新时间',
  `COMMISSION_STATUS` char(1) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT '1' COMMENT '提成状态',
  `COMMISSION_REMOVE_MARK` char(1) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT '0' COMMENT '提成剔除标记',
  `HC_ACCOUNT` varchar(32) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT NULL COMMENT '慧采账号',
  `OWNER_TYPE_CODE` char(1) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT NULL COMMENT '商品属主类型',
  `ORDER_LINE_NUMBER` varchar(128) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT NULL COMMENT '订单行号',
  `PLATFORM_SALES_UNIT_PRICE` decimal(14,6) DEFAULT NULL COMMENT '平台销售单价',
  `KING_DEE_MATERIAL_ID` varchar(32) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT NULL COMMENT '金蝶销售订单物料唯一主键',
  `KING_DEE_ENTITY_ID` varchar(32) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT NULL COMMENT '金蝶分录行唯一编码',
  `SUPPLY_PRICE` decimal(14,6) DEFAULT NULL COMMENT '供货价',
  `MODIFY_REMARK` varchar(128) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci DEFAULT NULL COMMENT '修改总价的商品记录下总价',
  `SETTLEMENT_ORGANIZATION_ID` int DEFAULT NULL COMMENT '采购结算组织ID',
  `MODEL` varchar(256) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT NULL COMMENT '型号/规格',
  `SALES_UNIT` varchar(3) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT NULL COMMENT '基本单位',
  `CATEGORY_ID` int DEFAULT NULL COMMENT '商品分类',
  `AFTER_SALE_QUANTITY` int DEFAULT NULL COMMENT '售后数量',
  `AFTER_SALE_TYPE` varchar(10) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci DEFAULT NULL COMMENT 'SEND:补货,RETURN:退回',
  `KINGDEE_SYNC_ID` bigint DEFAULT NULL COMMENT '金蝶订单同步Id',
  `ITEM_MATCH_STATUS` char(1) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT '1' COMMENT '商品匹配状态：0-未匹配  1-已匹配',
  `ACCOUNT_CHECK_BATCH_NO` bigint DEFAULT NULL COMMENT '对账批次号',
  `PURCHASE_SETTLE_QUANTITY` int DEFAULT NULL COMMENT '采购结算数量',
  `SALE_SETTLE_QUANTITY` int DEFAULT NULL COMMENT '销售结算数量',
  `HC_P_ORDER_NO` varchar(32) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT NULL COMMENT '慧采父订单编号',
  PRIMARY KEY (`SALE_ORDER_ITEM_ID`,`START_DATE`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_bin COMMENT='销售订单商品';
```

tf_item_sku

```scheme
-- zongheng_111.tf_item_sku definition

CREATE TABLE `tf_item_sku` (
  `ITEM_SKU_ID` bigint NOT NULL COMMENT '商品标识',
  `WAREHOUSE_ID` int DEFAULT NULL COMMENT '仓库标识',
  `OWNER_SKU_CODE` varchar(64) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT NULL COMMENT '商品编码（属主定义）',
  `OWNER_SKU_NAME` varchar(256) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin NOT NULL COMMENT '商品名称（属主定义）',
  `OWNER_TYPE_CODE` char(1) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin NOT NULL COMMENT '商品属主类型',
  `OWNER_ID` int NOT NULL COMMENT '商品属主编码',
  `SKU_ID` int DEFAULT NULL COMMENT '产品SKU编码(绑定）;最小存货单位（SKU），全称为Stock Keeping Unit，即库存进出计量的基本单元',
  `HC_SKU_TYPE` char(1) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT '0' COMMENT '是否慧采商品，0否 1是',
  `HC_SKU_CODE` varchar(64) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT NULL COMMENT '慧采商品编码',
  `HC_ACCOUNT` varchar(64) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT '0' COMMENT '慧采账号',
  `BRAND_ID` int DEFAULT NULL COMMENT '品牌标识',
  `MODEL` varchar(256) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT NULL COMMENT '型号',
  `IS_IMPORT` char(1) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT NULL COMMENT '是否进口',
  `BARCODE` varchar(64) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT NULL COMMENT '产品条形码',
  `SALES_UNIT` char(3) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin NOT NULL COMMENT '基本单位',
  `ITEM_NO` varchar(64) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT NULL COMMENT '货号',
  `WEIGHT` decimal(8,2) DEFAULT NULL COMMENT '产品重量',
  `PACKAGE_SPECS` varchar(255) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT NULL COMMENT '包装规格',
  `PACKAGE_LIST` varchar(255) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT NULL COMMENT '包装清单',
  `ON_COUNTER_TAG` char(1) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin NOT NULL DEFAULT '1' COMMENT '上柜状态',
  `REVIEW_TAG` char(1) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin NOT NULL COMMENT '审核状态',
  `REVIEW_REASON` varchar(255) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT NULL COMMENT '审核原因;配置CONFIG_LIST',
  `TENANT_ID` int NOT NULL COMMENT '租户标识',
  `START_DATE` datetime NOT NULL COMMENT '开始时间',
  `END_DATE` datetime NOT NULL DEFAULT '2099-12-31 23:59:59' COMMENT '结束时间',
  `CREATE_USER` int NOT NULL COMMENT '创建人',
  `CREATE_TIME` datetime NOT NULL COMMENT '创建时间',
  `UPDATE_USER` int DEFAULT NULL COMMENT '更新人',
  `UPDATE_TIME` datetime DEFAULT NULL COMMENT '更新时间',
  `OPERATE_TAG` char(255) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT NULL COMMENT '操作标记;1:新增，2修改 3：新增后即删除 4：修改后删除',
  `SALE_TAX_RATE` decimal(10,4) DEFAULT '0.0000' COMMENT '销售税率',
  `OWNER_CATEGORY_ID` int DEFAULT NULL COMMENT '商品属主分类',
  `ACCESS_LINK` varchar(1024) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT NULL COMMENT '访问链接',
  `OWNER_CATEGORY_NAME` varchar(32) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT NULL COMMENT '商品属主分类名称',
  `FILE_PATH` varchar(128) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT NULL COMMENT '商品图片',
  `SALES_UNIT_ITEM` char(3) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT NULL COMMENT '销售单位',
  `PURCHASE_UNIT` char(3) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT NULL COMMENT '采购单位',
  `SHELF_LIFE_MONTHS` decimal(10,2) DEFAULT NULL COMMENT '保质期（月）',
  `SYNC_STATUS` char(1) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT '0' COMMENT '同步状态 0未同步 1已同步',
  `SYNC_TIME` datetime DEFAULT NULL COMMENT '同步时间',
  `RESERVE_FIELD1` varchar(256) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT NULL COMMENT '预留字段1',
  `RESERVE_FIELD2` varchar(256) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT NULL COMMENT '预留字段2',
  `RESERVE_FIELD3` varchar(256) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT NULL COMMENT '预留字段3',
  `RESERVE_FIELD4` varchar(256) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT NULL COMMENT '预留字段4',
  `RESERVE_FIELD5` varchar(256) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT NULL COMMENT '预留字段5',
  `CATEGORY_EXTRA_ID` int DEFAULT NULL COMMENT '额外分类ID',
  `BIND_ITEM_SKU_ID` bigint DEFAULT NULL COMMENT '绑定的标准输出商品ID',
  `BIND_TIME` datetime DEFAULT NULL COMMENT '绑定时间',
  `SHOP_ITEM_SUPPLIER_ID` int DEFAULT NULL COMMENT '店铺商品供应商',
  `FORBID_TEMPLATE_ID` int DEFAULT NULL COMMENT '绑定的禁售区域的唯一标识',
  `ALLOW_DISMANTLING_AND_MERGE_STATUS` char(1) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT '0' COMMENT '拆合单规则是否允许：1-允许，0-不允许',
  `MAX_NUM` int DEFAULT NULL COMMENT '拆合单上限数量',
  PRIMARY KEY (`ITEM_SKU_ID`,`START_DATE`),
  KEY `tf_item_sku_ITEM_SKU_ID_IDX` (`ITEM_SKU_ID`) USING BTREE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_bin COMMENT='商品SKU信息表;';
```

<!-- tab MySQL执行语句 ->

新·中台#(tb_sale_order)#分页查询

```sql
select
    ORDER_ID,
    ORDER_NO,
    ORDER_CREATE_TIME,
    DELIVER_TIME,
    ORDER_CREATE_NAME,
    ORDER_APPROVAL_TIME,
     COMPANY_ID,
     CUSTOMER_COMPANY_ID,
     PLATFORM_ID, SHOP_ID,
     ACCOUNT_MANAGER_STAFF_ID,
     PRODUCT_STAFF_ID,
     ORDER_TYPE,
     ORDER_PRICE
from tb_sale_order
limit 0,500;
```

新·中台#(tb_sale_order)#条件查询

```sql
select
    ORDER_ID,
    ORDER_NO,
    ORDER_CREATE_TIME,
    DELIVER_TIME,
    ORDER_CREATE_NAME,
    ORDER_APPROVAL_TIME,
     COMPANY_ID,
     CUSTOMER_COMPANY_ID,
     PLATFORM_ID, SHOP_ID,
     ACCOUNT_MANAGER_STAFF_ID,
     PRODUCT_STAFF_ID,
     ORDER_TYPE,
     ORDER_PRICE
from tb_sale_order tso
where tso.ORDER_ID = 100023;
```

新·中台#(tb_sale_order、tb_sale_order_item、tf_item_sku)#连表分页查询

```sql
select
    tso.ORDER_ID ,
    tso.ORDER_CREATE_NAME ,
    tso.START_DATE ,
    tsoi.SALE_ORDER_ITEM_ID ,
    tsoi.SALE_PRICE ,
    tsoi.SALE_QUANTITY ,
    tsoi.START_DATE ,
    tis.ITEM_SKU_ID ,
    tis.OWNER_SKU_NAME ,
    tis.START_DATE
from tb_sale_order tso
left join tb_sale_order_item tsoi on tso.ORDER_ID = tsoi.ORDER_ID
left join tf_item_sku tis on tsoi.ITEM_SKU_ID = tis.ITEM_SKU_ID
limit 0,1000;
```

<!-- tab MySQL执行结果 ->

| label                                                        | 样本 | 平均值 ms | 最小值 ms | 最大值 ms | 标准偏差           | 异常% | 吞吐量             | 接收 KB/sec        | 发送 KB/sec | 平均字节数 |
| ------------------------------------------------------------ | ---- | --------- | --------- | --------- | ------------------ | ----- | ------------------ | ------------------ | ----------- | ---------- |
| 新·中台#(tb_sale_order)#分页查询                             | 1000 | 5         | 2         | 431       | 19.89454495584154  | 0.0   | 2.4540539744631147 | 124.84759941372651 | 0.0         | 52095.0    |
| 新·中台#(tb_sale_order)#条件查询                             | 1000 | 0         | 0         | 4         | 0.4020149250960716 | 0.0   | 2.4567006510256726 | 0.74372773615035   | 0.0         | 310.0      |
| 新·中台#(tb_sale_order、tb_sale_order_item、tf_item_sku)#连表分页查询 | 1000 | 4047      | 3496      | 5816      | 372.96866342897783 | 0.0   | 2.435798442550476  | 263.58479044826    | 0.0         | 110810.0   |
| 总体                                                         | 3000 | 1351      | 0         | 5816      | 1918.7305292496276 | 0.0   | 7.299518718399167  | 387.8225740961979  | 0.0         | 54405.0    |

{% endtabs %}

# PgSQL
{% tabs active:2 align:center %}

<!-- tab PgSQL表信息 ->
tb_sale_order

```scheme
-- zongheng_111.tb_sale_order definition

-- Drop table

-- DROP TABLE zongheng_111.tb_sale_order;

CREATE TABLE zongheng_111.tb_sale_order (
	order_id int8 NOT NULL,
	order_no varchar(32) NOT NULL,
	order_create_time timestamptz NULL,
	deliver_time timestamptz NULL,
	order_create_name varchar(32) NULL,
	order_approval_time timestamptz NULL,
	company_id int4 NOT NULL,
	customer_company_id int4 NULL,
	platform_id int4 NOT NULL,
	shop_id int4 NOT NULL,
	account_manager_staff_id int4 NULL,
	product_staff_id int4 NULL,
	order_type bpchar(1) NOT NULL,
	order_price numeric(14, 6) NULL,
	order_freight numeric(14, 6) NULL,
	consignee_user_name varchar(64) NOT NULL,
	consignee_user_phone varchar(32) NOT NULL,
	consignee_province_id int4 NULL,
	consignee_city_id int4 NULL,
	consignee_district_id int4 NULL,
	consignee_town_id int4 NULL,
	consignee_detail_address varchar(128) NULL,
	consignee_all_address varchar(255) NULL,
	create_time timestamptz NOT NULL,
	deducted_tag bpchar(1) NULL,
	tenant_id int4 NOT NULL,
	start_date timestamptz NOT NULL DEFAULT CURRENT_TIMESTAMP,
	end_date timestamptz NOT NULL DEFAULT '2099-12-31 23:59:59+08'::timestamp with time zone,
	operate_tag bpchar(1) NULL,
	update_user int4 NULL,
	update_time timestamptz NULL,
	create_user int4 NOT NULL,
	batch_no int8 NULL,
	order_source bpchar(1) NOT NULL DEFAULT '0'::bpchar,
	control_status bpchar(1) NULL DEFAULT '0'::bpchar,
	consignee_province_name varchar(16) NULL,
	consignee_city_name varchar(16) NULL,
	consignee_district_name varchar(16) NULL,
	consignee_town_name varchar(16) NULL,
	sync_status int4 NOT NULL DEFAULT 0,
	sync_time timestamptz NULL,
	mng_id int4 NULL,
	order_deliver_type bpchar(1) NULL,
	operate_staff_id int4 NULL,
	model bpchar(1) NULL,
	item_match_status bpchar(1) NULL DEFAULT '1'::bpchar,
	remark varchar(255) NULL,
	customer_remarks varchar(256) NULL,
	invalid_status bpchar(1) NULL DEFAULT '1'::bpchar,
	invalid_remark varchar(255) NULL,
	hc_account varchar(32) NULL,
	product_match_id int8 NULL,
	CONSTRAINT idx_19683_primary PRIMARY KEY (order_id, start_date)
);
```

tb_sale_order_item

```scheme
-- zongheng_111.tb_sale_order_item definition

-- Drop table

-- DROP TABLE zongheng_111.tb_sale_order_item;

CREATE TABLE zongheng_111.tb_sale_order_item (
	sale_order_item_id int8 NOT NULL,
	order_id int8 NOT NULL,
	order_no varchar(32) NOT NULL,
	item_sku_id int8 NOT NULL,
	owner_sku_code varchar(64) NULL,
	origin_sku_name varchar(256) NULL,
	sale_price numeric(14, 6) NOT NULL,
	sale_quantity int4 NOT NULL,
	deduction_quantity int4 NULL,
	sale_tax_rate numeric(10, 2) NULL,
	purchase_status bpchar(1) NOT NULL,
	purchased_quantity int4 NOT NULL,
	purchase_apply_quantity int4 NOT NULL,
	invoic_status bpchar(1) NOT NULL,
	invoiced_quantity int4 NULL,
	invoic_apply_quantity int4 NULL,
	payment_status bpchar(1) NOT NULL,
	payment_money numeric(14, 6) NULL,
	outbound_status bpchar(1) NOT NULL,
	outbound_quantity int4 NULL,
	data_type bpchar(1) NOT NULL,
	match_type bpchar(1) NOT NULL DEFAULT '0'::bpchar,
	tenant_id int4 NOT NULL,
	create_user int4 NULL,
	create_time timestamptz NULL,
	start_date timestamptz NOT NULL DEFAULT CURRENT_TIMESTAMP,
	end_date timestamptz NOT NULL DEFAULT '2099-12-31 23:59:59+08'::timestamp with time zone,
	operate_tag bpchar(1) NULL,
	update_user int4 NULL,
	update_time timestamptz NULL,
	commission_status bpchar(1) NULL DEFAULT '1'::bpchar,
	commission_remove_mark bpchar(1) NULL DEFAULT '0'::bpchar,
	hc_account varchar(32) NULL,
	owner_type_code bpchar(1) NULL,
	order_line_number varchar(128) NULL,
	platform_sales_unit_price numeric(14, 6) NULL,
	king_dee_material_id varchar(32) NULL,
	king_dee_entity_id varchar(32) NULL,
	supply_price numeric(14, 6) NULL,
	modify_remark varchar(128) NULL,
	settlement_organization_id int4 NULL,
	model varchar(256) NULL,
	sales_unit varchar(3) NULL,
	category_id int4 NULL,
	after_sale_quantity int4 NULL,
	after_sale_type varchar(10) NULL,
	kingdee_sync_id int8 NULL,
	item_match_status bpchar(1) NULL DEFAULT '1'::bpchar,
	account_check_batch_no int8 NULL,
	purchase_settle_quantity int4 NULL,
	sale_settle_quantity int4 NULL,
	hc_p_order_no varchar(32) NULL,
	CONSTRAINT idx_19695_primary PRIMARY KEY (sale_order_item_id, start_date)
);
```

tf_item_sku

```scheme
-- zongheng_111.tf_item_sku definition

-- Drop table

-- DROP TABLE zongheng_111.tf_item_sku;

CREATE TABLE zongheng_111.tf_item_sku (
	item_sku_id int8 NOT NULL,
	warehouse_id int4 NULL,
	owner_sku_code varchar(64) NULL,
	owner_sku_name varchar(256) NOT NULL,
	owner_type_code bpchar(1) NOT NULL,
	owner_id int4 NOT NULL,
	sku_id int4 NULL,
	hc_sku_type bpchar(1) NULL DEFAULT '0'::bpchar,
	hc_sku_code varchar(64) NULL,
	hc_account varchar(64) NULL DEFAULT '0'::character varying,
	brand_id int4 NULL,
	model varchar(256) NULL,
	is_import bpchar(1) NULL,
	barcode varchar(64) NULL,
	sales_unit bpchar(3) NOT NULL,
	item_no varchar(64) NULL,
	weight numeric(8, 2) NULL,
	package_specs varchar(255) NULL,
	package_list varchar(255) NULL,
	on_counter_tag bpchar(1) NOT NULL DEFAULT '1'::bpchar,
	review_tag bpchar(1) NOT NULL,
	review_reason varchar(255) NULL,
	tenant_id int4 NOT NULL,
	start_date timestamptz NOT NULL,
	end_date timestamptz NOT NULL DEFAULT '2099-12-31 23:59:59+08'::timestamp with time zone,
	create_user int4 NOT NULL,
	create_time timestamptz NOT NULL,
	update_user int4 NULL,
	update_time timestamptz NULL,
	operate_tag bpchar(255) NULL,
	sale_tax_rate numeric(10, 4) NULL DEFAULT 0.0000,
	owner_category_id int4 NULL,
	access_link varchar(1024) NULL,
	owner_category_name varchar(32) NULL,
	file_path varchar(128) NULL,
	sales_unit_item bpchar(3) NULL,
	purchase_unit bpchar(3) NULL,
	shelf_life_months numeric(10, 2) NULL,
	sync_status bpchar(1) NULL DEFAULT '0'::bpchar,
	sync_time timestamptz NULL,
	reserve_field1 varchar(256) NULL,
	reserve_field2 varchar(256) NULL,
	reserve_field3 varchar(256) NULL,
	reserve_field4 varchar(256) NULL,
	reserve_field5 varchar(256) NULL,
	category_extra_id int4 NULL,
	bind_item_sku_id int8 NULL,
	bind_time timestamptz NULL,
	shop_item_supplier_id int4 NULL,
	forbid_template_id int4 NULL,
	allow_dismantling_and_merge_status bpchar(1) NULL DEFAULT '0'::bpchar,
	max_num int4 NULL,
	CONSTRAINT idx_19706_primary PRIMARY KEY (item_sku_id, start_date)
);
CREATE INDEX idx_19706_tf_item_sku_item_sku_id_idx ON zongheng_111.tf_item_sku USING btree (item_sku_id);
```

<!-- tab PgSQL执行语句 ->
新·中台#PGSQL(tb_sale_order)#分页查询

```sql
select
    ORDER_ID,
    ORDER_NO,
    ORDER_CREATE_TIME,
    DELIVER_TIME,
    ORDER_CREATE_NAME,
    ORDER_APPROVAL_TIME,
     COMPANY_ID,
     CUSTOMER_COMPANY_ID,
     PLATFORM_ID, SHOP_ID,
     ACCOUNT_MANAGER_STAFF_ID,
     PRODUCT_STAFF_ID,
     ORDER_TYPE,
     ORDER_PRICE
from tb_sale_order
limit 500
offset 0;
```

新·中台#PGSQL(tb_sale_order)#条件查询

```sql
select
    ORDER_ID,
    ORDER_NO,
    ORDER_CREATE_TIME,
    DELIVER_TIME,
    ORDER_CREATE_NAME,
    ORDER_APPROVAL_TIME,
     COMPANY_ID,
     CUSTOMER_COMPANY_ID,
     PLATFORM_ID, SHOP_ID,
     ACCOUNT_MANAGER_STAFF_ID,
     PRODUCT_STAFF_ID,
     ORDER_TYPE,
     ORDER_PRICE
from tb_sale_order tso
where tso.ORDER_ID = 100023;
```

新·中台#PGSQL(tb_sale_order、tb_sale_order_item、tf_item_sku)#连表分页查询

```sql
select
    tso.ORDER_ID ,
    tso.ORDER_CREATE_NAME ,
    tso.START_DATE ,
    tsoi.SALE_ORDER_ITEM_ID ,
    tsoi.SALE_PRICE ,
    tsoi.SALE_QUANTITY ,
    tsoi.START_DATE ,
    tis.ITEM_SKU_ID ,
    tis.OWNER_SKU_NAME ,
    tis.START_DATE
from tb_sale_order tso
left join tb_sale_order_item tsoi on tso.ORDER_ID = tsoi.ORDER_ID
left join tf_item_sku tis on tsoi.ITEM_SKU_ID = tis.ITEM_SKU_ID
limit 1000
offset 0;
```

<!-- tab PgSQL执行结果 ->
| label                                                        | 样本 | 平均值 ms | 最小值 ms | 最大值 ms | 标准偏差           | 异常% | 吞吐量             | 接收 KB/sec        | 发送 KB/sec | 平均字节数         |
| ------------------------------------------------------------ | ---- | --------- | --------- | --------- | ------------------ | ----- | ------------------ | ------------------ | ----------- | ------------------ |
| 新·中台#PGSQL(tb_sale_order)#分页查询                        | 1000 | 4         | 3         | 251       | 9.86334304381633   | 0.0   | 2.1433240813177155 | 117.31769019322067 | 0.0         | 56050.0            |
| 新·中台#PGSQL(tb_sale_order)#条件查询                        | 1000 | 0         | 0         | 11        | 0.5954149813365464 | 0.0   | 2.1445283431588473 | 0.6575995114764435 | 0.0         | 314.0              |
| 新·中台#PGSQL(tb_sale_order、tb_sale_order_item、tf_item_sku)#连表分页查询 | 1000 | 4398      | 1682      | 8430      | 2235.9899229895923 | 0.0   | 2.1361678857064734 | 267.7493209155669  | 0.0         | 128349.137         |
| 总体                                                         | 3000 | 1467      | 0         | 8430      | 2441.277049545631  | 0.0   | 6.404850606859595  | 385.1106925811815  | 0.0         | 61571.045666666665 |
{% endtabs %}
# 综合比对结果

| label                                                        | 样本 | 平均值 ms | 最小值 ms | 最大值 ms | 标准偏差           | 异常% | 吞吐量             | 接收 KB/sec        | 发送 KB/sec | 平均字节数         |
| ------------------------------------------------------------ | ---- | --------- | --------- | --------- | ------------------ | ----- | ------------------ | ------------------ | ----------- | ------------------ |
| 新·中台#MySQL(tb_sale_order)#分页查询                        | 1000 | 5         | 2         | 431       | 19.89454495584154  | 0.0   | 2.4540539744631147 | 124.84759941372651 | 0.0         | 52095.0            |
| 新·中台#MySQL(tb_sale_order)#条件查询                        | 1000 | 0         | 0         | 4         | 0.4020149250960716 | 0.0   | 2.4567006510256726 | 0.74372773615035   | 0.0         | 310.0              |
| 新·中台#MySQL(tb_sale_order、tb_sale_order_item、tf_item_sku)#连表分页查询 | 1000 | 4047      | 3496      | 5816      | 372.96866342897783 | 0.0   | 2.435798442550476  | 263.58479044826    | 0.0         | 110810.0           |
| MySQL总体                                                    | 3000 | 1351      | 0         | 5816      | 1918.7305292496276 | 0.0   | 7.299518718399167  | 387.8225740961979  | 0.0         | 54405.0            |
|                                                              |      |           |           |           |                    |       |                    |                    |             |                    |
| 新·中台#PGSQL(tb_sale_order)#分页查询                        | 1000 | 4         | 3         | 251       | 9.86334304381633   | 0.0   | 2.1433240813177155 | 117.31769019322067 | 0.0         | 56050.0            |
| 新·中台#PGSQL(tb_sale_order)#条件查询                        | 1000 | 0         | 0         | 11        | 0.5954149813365464 | 0.0   | 2.1445283431588473 | 0.6575995114764435 | 0.0         | 314.0              |
| 新·中台#PGSQL(tb_sale_order、tb_sale_order_item、tf_item_sku)#连表分页查询 | 1000 | 4398      | 1682      | 8430      | 2235.9899229895923 | 0.0   | 2.1361678857064734 | 267.7493209155669  | 0.0         | 128349.137         |
| PgSQL总体                                                    | 3000 | 1467      | 0         | 8430      | 2441.277049545631  | 0.0   | 6.404850606859595  | 385.1106925811815  | 0.0         | 61571.045666666665 |

# 附录

MySQL8同步数据至PgSQL15

```bash
 pgloader --debug --verbose mysql://zongheng_sdb:[password]@192.168.0.200:3165/zongheng_111 postgresql://postgres:[password]@192.168.0.200:5432/zongheng_111
```

防止GC建议执行

```bash
pgloader mysql://zongheng_sdb:[password]@192.168.0.200:3165/zongheng_111 postgresql://postgres:[password]@192.168.0.200:5432/zongheng_111
```



