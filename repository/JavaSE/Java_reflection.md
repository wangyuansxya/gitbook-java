**Java反射总结**

1.反射获取类的class对象的方式

	Class<Student> c1 = (Class<Student>) stu1.getClass();
	Class<Student> c2 = Student.class;
	Class<Student> c = (Class<Student>) 		Class.forName("com.reflex.Student");

2.证明在运行期间，一个类，只有一个Class对象产生

	System.out.println("是否c1=c2" + (c1 == c2));
	System.out.println("是否c=c1" + (c == c1));

3.通过class对象获取类的实例

	Student stu = c.newInstance();

4.获取类的私有变量

	Field f = c.getDeclaredField("name");

5.允许通过反射访问字段

	f.setAccessible(true);

6.给字段赋值

	f.set(stu, "刘德华");

7.获取类的构造函数并初始化该类

	Constructor<Student> constructor = c.getDeclaredConstructor(String.class);
	Student student = constructor.newInstance("aaaa");

String.class是构造参数

8.获取类方法并执行方法

	Method method = c.getDeclaredMethod("setName", String.class);
	method.invoke(stu, "zzz");

	Method getName = c.getDeclaredMethod("getName");
	String name = (String) getName.invoke(stu);

9.通过反射越过泛型检查

	List<?> list = stu.getList();
	Class<?> listClass = list.getClass();
	Method listAdd = listClass.getDeclaredMethod("add", Object.class);
	listAdd.invoke(list, 100);
	System.out.println("list设置=" + list.get(0));

10.通过使用配置文件，反射给对象赋值

10.1，新建文件setting.txt

	className=com.reflex.Student
	methodName=setCourseName

10.2 获取配置文件中的数据

	private static String getProperty(String key) throws IOException {
		Properties properties = new Properties();
		FileReader reader = new FileReader("setting.txt");
		properties.load(reader);
		reader.close();
		return properties.getProperty(key);
	}

10.3 赋值

	String className = getProperty("className");
	String methodName = getProperty("methodName");

	Method propertyMethod = stu.getDeclaredMethod(methodName, String.class);
	Student propertyStudent = stu.newInstance();
	propertyMethod.invoke(propertyStudent, "cccc");

	Field teaName = stu.getDeclaredField("name");
	teaName.setAccessible(true);