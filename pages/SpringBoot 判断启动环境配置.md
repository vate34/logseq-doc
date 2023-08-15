- #代码片段
- ```java
  public class EnvironmentUtil {
      private static ApplicationContext applicationContext;
      private static byte environ = 0;
  
      public static void setApplicationContext(ApplicationContext context) {
          EnvironmentUtil.applicationContext = context;
          Environment env = applicationContext.getEnvironment();
          String[] activeProfiles = env.getActiveProfiles();
          for (String profile : activeProfiles) {
              if ("dev".equals(profile)) {
                  environ = 1;
              } else {
                  environ = 2;
              }
              break;
          }
      }
  
      public static boolean isDev() {
          return environ == 1;
      }
  }
  ```