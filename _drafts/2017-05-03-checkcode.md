OrderInfoExtraImpl.java isNeedCheckCode  tLogger.info :"isNeedCheckCode#1"
CheckSubmitServiceImpl checkRedisAnswer  umpkey :getCheckCodeAnswer
PaymentConditionServiceImpl.java needCheckCode 
PaymentConditionServiceImpl.java queryCheckCode






```
    /**
     * 如果开启所有商品的验证码时 需要在初始化订单前先进行验证码的校验
     */
    public void checkAnswerCodeBeforeInitOrder(OrderFrom orderFrom, String realAnswer, String answer, String checkRid, int userlevel) {

        /**
         * 验证码限流开关
         */
        if (userlevel != 0) {
            LimiterConfig limiterConfig = configService.getConfigByTypeId(ConfigTypeEnum.限流配置.getValue(), LimiterConfig.class);
            if (limiterConfig != null) {
                Map<String, UserLevelLimitConfig> userLevelMap = limiterConfig.getUserLevelLimit();
                if (userLevelMap != null && !userLevelMap.isEmpty()) {
                    if (userLevelMap.containsKey(userlevel + "")) {
                        UserLevelLimitConfig useLimitConfig = userLevelMap.get(userlevel + "");
                        if (useLimitConfig.getCheckCode() != 0) {
                            checkRedisAnswer(checkRid, answer, orderFrom);
                            return;
                        }
                    }
                }
            }
        }
        YushouLimitConfig yushouLimitConfig = configService.getConfigByTypeId(ConfigTypeEnum.预约防刷配置.getValue(), YushouLimitConfig.class);
        if (yushouLimitConfig != null && yushouLimitConfig.isOpenCheckCodeByAllSku()) {
            checkRedisAnswer(checkRid, answer, orderFrom);
            return;
        }
    }


    private void checkRedisAnswer(String checkRid, String answer, OrderFrom orderFrom) {

        if (!isCheckAnserCode(orderFrom.getOriginId())) {
            return;
        }
        // 验证码开关
        DemotePowerConfig demotePowerConfig = configService.getConfigByTypeId(DemotePowerConfig.getTradeSdkConfigId(), DemotePowerConfig.class);
        // 验证码开关打开则校验验证码
        if (demotePowerConfig != null && demotePowerConfig.getOpenCheckCode() == 1) {
            return;
        }
        if (orderFrom.isCaptchaHasChecked()) {
            return;
        }
        if (StringUtils.isNotBlank(checkRid) && StringUtils.isNotBlank(answer)) {
            Object queryRedisCountObject = null;
            Object delRedisCountObject = null;
            try {
                queryRedisCountObject = CountExecutor.start(getUMPKey("getCheckCodeAnswer"));
                String redisAnswer = checkCodeRdies.get(CacheKeyUtils.getCheckCodeRedisKey(checkRid));
                CountExecutor.end(queryRedisCountObject);
                if (redisAnswer != null && redisAnswer.toUpperCase().equals(answer.toUpperCase())) {
                    try {
                        delRedisCountObject = CountExecutor.start(getUMPKey("delCheckCodeAnswer"));
                        checkCodeRdies.del(CacheKeyUtils.getCheckCodeRedisKey(checkRid));
                        CountExecutor.end(delRedisCountObject);
                        orderFrom.setCaptchaHasChecked(true);
                    } catch (Exception ex) {
                        CountExecutor.catchException(delRedisCountObject);
                    }
                } else {
                    throw new EngineRuntimeException(ResultCodeEnum.OTHER__CHECK_ANSWER);
                }
            } catch (EngineRuntimeException e) {
                CountExecutor.catchException(queryRedisCountObject);
                throw e;
            } catch (Exception e) {
                CountExecutor.catchException(queryRedisCountObject);
                logger.error("checkSubmitCoreImpl-checkAnswer" + e);
            }finally {
            }
        } else {
            throw new EngineRuntimeException(ResultCodeEnum.OTHER__CHECK_ANSWER);
        }

    }
```    

submitorder
2322:当前用户级别在userLevelLimit中，且配置了checkCode!=0时开启校验。
    2402:isOpenCheckCode=0（当前值为0）
并且
2235:openCheckCodeByAllSku=true(当前值为false)
    2402:isOpenCheckCode=0（当前值为0）

```
    @Override
    public boolean needCheckCode(CartBO cartBO) {
        // 验证码开关
        DemotePowerConfig demotePowerConfig = configService.getConfigByTypeId(DemotePowerConfig.getTradeSdkConfigId(), DemotePowerConfig.class);
        if (demotePowerConfig == null || demotePowerConfig.getOpenCheckCode() == 1) {
            return false;
        }
        try {
            // 获取购物车
            if (cartBO != null) {
                List<SkuBO> skuList = cartBO.obtainAllSkuList();
                // 初始化购物车中商品的相关属性
                if (skuList != null && skuList.size() > 0) {
                    // productBussiness.initProductProperty(skuList);
                    for (SkuBO skuBO : skuList) {
                        if (skuBO.getSkuExtsBO().isSkuNeedCheckCodeFlag()) {
                            return true;
                        }
                    }
                }
            }
        } catch (Exception e) {
            tLogger.error("needCheckCode", null, "异常", e, cartBO);
        }
        return false;
    }

    /**
     * 是否需要验证码
     */

    public CheckCodeVO queryCheckCode(CartBO cartBO,UserBO userBO ,ClientInfo clientInfo) {
        // 验证码开关
        CheckCodeVO checkCodeVO = new CheckCodeVO();
        try {
            DemotePowerConfig demotePowerConfig = configService.getConfigByTypeId(DemotePowerConfig.getTradeSdkConfigId(), DemotePowerConfig.class);
            if (demotePowerConfig == null || demotePowerConfig.getOpenCheckCode() == 1) {
                return checkCodeVO;
            }
            /**
             * 验证码限流开关
             */
            if(userBO != null && userBO.getUserLevel() != 0){
                LimiterConfig limiterConfig = configService.getConfigByTypeId(ConfigTypeEnum.限流配置.getValue(), LimiterConfig.class);
                if(limiterConfig != null){
                    Map<String,UserLevelLimitConfig> userLevelMap = limiterConfig.getUserLevelLimit();
                    if(userLevelMap != null && !userLevelMap.isEmpty()){
                        if(userLevelMap.containsKey(userBO.getUserLevel()+"")){
                            UserLevelLimitConfig useLimitConfig = userLevelMap.get(userBO.getUserLevel()+"");
                            if(useLimitConfig.getCheckCode() != 0){
                                return buildCheckCodeVO(clientInfo, checkCodeVO);
                            }
                            if(useLimitConfig.getTooManyRatio()>0){
                                String key = CacheKeyUtils.SUBMIT_TOTAL+userBO.getPin();
                                String totalTimesStr = redisFactory.getRedisInstance().get(key);
                                int totalTimes = totalTimesStr==null?0:Integer.valueOf(totalTimesStr).intValue();
                                if(totalTimes>0 &&totalTimes>=useLimitConfig.getTooManyThreshold()){
                                    return buildCheckCodeVO(clientInfo, checkCodeVO);
                                }
                            }
                        }
                    }
                }
            }

            /* 全开验证码 */
            YushouLimitConfig yushouLimitConfig = configService.getConfigByTypeId(ConfigTypeEnum.预约防刷配置.getValue(), YushouLimitConfig.class);
            if (yushouLimitConfig != null) {
                if (yushouLimitConfig.isOpenCheckCodeByAllSku()) {
                    return buildCheckCodeVO(clientInfo, checkCodeVO);
                }
            }

            // 获取购物车
            if (cartBO != null) {
                List<SkuBO> skuList = cartBO.obtainAllSkuList();
                // 初始化购物车中商品的相关属性
                if (skuList != null && skuList.size() > 0) {
                    for (SkuBO skuBO : skuList) {
                        if (skuBO.getSkuExtsBO().isSkuNeedCheckCodeFlag()) {
                            return buildCheckCodeVO(clientInfo, checkCodeVO);
                        }
                    }
                }
            }

        } catch (Exception e) {
            tLogger.error("queryCheckCode", null, "异常", e, cartBO);
        } finally {
        }
        return checkCodeVO;
    }

```
getCurrentOrder
"2402.isOpenCheckCode!=1
    4、sku要求开启验证码。有扩展属性needCheckCode=""1""的sku（购物车传下来的）"
    1、根据用户级别校验验证码。2322设置了对应的用户级别checkCode!=0
    2、根据用户级别限制提单频率到达上限。2322设置了对应的用户级别tooManyRatio>0&&用户指定时间内（tooManyInterval,默认86400）访问次数超过阀值（tooManyThreshold，默认值Integer.MAX_VALUE）
    3、预售配置要求开启验证码。2235.openCheckCodeByAllSku==true时开启校验
 


