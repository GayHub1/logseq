- tags: #[[SpringBoot]] #[[CMD]]
- date: [[2020-07-10  ]]
- ```java
  System.out.println(AreaUtil.camelName("ioaName"));
  Runtime runtime = Runtime.getRuntime();
  Process p = null;
  try {
      //此处执行的是ipconfig命令，可以换成任何cmd 里的命令。
      p = runtime.exec("cmd /c ipconfig /all");
      System.out.println(AreaUtil.camelName("ioaName"));
      BufferedReader reader = new BufferedReader(new InputStreamReader(p.getInputStream(), "GBK"));
      String line = null;
      while ((line = reader.readLine()) != null) {
          System.out.println(line + "\n");
      }
  } catch (IOException e) {
      e.printStackTrace();
  } finally {
      if (p != null) {
          p.destroy();
      }
  }
  System.out.println("do soming" + Calendar.getInstance().getTime());
  ```