### 1. query 수정 : insert 한 row의 id 를 반환받고자 할 경우
```xml
<!-- 기존 -->
<insert id="insert">/* admin_info.insert */
      insert into admin_info (loginid, passwd, name, ip, phone, email, admin, group_id, part)
      values (#loginid#, #passwd#, #name#, #ip#, #phone#, #email#, #admin#, #groupId#, #part#)
      <selectKey resultClass="java.lang.Integer">
          select currval('admin_info_id_seq')
      </selectKey>
  </insert>

<!-- 수정 -->
<select  id="insert" resultType="int">/* admin_info.insert */
    insert into admin_info (loginid, passwd, name, ip, phone, email, admin, group_id, part)
    values (#{loginid}, #{passwd}, #{name}, #{ip}, #{phone}, #{email}, #{admin}, #{groupId}, #{part})
    RETURNING id;
</select >

<!--  returning 의 경우 postgres 에서만 지원하는 문법으로 returning  말고 select key 를 사용-->
<selectKey keyProperty="id" resultType="java.lang.Integer">
      select currval('s_pcscan_report_id')
</selectKey>
```


### 2. ibatis to mybatis : enum
```xml
<!-- mybatis enum 제거 -->
<!-- ## [ibatis] -->
<sql id="exampleSetClause">
        <iterate conjunction="," prepend="set" property="updateParam">
            $updateParam[].field$ =
            <isEqual property="updateParam[].type" compareValue="EXPR"> $updateParam[].value$ </isEqual>
            <isNotEqual property="updateParam[].type" compareValue="EXPR"> #updateParam[].value# </isNotEqual>
        </iterate>
</sql>

<!-- ## [mybatis] :: 해당변수의 어떤값에서 name() 을 가져온다고 써야 Enum과 String을 비교할 수 있음 -->
 <sql id="exampleSetClause">
        <foreach collection="updateParam" item="item" separator="," open="set ">
            ${item.field} =
                <if test='item.type.name() == "EXPR"'> ${item.value} </if>
                <if test='item.type.name() != "EXPR"'> #{item.value} </if>
        </foreach>
</sql>
```

### 3. 조건문
```xml
<!-- ibatis -->
<isEqual property="noSave" compareValue="false">
</isEqual>


<!-- mybatis -->
<if test="noSave == false">
</if>
```

### 4. upsert 구문
```sql
-- insert 를 하는데 sid 가 충돌일 경우 update 진행
insert into MY_TABLE (sid, id, key, value)
values (#{sid}, #{id}, #{key}, #{value})
on conflict (sid)
do update set
    id = #{id}
    , key = #{key}
    , value = #{value}
```

### 5. table, column 존재여부 확인
```sql
-- 테이블 존재여부 확인
select count(*) from pg_tables where schemaname = 'public' and tablename = #{tableName}

-- 특정 테이블에 해당 컬럼명이 있는지 확인
select count(*) from information_schema.columns where table_name = #{tableName} and column_name = #{columnName}
```
- 참고URL
    - [[PostgreSQL] query로 테이블 존재여부 판단](https://bloodguy.tistory.com/entry/PostgreSQL-query%EB%A1%9C-%ED%85%8C%EC%9D%B4%EB%B8%94-%EC%A1%B4%EC%9E%AC%EC%97%AC%EB%B6%80-%ED%8C%90%EB%8B%A8)
    - [[postgresql][postgresql 컬럼 조회]컬럼 있는지 확인](https://blog.naver.com/angersadness/221120647555)
