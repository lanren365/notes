# 延迟List值创建
这个包是Apache提供的，当List中的对象被需要是进行创建。事例如下：
```java
public static void main(String[] args) {   
    // Example n°1 with a customized Factory   
    {   
        System.out.println("------ Example 1 with a customized Factory -------");   
        Factory factory = new Factory() {   
             public Object create() {   
                 try{   
                  Thread.currentThread().sleep(1000);   
                 }catch (Throwable e) {   
                  e.printStackTrace();   
                 };   
                 return new GregorianCalendar().getInstance();   
             }   
         };   
         Object obj = null;   
        List<Calendar> listCal = new ArrayList<Calendar>();   
        System.out.println("1. listCal.size()="+listCal.size());   
        listCal = ListUtils.lazyList(listCal, factory);   
        System.out.println("2. listCal.size()="+listCal.size());   
        //   
        obj = listCal.get(3);   
        System.out.println(obj instanceof GregorianCalendar);   
        System.out.println(((GregorianCalendar)obj).getTime().toString());   
        System.out.println("3. listCal.size()="+listCal.size());   
        //   
        obj = listCal.get(5);   
        System.out.println(obj instanceof GregorianCalendar);   
        System.out.println(((GregorianCalendar)obj).getTime().toString());   
        System.out.println("4. listCal.size()="+listCal.size());   
        //   
        listCal.remove(1);   
        System.out.println("5. listCal.size()="+listCal.size());   
        listCal.remove(3);   
        System.out.println("6. listCal.size()="+listCal.size());   
        //   
        obj = listCal.get(4);   
        System.out.println(obj instanceof GregorianCalendar);   
        System.out.println(((GregorianCalendar)obj).getTime().toString());   
        System.out.println("7. listCal.size()="+listCal.size());   
    }   
    // Example n°2 with factory from Apache library   
    {   
        System.out.println("------ Example 2 with factory from Apache library -------");   
        List<Calendar> listCal = new ArrayList<Calendar>();   
        listCal = ListUtils.lazyList(listCal, FactoryUtils.instantiateFactory(GregorianCalendar.class));   
        Object obj1 = listCal.get(1);   
        System.out.println(obj1 instanceof GregorianCalendar);   
        System.out.println(((GregorianCalendar)obj1).getTime().toString());   
        //   
        Object obj12 = listCal.get(12);   
        System.out.println(obj12 instanceof GregorianCalendar);   
        System.out.println(((GregorianCalendar)obj12).getTime().toString());   
    }         
}    
```
运行结果如下：
```md
> ------ Example 1 with a customized Factory -------   
1. listCal.size()=0   
2. listCal.size()=0   
true   
Mon May 14 13:40:32 CEST 2012   
3. listCal.size()=4   
true   
Mon May 14 13:40:33 CEST 2012   
4. listCal.size()=6   
5. listCal.size()=5   
6. listCal.size()=4   
true   
Mon May 14 13:40:34 CEST 2012   
7. listCal.size()=5   
------ Example 2 with factory from Apache library -------   
true   
Mon May 14 13:40:34 CEST 2012   
true   
Mon May 14 13:40:34 CEST 2012  
```