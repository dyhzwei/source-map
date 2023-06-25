



```java
@Override
    public Result execute(PtItemVersion version, PtItemVersionDetail detail) {
        Result result=new Result();
        result.setPlatform(version.getPlatform());

        try {

            commonLogService.add(PtItemVersionDetail.class,detail.getDetailId(),"版本任务开始执行","SYS");
//            ShopConfig shopConfig = shopConfigMapper.selectByPrimaryKey(version.getShopId());
            Long productId = Long.valueOf(detail.getItemId());

            JdZyItemEditVo editVo = JSON.parseObject(detail.getContent(), JdZyItemEditVo.class);
            TraceInfo traceInfo = new TraceInfo(TraceInfoI.Type.VERSION,version.getVersionId());
            traceInfo.setUserName("SYS");
            traceInfo.setUserId(-1L);

            JdzyItemInfoService.EditExchange exchange = new  JdzyItemInfoService.EditExchange();
            exchange.setUserId(-1L);
            exchange.setUserName("SYS");
            CommonResult commonResult = jdzyItemInfoService.edit(editVo,exchange);

            if (!commonResult.isSuccess()){
                result.setSuccess(false);
                result.setResCode("500");
                result.setMessage(commonResult.getMessage());
            }
            else {
                result.setSuccess(true);
            }

            if (result.getSuccess()){
                commonLogService.add(PtItemVersionDetail.class,detail.getDetailId(),"版本任务执行成功","SYS");
                commonLogService.add(JdItemInfoService.commonLogType,productId,"版本修改商品成功","SYS","");
            }
            return result;
        }catch (Exception e){
            log.error("京东自营版本更新商品出错",e);
            result.setSuccess(false);
            result.setPlatform(version.getPlatform());
            result.setResCode("500");
            result.setMessage("京东自营版本更新商品出错"+e.getMessage());
            commonLogService.add(PtItemVersionDetail.class,detail.getDetailId(),"版本任务执行失败","SYS");
            return result;
        }
    }
```

