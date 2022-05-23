> [操作手册-Link](https://gitee.com/filichen/puerts/blob/master/doc/unity/manual.md)

- 获取泛型，$generic()泛型，< T >

  每种泛型类型至少声明过一次，例如

  ```tsx
  let Dictionary = $generic(
      System.Collections.Generic.Dictionary$2, 
      System.String, 
      System.String)
  ```

  

  