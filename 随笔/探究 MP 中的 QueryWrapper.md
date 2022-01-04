# 探究 MP 中的 QueryMapper
## 1 探究原因
在对 MP 中填入各种 eq、oederBy 等查询条件时，QueryMapper 中会存储这些关键字信息。在一次编写单元测试想对其进行校验时，我花费一番功夫阅读了 MP 中相关源码，自行编写了一个简单的工具类。其功能是取出 QueryMapper 中的真实关键字进行判断、对比，而非带有形如 "MPGENVAL" 关键字的 MP 自动拼接的 sql 语句。

## 2 工具类编写的心路历程
将查询条件分为 3 大类：normal、having、orderBy
### normal

```java
public class QueryWrapperParamCheckUtil {
    /**
     * 判断所有参数均相等
     *
     * @param normalWanted normal 条件数组
     */
    public static boolean normalCheck(Object[] normalWanted, Wrapper<?> wrapper) {
        AbstractWrapper wp = (AbstractWrapper) wrapper;

        // Map 的 key 记录参数、关键字等；value 记录参数出现的次数（用于比较相同参数多次出现，需要判断其出现次数的情况）
        Map<Object, Integer> normalMap = new HashMap<>();

        if (!isEmptyNormalParams(wp))
            getNormalParams(normalMap, wp);

        // 处理输入参数中的集合并进行拆分
        if (normalWanted != null)
            normalWanted = splitArray(normalWanted);

        return isEquals(normalWanted, normalMap);

    }

    /**
     * 判断所有参数均相等
     *
     * @param havingWanted having 条件数组
     */
    public static boolean havingCheck(Object[] havingWanted, Wrapper<?> wrapper) {
        AbstractWrapper wp = (AbstractWrapper) wrapper;

        // Map 的 key 记录参数、关键字等；value 记录参数出现的次数（用于比较相同参数多次出现，需要判断其出现次数的情况）
        Map<Object, Integer> havingMap = new HashMap<>();

        if (!isEmptyHaving(wp))
            getHavingParams(havingMap, wp);

        // 处理输入参数中的集合并进行拆分
        if (havingWanted != null)
            havingWanted = splitArray(havingWanted);

        return isEquals(havingWanted, havingMap);

    }

    /**
     * 判断所有参数均相等
     *
     * @param orderByWanted orderBy 排序需求
     */
    public static boolean orderByCheck(Object[] orderByWanted, Wrapper<?> wrapper) {
        AbstractWrapper wp = (AbstractWrapper) wrapper;

        // Map 的 key 记录参数、关键字等；value 记录参数出现的次数（用于比较相同参数多次出现，需要判断其出现次数的情况）
        Map<Object, Integer> orderByMap = new HashMap<>();

        if (!isEmptyOrderBy(wp))
            getOrderByParams(orderByMap, wp);

        // 处理输入参数中的集合并进行拆分
        if (orderByWanted != null)
            orderByWanted = splitArray(orderByWanted);

        return isEquals(orderByWanted, orderByMap);

    }

    /**
     * 校验 LastSQL
     *
     * @param lastSQL LastSQL
     * @return 若包含 LastSQL 返回 true，否则返回 false
     */
    public static boolean lastSQLCheck(String lastSQL, Wrapper<?> wrapper) {
        AbstractWrapper wp = (AbstractWrapper) wrapper;
        String sqlSegment = wp.getSqlSegment();
        // 重置 map，避免重复调用重复获取
        wp.getParamNameValuePairs().clear();
        return sqlSegment.indexOf(lastSQL) == sqlSegment.length() - lastSQL.length();
    }

    /**
     * 判断所有参数均相等
     *
     * @param normalWanted  normal 条件数组
     * @param havingWanted  having 条件数组
     * @param orderByWanted orderBy 排序需求
     */
    @Deprecated
    public static boolean paramCheck(Object[] normalWanted, Object[] havingWanted, Object[] orderByWanted, Wrapper<?> wrapper) {
        AbstractWrapper wp = (AbstractWrapper) wrapper;

        // Map 的 key 记录参数、关键字等；value 记录参数出现的次数（用于比较相同参数多次出现，需要判断其出现次数的情况）
        Map<Object, Integer> normalMap = new HashMap<>();
        Map<Object, Integer> havingMap = new HashMap<>();
        Map<Object, Integer> orderByMap = new HashMap<>();

        if (!isEmptyNormalParams(wp))
            getNormalParams(normalMap, wp);
        if (!isEmptyHaving(wp))
            getHavingParams(havingMap, wp);
        if (!isEmptyOrderBy(wp))
            getOrderByParams(orderByMap, wp);

        if (normalWanted != null)
            normalWanted = splitArray(normalWanted);
        if (havingWanted != null)
            havingWanted = splitArray(havingWanted);
        if (orderByWanted != null)
            orderByWanted = splitArray(orderByWanted);

        return isEquals(normalWanted, normalMap)
                && isEquals(havingWanted, havingMap)
                && isEquals(orderByWanted, orderByMap);

    }

    /**
     * 判断预期结果和实际结果是否完全相等（大小相等，内容一致）
     *
     * @param wanted 预期结果
     * @param res    实际结果
     * @return 相等返回 true ，不相等返回 false
     */
    private static boolean isEquals(Object[] wanted, Map<Object, Integer> res) {
        return sameSize(wanted, res) && sameParam(wanted, res);
    }

    /**
     * 判断预期结果和实际结果大小是否相等
     *
     * @param wanted 预期结果
     * @param res    实际结果
     * @return 大小相等或输入预期为空时返回 true，不相等返回 false
     */
    private static boolean sameSize(Object[] wanted, Map<Object, Integer> res) {
        if (null == wanted) {
            return true;
        }
        int size = 0;
        for (Integer value : res.values()) {
            size += value;
        }
        return wanted.length == size;
    }

    /**
     * 判断预期结果和实际结果内容是否一致
     *
     * @param wanted 预期结果
     * @param res    实际结果
     * @return 内容一致或输入预期为空时返回 true，任一不等返回 false
     */
    private static boolean sameParam(Object[] wanted, Map<Object, Integer> res) {
        if (null == wanted) {
            return true;
        }
        /*
          遍历预期结果，在 Map 中减少计数
         */
        for (Object param : wanted) {
            if (res.containsKey(param)) {
                res.put(param, res.get(param) - 1);
            }
        }
        /*
          若存在 key 的计数没有减到 0 则返回 false
         */
        for (Integer counts : res.values()) {
            if (counts != 0)
                return false;
        }
        return true;
    }

    /**
     * 获取 normal 参数
     *
     * @param paramMap 实际结果
     */
    private static void getNormalParams(Map<Object, Integer> paramMap, AbstractWrapper wrapper) {
        // 获取 wrapper 中 expression 下 normal 中的关键字列表
        NormalSegmentList normalList = wrapper.getExpression().getNormal();

        for (ISqlSegment iSqlSegment : normalList) {
            /*
             * sql 关键字无法通过 getSqlSegment 转换，需要抓住异常
             * 最终 ParamNameValuePairs 中只有输入的查询条件参数
             */
            try {
                iSqlSegment.getSqlSegment();
            } catch (MybatisPlusException mybatisPlusException) {
                continue;
            }

        }

        for (Object key : wrapper.getParamNameValuePairs().keySet()) {
            // 获取 ParamNameValuePairs 中的value，将 DateTime 类型转换为 String
            Object curValue = wrapper.getParamNameValuePairs().get(key);
            paramMap.put(curValue, paramMap.getOrDefault(curValue, 0) + 1);
        }
        // 重置 map，避免重复调用重复获取
        wrapper.getParamNameValuePairs().clear();
    }

    /**
     * 获取 having 参数
     *
     * @param paramMap 实际结果
     */
    private static void getHavingParams(Map<Object, Integer> paramMap, AbstractWrapper wrapper) {
        // 获取 wrapper 中 expression 下 having 中的关键字列表
        HavingSegmentList havingList = wrapper.getExpression().getHaving();
        for (ISqlSegment curSqlSegment : havingList) {
            if (!curSqlSegment.getClass().equals(SqlKeyword.class)) {
                // 获取待拼接的 sql
                String value = curSqlSegment.getSqlSegment();
                String sqlAfterReplace = replacehavingSQLWithRealParams(value, wrapper);
                paramMap.put(sqlAfterReplace, paramMap.getOrDefault(sqlAfterReplace, 0) + 1);
            }
        }
        // 重置 map，避免重复调用重复获取
        wrapper.getParamNameValuePairs().clear();
    }

    /**
     * 获取 orderBy 参数
     *
     * @param paramMap 实际结果
     */
    private static void getOrderByParams(Map<Object, Integer> paramMap, AbstractWrapper wrapper) {
        // 获取 wrapper 中 expression 下 oederBy 中的关键字列表
        OrderBySegmentList orderByList = wrapper.getExpression().getOrderBy();
        for (ISqlSegment curSqlSegment : orderByList) {
            if (curSqlSegment.getClass().equals(SqlKeyword.class)) {
                String order = curSqlSegment.toString();
                paramMap.put(order, paramMap.getOrDefault(order, 0) + 1);
            }
        }
    }

    /**
     * 判断 normal 中是否有参数
     */
    public static boolean isEmptyNormalParams(Wrapper<?> wrapper) {
        return wrapper.getExpression().getNormal().size() == 0;
    }

    /**
     * 判断 having 中是否有参数
     */
    public static boolean isEmptyHaving(Wrapper<?> wrapper) {
        return wrapper.getExpression().getHaving().size() == 0;
    }

    /**
     * 判断 orderBy 中是否有参数
     */
    public static boolean isEmptyOrderBy(Wrapper<?> wrapper) {
        return wrapper.getExpression().getOrderBy().size() == 0;
    }

    /**
     * 将 having 子句中形如 {0} 的部分替换为真实参数
     *
     * @param sql 需要替换参数的 SQL 语句
     */
    private static String replacehavingSQLWithRealParams(String sql, AbstractWrapper wrapper) {
        // Mybatis Plus 自动替换的样式
        String autoReplace = "\\#\\{ew.paramNameValuePairs.MPGENVAL\\d\\}";
        // 正则表达式匹配
        Pattern pattern = Pattern.compile(autoReplace);
        Matcher matcher = pattern.matcher(sql);
        // 用于接收真实参数
        Object real;
        while (matcher.find()) {
            String needReplace = matcher.group(0);
            // 截取真实参数在 paramNameValuePairs 中的 key
            String paramNameValuePairsKey = needReplace.replace("#{ew.paramNameValuePairs.", "").replace("}", "");
            // 获取真实参数
            real = wrapper.getParamNameValuePairs().get(paramNameValuePairsKey);
            sql = sql.replace(needReplace, real.toString());
        }

        return sql;
    }

    /**
     * 处理输入数组中可能存在的集合类型并将其拆分
     *
     * @param original 输入数组
     * @return 拆分集合后的原数组
     */
    private static Object[] splitArray(Object[] original) {
        ArrayList<Object> tmpArray = new ArrayList<>();
        for (Object o : original) {
            if (o.getClass().isArray()) {
                tmpArray.addAll(Arrays.asList((Object[]) o));
                continue;
            }
            if (o instanceof List<?>) {
                tmpArray.addAll((List<?>) o);
                continue;
            }
            tmpArray.add(o);
        }
        return tmpArray.toArray();
    }

}

```