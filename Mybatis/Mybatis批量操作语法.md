# Mybatis批量操作语法

### 批量增加
```mybatis
    <insert id="batchInsert" parameterType="java.util.List">
        insert into test(id,name,code,is_valid,remark,create_time)
        values
        <foreach collection="list" item="item" index="index" separator=",">
            (
            #{item.id},#{item.name},#{item.code},
            <choose>
                <when test="item.isValid != null and item.isValid !=''">
                    #{item.isValid},
                </when>
                <otherwise>
                    1,
                </otherwise>
            </choose>
            #{item.remark},now())
        </foreach>
    </insert>
```

### 批量更新
```mybatis
    <update id="batchUpdate" parameterType="java.util.List" >
        update test
        <trim prefix="set" suffixOverrides=",">
            <trim prefix="status=case" suffix="end,">
                <foreach collection="list" index="index" item="item">
                    WHEN id=#{item.id} THEN #{item.status}
                </foreach>
            </trim>
            <trim prefix="name=case" suffix="end,">
                <foreach collection="list" index="index" item="item">
                    WHEN id=#{item.id} THEN #{item.name}
                </foreach>
            </trim>
        </trim>
        WHERE id IN
        <foreach collection="list" index="index" item="item" separator="," open="(" close=")">
            #{item.id}
        </foreach>
    </update>
```