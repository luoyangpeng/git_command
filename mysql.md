# MYSQL
### 查询 id =1 的所有下级
$ select id from 
	( SELECT id,parent_id,
	@le:= IF (parent_id = 0 ,0,
	IF( LOCATE(CONCAT('|',parent_id,':'),@pathlevel) > 0,SUBSTRING_INDEX( SUBSTRING_INDEX(@pathlevel,CONCAT('|',parent_id,':'),-1),'|',1) +1,@le+1) ) levels,@pathlevel:= CONCAT(@pathlevel,'|',id,':', @le ,'|') pathlevel	,@pathnodes:= 
	IF( parent_id =0,',0',CONCAT_WS(',',
	IF( LOCATE( CONCAT('|',parent_id,':'),@pathall) > 0 , SUBSTRING_INDEX( SUBSTRING_INDEX(@pathall,CONCAT('|',parent_id,':'),-1),'|',1),@pathnodes ) ,parent_id ) )paths	,@pathall:=CONCAT(@pathall,'|',id,':', @pathnodes ,'|') pathall 
	FROM 表名称, 
	(SELECT @le:=0,@pathlevel:='', @pathall:='',@pathnodes:='') vv 
	ORDER BY parent_id,id ) src 
	where LOCATE(',查询的Id',paths) > 0 
	order by id 