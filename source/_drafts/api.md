---
uuid: 49c2e3d0-120c-11ed-aacc-b11deac0aae6
title: api
abbrlink: 2902841359
tags:
---
<!--
 * @Description: 
 * @Author: Lee
 * @Date: 2021-03-11 15:30:48
 * @LastEditTime: 2021-03-11 15:51:03
-->
名称|接口地址|备注:
---|:--:|---:
获取主版本内容列表 | '/international/resourcecontent/list' | get
获取内容配置 | '/international/resourcecontent/detail' | get
新增或者更新内容 | '/international/resourcecontent/update' / '/international/resourcecontent/add' | post
删除资源位主版本内容 | '/international/resourcecontent/del' | post
更新内容序号 | '/international/resourcecontent/indexno/update' | post
资源位主版本内容下线到备用池 | '/international/resourcecontent/moveToSpare' | post
资源位内容列表，按序号进行定帧设置，或清空定帧设置 | '/international/resourcecontent/manualPosition/update' | post
获取定时版本列表 | '/international/resourcelocation/version/list' | post
新增定时版本 | '/international/resourcelocation/version/add' | post
删除定时版本 | '/international/resourcelocation/version/del' | post
终止定时版本发布 | '/international/resourcelocation/version/stop' | post
获取定时版本内容列表 | '/international/resourcelocation/version/content/list' | post
发布定时版本 | '/international/resourcelocation/version/timer/update' | post
更新定时版本内容序号 | '/international/resourcelocation/version/content/index/update' | post
获取定时版本内容配置 | '/international/resourcelocation/version/content/detail' | post
新增或修改定时版本内容配置 | '/international/resourcelocation/version/content/update' / '/international/resourcelocation/version/content/add' | post
下线定时版本的内容 | '/international/resourcelocation/version/content/moveToSpare' | post
删除资源位定时版本内容  | '/international/resourcelocation/version/content/del' | post
获取备用池内容列表 | '/international/resourcesparecontent/list' | post
获取备用池内容详情 | '/international/resourcesparecontent/detail' | get
新增或保存备用池内容 | '/international/resourcesparecontent/update' / '/international/resourcesparecontent/add' | post
删除备用池内容 | '/international/resourcesparecontent/del' | post
备用池内容导入线上主版本 | '/international/resourcesparecontent/importToMain' | post
备用池内容导入线上定时版本 | '/international/resourcelocation/version/content/import' | post
获取推荐规则配置 | '/external/twpastor/datastore/backend/common_recommend_rule/select' | get
保存推荐规则配置 | '/external/twpastor/datastore/backend/common_recommend_rule/save/${id}'`| post
获取推荐规则结果 | '/external/twpastor/datastore/backend/common_recommend_config/timeline' | get
查询节目 | '/api/locale/videos' | get
版本同步 | '//intl.lego.iqiyi.com/external/resource-collection/intl-be-resource-container/resourceContainer/syncSingleVersion' | post
内容同步 | '//intl.lego.iqiyi.com/external/resource-collection/intl-be-resource-container/resourceContainer/syncSingleContent' | post
按资源位ID查询其全部关联组内的资源位列表 | '//intl.lego.iqiyi.com/external/resource-collection/intl-be-resource-container/resourceContainer/listByResourceContainerId' | get
判断目标资源位是否存在重复内容 | '//intl.lego.iqiyi.com/external/resource-collection/intl-be-resource-container/resourceContainer/checkSyncSingleContentCondition' | post
判断目标资源位是否存在版本限制 | '//intl.lego.iqiyi.com/external/resource-collection/intl-be-resource-container/resourceContainer/checkSyncSingleVersionCondition' | post
保存映射关系 | '/international/resourcelocation/sync/mapping/save' | post
查询映射关系   根据id查询 或者查询全部 | '/international/resourcelocation/sync/mapping/list' | post
同步接口(国际站资源位同步内容到台湾资源位) | '/international/resourcelocation/sync/trigger' | post
根据qipuid获取同步结果 | '/international/resourcelocation/sync/history/list' | post