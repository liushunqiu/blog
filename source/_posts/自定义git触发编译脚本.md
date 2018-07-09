---
title: 自定义git触发编译脚本
date: 2018-06-20 11:03:06
tags:
	- Linux
---
1.通过 gitee push之后触发事件，执行WebHooks请求到我们设置的地址
2.在设置请求的地址里面判断当前commit里面是否包含某个"需要自动化编译的标示"，如果包含的话执行Linux上面的脚本编译自动部署项目
eg: 例子上面给出的是判断当前commit里面是否包含10086，如果包含的话执行Linux脚本
```java
@RequestMapping(value = "/common/run",method = RequestMethod.POST)
public ResponseEntity run(HttpServletRequest request) throws  ParseException {
    try {
        List<String> jsonList = org.apache.commons.io.IOUtils.readLines(request.getInputStream());
        if (Objects.nonNull(jsonList) && jsonList.size()>0){
            StringBuilder jsonBuilder = new StringBuilder();
            jsonList.forEach(str->{
                jsonBuilder.append(str);
            });
            JSONObject jsonObject = JSON.parseObject(jsonBuilder.toString());
            JSONArray commitsJSONArray = (JSONArray)jsonObject.get("commits");
            boolean flag = false;
            for(int i=0,length = commitsJSONArray.size();i<length;i++){
                JSONObject  childJSONObject = (JSONObject) commitsJSONArray.get(i);
                String message = (String) childJSONObject.get("message");
                log.info("获取到commit里面的消息 {}",message);
                if (message.contains("10086")){
                    flag = true;
                }
            }
            log.info("flag的值{}",flag);
            if (flag){
                process(new String[]{"sh","/root/run.sh"});
            }
        }
    } catch (Throwable throwable) {
        throwable.printStackTrace();
    }
    return ResponseEntity.ok("执行完毕");
}
/**
 *  调用脚本
 * @param cmdArray
 * @throws Throwable
 */
protected void process(String[] cmdArray) throws Throwable {
    ProcessBuilder pb = new ProcessBuilder(cmdArray);
    pb.redirectErrorStream(true);
    Process p = null;
    BufferedReader br = null;
    try {
        p = pb.start();
        br = new BufferedReader(new InputStreamReader(p.getInputStream()));
        String line = null;
        log.info("Invoke shell: {}", StringUtils.join(cmdArray, " "));
        while ((line = br.readLine()) != null) {
            log.info(line);
        }
        p.waitFor();
    } finally {
        if (br != null) {
            br.close();
        }
        if (p != null) {
            p.destroy();
        }
    }
}
```
![](/images/git_自定义_push.png)
